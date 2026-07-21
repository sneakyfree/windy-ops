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
- **No live GitHub PAT exists anywhere** (old one dead 07-17, residue in a possibly-dead EC2's cloud-init). Consequence: Fable's sandbox cannot push/pull GitHub directly; everything rides Grant-paste scripts. A new fine-grained PAT (repo scope, sneakyfree) stored in the lockbox would restore agent autonomy. Needs Grant at github.com (~3 min) — queued as a morning ask, not urgent.
- **`kit_mesh` fleet private key** still exists only on Kit 0 + OC5, not in `secrets/`. Blocks key-based fleet SSH from this Mac (password fallback works).
- **`windy-mac-key`** private half only on Windy 0 (scorecard item) — add to `secrets/mac-mini/` for consistency.

## RESIDUE (documented, low risk, no action)
- Dead GitHub PAT baked into old EC2 cloud-init user-data — moot if/when zombies terminated (see AWS audit).
- Retired `windy-imac-key` persists in kit-army-config git history — accepted (history rewrite forbidden), access target no longer exists.
