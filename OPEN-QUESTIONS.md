# Open Questions

Automation gaps and decisions for client onboarding. None are built yet — pick a direction, then promote to issues.

- [ ] **Auto-connect the client's GitHub/GitLab.** Today the client connects their own host (Step 2). Can SAMO provision bot access + push + PR rights automatically?
- [ ] **samoname service.** Pick + register the client's domain via Cloudflare. Tanya has a UI concept. Decide scope, who owns registration/billing, and whether it ships with onboarding.
- [ ] **Define the "SAMO Solo" plan (~$30/mo).** Host for prod + preview + a Telegram group wired to prod. Needs concrete inclusions, limits, billing. The host capability exists (samohost); the packaged plan doesn't.
- [ ] **Automate bot + Telegram-group bring-up.** Heavily manual today (see [bring-up](docs/bot-bringup-and-harness.md)). Decide what to automate first: provisioning, group creation, project wiring.
- [ ] **Pick a harness so the bot never drops the loop** (issue → PR → CI → samorev → samotest). Nik suggests evaluating [flueframework](https://flueframework.com) (durable execution + sandboxes + GitHub/MCP; fits on paper, unverified). Candidate, not a decision — eval questions in [bring-up](docs/bot-bringup-and-harness.md#harness-evaluate-flueframework).
