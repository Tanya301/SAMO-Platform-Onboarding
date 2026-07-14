# SAMO Client-Agent Handbook

Working handbook for onboarding **OpenClaw bots** and the operators who bring
them into client projects. It documents the current human-run workflow. A
future harness may enforce parts of it, but enforcement is not required to use
this handbook today.

An OpenClaw bot is the agent SAMO gives a client to run that client's own
project.

For workflow questions, [Development principles](PRINCIPLES.md) and
[Bot self-onboarding](docs/bot-self-onboarding.md) are canonical; the operator
checklists summarize them. For project-specific facts, the completed assignment
wins over generic examples in the handbook.

**Each client gets exactly one OpenClaw bot — their single manager.** It delegates implementation to ephemeral **sub-agents** and drives the loop (issue → PR → preview → review → test → merge → deploy). Those sub-agents are the bot's disposable workers: not additional bots, and never client-facing. A bot is not a generic coding assistant.

## Start here: onboard a bot

1. The operator completes the
   [agent assignment](docs/agent-assignment-template.md). Never put tokens or
   passwords in it.
2. Give the bot this handbook and the completed assignment.
3. The bot reads, in full:
   1. [Development principles](PRINCIPLES.md)
   2. [Bot self-onboarding](docs/bot-self-onboarding.md)
   3. Its completed assignment
   4. The client repo's own `AGENTS.md`, `CLAUDE.md`, `README`, and current work
4. Before contacting the client or changing anything, the bot returns a short
   readiness note: its assignment, what access it verified, the preview safety
   profile, and any blockers.
5. When it is ready:
   - For an existing app, the operator sends the canonical
     [Telegram kickoff](docs/client-welcome-message.md).
   - For a brand-new app, the operator sends the project-specific kickoff
     recorded in the assignment. This handbook intentionally does not invent a
     generic greenfield message or stack choice.
   The bot answers the client's first request; it does not repeat the
   introduction.

## Two tracks

**Operator** — bring a new client project online:
- [Copy/paste Telegram client kickoff](docs/client-welcome-message.md) — the
  operator's send-ready first message and the bot's first-preview handoff
- [Client onboarding checklist](docs/client-onboarding-checklist.md)
- [Agent assignment template](docs/agent-assignment-template.md) — the
  client-specific brief handed to the bot
- [Bot bring-up & harness](docs/bot-bringup-and-harness.md) — standing up the bot + Telegram group (ops)
- [Open questions](OPEN-QUESTIONS.md) — automation gaps still to decide

**Bot self-onboarding** — what a new bot reads to start working:
- [Development principles](PRINCIPLES.md)
- [Bot self-onboarding](docs/bot-self-onboarding.md)

The included client kickoff is for an existing app. A brand-new app is ready
only when its assignment supplies a bootstrap guide, first task, and
project-specific kickoff.

## Domains (get these right)

| Use | Pattern | Notes |
| --- | --- | --- |
| Client production | `<app>.samo.team` | Cloudflare-proxied wildcard, e.g. acme.samo.team |
| Client preview | `<app>-<branch>.samo.cat` | One per PR; DBLab thin-clone; DNS via `CLOUDFLARE_SAMOCAT` |
| SAMO internal | `samo.green` | Never client-facing — don't hand a client this URL |

**Live** = works today (status tag — distinct from the client-facing *make it live*, which means deploy to the real site). **Proposed** = idea, not built — never shown to a client as real.
