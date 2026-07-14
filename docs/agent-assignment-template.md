# OpenClaw Agent Assignment

The operator completes one copy per client and gives it to that client's bot
with the [handbook](../README.md). Replace every bracketed field. Do not put
tokens, passwords, private keys, or copied customer records in this document.

## Client and project

- **Client:** [name and preferred form of address]
- **Working language:** [language]
- **Project:** [app name]
- **Outcome:** [one sentence describing what the app does]
- **Starting point:** [brand-new app / existing app with real users]
- **Current work:** [first task, open issue, or “wait for the client's first request”]
- **Bootstrap guide:** [required for a brand-new app / not applicable]
- **Client kickoff:** [canonical existing-app message / project-specific text or
  path for a brand-new app]
- **Client-facing bot:** [@handle]
- **Telegram group:** [name or internal identifier]
- **Operator escalation contact:** [name/role and contact path]

## Code and environments

- **Repo:** [host/owner/repo]
- **Default branch:** [branch]
- **Issue tracker:** [URL]
- **App slug:** [slug]
- **Production URL:** [URL]
- **Production channel:** [branch-tracked / tag-gated]
- **Tracked branch:** [branch for branch-tracked apps / not applicable]
- **Release-tag policy:** [pattern, format, required CI workflow, and target-SHA
  resolution for tag-gated apps / not applicable]
- **samohost target:** [VM name and app-record name]
- **Production deploy path:** [the verified trigger or documented manual path]
- **Production identity proof:** [live response containing the SHA, or a live
  version/tag plus its deterministic SHA mapping; local deploy state alone is
  not proof]
- **Preview pattern:** [for example, `<slug>-<branch>.samo.cat`]
- **Preview publishing path:** [`samohost trigger run --pr-previews`, documented
  manual command, or other verified path]
- **Project guidance to read:** [AGENTS.md / CLAUDE.md / README / spec paths]

## Tool entrypoints

- **samospec:** [installed command or checkout path]
- **samorev deterministic gate:** [installed command or checkout path]
- **samorev code-analysis surface:** [verified command or reviewer workflow that
  emits a verdict tied to the exact PR/MR head SHA; required because the CLI
  gate does not review code]
- **samotest:** [installed command or checkout path]
- **samohost:** [installed command or checkout path]

## Client communication

- **Other people in the group:** [roles, if relevant]
- **Communication notes:** [tone, availability, accessibility, or “none”]

Any person in the project Telegram group can approve the current preview.
“Looks good,” “go ahead,” or “make it live” are all enough. Ask again only if
the change or intent is unclear. If people give conflicting directions, ask the
group which version they want.

## Preview safety profile

- **Personal information:** [yes / no / not sure]
- **Preview access:** [public to link / sign-in required]
- **Preview records:** [test-only / copied live records / not sure]
- **Connected services confirmed safe to test:** [list; “none” is valid]
- **Connected services real or unknown:** [do-not-test list]
- **Safe verification for real integrations:** [sandbox, test account,
  operator-run check, or not applicable]
- **Credentials or test accounts:** [where the bot can retrieve them securely;
  never place the values here]

Treat `not sure` as sensitive or real until it is verified. A separate preview
database does not isolate payments, email, messages, bookings, or other external
services.

## Access verified by the operator

Complete the production and preview checks now for an existing app. For a
brand-new empty repo, record the planned paths now and complete those two checks
after the bootstrap PR, before the first preview handoff or production claim.

- [ ] The bot can read the client repo and issue tracker.
- [ ] The bot can push a test branch and open a PR/MR.
- [ ] The bot can read PR/MR CI status and post comments.
- [ ] The bot can use the required SAMO tool repos and their current runbooks.
- [ ] Any in-scope host, DNS, or deploy access is available.
- [ ] The production deploy path and live identity proof have been verified.
- [ ] The preview publishing path has produced a real preview that serves HTTP
      200.
- [ ] Telegram messages reach the bot and its replies reach the group.

## Bot readiness response

Before contacting the client or changing project state, reply to the operator
with a short note in this shape:

> Ready for [client] / [project]. I read the handbook, project guidance, and
> current work. Repo and PR access: [verified or blocker]. Production: [URL].
> Preview safety: [access, records, safe actions, do-not-test actions]. I will
> escalate through [contact]. [No blockers / blockers].

Do not claim “ready” when a required fact or access check is still unknown.
