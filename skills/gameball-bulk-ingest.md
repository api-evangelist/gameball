---
name: gameball-bulk-ingest
description: >-
  Backfill or bulk-sync customers, orders, events and point adjustments into
  Gameball through its asynchronous batch API, then poll for completion. Use for
  a migration, a nightly reconciliation, or any load that would otherwise blow
  through the per-second rate limits.
api: Gameball REST API v4.0
base_url: https://api.gameball.co/api/v4.0
operations:
  - batchOrderTransactions
generated: '2026-08-13'
method: generated
source: openapi/gameball-openapi.json
---

# Bulk ingest into Gameball

Grounded in `openapi/gameball-openapi.json`. Only one of the ten batch
operations carries an `operationId` in the published specification
(`batchOrderTransactions`); the rest are addressed by method and path.

## Why this exists

The per-resource limits are low enough that a migration cannot be done by looping
the single-record operations: orders, transactions and coupons cap at
**30 requests/second**. The batch surface is the supported path, and it is
asynchronous — you submit, get a batch id, and poll.

## Credentials

`APIKey` + `SecretKey` on every batch operation.

## Steps

1. **Submit the batch.** Pick the operation that matches the payload:

   | Purpose | Operation |
   |---|---|
   | Customer profiles | `POST /api/v4.0/integrations/batch/customers` |
   | Orders | `POST /api/v4.0/integrations/batch/orders` |
   | Ledger entries for orders | `batchOrderTransactions` — `POST /api/v4.0/integrations/batch/orders/transactions` |
   | Read many balances | `POST /api/v4.0/integrations/batch/balance-inquiry` |
   | Adjust many balances | `POST /api/v4.0/integrations/batch/balance-adjustment` |
   | Cashback awards | `POST /api/v4.0/integrations/batch/cashback` |
   | Redemptions | `POST /api/v4.0/integrations/batch/redeem` |
   | Behavioral events | `POST /api/v4.0/integrations/batch/events` |

   Keep the returned batch id.

2. **Poll for completion.**
   `GET /api/v4.0/integrations/batches/{batchId}/status`
   Poll on a backoff — the status read is itself rate limited. Gameball
   publishes no webhook for batch completion, so polling is the only mechanism.

3. **Abort if you need to.**
   `POST /api/v4.0/integrations/batches/{batchId}/stop`
   Worth wiring up before you run a large adjustment batch in production: it is
   the only kill switch, and a `balance-adjustment` batch moves real customer
   points.

## Retrying safely

Same natural-key rule as every other Gameball write: idempotency is keyed on the
transaction/order ids **inside** the payload, not on a header. Resubmitting the
same batch with the same ids produces `9004` (duplicate transaction ID) per
offending record rather than double-awarding. Resubmitting with regenerated ids
double-awards.

So: **generate your ids deterministically from your own source data** before the
first submission. If you generate them at request time, you cannot safely retry a
batch whose response you never saw.

## Ordering

Ingest customers before orders and events. A ledger write against a customer that
does not exist fails with `7000` / 404, and the batch does not create customers
for you.

## Errors

Batch submission errors use the same envelope (`code` / `type` / `message` /
`documentationUrl` / `requestId`). Per-record outcomes come back from the status
read. Watch for:

- `3013` / 415 — unsupported file or request type.
- `3011` / 422 — operation exceeds limits. Gameball does not publish a maximum
  batch size, so find it empirically and stay under it.
- `8000` / 403 — feature unavailable on the current subscription tier. The
  pricing page distinguishes "Standard API Access" from "Advanced API Access"
  without saying which endpoints fall where, so verify batch access against your
  own plan before building on it. See `plans/gameball-plans-pricing.yml`.
- `5003` / 503 — temporary unavailability. Retry the whole batch with the same
  ids; duplicates are rejected, not applied.

## SDK warning

Do not reach for the server SDKs for this. Every first-party server SDK on a
package registry is stale against v4.0 — .NET 3.0.0 (2022-02-09), Ruby 3.1.5
(2022-01-16), PHP v3.0.0 (2022-01-24), Python 1.0.0 (2020-08-25), Node.js 0.1.7
(2020-09-30). Call the REST API directly. See `packages/gameball-packages.yml`.
