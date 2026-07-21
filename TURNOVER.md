# TURNOVER.md — standing letter to the next instance

*Last updated: 2026-07-21, mid-Phase-2. Update me every time you checkpoint.*

You are Fable, CTO of Windy, working in Cowork on Grant's Mac mini. Grant is non-technical; never ask him technical questions; one step at a time when you need his hands. The Seven Principles (kit-army-config/doctrine/00-SEVEN-GUIDING-PRINCIPLES.md) decide everything — read them first, they're 24 lines.

**Where I stopped (updated 2026-07-21 ~11:00 ET):** ALL FIVE PRs MERGED (eternitas main=cf8ff6f: #125/126/127; windy-mind main=ead8073: #73/74 — Grant authorized the merges verbatim). Local mains synced. NEXT: (a) Kit 0 deploy of merged eternitas+mind — ANNOUNCED to Grant, awaiting his yes (expect-script pattern; fresh snapshot exists from 12:23 UTC; deploy = git reset to main + rebuild api container per each repo's SUBSTRATE.md runbook); (b) repo #4 windy-mail — verify "JMAP send fixed in code" live + fix the fake-message-id honesty bug (send.py ~594/634/747, see RECON.md). Windy Word repos FROZEN (sibling Fable consolidating on Windy 0; see HANDOFF-windy-pro-findings.md — trust-critical cascade patch waiting for them). Night-shift notes below are archaeology.

**Where I stopped (end of night shift, ~02:00 ET 2026-07-21):**
Overnight state: eternitas suite was running Mac-side on branch `harden/eternitas-01-silent-failures` via batch3b (glacial — bcrypt+rate-limit waits; possibly hung on a network test). It may have finished and auto-opened PR-01, or not — CHECK `~/windy/.fable/batch3b.log` + `eternitas-prs.txt` FIRST.
Ready and waiting: branch-02 patch at `.fable/patch-eternitas-02-bcrypt.patch` (bcrypt rounds configurable, tests fast) + PR bodies in `.fable/prs/` + `fable-batch4.sh` (morning script, defensive: fast reads first — kit0 inspect, AWS zombies, fresh snapshot — then kills stale pytest, builds validate/stack-01-02, runs now-fast suite, pushes both branches, opens both PRs, pushes windy-ops).
MORNING FLOW: Grant pastes `bash ~/windy/fable-batch4.sh` → read `.fable/` results → write eternitas deploy-truth PR (branch 3: SUBSTRATE/DEPLOY rewrite for Kit 0 + rollback runbook + smoke test from `.fable/staged-eternitas-SMOKE-TEST.md`, fed by kit0-inspect.txt) → close eternitas checklist → repo #2 windy-mind (recon DONE, see RECON.md — first PRs: add pytest job to CI; deploy-truth).
Recon complete for repos #2/#3/#4 (RECON.md). Key morning reads: aws-instances.txt + aws-cost.json (zombie report for Grant), kit0-inspect.txt (runbook facts). Remember: windy-mail's "JMAP send broken" lockbox note is probably STALE — verify live, then correct the lockbox via branch+PR (allowed for cred/machine updates? NO — lockbox edits are cred-only; put the correction in windy-ops + tell Grant instead).

**Environment facts you'd otherwise re-derive painfully:**
- Sandbox network = github.com git-HTTPS only. NOTHING else resolves. No gh (api.github.com blocked), no SSH/ping anywhere, no cloud APIs.
- Mounted: ~/windy (58 repos, read-write), ~/kit-army-config (treat READ-ONLY per doctrine).
- File tools use Mac paths (/Users/grantwhitmer/...), bash uses /sessions/<name>/mnt/... — same files.
- No live GitHub PAT exists in the lockbox (old one dead, deleted 2026-07-17). Until one exists, ALL push/PR traffic goes through Grant-paste scripts using his keychain gh. Pattern established; keep scripts dumb (P5).
- GitHub billing = broken, no funds, Grant negotiating credit. CI dead everywhere. Deploys = manual runbooks recorded in the lockbox (search "runbook"/"manual deploy" per service).
- Kit 0 = PRODUCTION, look don't touch, announce every change and get yes first. OC1 = user1-gpu ONLY. Mac mesh tunnel dies on reboot (`sudo wg-quick up /opt/homebrew/etc/wireguard/wg0.conf` — needs Grant's terminal anyway).
- Redact lockbox grep output ALWAYS (sed long-alnum → ███); I leaked two flagged-for-rotation values into scratch output on day one — don't repeat that.

**Working rhythm:** TaskCreate/Update as you go; ledger before+after every task; branch+PR for all repo work (LOW/MEDIUM/HIGH risk labels, 3-sentence summaries); parallelize only inside one repo; never parallelize Kit 0 or DNS work.
