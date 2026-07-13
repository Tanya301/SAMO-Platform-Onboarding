# Messages to Start a Client in Telegram

Before sending:

1. Replace `[client name]`, `[app name]` or `[project name]`, `[@bot]`, and
   `[approver]`. `[approver]` must be the person recorded for release approval.
2. Record **Personal information** as `yes`, `no`, or `not sure`. This
   classification is required before kickoff. Treat `not sure` as `yes`.
3. For `yes` or `not sure`, insert this sentence immediately before the
   paragraph beginning “Start with”. For `no`, omit it:

> For examples in Telegram, use made-up names and details.

Choose the relevant kickoff after the client and bot are in the group.

## Existing App

---

Hi [client name] — welcome to SAMO 👋

[@bot] builds [app name].

Start with one small change. Name the page. Say what happens now and what you
want instead.

The bot asks questions and builds the change.

When it is ready, the bot sends a preview link. A preview is a separate version
of the project where you can review and try changes before they go live. The
link message explains what to try.

Tell the bot what to adjust. It updates the preview.

SAMO must finish its checks. [approver] must then say, “Make this change live.”

For a guided start, send one command:

- “Guide me through changing the wording or color.”
- “Guide me through adding a dark theme to one section.”
- “Guide me through building one landing-page section.”
- “Guide me through improving the login layout.”

---

## New Project

---

Hi [client name] — welcome to SAMO 👋

[@bot] builds [project name].

Start with one small task. Say who needs it and what they need to do.

The bot asks questions and builds the first part.

When it is ready, the bot sends a preview link. A preview is a separate version
of the project where you can review and try changes before they go live. The
link message explains what to try.

Tell the bot what to adjust. It updates the preview.

SAMO must finish its checks. [approver] must then say, “Make this project live.”

For a guided start, send one command:

- “Guide me through changing the wording or color.”
- “Guide me through adding a dark theme to one section.”
- “Guide me through building one landing-page section.”
- “Guide me through improving the login layout.”

---

## First Preview Handoff

Send this later, after the preview opens successfully and its access, data, and
connected services have been checked. Replace every bracketed field.

---

Your first preview is ready. It is a separate version of [app name] where you
can check [what changed]:

[preview link]

Please don't share this link publicly.

Before you test it:

- Access: [anyone with the link can view it / anyone with the link can reach
  the sign-in page; an existing login is required]
- Records: [test data only / a separate copy of live records]
- Safe to test: [specific actions confirmed safe]
- Do not test: [specific actions that are real or not yet confirmed safe]

Changes to preview records do not change the live app's database. However,
payments, emails, messages, bookings, and other connected actions can still be
real. Follow the lists above.

Please don't send passwords or private customer information in Telegram
messages or screenshots.

Please check [specific page and behavior] on [phone, computer, or both]. Does
it match what you expected?

This link stops working after we make the change live or stop the work.

---

If no connected action is confirmed safe, use “Safe to test: viewing the
changed page only.” Never send a bare preview link or leave a bracketed choice
in the client message. Praise such as “looks good” is feedback, not release
approval; only the recorded approver's explicit “Make this change live” is
approval for the named change.
