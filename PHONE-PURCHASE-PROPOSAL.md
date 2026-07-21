# PROPOSAL for Grant — guard live Twilio number purchases behind an explicit opt-in

**Status:** awaiting Grant's decision. NOT shipped (money-behavior change; I only ship the test fix unilaterally).
**Found:** 2026-07-21 while triaging windy-agent's mock-provisioning test failures.

## The risk, plainly
`src/windyfly/phone_provision.py::provision_phone()` is called during **every agent hatch** (`hatch_orchestrator.py::_step_phone`). Its logic:

1. If `TWILIO_PHONE_NUMBER` is set in the environment → return that number.
2. Else if `TWILIO_ACCOUNT_SID` **and** `TWILIO_AUTH_TOKEN` are both set → **`_provision_twilio()` searches for and BUYS a real number** (`POST /IncomingPhoneNumbers.json`) — real money.
3. Else if a DB is present → mock pool (free).

The purchase in step 2 fires on the mere **presence of credentials in the environment**. There is no explicit "yes, spend money" switch. So any machine — a developer's laptop, a CI runner, a future grandma's box — that happens to have real Twilio creds exported will **silently buy a phone number** the first time it hatches an agent. Given Twilio may not even be paid up right now, that's a live surprise-charge (or a failed-charge error mid-hatch) waiting to happen.

This is a Principle 1 issue (honey-badger stability; a normie must never be surprised by a charge) and Principle 5 (dumb, predictable tools).

## Proposed fix (small, reversible)
Gate the purchase on an explicit flag, default OFF:

```python
allow_purchase = os.environ.get("WINDYFLY_ALLOW_PHONE_PURCHASE", "").lower() in ("1", "true", "yes")
if account_sid and auth_token and allow_purchase:
    try:
        return await _provision_twilio(...)
    except Exception as exc:
        logger.warning("Twilio purchase failed, falling back to mock: %s", exc)
# else: fall through to mock (free) — never spend money we weren't told to spend
```

Ambient `TWILIO_PHONE_NUMBER` (reusing a number you already own — no spend) is untouched. Only the *buy-a-new-number* path becomes opt-in.

**Effect:** hatching becomes free-by-default (mock number) unless an operator deliberately sets `WINDYFLY_ALLOW_PHONE_PURCHASE=1`. Anyone who currently relies on ambient-cred purchasing sets one flag to restore it.

## Why I didn't just ship it
It changes real spending behavior, and I can't test the live Twilio purchase path from here (and shouldn't — Grant flagged Twilio may be unpaid). Changing when money moves is Grant's call, not mine. The test-isolation fix (branch `harden/agent-02-phone-test-isolation`, PR pending) is unambiguous and ships now; this guard waits for a yes.

**If Grant says yes:** one small PR to phone_provision.py + a test asserting no purchase attempt without the flag. ~15 minutes.
