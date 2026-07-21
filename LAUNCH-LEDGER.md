# LAUNCH-LEDGER.md — per-repo state + what's in flight

Update BEFORE starting any task and AFTER finishing. Checklist per repo (Phase 3): builds / tests+CI / no-secrets / error-handling / runbook / DNS-SSL / smoke-test / README / principles-pass.

## IN FLIGHT (right now)
- **Phase 3 repo #1: eternitas** — first PR ready: branch `harden/eternitas-01-silent-failures` (commit 84b7996, LOW risk, observability-only). Test run + push + PR-open ride batch3. Kit 0 inspection (for the deploy-truth runbook PR) also rides batch3. Sandbox CANNOT run eternitas tests (needs py3.12; blocked download) — Mac-side testing is the standing pattern for py3.12+ repos; sandbox CAN run pip/npm + ruff.
- **DNS truths learned:** api.windymind/clone/drops/search/mail/call/cell/text + cloud.windycloud.com all → Kit 0. Legacy `windypro.thewindstorm.uk` = a Cloudflare TUNNEL (cfargotunnel) still fronting api.windyfly.ai, api.windychat.ai, api.windycode.org, push.windychat.ai, updates.windycode.org. SPAs on CF Pages (app.windyword/chat/mail). Stragglers: api.nachocrunch.org→3.85.96.56 (AWS, non-Windy), cloud.windyword.ai→32.193.70.195 (stale?). Hostinger WEEKLY auto-backups exist (07-13, 07-20) + my snapshot. NOTE: its deploy target is now Kit 0 (not AWS) — all deploy/runbook work must respect look-don't-touch + announce-first.
- **Next terminal-script batch (queue for when Grant's hands are needed anyway):** Cloudflare API read-only DNS export (reveal proxied origins), Hostinger `GET /backups` (does Kit 0 have durable auto-backups beyond the 24h snapshot?), AWS zombie-instance audit, push pending windy-ops commits.

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
1. eternitas ⬜ · 2. windy-mind ⬜ · 3. windy-pro/account-server ⬜ · 4. windy-mail ⬜ · 5. windy-agent ⬜ · 6. windy-connect ⬜ · 7. windy-registry ⬜ · 8. windy-drops ⬜ · 9. windy-admin ⬜ → CONCERT PASS 1 ⬜
10. windy-chat ⬜ · 11. WindyCloud ⬜ · 12. windy-cloud-sites ⬜ · 13. windy-cloud-domains ⬜ · 14. windy-search ⬜ · 15. Windy-Clone ⬜ → CONCERT PASS 2 ⬜
16. windytalk ⬜ · 17. windy-stt ⬜ · 18. windy-cell ⬜ · 19. windy-call ⬜ · 20. windy-text ⬜ · 21. windy-whatsapp-gateway ⬜ · 22. windy-code ⬜ · 23. windy-code-web ⬜ · 24. windy-pro-mobile ⬜ → CONCERT PASS 3 ⬜
25. control-panel/word-mcp/fix-me ⬜ · 26. windy-calendar ⬜ · 27. windy-iron ⬜ · 28. windy-ads ⬜ (scope decision first) · 29. sites (each after its product) ⬜ → FINAL CONCERT PASS ⬜
30. Tier D: parked permanently this era.

## BLOCKED-AND-LOGGED
- CI green checkbox in EVERY repo checklist: blocked on GitHub billing (see Phase 2). Repos can complete all other boxes; CI box inherits unblock automatically when billing lands.
