# DECISIONS.md — every judgment call, and which principle decided it

Format: date · decision · deciding principle(s) · notes.

- **2026-07-21 · Phase 1 = survey-level, not 58 full builds.** P3 (solve the minimum) + P4 (minimalist). Build verification happens per-repo at hardening time; SOTU marks it TBC honestly.
- **2026-07-21 · Ranked order = identity → mind → accounts → mail → agent.** P6 (Eternitas is load-bearing — "welcome bots" is only safe if the passport office is sound) + P2 (Mind is the portability moat).
- **2026-07-21 · Tier D repos stay parked.** P4 — resurrecting vision scaffolds now is feature bloat.
- **2026-07-21 · Sandbox is network-walled → "one-paste script" pattern.** P1 (Grant stays terminal-free except single pastes) + P5 (dumb script, smart model). Scripts staged in ~/windy, results to ~/windy/.fable/.
- **2026-07-21 · GitHub billing stays broken for now (Grant: no funds, negotiating credit).** Consequence accepted: CI dead, manual deploy runbooks remain the standing path. Revisit the moment his credit lands.
- **2026-07-21 · windy-ops approved by Grant (verbatim yes), staged at ~/windy/windy-ops.** P7.
- **2026-07-21 · Kit 0 snapshot approved by Grant.** Executed via Hostinger API from his terminal. Announced plainly: a new snapshot REPLACES the previous one (Hostinger keeps one).
- **2026-07-21 · Honesty flag: two token values (Brave, PyPI) surfaced in my scratch grep output.** Both were already rotation-flagged in the lockbox; folded into Phase 2 hygiene list. Lesson recorded: always pipe lockbox greps through a redaction sed.
- **2026-07-21 · Grant reports FULL AWS→Hostinger migration (~days ago); lockbox only records partial (account-server 07-16, kernel 07-17).** Verifying with a DNS audit before correcting SOTU — trust but verify, since the lockbox is SoT and currently disagrees with the founder's memory. No AWS teardown assumptions until proven.
