# STATE OF THE UNION — Windy Ecosystem
**Prepared by:** Fable (CTO) · **Date:** 2026-07-21 · **Scope:** all 58 repos in `~/windy` + deployed footprint per ACCESS_LOCKBOX.md
**Honesty note:** this is a survey-level pass (metadata, READMEs, dependency grep, lockbox history). "Builds clean" is NOT verified here — that happens per-repo in Phase 3. Anything marked TBC gets confirmed at hardening time.

---

## 1. DEPLOYED FOOTPRINT (what is actually running)

> **CORRECTED 2026-07-21 after live DNS audit.** Grant migrated production off AWS onto the Hostinger VPS (Kit 0) in mid-July. DNS proves it: `admin.windyword.ai`, `api.eternitas.ai`, `chat.windychat.ai`, and `mail.windymail.ai` all A-record **directly to 72.60.118.54**, and Kit 0's reverse-DNS is set to `mail.windymail.ai` (only done if mail sends from there). Most other prod domains are Cloudflare-proxied (origin hidden — presumed Kit 0, verify via Cloudflare API on next terminal run). The lockbox's AWS Wave-13 blocks are now HISTORY, not current state.

| Where | What | Notes |
|---|---|---|
| **Kit 0** — Hostinger VPS `72.60.118.54` / `10.10.0.1` (KVM 4: 4 vCPU / 16 GB / 200 GB, Ubuntu 24.04) | **Nearly everything**: OpenClaw gateway, KMM, WireGuard hub, + migrated prod services — mail (Stalwart), eternitas, account-server, chat (Synapse), admin, cloud kernel; likely clone/search/mind too (origin-verify pending) | PRODUCTION. Look, don't touch. **THE single point of failure for the whole ecosystem now.** Snapshot taken 2026-07-21 — but it EXPIRES in 24h (Hostinger keeps snapshots 1 day). Durable backup strategy = open Phase 2 item |
| **AWS EC2 fleet** (mail/mind/chat/cloud boxes, RDS) | **Status unknown post-migration** — possibly still running and billing | ZOMBIE AUDIT NEEDED: if instances still run, Grant is paying for dead boxes. Also `fly.windyword.ai` has NO DNS anymore — Fly gateway's current home unknown |
| **Cloudflare Pages** | most `*-site` marketing sites | Deploys manual via wrangler while CI is dead |
| **Cloudflare R2** | Desktop release buckets (Windy Word, Windy Code) | Notarized artifacts uploaded 2026-05-22 |
| **AWS S3+CloudFront** | windyword-site | Own IAM deploy user |
| **GPU fleet** (Veron/OC boxes via mesh) | windy-stt lane, windytalk engine, clone training | Veron = user1-gpu ONLY |
| **Third-party rails** | Resend (email out), Twilio ×2 (call/text/WhatsApp), RunPod (STT), Brave (search) | All keyed via lockbox |

### Cross-cutting breakages (everything inherits these)
1. **GitHub Actions BILLING-LOCKED account-wide** → CI is dead in all 58 repos; every deploy is a manual runbook. Also risks private-repo access lapsing — including the lockbox repo itself, the crown jewels.
2. **OpenAI org billing-blocked** (429 on all keys) → Mind's OpenAI lane down; 8 other providers cover it.
3. **Token hygiene:** old Cloudflare god token degraded (replacement live: `godtoken02JUL26`); a dead GitHub PAT noted in lockbox; PyPI hand-publish token awaiting revocation; several rotation TODOs recorded in the lockbox itself.
4. **Legacy `.thewindstorm.uk` URLs** — ~244 references across 7 repos with a locked replacement table in the lockbox. Mechanical cleanup, one PR per repo.
5. **Mesh fragility:** Mac mini tunnel dies on reboot; `kit_mesh` key absent from lockbox; hub slots .12–.21 are dead entries.

---

## 2. REPO INVENTORY

Legend: **Last** = last meaningful commit · **T** = has tests · **CI** = workflow count (all currently dead per billing lock) · Build status TBC for all until Phase 3.

### TIER A — FOUNDATIONS (everything else stands on these)

| Repo | What it is | Deployed at | Last | T | CI | Depends on | Top blockers |
|---|---|---|---|---|---|---|---|
| **eternitas** | Passport office for AI agents — identity, integrity, revocation. THE load-bearing service (Principle 6) | api.eternitas.ai (mail box) | 07-18 | Y | 5 | mail (welcome flow) | CI dead; recent trust-kernel bug class (op demotion) wants regression tests; hatch rate-limit issue history |
| **eternitas-authenticator** | Trust anchor companion app (co-sign/submit/push) | not yet shipped | 07-14 | Y | 6 | eternitas | CI dead; distribution unshipped; E2E vs live API unverified |
| **windy-mind** | BYOM intelligence layer — brokers every LLM call, 9 providers. THE portability moat (Principle 2) | mind box EC2 | 07-18 | Y | 4 | eternitas (platform creds) | CI dead + manual deploy drift risk; OpenAI lane billing-blocked; SUBSTRATE.md stale per lockbox |
| **windy-pro** (account-server part) | Accounts/auth for the whole ecosystem + Windy Word desktop | account.windyword.ai (mail box) | 06-05 | Y | 7 | eternitas, mail, Stripe | 743-commit monorepo mixing account-server + desktop app; 115 legacy URLs; CI dead |
| **windy-mail** | Agent-friendly email (Stalwart) — every agent's inbox | mail box EC2 | 07-20 | Y | 5 | eternitas (EPT gate), Resend | **JMAP send broken for ALL accounts** (lockbox); CI dead; hatch-provision 401 history |
| **windy-agent** | Windy Fly — the personal agent product itself | fly.windyword.ai gateway | 07-18 | Y | 4 | eternitas, mind, mail, account | CI dead; deepest dependency surface = inherits every foundation bug; supervisor/launchd edge cases |
| **windy-connect** | One-command agent onboarding kernel | TBC | 07-11 | Y | 3 | eternitas, mind, mail | CI dead; orchestrator Worker secrets set 05-21 need re-verify; E2E onboarding unproven post-changes |
| **windy-contracts** | Agent-control canon (ADRs, doctrine) — READ-ONLY | n/a (docs) | 07-15 | Y | 0 | — | None blocking; reference only |
| **windy-registry** | Drops marketplace registry (FastAPI + R2) | TBC | 07-13 | Y | 4 | eternitas, R2 | CI dead; R2 health probe recently fixed — verify live |
| **windy-admin** | Super-admin dashboard, admin.windyword.ai | TBC | 07-19 | Y | 0 | all services (read scopes) | No CI at all; deploy state TBC; read-scope tokens fresh |

### TIER B — PRODUCTS (user-facing, launch candidates)

| Repo | What it is | Deployed at | Last | T | CI | Depends on | Top blockers |
|---|---|---|---|---|---|---|---|
| **windy-chat** | E2E messaging + translation (Matrix/Synapse) — "distribution engine" | chat.windychat.ai | 07-19 | Y | 5 | account, eternitas, mind | CI dead; mobile OTP dead code in pro-mobile still points here; GDPR flows fresh |
| **WindyCloud** | Storage/compute portal | cloud.windycloud.com | 07-20 | Y | 3 | eternitas, account, Stripe | CI dead; Stripe still sandbox; billing RDS untested under load |
| **windy-cloud-domains** | Domains tile in Cloud portal | same-origin Cloud | 07-17 | Y | 0 | WindyCloud, registrars | No CI; purchase error paths fresh |
| **windy-cloud-sites** | Websites tile in Cloud portal | same-origin Cloud | 07-19 | Y | 0 | WindyCloud, Cloudflare | No CI; CF edge providers fresh |
| **Windy-Clone** | Voice clone — "your voice lives forever" | mail box EC2 (API) | 07-20 | Y | 5 | eternitas, GPU fleet | CI dead; GPU training pipeline verify; revocation path fresh |
| **windy-search** | Agent web access (Brave-backed) | mail box EC2 | 07-13 | Y | 4 | eternitas, Brave key | Brave free credits will 429 when exhausted (card decision); single search bridge = no fallback |
| **windytalk** | Universal voice layer (Platform 14) | GPU fleet | 07-16 | Y | 1 | mind, agent | CI thin; TLS/CA fixes fresh; distribution unshipped |
| **windy-call** | Agent phone numbers (Twilio voice) | TBC | 07-07 | Y | 4 | eternitas, Twilio, cell | CI dead; Twilio A2P/TrustHub state to re-verify |
| **windy-text** | Agent SMS (Twilio) | TBC | 07-07 | Y | 4 | eternitas, Twilio, cell | CI dead; same Twilio dependencies |
| **windy-cell** | Phone-number registry (agent identity) | TBC | 07-07 | Y | 4 | eternitas | CI dead; internal key (C.6) rotation state |
| **windy-whatsapp-gateway** | Agents on WhatsApp | TBC | 07-02 | N | 0 | Twilio WA, chat (Matrix bridge) | Single commit, no tests, no CI — early P1 |
| **windy-code** | VS Code fork, AI-native editor | R2 releases | 07-10 | Y | 17 | account, eternitas, drops | Heaviest CI in ecosystem (17 workflows) all dead; desktop signing chain |
| **windy-code-web** | Browser-based Code builder | TBC | 07-18 | Y | 0 | code, account | No CI; session hygiene fresh |
| **windy-pro-mobile** | Windy Word mobile (voice-to-text) | TestFlight lane | 07-20 | Y | 2 | account, mail, RevenueCat | 90 legacy URLs; dead OTP screens; TestFlight needs this Mac (arm64) |
| **windy-drops** | Open marketplace for drops | TBC | 07-07 | Y | 2 | registry, R2 | CI dead; publish→registry upload path fresh |
| **windy-control-panel** | First consumer Drops surface | TBC | 07-07 | Y | 2 | drops, registry | CI dead |
| **windy-word-mcp** | Windy Word as MCP server | npm | 07-13 | N | 2 | windy-pro desktop | No tests; control-token flow fresh |
| **windy-fix-me** | Windy Doctor cloud relay | CF Worker | 06-06 | Y | 1 | word-mcp | CI dead; low churn |
| **windy-calendar** | Dead-simple scheduler | CF (wrangler) | 07-15 | Y | 0 | eternitas (light) | No CI; email wired to personal address (grant@grantwhitmer.com) — needs product address |
| **windy-stt** | faster-whisper GPU lane | Veron/GPU | 07-14 | N | 0 | mind (lane contract) | Single commit; no tests/CI; GPU box dependency |
| **windy-iron** | AI hardware by the minute | TBC | 07-17 | Y | 1 | — | CI dead; early product |
| **windy-ads** | AI UGC video-ad studio | TBC | 05-29 | Y | 1 | — | Dormant ~2 months; scope decision needed |

### TIER C — MARKETING SITES (harden after their products)

| Repo | For | Last | Notes |
|---|---|---|---|
| windyword-site | Windy Word | 07-20 | Active; honest-copy passes ongoing; S3+CloudFront |
| eternitas-site | Eternitas | 07-05 | README still says "attorney-toss-site" — cosmetic but sloppy |
| windy-mind-site | Mind | 07-12 | Swagger CTAs fixed |
| windy-drops-site | Drops | 07-12 | CF Pages |
| windy-search-site | Search | 07-12 | — |
| windy-connect-site | Connect | 07-02 | — |
| windycloud-site | Cloud | 04-19 | Stale ~3 months |
| windy-chat-site / Windy-Clone-site / windy-code-site / windy-fly-site / windy-mail-site | Chat/Clone/Code/Fly/Mail | 05-22 | All parked at canonical-domains-lint pass |
| windytranslate-site / windytraveler-site | Translate/Traveler | 05-22 / 04-19 | Products not launched |
| windyword-site-book-launch | Book launch | 07-03 | Campaign site |
| windy-call-site / windy-cell-site / windy-text-site / windy-mobile-site | Call/Cell/Text/Mobile | 05-08 | Single-commit placeholders |

### TIER D — PARKED / VISION / RECORDS (no hardening this era)

| Repo | Why parked |
|---|---|
| windy-mobile | Vision-locked MVNO ambition, no V1 code by design |
| windy-translate | Empty scaffold; real code lives in windy-pro services |
| windy-traveler | Built on translate — waits for it |
| windy-hand | 2 commits; web-actuation vision (Search finds, Hand acts) |
| windy-infra | Terraform snapshot of AWS; useful reference, CI = fmt/validate only |
| windy-cloud-vps | Doctrine docs only so far |
| Windy-Clinic | Operational records archive — private, keep private |

---

## 3. RANKED HARDENING ORDER (Phase 3 execution list)

Foundations before products; products before their sites; dependency-honoring. One line of rationale each.

1. **eternitas** — every repo references it; identity is what makes "welcome bots" safe (Principle 6). Nothing is trustworthy until the trust kernel is.
2. **windy-mind** — the portability moat itself (Principle 2); every product's brain-stem.
3. **windy-pro / account-server** — sign-in for everything; scoped to account-server paths first, desktop app second.
4. **windy-mail** — agents' inboxes; the known ALL-accounts JMAP send bug makes it the sickest foundation today.
5. **windy-agent** — the flagship product; consumes all four above, so hardening it validates them end-to-end.
6. **windy-connect** — the onboarding kernel; must be bulletproof before any launch invites outsiders in.
7. **windy-registry** → 8. **windy-drops** — marketplace substrate then marketplace.
9. **windy-admin** — observability across the fleet; needed before heavy merging eras.
   — *CONCERT PASS #1 (foundations): full hatch flow — sign up → passport → inbox → welcome email → agent alive → Mind call.*
10. **windy-chat** — distribution engine, already live with real users' data shapes (GDPR paths).
11. **WindyCloud** → 12. **windy-cloud-sites** → 13. **windy-cloud-domains** — portal then its tiles.
14. **windy-search** — every agent's web access; single-bridge fragility.
15. **Windy-Clone** — live API on the prod mail box deserves early attention.
    — *CONCERT PASS #2.*
16. **windytalk** → 17. **windy-stt** — voice lane pair.
18. **windy-cell** → 19. **windy-call** → 20. **windy-text** → 21. **windy-whatsapp-gateway** — comms stack in dependency order (registry before consumers).
22. **windy-code** → 23. **windy-code-web** — biggest CI surface, wants CI back first.
24. **windy-pro-mobile** — TestFlight lane now unblocked by this Mac (arm64).
    — *CONCERT PASS #3.*
25. **windy-drops-site consumer surfaces:** windy-control-panel → windy-word-mcp → windy-fix-me.
26. **windy-calendar** → 27. **windy-iron** → 28. **windy-ads** (scope decision first).
29. **Sites**, each immediately after its product: windyword-site, eternitas-site, mind-site, chat-site, cloud sites, search-site, connect-site, drops-site, fly-site, mail-site, clone-site, code-site, call/cell/text placeholders, translate/traveler when products exist, book-launch, mobile-site.
    — *FINAL CONCERT PASS: whole-ecosystem integration.*
30. **Tier D** stays parked (Principle 4 — no bloat resurrection).

---

## 4. DECISIONS THAT ARE GRANT'S

1. **GitHub billing (money, #1 ask):** fix the failed payment method at github.com → Settings → Billing. Until then CI is dead in all 58 repos, every deploy is manual, and the private lockbox repo itself is at risk if access lapses. Likely cost: whatever the existing plan bills, typically ~$4–20/mo unless there's accrued Actions debt.
2. **Windy-ops repo (permission, free):** one private repo `sneakyfree/windy-ops` for my durable memory (state, ledger, decisions, turnover letter) so any future instance resumes in two minutes. Principle 7 applied to me.
3. **Kit 0 snapshot (approval, ~free):** before heavy merging begins I want a fresh Hostinger snapshot of Kit 0. It's production, so per your rule I'll announce the exact API call and wait for your yes.
4. **Launch sequencing preference (yours by right):** the ranked order above is dependency-driven; if you want a particular product FIRST to market, say the word and I'll re-slot its chain.
5. **Small money flags, not urgent:** Brave Search free credits will eventually 429 (card adds headroom); OpenAI org debt only matters if you want that lane back (8 other providers live).

---

## 5. PHASE 2 QUEUE (foundations-of-foundations, before repo #1)

- [ ] GitHub billing unlocked (Grant) → verify Actions runs on one repo
- [ ] Kit 0 snapshot verified current (announce → Grant's yes → API call)
- [ ] Token hygiene sweep: retire degraded CF god token, dead GitHub PAT, PyPI hand-token — flag-only list with blast radius per token, no rotation without sign-off
- [ ] Mesh reboot-survival on this Mac (LaunchDaemon) — my call, my machine, will just do it via PR-able script
- [ ] `.thewindstorm.uk` → production-domain cleanup PRs (mechanical, 7 repos, table is locked)
- [ ] windy-ops repo created (after Grant's yes) + ledgers seeded
