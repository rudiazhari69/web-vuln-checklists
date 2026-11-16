# Business Logic Checklist

Business logic flaws: the app works as coded, but the code lets you
break the rules of the business — prices, quantities, workflows,
and the order of operations.

## Setup (before you start)

- [ ] Target flow is in scope (checkout, ordering, coupons,
      loyalty, refunds, booking, subscriptions).
- [ ] You understand the *intended* flow first (happy path, prices,
      limits, steps) before testing deviations.
- [ ] You are testing with your own account and your own money/test
      orders only. Never complete a real purchase you do not intend
      to pay for, and cancel/void test orders where the app allows it.

## Price and quantity

- [ ] Negative quantity or negative price in the request.
- [ ] Zero quantity / zero price.
- [ ] Decimal/fractional quantity where only whole units make sense.
- [ ] Price taken from the client request instead of the server
      (change the price field and see what is charged — on a test
      order you cancel, never a completed real payment).
- [ ] Currency parameter changed (different currency, different
      rounding).
- [ ] Quantity above stock / above a stated limit.
- [ ] Multiple items where the total is computed client-side.

## Coupons, discounts, loyalty

- [ ] Coupon applied twice / stacked with another coupon.
- [ ] Expired coupon, or a coupon for a different user/segment.
- [ ] Coupon for a percentage over 100, or a fixed discount larger
      than the total.
- [ ] Loyalty points / credits: negative balance, double-spend,
      points earned without the qualifying action.
- [ ] Referral rewards: self-referral, repeated referral.
- [ ] Free-trial / first-order-only offers used more than once.

## Workflow and state

- [ ] Steps skipped: go directly to a later step URL (checkout step
      3 without step 1–2; payment confirmation without payment).
- [ ] Steps repeated: submit the same step twice (double order,
      double charge, double credit).
- [ ] State transitions: cancel an already-shipped order, refund an
      already-refunded order, re-activate a cancelled subscription.
- [ ] Order of operations: apply a discount after payment, change
      the shipping address after dispatch, change items after the
      price is locked.
- [ ] Forced browsing: reach a page or action the UI hides (admin
      review step, internal approval step).

## Race conditions

- [ ] Concurrent requests on the same action (two redemptions of a
      one-time coupon, two withdrawals of the same balance) — test
      with a small number of parallel requests against your own
      account, inside the program's rate limits.
- [ ] Record whether the server serialises the operation or lets
      both through. One duplicate effect is proof; do not repeat it
      to drain anything.
- [ ] Never race a real payment or a real payout.

## Time and limits

- [ ] Expiry bypass: use an expired offer, token, or session.
- [ ] Trial reuse: register again, or reuse a trial identifier.
- [ ] Daily/monthly limits: exceed a stated cap (uses, downloads,
      redemptions) and see whether the server enforces it.
- [ ] Timezone / clock manipulation in date parameters (booking in
      the past, backdated actions).

## Evidence — what proves it

- [ ] Before/after state: balance, order total, order status, or
      coupon state — shown with the app's own UI or API responses.
- [ ] The exact requests (yours only) and the amounts involved.
- [ ] Financial impact quantified honestly (the difference the flaw
      creates on one test order — do not extrapolate to "all users"
      without evidence).
- [ ] Test orders cancelled/voided where possible; no real money
      kept or taken.

## What does NOT count

- A UI quirk with no server-side effect (the server recalculates
  the real price).
- A "flaw" that requires you to also break another control (for
  example it only works with an admin account you do not have).
- Duplicating an order by double-clicking when the server dedupes it
  (no duplicate created = no finding).

## Impact notes (for the report)

- State the rule that was broken, in one sentence.
- State the financial effect per transaction (amount, currency).
- State the preconditions (own account only, or any user; one
  request or a race).
- State whether the effect is reversible by the user (cancel/refund)
  or needs support intervention.
