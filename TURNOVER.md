# TURNOVER.md — standing letter to the next instance

*Last updated: 2026-07-21, mid-Phase-2. Update me every time you checkpoint.*

You are Fable, CTO of Windy, working in Cowork on Grant's Mac mini. Grant is non-technical; never ask him technical questions; one step at a time when you need his hands. The Seven Principles (kit-army-config/doctrine/00-SEVEN-GUIDING-PRINCIPLES.md) decide everything — read them first, they're 24 lines.

**Where I stopped, mid-thought:**
Phases 1–2 done. Migration to Kit 0 CONFIRMED (see ledger); SOTU corrected; snapshot taken (24h expiry — re-take before merge eras via Hostinger API script); windy-ops live on GitHub (local commits may be ahead — push rides the next Grant-paste script, see ledger IN FLIGHT for the queued script contents).
NOW: Phase 3 repo #1 **eternitas** — began with a repo deep-dive (build, tests, secrets scan, error handling, runbook state vs its NEW Kit 0 home). Work pattern: branch + PR per fix, risk labels, plain-English 3-sentence summaries. PRs push via Grant-paste script batches.

**Environment facts you'd otherwise re-derive painfully:**
- Sandbox network = github.com git-HTTPS only. NOTHING else resolves. No gh (api.github.com blocked), no SSH/ping anywhere, no cloud APIs.
- Mounted: ~/windy (58 repos, read-write), ~/kit-army-config (treat READ-ONLY per doctrine).
- File tools use Mac paths (/Users/grantwhitmer/...), bash uses /sessions/<name>/mnt/... — same files.
- No live GitHub PAT exists in the lockbox (old one dead, deleted 2026-07-17). Until one exists, ALL push/PR traffic goes through Grant-paste scripts using his keychain gh. Pattern established; keep scripts dumb (P5).
- GitHub billing = broken, no funds, Grant negotiating credit. CI dead everywhere. Deploys = manual runbooks recorded in the lockbox (search "runbook"/"manual deploy" per service).
- Kit 0 = PRODUCTION, look don't touch, announce every change and get yes first. OC1 = user1-gpu ONLY. Mac mesh tunnel dies on reboot (`sudo wg-quick up /opt/homebrew/etc/wireguard/wg0.conf` — needs Grant's terminal anyway).
- Redact lockbox grep output ALWAYS (sed long-alnum → ███); I leaked two flagged-for-rotation values into scratch output on day one — don't repeat that.

**Working rhythm:** TaskCreate/Update as you go; ledger before+after every task; branch+PR for all repo work (LOW/MEDIUM/HIGH risk labels, 3-sentence summaries); parallelize only inside one repo; never parallelize Kit 0 or DNS work.
