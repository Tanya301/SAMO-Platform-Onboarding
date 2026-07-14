# Client Onboarding Checklist (operator)

## Project

- [ ] Record the app name, one-line outcome, app slug, repo, issue tracker, and
      whether this is an existing or brand-new app.
- [ ] Complete the [private agent assignment](agent-assignment-template.md).
- [ ] For a brand-new app, add its bootstrap guide, first task, and
      project-specific kickoff.

## Access and environments

- [ ] Confirm the bot can read issues, push a branch, and open a PR/MR.
- [ ] Allocate the samohost production and preview environments.
- [ ] Record how production ships and how the live version is checked.
- [ ] Record preview access, records, safe actions, and real or unknown
      connected actions.
- [ ] Confirm a real preview opens before the first client handoff. For a
      brand-new app, do this after bootstrap.

## Telegram handoff

- [ ] Bring up the bot and Telegram group using the
      [operator guide](bot-bringup-and-harness.md).
- [ ] Send the [existing-app kickoff](client-welcome-message.md), or the
      project-specific greenfield kickoff.
- [ ] Confirm the bot answers the client's first request or asks one clear
      starting question.

## Delivery gate

Before any merge: green CI, real review on the current PR/MR head, and current
test evidence for UI changes. After approval: publish through the configured
production method, verify the live version, and check the safe changed flow.

The detailed working loop lives in [Bot Self-Onboarding](bot-self-onboarding.md).
