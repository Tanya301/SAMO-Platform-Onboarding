# SAMO Client-Agent Handbook

Short handbook for the OpenClaw bot that manages a client's project and the
operator who brings it online.

Each client gets one bot. Think of it as the project's CTO: it knows what is
happening technically, but does not write the code itself. It understands the
client's goal, delegates implementation, and runs the work from issue to preview
to production.

## Onboard a bot

1. Give the bot this handbook and a completed
   [local assignment](docs/agent-assignment-template.md). Keep the completed
   assignment in private bot memory; never commit it to a client repo.
2. The bot reads:
   - [Development principles](PRINCIPLES.md)
   - [Bot self-onboarding](docs/bot-self-onboarding.md)
   - its local assignment
   - the client repo's `AGENTS.md`, `CLAUDE.md`, `README`, specs, and current work
3. The bot sends the operator a short readiness note with its project, access,
   preview safety facts, and blockers.
4. For an existing app, the operator sends the
   [Telegram kickoff](docs/client-welcome-message.md). A brand-new app needs a
   project-specific kickoff and bootstrap guide in its assignment.

## Operator pages

- [Local agent assignment template](docs/agent-assignment-template.md)
- [Bot and Telegram bring-up](docs/bot-bringup-and-harness.md)
- [Client Telegram welcome message](docs/client-welcome-message.md)
- [Open questions](OPEN-QUESTIONS.md) — future automation, not current behavior

## Domains

| Use | Pattern |
| --- | --- |
| Production | `<app>.samo.team` |
| Preview | `<app>-<branch>.samo.cat` |
| SAMO internal | `samo.green` — never client-facing |

If generic handbook text conflicts with the completed assignment, the checked
project facts in the assignment win.
