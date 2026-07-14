<!-- SAMO-DEV-PRINCIPLES:START (canonical synced block — edit here, then re-sync downstream copies) -->
## Development principles

Accumulated, non-negotiable working principles for SAMO projects. Canonical
source: Tanya301/SAMO-Platform-Onboarding > PRINCIPLES.md. If a downstream copy
differs, this file wins. Project-specific stack rules live in the
[samohost stack handbook](https://github.com/NikolayS/samohost/tree/main/docs/stack);
the OpenClaw manager workflow lives in
[Bot Self-Onboarding](https://github.com/Tanya301/SAMO-Platform-Onboarding/blob/main/docs/bot-self-onboarding.md).

### 1. Re-review after ANY post-review change — green CI is not "reviewed"

Once a reviewer approves an MR/PR, **any** later commit invalidates that
approval — **including a commit that fixes the review's own findings**.
Re-review the change (at minimum the new delta) **before** merge. Never merge a
post-review commit on the strength of the prior PASS plus a green pipeline: a
passing pipeline proves it builds and tests pass, not that it was reviewed.

Order: review → fix-commit → **re-review the delta** → merge. (NOT: review → fix-commit → merge.)

### 2. TDD — RED commit before GREEN commit

For behavior-changing code, write a failing test first and commit it as RED.
Then implement the change and commit it as GREEN. Never bundle the test and
implementation into one commit. For a change that cannot be meaningfully tested
(for example, prose-only documentation), say why instead of inventing a test.

### 3. Tests must match the production shape

Before writing a test double or mock, read the real production code path and
record the exact shape it returns. A passing test against the wrong shape is a
false positive.

### 4. Fix the root cause, not the symptom

Trace incorrect state to where it is produced. A guard added only at the
consumer usually hides the symptom. Record the root-cause analysis in the PR
description.

### 5. No dead UI time

Every wait longer than one second needs continuous, meaningful visible
feedback. Never leave the user wondering whether their click worked.

### 6. No secrets in committed artifacts

Never commit tokens, passwords, or API keys. Use environment-variable names as
placeholders; the runtime provides the real values.

### 7. Real or clearly labeled

Do not present a first-party mock, stub, or unwired UI as working functionality.
If a screen is intentionally not wired yet, label it visibly as a preview.

### 8. Use plain language in the product

Internal tool names, database fields, state-machine values, and engineering
jargon do not belong in client-facing UI or messages. Explain the outcome and
the client's next step in language they already use.

### 9. Tagged is not shipped

A tag, merge, green pipeline, or successful deploy command is not proof that a
change works in production. Verify the deployed SHA/version and walk the changed
flow through the project's safe verification path before reporting it done.

### 10. Reproduce before fixing

For a bug, reproduce the reported symptom on the currently deployed version
before claiming a fix. Record the reproduction and the post-fix proof so the PR
shows that the root cause, not a nearby symptom, changed.
<!-- SAMO-DEV-PRINCIPLES:END -->
