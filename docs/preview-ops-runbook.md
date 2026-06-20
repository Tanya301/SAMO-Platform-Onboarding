# Field-Record Preview Pipeline — OPERATOR RUNBOOK

Audience: a human operator (Tanya / Nik) with host access. This is the recovery
path when a `field-record` preview won't come up. The point of this document:
**you do not need Claude Code (or any AI dev tool) to run or fix this pipeline.**
Everything below is copy-pasteable from a normal shell on the host.

Host: `samo-we-field-record` (178.105.246.151, ssh port 2223).
Runs as the unprivileged **`agent`** user. Nothing here needs general root —
only the narrow scoped NOPASSWD grants listed below.

> Naming: `EN` (env-name) = `field-record-<branchLabel>`, where `branchLabel`
> is the branch lowercased with every run of non-`[a-z0-9]` collapsed to `-` and
> trimmed. Preview branches live under `refs/heads/preview/*`.
> Example: branch `preview/pink-background-2`
> -> `EN=field-record-preview-pink-background-2`
> -> public URL `https://field-record-preview-pink-background-2.samo.cat/`.

---

## 1. How auto-deploy + teardown work

Two cron jobs owned by the `agent` user do the whole pipeline. See them with
`crontab -l` (as `agent`):

```
*/3  * * * * flock -n /tmp/prev-recon.lock    /home/agent/bin/preview-reconcile.sh >> /home/agent/preview-reconcile.log 2>&1
*/10 * * * * flock -n /tmp/prev-teardown.lock /home/agent/bin/preview-teardown.sh  >> /home/agent/preview-teardown.log 2>&1
```

| | Auto-deploy / update | Auto-teardown |
| --- | --- | --- |
| Script | `/home/agent/bin/preview-reconcile.sh` | `/home/agent/bin/preview-teardown.sh` |
| Interval | every **3 min** | every **10 min** |
| Log | `/home/agent/preview-reconcile.log` | `/home/agent/preview-teardown.log` |
| flock | cron: `/tmp/prev-recon.lock`; script also self-locks `/tmp/preview-reconcile.flock` | cron: `/tmp/prev-teardown.lock`; script self-locks `/tmp/preview-teardown.flock` |
| Runs as | `agent` | `agent` |

**reconcile (deploy + update):** for every open `preview/*` branch on origin it
makes the live env match the branch's current origin commit.
- env dir missing OR service not active -> **fresh deploy** (git clone, `npm ci && npm run build`, isolated DBLab clone, `.env` with its own `DATABASE_URL`+`PORT`, systemd unit, caddy vhost, health-probe).
- env live but deployed HEAD != origin sha -> **redeploy in place** (`git fetch` + `reset --hard origin/<branch>` + `npm ci && npm run build` + restart), keeping the **same PORT and the same DB clone**.
- env live and already at origin sha -> **skip** (logged).
- reconcile **never tears anything down**.

**teardown (retire only):** retires *orphaned* previews — a live
`field-record-preview-*` env whose source branch no longer exists on origin
(PR merged/closed). It computes OPEN = labels of open `preview/*` branches, LIVE
= every `field-record-preview-*` systemd unit, and retires `LIVE - OPEN`.
Safety guards baked in: hard never-touch list (`field-record`, `field-record-main`,
`field-record-demo-red-login`); only `field-record-preview-*` units are ever
considered; **if `git ls-remote` fails or returns no preview branches it does
NOTHING** (a transient blip must never be read as "delete everything").

### The scoped NOPASSWD sudo grants the scripts rely on

The `agent` user is allowed (NOPASSWD) ONLY these privileged operations — this
is the entire privileged surface of the pipeline:

```
systemctl enable  --now field-record@*.service
systemctl disable --now field-record@*.service
systemctl reset-failed  field-record@*.service
systemctl reload caddy
tee   /etc/caddy/sites.d/*.caddy
rm -f /etc/caddy/sites.d/*.caddy
```

Note: a plain `systemctl restart field-record@*` is **NOT** granted. An in-place
restart is therefore done as `disable --now` -> `reset-failed` -> `enable --now`
(equivalent for a `Type=simple` `npm start` unit; the `.env` and DB clone are
left untouched). DBLab clone create/destroy and `git`/`npm` run unprivileged.

---

## 2. Operator commands (copy-paste)

All commands run as the **`agent`** user on the host. Set this once per shell:

```bash
export PATH=$PATH:/home/agent/bin          # for the dblab binary
APP=/opt/field-record/app
ENVS=/opt/field-record/envs
```

> To reach the host from your laptop:
> `ssh -i <samo-we-field-record key> -p 2223 agent@178.105.246.151`

### 2a. Read / tail the logs

```bash
tail -n 50 /home/agent/preview-reconcile.log      # last deploy/update activity
tail -n 50 /home/agent/preview-teardown.log       # last retire activity
tail -f    /home/agent/preview-reconcile.log      # follow live
journalctl -u field-record@<EN>.service -n 100 --no-pager   # one env's app logs
journalctl -u field-record@<EN>.service -f                  # follow one env
```

### 2b. Check an env's status + port + deployed commit

```bash
EN=field-record-preview-<label>
systemctl is-active field-record@$EN.service
systemctl status   field-record@$EN.service --no-pager | head -20
grep '^PORT=' $ENVS/$EN/.env                       # which local port it serves
git -C $ENVS/$EN rev-parse HEAD                    # deployed commit
git -C $ENVS/$EN log -1 --oneline
curl -s -o /dev/null -w '%{http_code}\n' http://localhost:$(grep -oE '[0-9]+' <(grep '^PORT=' $ENVS/$EN/.env))/   # local health
curl -s -o /dev/null -w '%{http_code}\n' https://$EN.samo.cat/                                                    # public health
```

What origin currently has for all preview branches (one round-trip):

```bash
git ls-remote --heads "$(git -C $APP remote get-url origin)" 'refs/heads/preview/*'
```

### 2c. Force a reconcile / teardown run now (don't wait for cron)

```bash
flock -n /tmp/prev-recon.lock    /home/agent/bin/preview-reconcile.sh >> /home/agent/preview-reconcile.log 2>&1
flock -n /tmp/prev-teardown.lock /home/agent/bin/preview-teardown.sh  >> /home/agent/preview-teardown.log 2>&1
# then read the tail:
tail -n 30 /home/agent/preview-reconcile.log
```

(The `flock` matches the cron entry so a manual run can never race the cron one;
the scripts also self-flock internally.)

### 2d. Manually deploy / redeploy / retire ONE preview

Normally you never do these by hand — reconcile/teardown do it. Use them only to
recover a stuck env or to act faster than the next cron tick.

**Most cases: just let reconcile/teardown do it** (2c). The scripts are
idempotent and re-entrant. If you must do it by hand:

**Redeploy in place (same branch, new commits — keep PORT + DB clone):**
```bash
BRANCH=preview/<label> ; EN=field-record-preview-<label>
cd $ENVS/$EN && git fetch origin "$BRANCH" && git reset --hard "origin/$BRANCH" \
  && npm ci && npm run build \
  && sudo /usr/bin/systemctl disable --now field-record@$EN.service \
  && sudo /usr/bin/systemctl reset-failed field-record@$EN.service \
  && sudo /usr/bin/systemctl enable  --now field-record@$EN.service
```
(`disable/reset-failed/enable --now` is used instead of `restart` because
`restart` is not in the NOPASSWD grant.)

**Fresh deploy (no live env yet):** simplest is to remove any stale dir and let
reconcile rebuild it:
```bash
EN=field-record-preview-<label>
rm -rf $ENVS/$EN
flock -n /tmp/prev-recon.lock /home/agent/bin/preview-reconcile.sh >> /home/agent/preview-reconcile.log 2>&1
```

**Retire one preview (full recipe):**
```bash
EN=field-record-preview-<label>
sudo /usr/bin/systemctl disable --now field-record@$EN.service
sudo /usr/bin/systemctl reset-failed field-record@$EN.service 2>/dev/null
sudo /usr/bin/rm -f /etc/caddy/sites.d/$EN.caddy
sudo /usr/bin/systemctl reload caddy
dblab clone destroy $EN
rm -rf $ENVS/$EN
```

### 2e. Unstick a FAILED env

```bash
EN=field-record-preview-<label>
systemctl status field-record@$EN.service --no-pager | head -20
journalctl -u field-record@$EN.service -n 80 --no-pager      # find the real error
sudo /usr/bin/systemctl reset-failed field-record@$EN.service
sudo /usr/bin/systemctl enable --now  field-record@$EN.service
# if it's wedged on bad code / half clone, wipe the dir and let reconcile rebuild:
rm -rf $ENVS/$EN
flock -n /tmp/prev-recon.lock /home/agent/bin/preview-reconcile.sh >> /home/agent/preview-reconcile.log 2>&1
tail -n 30 /home/agent/preview-reconcile.log
```

If the failure is `npm ci`/`npm run build`, it's the **branch's code**, not the
pipeline — the log line says `ERROR <branch>: npm ci/build failed`. That goes
back to the dev/bot to fix the branch; there is nothing to fix on the host.

---

## 3. KNOWN GAPS — need ROOT / platform work (operator's to fix, NOT Claude Code)

These are outside the `agent` user's grants. They are the operator's / platform
team's responsibility. **Do not ask an AI dev tool to "fix" these — they need
root or upstream platform changes.**

### 3a. The OS `preview-cleanup.service` is a dead no-op
`/etc/systemd/system/preview-cleanup.service` (triggered by
`preview-cleanup.timer`, ~every 30 min) is supposed to TTL-expire previews via
`/usr/local/bin/samo preview cleanup --ttl 3d --force`. But **`/usr/local/bin/samo`
does not exist on this host**, so the unit has `ConditionPathExists=/usr/local/bin/samo`
**unmet** and exits without doing anything — every run is "condition unmet,
dead." Tracking: https://gitlab.com/NikolayS/samo.team/-/issues/271

Our `agent` **`preview-teardown.sh`** cron is the working stand-in (it retires
orphaned previews on PR close). The **proper fix is platform-side**: ship the
`samo` binary (or formally retire this unit). Verify the dead state with:
```bash
systemctl status preview-cleanup.service --no-pager   # "condition unmet"
ls -l /usr/local/bin/samo                             # No such file
```
Do not delete our teardown cron expecting this unit to cover it — it does not.

### 3b. NO monitoring / alerting
Nothing pages anyone when a deploy fails, an env crashes, or a cron stops
running. Failures are only visible by **reading the logs** (section 2a). Until
real alerting exists, the operator's manual check is:
```bash
grep -i error /home/agent/preview-reconcile.log | tail
grep -iE 'warn|REFUS|ERROR' /home/agent/preview-teardown.log | tail
# and confirm cron actually ran recently (timestamps near "now"):
tail -n 3 /home/agent/preview-reconcile.log
```

### 3c. DBLab clone pool + port pool will eventually EXHAUST
Two finite pools back every preview:
- **Ports:** reconcile picks the lowest free TCP port in **3103–3199** (free =
  not in `ss -ltn` AND not declared `PORT=` in any env `.env`). ~97 slots.
- **DBLab clones:** one isolated thin-clone per preview, from the shared DBLab
  engine (clone ports are in the 6000s, assigned by DBLab).

**How to spot exhaustion** (it shows up as deploys that won't start):
```bash
# port pool — count what's taken in range and look for the log line:
grep 'no free port' /home/agent/preview-reconcile.log
ss -ltn | grep -oE ':31[0-9][0-9]' | sort -u
grep -rhsE '^PORT=' $ENVS/*/.env

# dblab clones — list them and watch for "clone create failed":
dblab clone list
grep 'dblab clone create failed' /home/agent/preview-reconcile.log
```
`ERROR <branch>: no free port in 3103-3199` or
`ERROR <branch>: dblab clone create failed` in the reconcile log = a pool is
full. **What to clear:** retire previews whose PRs are already merged/closed
(section 2d retire, or just let teardown run) — that frees both the port and the
clone. If genuinely all ~97 ports / all DBLab capacity are in use by *open*
previews, that is a **capacity / root decision** (raise DBLab pool size, widen
the port range, add a host) — escalate; the `agent` user cannot change either
limit.

### 3d. samohost `env create` is UNFINISHED — don't rely on it
The `samohost` CLI's `env create` path with `--db dblab` is **stubbed**: its
DBLab clone step is annotated *"PLAN HOOK: flags to be confirmed against the live
DBLab Engine CLI"* and has not been validated against a running engine. **Do not
use `samohost env create` to bring up a preview.** The working path is the
**`preview-reconcile.sh`** cron (auto) or the manual recipes in section 2d.

---

## 4. Escalation contract (READ THIS)

When a preview won't come up, the contract is:

1. **Eli (the bot) must never dead-end the client.** Every blocked reply still
   tells the client (a) what's done, (b) that it's being handled + a short ETA,
   (c) that their real site is untouched. Eli never says "can't do, bye." This
   is already in the bot's onboarding (`docs/bot-self-onboarding.md`, section
   "Publishing a preview & never dead-ending the client").
2. **Eli escalates to the OPERATOR (Tanya / Nik) — never to Claude Code.** The
   recovery path is a human with host access running the commands in this
   runbook. Claude Code is a dev tool for writing the app, not the on-call for
   the preview host.
3. The escalation must carry, every time:
   - the **branch** (`preview/<label>`) and the **PR link**,
   - the **exact failing log line** (from `/home/agent/preview-reconcile.log` or
     `journalctl -u field-record@<EN>.service`),
   - and the env-name / public URL.
4. The operator then uses sections 2 and 3 of this runbook to fix it (or, if
   it's a capacity/root gap in section 3, escalates that upstream) and reports
   back so Eli can send the client the working `200` link.

Short version: **client problem -> Eli handles + reassures -> Eli escalates to
the operator with branch + PR + failing log line -> operator fixes per this
runbook. Never dead-end the client; never escalate to Claude Code.**
