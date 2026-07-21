# RECON.md — read-only deep-dives completed ahead of hardening (night shift 2026-07-21)

Common syndrome across ALL foundations: **deploy docs describe the dead AWS home; nothing in any repo knows about Hostinger/Kit 0.** The per-repo "deploy-truth" PR (SUBSTRATE/DEPLOY rewrite + rollback runbook + smoke test) is the repeating core deliverable, fed by kit0-inspect.txt.

## eternitas (#1 — active)
Strong tests (~95 files; PR#101 regression covered: tests/test_auto_demotion.py), no secrets, migrations CI. Gaps: deploy docs 100% AWS (deploy-prod/SUBSTRATE.md, DEPLOY.md — no rollback), deploy.yml + .github/lint refs stale, silent-except paths (FIXED in branch 01), bcrypt rounds=12 hardcoded → slow suite (FIXED in staged branch 02). Suite has deliberate slow sections (rate-limit waits). Known-failing tests pre-bundled as xfail (conftest deny-list, issue #33/PR #71).

## windy-mind (#2 — recon done)
Python 3.12, port 8900, alembic 0001-0006, clean test suite (no xfails), adapters have timeouts + ProviderError wrapping (no silent swallowing; no retries but fallback chain compensates). RED FLAGS: **no CI job runs pytest/ruff at all** (only migrations/lint/deploy) — add test job = PR one; SUBSTRATE.md + CLAUDE.md + deploy.yml pin dead EC2 35.173.154.119 and invent env names that don't exist in code (MIND_HMAC_SECRET, NIM_API_KEY); prod host = flat rsync, not git, hand-edited compose/.env excluded from repo; no rollback anywhere.

## windy-pro / account-server (#3 — recon done)
TS+Express, Node 20, port 8098, SQLite dev/PG prod, jest suite ~60 files w/ good auth coverage; CI test job EXISTS. RED FLAGS: legacy contract-broken Eternitas webhook handler STILL MOUNTED + reachable at POST /api/v1/identity/webhooks/eternitas (identity.ts:1888, HMAC-over-reserialized-JSON at :1906 — can never verify; modern raw-body route is the live one) → remove/410 it = PR one; silent catches in routes/auth.ts (~7 sites, some annotated); DEPLOY.md + deploy-account-server.yml 100% AWS (/opt/windy-pro vs real /root/windy-pro on Kit 0); rollback §520 exists but AWS-keyed.

## windy-mail (#4 — recon done)
FastAPI control-plane (port 8200) around Stalwart container; Resend smarthost. **The lockbox's "JMAP send broken for ALL accounts" (May 6) appears FIXED in code**: Basic-auth fix, primaryAccounts accountId, two-stage Email/set→EmailSubmission/set, Drafts/Sent self-heal (services/send.py, stalwart_jmap.py) + the 2026-07-06 session-cache-poisoning fix (send.py:294-301, per-account keying); real test coverage exists (test_send_selfheal.py etc.). VERIFY LIVE before closing the lockbox note. RED FLAGS: send.py swallows httpx.ConnectError and returns FAKE message-ids (~594, 634, 747) — send failures masquerade as success (P1 honesty violation in code) = PR one; welcome-email senders catch-all; deploy.yml + DEPLOY.md pin dead EC2 54.88.113.79; no Hostinger runbook, no real rollback.
