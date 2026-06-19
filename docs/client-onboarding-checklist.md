# Client Onboarding Checklist (operator)

Bring a new client project online, then hand it to a fresh OpenClaw bot. Built from Nik's 5 steps. Tags: **live** (works today) · **manual** (by hand today) · **proposed** (not built — don't promise it to a client).

## Steps

**1. Name + one-line description** — manual
- [ ] Agree the project name and a one-line description. Seeds the spec later.

**2. Connect the code host** — manual
- [ ] Connect the client's GitHub/GitLab so the bot can open issues, push branches, open PRs.
- [ ] Confirm the bot has push + PR/MR rights on the repo.
- Open: could SAMO auto-connect the client's host instead? See [open questions](../OPEN-QUESTIONS.md).

**3. Allocate host (prod + preview) + Telegram group** — live / manual
- [ ] Allocate a [samohost](https://github.com/NikolayS/samohost) host serving prod + previews (per-branch envs, DNS checks, deploys, auto-deploy trigger). *live*
- [ ] Stand up the OpenClaw bot + a Telegram group wired to prod. *manual, heavily — see [bring-up](bot-bringup-and-harness.md).*
- Proposed: the **SAMO Solo** plan (~$30/mo) would package this as one offering. Plan/pricing not built; the host capability is real.

**4. Pick a name + domain** — manual
- [ ] Pick and register the production domain (registrable via Cloudflare).
- Proposed: **samoname** would pick + register the domain for the client (Tanya has a UI concept). Not built.

**5. Spec + build** — live
- [ ] Use [samospec](https://github.com/NikolayS/samospec) to turn intent into a reviewed spec — early, while scope is fuzzy.
- [ ] Hand to the bot. From here it runs the merge gate below; nothing ships without it.

## Merge gate (confirm the bot follows it)

issue → PR → preview on `<app>-<branch>.samo.cat`. Near done, all three before merge:
- [ ] PR-head CI **green**
- [ ] [samorev](https://github.com/Tanya301/samorev) returns **PASS**
- [ ] [samotest](https://github.com/Tanya301/samotest) walk evidence for any **UI** change

Then: merge → trigger auto-deploys from `origin/main` → new tag → **walk the deployed tag** before calling it done.

## Tools

samo.team is on **GitLab**; the four tools are on **GitHub** (easy to mix up).

| Tool | For | Repo |
| --- | --- | --- |
| samospec | intent → reviewed spec | github.com/NikolayS/samospec |
| samorev | review PRs/code before humans trust it | github.com/Tanya301/samorev |
| samotest | run test scenarios, collect evidence | github.com/Tanya301/samotest |
| samohost | deploy/preview infra, DNS, triggers | github.com/NikolayS/samohost |
| samo.team | the platform | gitlab.com/NikolayS/samo.team |

Current client: **game changers** — https://game-changers.samo.team/
