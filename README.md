# SAMO Platform Onboarding

Onboarding handbook for **OpenClaw bots**. An OpenClaw bot is the agent SAMO gives a client to run that client's own project.

**Each client gets exactly one OpenClaw bot — their single manager.** It delegates implementation to ephemeral **sub-agents** and drives the loop (issue → PR → preview → review → test → merge → deploy). Those sub-agents are the bot's disposable workers: not additional bots, and never client-facing. A bot is not a generic coding assistant.

## Two tracks

**Operator** — bring a new client project online:
- [Client onboarding checklist](docs/client-onboarding-checklist.md)
- [Open questions](OPEN-QUESTIONS.md) — automation gaps still to decide

**Bot self-onboarding** — what a new bot reads to start working:
- [Bot self-onboarding](docs/bot-self-onboarding.md)
- [Bot bring-up & harness](docs/bot-bringup-and-harness.md)

## Domains (get these right)

| Use | Pattern | Notes |
| --- | --- | --- |
| Client production | `<app>.samo.team` | Cloudflare-proxied wildcard, e.g. game-changers.samo.team |
| Client preview | `<app>-<branch>.samo.cat` | One per PR; DBLab thin-clone; DNS via `CLOUDFLARE_SAMOCAT` |
| SAMO internal | `samo.green` | Never client-facing — don't hand a client this URL |

**Live** = works today. **Proposed** = idea, not built — never shown to a client as real.
