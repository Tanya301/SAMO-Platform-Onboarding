# Client Onboarding Checklist (operator)

Bring a new client project online, then hand it to a fresh OpenClaw bot. Built from Nik's 5 steps. Tags: **live** (works today) · **manual** (by hand today) · **proposed** (not built — don't promise it to a client).

## Steps

**1. Name + one-line description** — manual
- [ ] Agree the project name and a one-line description. Seeds the spec later.
- [ ] Agree and record the **app URL slug** (e.g. `field-record`, **not** the repo name `field-record-1`). It becomes `<slug>.samo.team` (prod) and `<slug>-<branch>.samo.cat` (previews); every downstream step and the [bring-up](bot-bringup-and-harness.md) doc need it.
- [ ] Capture the client's **starting point**: a brand-new app vs. an existing app with logins/real users — so onboarding and preview checks match the starting state.
- [ ] Classify **Personal information** as `yes`, `no`, or `not sure` before the
  first preview handoff. Treat `not sure` as `yes` when preparing its access,
  records, and testing instructions.

**2. Connect the code host** — manual
- [ ] Connect the client's GitHub/GitLab so the bot can open issues, push branches, open PRs. Mechanism: the client invites the SAMO machine account as a write collaborator, or SAMO provisions a deploy key + bot-account membership.
- [ ] Confirm the bot has push + PR/MR rights on the repo.
- Open: which mechanism (and could SAMO auto-connect the client's host instead)? See [open questions](../OPEN-QUESTIONS.md).

**3. Allocate host (prod + preview) + Telegram group** — live / manual
- [ ] Allocate a [samohost](https://github.com/NikolayS/samohost) host serving prod + previews (per-branch envs, DNS checks, deploys, auto-deploy trigger). *live*
- [ ] Stand up the OpenClaw bot + a Telegram group wired to prod. *manual, heavily — see [bring-up](bot-bringup-and-harness.md).*
- [ ] **Confirm the bot's code-host access works end-to-end before go-live** —
  it can both **push a branch** and **open a PR/MR** (a branch push alone won't
  publish a preview). If git or provider auth isn't set up, fix it here — never
  turn it into a client-facing failure later.
- [ ] Record the project's initial **preview safety profile**: whether access is
  public or sign-in-gated, whether records are test-only or copied from live,
  and which connected services are isolated, real, or unknown. Treat every
  unknown service as real until verified. The bot rechecks this before each
  preview handoff.
- [ ] Send the appropriate client kickoff. Use the canonical
  [Telegram kickoff](client-welcome-message.md) when there is already a site to
  inspect. For a brand-new app, record a project-specific kickoff and bootstrap
  guide in the agent assignment instead. Confirm the bot answers the client's
  first request or asks one clear starting question.
- Proposed: the **SAMO Solo** plan (~$30/mo) would package this as one offering. Plan/pricing not built; the host capability is real.

**4. Pick a name + domain** — manual / optional
- [ ] Pick and register a custom production domain (registrable via Cloudflare). **Optional/deferrable** — the bot works on `<app>.samo.team` from day one; a custom domain can come later.
- Proposed: **samoname** would pick + register the domain for the client (Tanya has a UI concept). Not built.

**5. Spec + build** — live
- [ ] Use [samospec](https://github.com/NikolayS/samospec) to turn intent into a reviewed spec — early, while scope is fuzzy.
- [ ] Hand to the bot. From here it runs the merge gate below; nothing ships without it.

## Merge gate (confirm the bot follows it)

issue → PR → preview on `<app>-<branch>.samo.cat`. Near done, all three before merge:
- [ ] PR-head CI **green**
- [ ] [samorev](https://github.com/Tanya301/samorev) is complete on the current
      head: deterministic CLI gate **PASS** plus actual code analysis. The CLI
      gate alone checks CI and draft state; it does not review the code. Follow
      the [bot-operation runbook](https://github.com/Tanya301/samorev/blob/main/docs/bot-operation.md).
- [ ] [samotest](https://github.com/Tanya301/samotest) evidence for any **UI**
      change is uploaded and its [review gate](https://github.com/Tanya301/samotest/blob/main/docs/samorev-integration.md)
      passes against the current PR/MR head SHA

Then the lifecycle is explicit: approved change → merge → publish through the
configured production channel (branch-tracked or tag-gated) → verify the
deployed SHA/version → **walk the safe changed production flow** before calling it
done → that change's preview is torn down → the next change gets a fresh preview
link. A merge or release tag alone is not proof of deployment.

## Tools

samo.team is on **GitLab**; the four tools are on **GitHub** (easy to mix up).

| Tool | For | Repo |
| --- | --- | --- |
| samospec | intent → reviewed spec | github.com/NikolayS/samospec |
| samorev | review PRs/code before humans trust it | github.com/Tanya301/samorev |
| samotest | run test scenarios, collect evidence | github.com/Tanya301/samotest |
| samohost | deploy/preview infra, DNS, triggers | github.com/NikolayS/samohost |
| samo.team | the platform | gitlab.com/NikolayS/samo.team |

## This engagement (fill in)

Copy these facts into the
[agent assignment template](agent-assignment-template.md); this checklist alone
is not the bot's assignment.

- **App slug:** _e.g. `field-record`_
- **Prod URL:** _`<slug>.samo.team`_
- **Repo:** _`<host>/<owner>/<repo>`_
- **Starting state:** _brand-new app · or existing app with logins/real users_
- **Personal information:** _yes · no · not sure (treat not sure as yes)_
- **Preview access:** _public to link · or sign-in required_
- **Preview records:** _test-only · or copied live records_
- **Connected services safe to test:** _verified list; “none” is valid_
- **Connected services real or unknown:** _do-not-test list_
