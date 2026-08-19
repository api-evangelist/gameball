---
name: gameball-redeem-points-at-checkout
description: >-
  Let a customer spend Gameball loyalty points at checkout — reserve the points
  with a hold, issue or validate a coupon, then burn it on order completion or
  release the hold if the cart is abandoned. Use when adding "pay with points"
  to a checkout flow.
api: Gameball REST API v4.0
base_url: https://api.gameball.co/api/v4.0
operations:
  - getCustomerBalance
  - getCustomerCoupons
generated: '2026-08-13'
method: generated
source: openapi/gameball-openapi.json
---

# Redeem points at checkout

Grounded in `openapi/gameball-openapi.json`. Several operations in this flow
carry **no `operationId`** in the published specification, so they are addressed
here by method and path — that is a defect in Gameball's spec, not an omission
here. See `overlays/gameball-openapi-overlay.yaml`.

## Credentials

`APIKey` **and** `SecretKey` on every call in this skill. All of them are
transactional and the specification marks all of them as requiring both headers.
Server-side only.

## Steps

1. **Check what the customer can spend.**
   `getCustomerBalance` — `GET /api/v4.0/integrations/customers/{customerId}/balance`
   and, if you show existing coupons in the cart,
   `getCustomerCoupons` — `GET /api/v4.0/integrations/customers/{customerId}/coupons`.
   Read the redemption rules with
   `GET /api/v4.0/integrations/configurations/rewards/redemption` so you apply
   Gameball's own minimum/maximum and conversion rate rather than your own.

2. **Reserve the points.**
   `POST /api/v4.0/integrations/transactions/hold`
   A hold takes the points out of the spendable balance without burning them,
   which is what stops a customer spending the same points in two tabs. Keep the
   returned hold reference — you need it for every subsequent step.

   Check a hold with `GET /api/v4.0/integrations/transactions/hold/{holdReferenceId}`.

3. **Issue or validate the discount.**
   - Issue from your own pool: `POST /api/v4.0/integrations/coupons/predefined`
   - Let Gameball generate one: `POST /api/v4.0/integrations/coupons/automatic`
   - Validate a code the customer typed: `POST /api/v4.0/integrations/coupons/{code}/validate`
     or `POST /api/v4.0/integrations/coupons/validate`

4. **On successful payment — commit.**
   `POST /api/v4.0/integrations/coupons/burn` marks the coupon consumed, and
   `POST /api/v4.0/integrations/transactions/redeem` writes the redemption to the
   points ledger.

5. **On abandonment or failure — release.**
   `DELETE /api/v4.0/integrations/transactions/hold/{holdReferenceId}` returns the
   points to the spendable balance.
   `DELETE /api/v4.0/integrations/coupons/{lockReference}` releases a locked coupon.

   **Always release.** Gameball publishes no hold expiry policy, so an
   unreleased hold is points the customer cannot spend and cannot see a reason
   for. Wire the release into the same error path that cancels the order.

6. **Refunds.**
   `POST /api/v4.0/integrations/transactions/refund` reverses a completed
   redemption. Note code `9000` — some transaction types are not reversible.

## Step-up verification

`POST /api/v4.0/integrations/transactions/otp` exists for redemption flows that
require a one-time password before the points move. Use it wherever redemption
is high value; Gameball does not enforce it for you.

## Retrying safely

No `Idempotency-Key` header. Resend the **same** transaction id and timestamp and
Gameball rejects the duplicate rather than double-spending:

- `9004` — duplicate transaction ID → the original redemption landed. Treat as success.
- `9003` — duplicate transaction timestamp → same.

Never retry a redemption with a fresh transaction id after a timeout. That is how
a customer's balance gets drained twice.

## Errors that matter here

- `9008` / 422 — insufficient balance points. Show it to the customer; do not retry.
- `9006` / 404 — hold reference not found. The hold already expired or was released.
- `9001` / 422 — transaction already cancelled.
- `9000` / 422 — non-reversible transaction type (on refund).
- `6003` / 403 — no access to coupon configurations on this plan.
- `8000` / 403 — feature unavailable on the current subscription tier.

Full catalog in `errors/gameball-error-codes.yml`.

## Rate limits

Transactions and coupons both throttle at **30/second, 360 per 30 seconds**, with
HTTP 429 and no rate-limit response headers. A checkout burst is the most likely
place to hit this. See `rate-limits/gameball-rate-limits.yml`.
