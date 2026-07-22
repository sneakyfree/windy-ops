# INTEGRATION-READINESS.md — do the platforms actually fit together?

*Built 2026-07-22 (night). Method: STATIC contract verification — for every place service A calls service B, confirm B actually exposes that route + auth. This is the stand-in for live integration testing, which needs the fleet on Kit 0 (sandbox can't reach it). Live end-to-end run = staged for a Grant-paste morning batch.*

**Headline:** across 11 dependency edges (22 concrete route+auth checks), **19 PASS / 3 MISMATCH / 0 ambiguous.** The CORE loop Grant named — "grandma voice-chats her agent" — is contract-sound end to end except ONE crack (account-deletion cleanup), which is not on the happy path.

## CORE LOOP (grandma ↔ agent) — the priority
| Edge | Verdict | Evidence |
|---|---|---|
| chat → account-server `POST /api/v1/identity/chat/provision` | ✅ | account-server identity.ts:556 (Bearer user-JWT); consumer chat onboarding/routes/provision.js:181 |
| chat → account-server `POST /api/v1/identity/mail/address-by-windy-id` | ✅ | identity.ts:920 (X-Service-Token=CHAT_SERVICE_TOKEN); consumer agent-roster/server.js:114 |
| chat → account-server `POST /api/v1/identity/chat/account-deleted` | ⚠️ **MISSING** | **no such route in account-server** (0 hits). chat calls it on Matrix teardown (onboarding/server.js:203) → **404, deletion webhook silently fails.** Not on happy path (it's GDPR cleanup) but real. |
| chat → account-server `GET /.well-known/jwks.json` | ✅ | server.ts:255 |
| chat → eternitas `GET /api/v1/trust/{passport}` (public) | ✅ | eternitas trust.py:54 |
| chat → mind `POST /v1/chat` (Bearer EPT) | ✅ | mind routes/chat.py:167; EPT resolved auth/middleware.py:91 |
| chat → mind `GET /v1/runtime/claim/{passport}/status` | ✅ | mind routes/runtime_claim.py:383 |
| chat → Synapse `POST /_synapse/admin/v1/register` (HMAC) | ✅ | homeserver.yaml:63 wires the shared secret |

**Core-loop verdict: SOUND.** An agent can be provisioned a Matrix identity, verify trust, and generate replies through Mind — all contracts line up. The only crack is account-deletion cleanup (below), which doesn't stop the loop.

## ONBOARDING (connect fan-out) — all ✅
connect → eternitas `POST /api/v1/bots/auto-hatch` (bots.py:141) ✅ · connect → eternitas `GET /.well-known/eternitas-keys` (wellknown.py:20) ✅ · connect → mail `POST /api/v1/provision/bot` X-Service-Token (mail provision.py:105) ✅ · connect → mind `POST /admin/keys` Bearer MIND_ADMIN_TOKEN (mind admin_keys.py:137) ✅

## MARKETPLACE
drops → registry: GET/POST/PUT/DELETE drops (browse.py:261, drops.py:103/275/449) all ✅ · registry → eternitas JWKS ✅ · registry → eternitas passport-status ⚠️ **MISMATCH**: registry's nightly integrity job hits `GET /api/v1/passports/{passport}/status` (refresh_integrity.py:34) which **eternitas does not expose** — real paths are `/api/v1/registry/{passport}/integrity` (registry.py:602) or `/api/v1/trust/{passport}` (trust.py:54). Nightly refresh 404s. Fixable in the CONSUMER (registry).

## ADMIN
admin → account-server JWKS ✅ · admin → eternitas public verify `GET /api/v1/registry/verify/{passport}` (registry.py:458) ✅ · admin → eternitas operator login `POST /api/v1/auth/login` (auth.py:186) ✅ · (`chat-validate` route exists in account-server auth.ts:568 but the real caller is Synapse, not admin — map attribution note, no contract break.)

## THE 3 MISMATCHES — disposition
1. **chat → account-server `chat/account-deleted` 404 (core-loop-adjacent, GDPR).** account-server is FROZEN (sibling Fable consolidating). → Routed as an agent-to-agent HANDOFF (see HANDOFF-windy-pro-findings.md) — the windy-pro Fable should add the route or tell chat the real path. Also aligns with chat's own incomplete-GDPR-fan-out finding (RECON.md windy-chat). NOT my repo to edit.
2. **registry → eternitas nightly integrity path 404 (refresh_integrity.py:34).** ⚠️ CORRECTION (07-22 morning, after reading both sides via the OpenAPI capture): this is NOT a one-line path swap. The job reads `integrity_band` + `clearance_level` from a `/passports/{p}/status` shape that eternitas NEVER exposes. Modern eternitas: `integrity_band` is no longer an API field anywhere (replaced by the multi-dimensional Integrity Index at `/api/v1/registry/{passport}/integrity` → overall(0-1000)+label+dimensions); `clearance_level` is an OPERATOR attribute (registry.py:818/1075/1106), not per-bot. So `refresh_integrity.py` targets a retired contract and has been silently failing (writes nothing on failure → author badges just stale). OFF the grandma core loop (cosmetic marketplace author-profile fields). PROPER FIX needs a mapping decision (e.g. derive band from the integrity-index `label`/`overall`, mirroring how connect's provision.ts derives `integrity_band` via mapTrustScoreToBand(trust_score)) + can't be unit-tested in-sandbox (py3.10). DISPOSITION: STAGE with recommendation, do NOT ship a guess (P1 don't write guessed data; P3/P4 it's non-core frosting). Low priority vs core.
3. **admin `chat-validate` attribution.** No action — route exists, real caller is Synapse. Recorded so the map is honest.

## WHAT STILL NEEDS A LIVE RUN (can't be settled statically — morning batch)
Contract shapes match, but these need the fleet on Kit 0: (a) the full hatch concert — signup → passport → inbox → welcome email → agent alive → Mind reply; (b) whether each consumer's BASE URL/token env is actually set on Kit 0 (several default to localhost/dead-EC2 in code and rely on prod env override); (c) chat's push-bus wiring drift (override.yml not loaded by prod compose). Staged as the concert-pass script.
