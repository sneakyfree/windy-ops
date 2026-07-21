# LAUNCH-LEDGER.md — per-repo state + what's in flight

Update BEFORE starting any task and AFTER finishing. Checklist per repo (Phase 3): builds / tests+CI / no-secrets / error-handling / runbook / DNS-SSL / smoke-test / README / principles-pass.

## IN FLIGHT (right now)
- **Phase 2 foundations.** Waiting on Grant to run `~/windy/fable-phase2.sh` (one paste). It does: lockbox pull → DNS audit of all prod domains → Kit 0 snapshot (GET then CREATE) → windy-ops repo create+push. Results → `~/windy/.fable/`.
- Next after script results: correct SOTU footprint (AWS→Hostinger reality), then token-hygiene flag list, then `.thewindstorm.uk` cleanup PRs (7 repos, mechanical), then Phase 3 repo #1 (eternitas).

## PHASE 2 QUEUE
| Item | State |
|---|---|
| GitHub billing | ⛔ BLOCKED — Grant has no funds now; negotiating credit with GitHub support. CI stays dead; manual runbooks standing |
| Kit 0 snapshot | 🟡 approved, script staged |
| DNS/migration audit | 🟡 script staged |
| windy-ops repo | 🟡 approved, content seeded, script creates it |
| Token hygiene flag-list | ⬜ (incl. Brave + PyPI from honesty note; degraded CF god token; dead GitHub PAT cloud-init residue — lockbox task #13) |
| Mac mesh reboot-survival (LaunchDaemon) | ⬜ my call, low risk |
| .thewindstorm.uk cleanup | ⬜ 7 PRs, table locked in lockbox |

## PHASE 3 RANKED LIST (state: ⬜ not started unless noted)
1. eternitas ⬜ · 2. windy-mind ⬜ · 3. windy-pro/account-server ⬜ · 4. windy-mail ⬜ · 5. windy-agent ⬜ · 6. windy-connect ⬜ · 7. windy-registry ⬜ · 8. windy-drops ⬜ · 9. windy-admin ⬜ → CONCERT PASS 1 ⬜
10. windy-chat ⬜ · 11. WindyCloud ⬜ · 12. windy-cloud-sites ⬜ · 13. windy-cloud-domains ⬜ · 14. windy-search ⬜ · 15. Windy-Clone ⬜ → CONCERT PASS 2 ⬜
16. windytalk ⬜ · 17. windy-stt ⬜ · 18. windy-cell ⬜ · 19. windy-call ⬜ · 20. windy-text ⬜ · 21. windy-whatsapp-gateway ⬜ · 22. windy-code ⬜ · 23. windy-code-web ⬜ · 24. windy-pro-mobile ⬜ → CONCERT PASS 3 ⬜
25. control-panel/word-mcp/fix-me ⬜ · 26. windy-calendar ⬜ · 27. windy-iron ⬜ · 28. windy-ads ⬜ (scope decision first) · 29. sites (each after its product) ⬜ → FINAL CONCERT PASS ⬜
30. Tier D: parked permanently this era.

## BLOCKED-AND-LOGGED
- CI green checkbox in EVERY repo checklist: blocked on GitHub billing (see Phase 2). Repos can complete all other boxes; CI box inherits unblock automatically when billing lands.
