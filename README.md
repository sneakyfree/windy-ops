# windy-ops — Fable's durable memory (Principle 7)

Private operational memory for the Windy CTO agent. Any future instance resumes from here in under two minutes, without asking Grant anything.

**Read order for a fresh instance:**
1. `TURNOVER.md` — where the last instance stopped, mid-thought.
2. `LAUNCH-LEDGER.md` — per-repo checklist state + what's in flight.
3. `DECISIONS.md` — every judgment call + which principle decided it.
4. `STATE-OF-THE-UNION.md` — full ecosystem inventory (refreshed as reality changes).

**Rules inherited:** never commit to main in any OTHER repo (this repo is the one exception — it is Fable's own notebook, created by Fable, and Grant merges nothing here). No secret values ever. Update the ledger BEFORE starting a task and AFTER finishing it.

**The working environment (verified 2026-07-21):**
- Cowork sandbox on Grant's Mac mini M4. Mounted: `~/windy` (58 repos), `~/kit-army-config` (read-only doctrine + lockbox).
- Sandbox network: github.com git-over-HTTPS ONLY. No DNS to anything else, no SSH, no ICMP, no cloud APIs, no api.github.com.
- Anything needing real network (DNS checks, Hostinger/Cloudflare APIs, gh, SSH to fleet) runs via a script staged in `~/windy/` that Grant pastes into his authenticated Claude Code terminal. Results land in `~/windy/.fable/` where the sandbox can read them.
