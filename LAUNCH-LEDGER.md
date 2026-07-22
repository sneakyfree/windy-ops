# LAUNCH-LEDGER.md — per-repo state + what's in flight

Update BEFORE starting any task and AFTER finishing. Checklist per repo (Phase 3): builds / tests+CI / no-secrets / error-handling / runbook / DNS-SSL / smoke-test / README / principles-pass.

## 2026-07-22 — GRANT DELEGATED PR MERGE AUTHORITY TO FABLE (CTO)
Grant (non-technical, doesn't want to review/merge) explicitly handed me review+merge decisions, to be made by the Seven Principles. My standing boundary (itself P1-derived) — I decide/do everything EXCEPT three classes, which still get one plain yes/no: (1) spending his money, (2) irreversible destruction, (3) flipping a change onto the LIVE app/Kit 0. **ALL SIX open PRs MERGED by me this session** (local git + PAT push, each merge verified landed on origin):
- windy-mail main → **e7fd36d**: #96 no-fake-send (MEDIUM), #97 deploy-truth, #98 archive-test
- windy-agent master → **f53a870**: #326 SOUL-MAP, #327 phone-test-isolation
- windy-connect main → **86b4b51**: connect-01 fetch-timeouts
⚠️ MERGED ≠ DEPLOYED: mail's honesty+deploy fixes are in main but the LIVE mail service on Kit 0 still runs old code until an announced deploy (a class-3 action → Grant yes/no when I propose it). Rollback for any merge = `git revert` (nothing deployed, fully reversible).

## IN FLIGHT (right now)
- **MORNING 07-21:** overnight suite GREEN on branch-01 (784 passed / 34 xfail / 0 fail, 50min). batch4b running: fast suite on validate/stack-01-02 → pushes branches 01+02 → opens PRs 1+2 (stacked). Kit 0 fully mapped (kit0-inspect.txt: ~50 containers; PROD ETERNITAS RUNS AS COMPOSE PROJECT `eternitas-dev` — naming trap documented; nginx is the host proxy, NOT caddy; repos under /root/*). AWS: ZERO instances anywhere-yet-checked but $121.61 July spend — service breakdown + EIP/volume + other-region checks ride batch4b. Runbook draft staged (.fable/staged-eternitas-SUBSTRATE-KIT0.md) with TBCs needing one more read-only inspect (docker inspect compose labels + nginx sites) → then branch 3 (deploy-truth PR) publishes.
- **(superseded) Phase 3 repo #1: eternitas** — first PR ready: branch `harden/eternitas-01-silent-failures` (commit 84b7996, LOW risk, observability-only). Test run + push + PR-open ride batch3b (batch3 hit 3 script bugs, all fixed: uv --extra dev, backtick-anchored password extraction, canonical AWS creds). NOTE: suite runs SLOW on the Mac (~1 test/several sec) — investigate (bcrypt rounds under test?) as a later eternitas PR. Kit 0 inspection (for the deploy-truth runbook PR) also rides batch3. Sandbox CANNOT run eternitas tests (needs py3.12; blocked download) — Mac-side testing is the standing pattern for py3.12+ repos; sandbox CAN run pip/npm + ruff.
- **DNS truths learned:** api.windymind/clone/drops/search/mail/call/cell/text + cloud.windycloud.com all → Kit 0. Legacy `windypro.thewindstorm.uk` = a Cloudflare TUNNEL (cfargotunnel) still fronting api.windyfly.ai, api.windychat.ai, api.windycode.org, push.windychat.ai, updates.windycode.org. SPAs on CF Pages (app.windyword/chat/mail). Stragglers: api.nachocrunch.org→3.85.96.56 (AWS, non-Windy), cloud.windyword.ai→32.193.70.195 (stale?). Hostinger WEEKLY auto-backups exist (07-13, 07-20) + my snapshot. NOTE: its deploy target is now Kit 0 (not AWS) — all deploy/runbook work must respect look-don't-touch + announce-first.
- **Next terminal-script batch (queue for when Grant's hands are needed anyway):** Cloudflare API read-only DNS export (reveal proxied origins), Hostinger `GET /backups` (does Kit 0 have durable auto-backups beyond the 24h snapshot?), AWS zombie-instance audit, push pending windy-ops commits.

## ETERNITAS CHECKLIST STATE (07-21 morning)
- [x] builds clean (fresh uv sync, twice, Mac) · [x] tests pass (784/0 fail, suite green on stacked branches) · [x] no secrets in code · [x] error handling pass (PR #125) · [ ] runbook (branch 3 — staged, needs kit0-verify TBCs) · [x] DNS/SSL verified (api.eternitas.ai → Kit 0, unproxied, live) · [ ] smoke test doc (staged, lands with branch 3) · [x] README adequate · [ ] principles pass (do at close)
- PRs OPEN: #125 (silent-failures, LOW), #126 (bcrypt rounds, LOW, base=#125, body corrected), branch 3 `harden/eternitas-03-deploy-truth` committed (34f38ce) — publishes via batch6 as PR (deploy-truth docs, LOW). Drift-lint verified passing locally.
- windy-mind PR #73 OPEN (CI test job, LOW). Mind suite: 483 passed in 7.62s locally — cleanest foundation.
- AWS ZOMBIE AUDIT CLOSED: daily cost $6.91 (07-14) → $0.04/day now; bleed stopped at migration; ~$1/mo residue. No action needed. July's $121 was pre-migration usage.
- Eternitas remaining after PR merges: principles-pass (last checklist box) + deny-list-xfail→skip PR (PR-04, saves ~15-20 min/suite) + deploy-prod/ retire-or-repurpose follow-up (logged in SUBSTRATE).
- Suite truth: 45 min even with fast bcrypt — real sink = 34 deny-listed xfail tests each burning a timeout + rate-limit waits. PR-04 candidate: deny-list → skip (saves ~15-20 min/run; they've failed 30+ commits; issue #33). Add --durations=20 to future runs.

## PHASE 2 RESULTS (2026-07-21)
| Item | State |
|---|---|
| GitHub billing | ⛔ BLOCKED — no funds; Grant negotiating credit with GitHub support. CI dead; manual runbooks standing |
| Kit 0 snapshot | ✅ taken 2026-07-21 04:36 UTC (id 309348, 30-min restore) — ⚠️ EXPIRES 24h later; there was NO prior snapshot. Re-take before every merge era; durable backup strategy still open |
| DNS/migration audit | ✅ done — migration CONFIRMED (4 domains A→72.60.118.54, Kit 0 PTR=mail.windymail.ai; proxied origins pending CF read). fly.windyword.ai has NO DNS (Fly gateway homeless?); windyword.com points at a stranger (118.89.190.223) |
| windy-ops repo | ✅ created private + pushed (root commit 4153a10) |
| Token hygiene flag-list | ⬜ (Brave + PyPI honesty-note items; degraded CF god token; dead-PAT cloud-init residue — lockbox task #13) |
| Mac mesh reboot-survival (LaunchDaemon) | ⬜ my call, low risk |
| .thewindstorm.uk cleanup | ⬜ 7 PRs, table locked in lockbox |
| AWS zombie audit | ⬜ NEW — EC2s/RDS may still be billing Grant post-migration |

## PHASE 3 RANKED LIST (state: ⬜ not started unless noted)
1. eternitas ✅✅ LAUNCH-READY + DEPLOYED to Kit 0 (cf8ff6f, health 200, alembic ran, 2026-07-21 15:02 UTC); CI box blocked-and-logged; follow-ups: PR-04 xfail→skip, deploy-prod retire, compose rename Grant-gated) · 2. windy-mind ✅✅ LAUNCH-READY + DEPLOYED to Kit 0 (ead8073, loopback+MESH both 200, /version stamps the SHA — deploy identity works here, 15:12 UTC) · 3. windy-pro/account-server ⏸️ FROZEN (sibling-Fable consolidation on Windy 0; trust-critical cascade gap found + fix staged in handoff-account-server-cascade.patch + HANDOFF-windy-pro-findings.md) · 4. windy-mail 🔄 PRs #96 (honesty fix, MEDIUM) + #97 (deploy truth, LOW) OPEN. Suite 603 pass / 1 pre-existing fail (archive→cloud, VERIFIED failing on clean main too — logged below, not mine). Remaining for launch-ready: principles pass. ✅ LIVE SEND PROVEN 2026-07-21 (HTTP 200 msg_id huaaaab0 → Grant's Gmail inbox; lockbox 'send broken' note CLOSED). ARCHIVE TEST TRIAGED + FIXED → branch harden/mail-03-archive-test-contract (36e373e): root cause was a STALE TEST (fake Cloud transport expected JSON; service correctly sends multipart) — archiving was never broken, its test was. NOW GREEN + PR #98 OPEN (fix needed request.aread() — multipart bodies stream; re-verified through a real AsyncClient). Mail PRs open: #96 honesty(MEDIUM), #97 deploy-truth(LOW), #98 archive-test(LOW). · 5. windy-agent 🔄 (recon: BEST-DEFENDED repo in fleet — 230 test files, anti-confabulation guards, contract tests per dependency, no stale hosts, no secrets, timeouts everywhere. PR #326 OPEN = docs/SOUL-MAP.md (Principle-7 artifact). Suite 3440 pass / 4 pre-existing fail (2 macOS socket-path, 2 mock-mode — proven on clean master). NOTE: default branch is `master`. 2 mock-provision failures TRIAGED: root cause = tests not isolating ambient TWILIO_* env (Grant's Mac has TWILIO_PHONE_NUMBER). Fix = PR #2 branch harden/agent-02-phone-test-isolation (hermetic env fixture), publishes via batch13. DEEPER FINDING: provision_phone BUYS a real number on ambient-cred presence with no spend-opt-in → PHONE-PURCHASE-PROPOSAL.md awaits Grant's yes. Remaining: principles pass, the 2 macOS-socket tests (env-only).) · 6. windy-connect 🔄 (recon DONE 07-22, see RECON.md. **PR #1 OPEN: harden/connect-01-fetch-timeouts (LOW)** — fetchWithTimeout on all 10 upstream calls; suite 32 pass, 0 new tsc errors (proven vs clean-tree stash); pushed autonomously via new PAT. Remaining top PRs: fan-out compensation for orphaned-identity leak (design change, needs Eternitas de-provision endpoint), ENABLE_REAL_PROVISIONING reconcile + dead STALWART_* vars (needs live secret verify), deploy-truth SUBSTRATE rewrite. live-verify: Worker secrets, upstream DNS→Kit 0, one real E2E mint) · 7. windy-registry ⬜ · 8. windy-drops ⬜ · 9. windy-admin ⬜ → CONCERT PASS 1 ⬜
10. windy-chat ⬜ · 11. WindyCloud ⬜ · 12. windy-cloud-sites ⬜ · 13. windy-cloud-domains ⬜ · 14. windy-search ⬜ · 15. Windy-Clone ⬜ → CONCERT PASS 2 ⬜
16. windytalk ⬜ · 17. windy-stt ⬜ · 18. windy-cell ⬜ · 19. windy-call ⬜ · 20. windy-text ⬜ · 21. windy-whatsapp-gateway ⬜ · 22. windy-code ⬜ · 23. windy-code-web ⬜ · 24. windy-pro-mobile ⏸️ FROZEN (Word consolidation) → CONCERT PASS 3 ⬜
25. control-panel/word-mcp/fix-me ⬜ · 26. windy-calendar ⬜ · 27. windy-iron ⬜ · 28. windy-ads ⬜ (scope decision first) · 29. sites (each after its product) ⬜ → FINAL CONCERT PASS ⬜
30. Tier D: parked permanently this era.

## DEPLOY LOG (rollback points — check here first in an incident)
| When (UTC) | Service | From SHA | To SHA | Result |
|---|---|---|---|---|
| 2026-07-21 15:02 | eternitas (Kit 0, project eternitas-dev) | 30aed34 | cf8ff6f | OK, health 200 |
| 2026-07-21 15:11 | windy-mind (Kit 0, project windymind-dev) | c5a3f87 | ead8073 | OK, loopback+mesh 200 |

## KNOWN PRE-EXISTING FAILURES (not caused by this era's work)
- **windy-mail** `tests/test_wave2_contracts.py::test_archive_runs_and_records_cloud_archive_id` — fails on clean `main` (verified 2026-07-21 by running it on an untouched checkout). Result: `{'archived': 0, 'failed': 1}` — the archive→Windy Cloud upload path errors under the fake transport. Triage before mail is declared launch-ready; it may indicate a real archive-to-Cloud break, which would matter for retention.

## CONNECT (windy-connect) PRE-EXISTING FINDINGS — not this era's work
- **6 tsc strict-null errors on clean main** (verified via stash-diff 07-22): magic_link.ts(80,85), provision.ts(97), routes/bundle.ts(46,47), sessions_do.ts(106) — all `Object is possibly 'undefined'` / `string | undefined`. `tsc --noEmit` is therefore NOT green on main; CI's worker job only does `wrangler deploy --dry-run` (bundle check), not tsc, so these never blocked. Candidate for a small dedicated strict-null PR. Vitest suite IS fully green (28 on main, 32 with PR #1).

## AGENT (windy-agent) PRE-EXISTING FAILURES — triage before launch-ready
- `test_sd_notify` ×2: `AF_UNIX path too long` — macOS tmpdir exceeds the UNIX socket path limit. Environmental; fix = bind under a short /tmp dir.
- `test_phone_provision::TestMockProvisioning` ×2: expects `is_mock=True`, gets `False`. **Possibly real** — mock mode not engaging could mean a dev/test run attempts LIVE Twilio provisioning. Root-cause before dismissing.

## BLOCKED-AND-LOGGED
- CI green checkbox in EVERY repo checklist: blocked on GitHub billing (see Phase 2). Repos can complete all other boxes; CI box inherits unblock automatically when billing lands.

## 🔴 LAUNCH-BLOCKING GATE (do not skip before any real public launch)
- **Repo visibility.** As of 2026-07-22 the entire production ecosystem + windy-ops are PUBLIC on github.com/sneakyfree (only kit-army-config is private). Grant chose LEAVE-AS-IS for the pre-launch sandbox (his informed call) but agreed to revisit before launch. BEFORE inviting any outside user in: (1) decide final visibility per repo, (2) confirm the git-HISTORY secret sweep is complete for every repo that will stay public. The no-secrets checklist box now means HISTORY-clean, not just HEAD-clean.
