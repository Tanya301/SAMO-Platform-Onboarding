# Bot Bring-Up & Harness (ops)

Standing up an OpenClaw bot + its Telegram group is **manual today**. This is what's real, what hurts, and the proposed harness. No invented internals.

## Bring-up today (manual)

Two things stand up together: the **bot** (provisioned with role/tools/access — see [self-onboarding](bot-self-onboarding.md)) and a **Telegram group** (where the client talks to it). There's no one-button flow — an operator does this by hand:

1. Provision the bot for the client project + give it this handbook and a
   completed [agent assignment](agent-assignment-template.md).
2. Confirm access to the client's code host, plus any in-scope infra
   (Cloudflare/host).
3. Create the Telegram group and add the bot.
4. Wire the group to the project (client messages reach the bot; the bot reports back).
5. Point the bot at prod (`<app>.samo.team`) + preview
   (`<app>-<branch>.samo.cat`) domains, the configured deploy paths, and the
   live production identity proof.
6. Smoke-check: the bot sees the repo/issues, is reachable in Telegram, and
   returns the readiness note required by the handbook.
7. Record the preview safety profile, then send the assigned kickoff: the
   canonical [existing-app message](client-welcome-message.md), or the
   project-specific greenfield message from the assignment.

(Steps are at altitude on purpose — unverified specifics aren't invented here.)

## What hurts

- The whole flow is hand-repeated per client.
- Telegram-group ↔ project wiring has no automated path.
- Connecting the client's GitHub/GitLab is partly manual.
- Without a harness, a bot can **forget** the loop — file the issue, open the PR, get CI green, request samorev, run samotest.

Proposed fixes (SAMO Solo, samoname, auto-connect, automated bring-up) live in [open questions](../OPEN-QUESTIONS.md). Don't present them as live.

## Harness: evaluate flueframework

**The problem:** a bot must reliably run `issue → PR → CI (green) → samorev → samotest` and never silently skip a step. Today nothing enforces it — it relies on the bot's discipline.

**Candidate (Nik's suggestion, not a decision):** [flueframework](https://flueframework.com). Per its site (unverified): a TypeScript framework for durable autonomous agents — durable execution (resume after a crash), sandboxes, workflows, and GitHub + MCP integration. On paper that maps to what we need: don't lose the loop on a crash; a safe sandbox for sub-agents; GitHub/MCP for the issue/PR/CI plumbing. But the site doesn't actually claim an issue/PR/review/CI tracking loop, and its framing reads as marketing — so it needs a hands-on eval before we commit.

Eval questions:
- [ ] Can a Flue workflow model `issue → PR → CI → samorev → samotest` end-to-end, with state that survives a crash?
- [ ] Does durable execution resume a half-finished merge-gate run without double-acting (e.g. re-opening a PR)?
- [ ] Does GitHub + MCP give reliable PR/issue/CI read+write, or only chat?
- [ ] How does it interop with samorev / samotest / samohost (as tools/MCP)?
- [ ] What's the real maturity behind the marketing (releases, contributors, open issues)?
- [ ] Self-host vs. hosted runtime — which fits per-client isolation?
