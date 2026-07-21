# HANDOFF — windy-pro findings for the consolidation Fable (Windy 0)

*From: Fable/CTO on the Mac mini, 2026-07-21. I stood down from all Windy Word repos on Grant's instruction (book-launch↔main consolidation in progress). The mounted repo was restored to `book-launch`, clean tree, no branches of mine. These findings are yours to fold in.*

## 🔴 Trust-critical: revoked passports are NOT cascaded locally (live gap, on BOTH branches)

Since 2026-07-06 Eternitas dispatches platform webhooks to the raw-body firehose route (`POST /webhooks/eternitas`, routes/webhooks-eternitas.ts) — whose handler is **Phase-1 log-and-ack only**. The ONLY implementation of the local cascade (suspend product_accounts, revoke bot_api_keys, update eternitas_passports) sits in the LEGACY route `POST /api/v1/identity/webhooks/eternitas` (identity.ts ~1888/1944), whose signature check HMACs `JSON.stringify(req.body)` — re-serialized bytes that can never match Eternitas's raw-body signature. **Net effect: a passport revoked in Eternitas keeps its local product accounts and bot API keys ACTIVE in account-server.** (The 300s Eternitas trust-cache plus products that re-check trust upstream soften this, but local state is wrong forever.)

**Ready-made fix (validated against `main` @46bc4da):** `handoff-account-server-cascade.patch` in this repo —
1. New `src/services/eternitas-passport-events.ts`: idempotent cascade (revoked/suspended/reinstated/trust-updated), extracted from the legacy handler, calling the existing `cascadeRevocation()`.
2. Firehose `processEternitasFirehoseEvent` wires passport.* events into it (async, post-ack, per the 5s-budget contract).
3. Legacy identity route → explicit **410 Gone** tombstone pointing at the firehose.
4. Tests: new `tests/eternitas-cascade.test.ts` (6 cases incl. idempotent redelivery) + `tests/eternitas-webhook-signature.test.ts` repointed to assert 410. **42/42 webhook-family tests green; tsc clean** (run in the Mac-mini sandbox against main + current shared/contracts).

## Also noted while inside (verify against your consolidated tree)
- `tests/api.test.ts` "translate/text returns 200 without auth" FAILS on pristine main in a clean environment (503) — likely env/provider-dependent or a dead-CI blind spot; not caused by the patch (fails without it).
- `tests/landing-stub.test.ts` /index.html expects the built web dist — environmental unless the web bundle is present.
- Recon summary for account-server (stack, env names, silent catches in routes/auth.ts, stale AWS deploy docs): see RECON.md §windy-pro.
- Deploy truth: live account-server runs on Kit 0 at `/root/windy-pro/account-server`, compose project `account-server`, configs `docker-compose.prod.yml` + `override-hosts` + `override-postgres` (verified via docker inspect 2026-07-21). `DEPLOY.md`/workflows still describe AWS.
