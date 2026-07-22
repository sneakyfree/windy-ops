# TOKEN-HYGIENE.md — flag list (NO rotations executed; every item lists blast radius first)
*Compiled 2026-07-21 from the lockbox's own rotation notes + this era's observations. Names only, never values.*

## ACT SOON (cheap, safe, real benefit)
| # | Token | Why | Blast radius if rotated | How |
|---|---|---|---|---|
| 1 | Old Cloudflare god token (`cfut_dkWWAP…`, DEGRADED, retired 07-02) | Lockbox's own TODO: delete now that `godtoken02JUL26` replaces it | None known — nothing should use it; degraded anyway | CF dash → API Tokens → delete. Grant-click or god-token API call |
| 2 | PyPI `windyfly-publish-2026-07` | Value surfaced in my chat scratch (07-21) + lockbox already says revoke when possible | Only hand-publishing windyfly; nothing automated uses it (Actions dead) | pypi.org → account → revoke. Re-mint only when next publish needed |
| 3 | Brave `WindySearch` key | Value surfaced in my chat scratch (07-21) | windy-search goes dark until new key lands in its `.env` + container recreate — **Kit 0 change → announce-first** | Brave dashboard re-key → single env edit + recreate, 2-min window |
| 4 | Resend `WindyEcosystemAPI1` + `YOLOTOKEN` | Lockbox's own note: both were pasted through chat at setup | Mail sending (hatch welcome etc.) during the swap — **Kit 0 change → announce-first** | Documented procedure at lockbox §Resend rotation plan |

## PAIRED WITH OTHER EVENTS (do when the trigger fires)
| # | Token | Trigger |
|---|---|---|
| 5 | OpenAI 7 "Codex"-flavored keys | BEFORE paying the -$397 debt: audit which fleet agents hold them, revoke unused, set spend cap — else the bleed resumes |
| 6 | Vapi + Retell sandbox keys | windy-call M2 ships to prod (checklist already in lockbox) |
| 7 | Stripe LIVE keys | Business verification completes |
| 8 | `ETERNITAS_WEBHOOK_SECRET`/HMAC family | Pre-consumer-scale (lockbox note) |
| 9 | NVIDIA NIM free-tier key | Traffic exceeds 1M tokens/day (rotate to paid) |

## AVAILABILITY GAPS (not rotation — missing/misplaced material)
- **GitHub PAT `fable-cowork-2026-07` — minted by Grant 2026-07-22 (in progress).** Standing location: `~/windy/.fable/gh-token` (0600, OUTSIDE all git repos — ~/windy itself is not a repo; delivered via drop.sh, value never in chat). Scope: fine-grained, sneakyfree all-repos, Contents+PRs read/write. Expires ~2026-10-20 — FLAG RENEWAL EARLY OCTOBER. Deviation from lockbox-as-SoT noted deliberately: Grant can't edit the lockbox himself and I may not write to it (read-only doctrine); this file documents the location so it stays discoverable. Restores: push/fetch/clone autonomy incl. windy-ops memory sync. Does NOT restore gh/API (api.github.com walled) — PR-open/merge remain Grant's clicks.
- **`kit_mesh` fleet private key** still exists only on Kit 0 + OC5, not in `secrets/`. Blocks key-based fleet SSH from this Mac (password fallback works).
- **`windy-mac-key`** private half only on Windy 0 (scorecard item) — add to `secrets/mac-mini/` for consistency.

## GIT-HISTORY SECRET SCAN (2026-07-22, night — done because repos are PUBLIC)
Scanned all-refs history (first ~400 commits/repo) of the 9 core repos (eternitas, mind, mail, agent, connect, registry, drops, admin, chat) for real credential-value shapes (gh_/sk-/sk-ant-/re_/xox/AKIA/AIza/SG./PEM private-key blocks/JWTs). **RESULT: CLEAN — zero real leaked credentials.** Only intentional TEST FIXTURES matched: eternitas `tests/golden_vectors/cosign/es256_v1.json` field `private_key_pem_TEST_ONLY` (a fixed keypair for deterministic ES256 golden vectors — test-only by design), and windy-agent test strings (`detect_provider("sk-...")`, `test_sanitize_outgoing.py` dummy `sk-ant-...` that exists to verify the redactor). Both benign. NOTE: bounded to ~400 commits/repo — before any real public launch run a fuller deep scan (trufflehog/gitleaks) as part of the visibility gate. But the recent-history exposure from repos being public is LOW — good news for Grant.

## RESIDUE (documented, low risk, no action)
- Dead GitHub PAT baked into old EC2 cloud-init user-data — moot if/when zombies terminated (see AWS audit).
- Retired `windy-imac-key` persists in kit-army-config git history — accepted (history rewrite forbidden), access target no longer exists.
