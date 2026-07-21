# TURNOVER.md — standing letter to the next instance

*Last updated: 2026-07-21, mid-Phase-2. Update me every time you checkpoint.*

You are Fable, CTO of Windy, working in Cowork on Grant's Mac mini. Grant is non-technical; never ask him technical questions; one step at a time when you need his hands. The Seven Principles (kit-army-config/doctrine/00-SEVEN-GUIDING-PRINCIPLES.md) decide everything — read them first, they're 24 lines.

**Where I stopped, mid-thought:**
Phase 1 done (SOTU written, ranked order accepted by Grant with no product-first override). Phase 2 in flight: I staged `~/windy/fable-phase2.sh` for Grant to paste into his Claude Code terminal. It pulls the lockbox, runs a DNS audit (Grant claims a FULL AWS→Hostinger migration days ago that the lockbox only partially records — VERIFY before trusting either), snapshots Kit 0 via Hostinger API (approved; replaces old snapshot), and creates+pushes this repo. Results land in `~/windy/.fable/phase2.log`, `dns-audit.txt`, `hostinger-*.json`.

**When results arrive:** 1) read them; 2) correct STATE-OF-THE-UNION §1 to the real footprint — if everything truly lives on Kit 0 now, the "one box runs everything" risk got WORSE and the snapshot matters even more; also the AWS EC2s may be zombie-billing Grant, flag potential savings; 3) then Phase 3 repo #1: eternitas.

**Environment facts you'd otherwise re-derive painfully:**
- Sandbox network = github.com git-HTTPS only. NOTHING else resolves. No gh (api.github.com blocked), no SSH/ping anywhere, no cloud APIs.
- Mounted: ~/windy (58 repos, read-write), ~/kit-army-config (treat READ-ONLY per doctrine).
- File tools use Mac paths (/Users/grantwhitmer/...), bash uses /sessions/<name>/mnt/... — same files.
- No live GitHub PAT exists in the lockbox (old one dead, deleted 2026-07-17). Until one exists, ALL push/PR traffic goes through Grant-paste scripts using his keychain gh. Pattern established; keep scripts dumb (P5).
- GitHub billing = broken, no funds, Grant negotiating credit. CI dead everywhere. Deploys = manual runbooks recorded in the lockbox (search "runbook"/"manual deploy" per service).
- Kit 0 = PRODUCTION, look don't touch, announce every change and get yes first. OC1 = user1-gpu ONLY. Mac mesh tunnel dies on reboot (`sudo wg-quick up /opt/homebrew/etc/wireguard/wg0.conf` — needs Grant's terminal anyway).
- Redact lockbox grep output ALWAYS (sed long-alnum → ███); I leaked two flagged-for-rotation values into scratch output on day one — don't repeat that.

**Working rhythm:** TaskCreate/Update as you go; ledger before+after every task; branch+PR for all repo work (LOW/MEDIUM/HIGH risk labels, 3-sentence summaries); parallelize only inside one repo; never parallelize Kit 0 or DNS work.
