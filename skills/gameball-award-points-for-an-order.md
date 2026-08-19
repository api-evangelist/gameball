---
name: gameball-award-points-for-an-order
description: >-
  Enroll a customer in a Gameball loyalty program and award loyalty points for a
  purchase, then read back the resulting balance. Use when wiring an
  e-commerce checkout or POS into Gameball for the first time.
api: Gameball REST API v4.0
base_url: https://api.gameball.co/api/v4.0
operations:
  - createCustomer
  - trackOrder
  - calculateOrderCashback
  - previewOrderRewards
  - getCustomerBalance
  - getOrderTransactions
generated: '2026-08-13'
method: generated
source: openapi/gameball-openapi.json
---

# Award loyalty points for an order

Grounded in `openapi/gameball-openapi.json` (OpenAPI 3.1.0, `info.version` 4.0.0).
Every operation named below exists in that specification.

## Credentials

Two headers, both from **Settings > Admin Settings > Account Integration** in the
Gameball dashboard:

- `APIKey` — required on every request.
- `SecretKey` — required on every operation in this skill, because all of them are
  transactional. Also required on *every* operation if the account has High
  Security Mode enabled.

Never send `SecretKey` from a browser, a mobile app, or any client you do not
control. It is also the key used to compute the widget customer hash.

Ignore the `bearerAuth` scheme in the published specification. It is the
document-level default but Gameball documents no bearer grant for the REST API,
and it applies only to the three `/plants` operations left over from a
documentation template.

## Steps

1. **Create or update the customer.**
   `createCustomer` — `POST /api/v4.0/integrations/customers`
   Upsert semantics: send your own `customerId` (the merchant-side id). If the
   customer already exists the profile is updated rather than rejected. Capture
   the returned Gameball customer id if you want to reconcile later.

2. **Optional — preview before you commit.**
   `previewOrderRewards` — `POST /api/v4.0/integrations/orders/reward-preview`
   returns what the order *would* earn without writing anything, and
   `calculateOrderCashback` — `POST /api/v4.0/integrations/orders/cashback`
   returns the cashback calculation alone. Use either to show a customer their
   pending points at checkout. Neither creates a ledger entry.

3. **Track the order.**
   `trackOrder` — `POST /api/v4.0/integrations/orders`
   This is the write that awards points. Send your own order id and the
   transaction time.

4. **Read the balance.**
   `getCustomerBalance` — `GET /api/v4.0/integrations/customers/{customerId}/balance`

5. **Reconcile.**
   `getOrderTransactions` — `GET /api/v4.0/integrations/orders/{orderId}/transactions`
   lists every ledger entry produced by that order. To reverse them, call
   `DELETE /api/v4.0/integrations/orders/{orderId}/transactions` (this operation
   carries no `operationId` in the published specification, so address it by
   method and path).

## Retrying safely

Gameball has **no `Idempotency-Key` header**. Idempotency is keyed on the domain
identifiers you supply, so a retry is safe as long as you resend the *same*
order id and transaction time:

- Application code `9004` — duplicate transaction ID.
- Application code `9003` — duplicate transaction timestamp.

Both are HTTP 422. On a retry after a timeout or a 5xx, **treat 9003 and 9004 as
success** — they mean the original write landed. Retrying with a *new* order id
double-awards points.

## Errors

Gameball does not use RFC 9457. Every failure is a JSON object with `code`,
`type`, `message`, `documentationUrl` and `requestId`. Read `code`, not the HTTP
status — the status is coarse and the specification declares no error response
at all on 58 of its 78 operations.

- `1000` / HTTP 401 — bad or missing credentials. Never retry.
- `7000` / HTTP 404 — customer not found. Run step 1 first.
- `9008` / HTTP 422 — insufficient balance. A business outcome, not a fault.
- HTTP 429 — throttled. See below.

Full catalog: `errors/gameball-error-codes.yml` and
`errors/gameball-problem-types.yml`.

## Rate limits

Orders and transactions throttle at **30 requests/second and 360 per 30 seconds**;
customer and event reads are higher. Gameball returns HTTP 429 and publishes
**no `X-RateLimit-*` and no `Retry-After` header**, so you must implement your own
backoff — there is no runtime signal to read. See `rate-limits/gameball-rate-limits.yml`.

## At volume

Do not loop this skill for a backfill. Use
`POST /api/v4.0/integrations/batch/customers` and
`POST /api/v4.0/integrations/batch/orders`, then poll
`GET /api/v4.0/integrations/batches/{batchId}/status`.
See `gameball-bulk-ingest.md`.
