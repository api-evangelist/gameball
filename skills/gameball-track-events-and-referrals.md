---
name: gameball-track-events-and-referrals
description: >-
  Send behavioral events to Gameball to drive action-based reward campaigns, and
  wire up a referral program — validate a referrer code at signup, then read
  referral and campaign progress back. Use for non-purchase engagement and
  refer-a-friend flows.
api: Gameball REST API v4.0
base_url: https://api.gameball.co/api/v4.0
operations:
  - sendEvents
  - previewEventRewards
  - validateReferrerCode
  - createCustomer
  - getCustomerReferrals
  - getCustomerReferralsCount
  - getCustomerCampaignsProgress
  - getCustomerActivities
  - getCustomerStampsProgress
  - getCustomerDailyStreakProgress
  - achieveSocialChallenge
  - attachCustomerTags
generated: '2026-08-13'
method: generated
source: openapi/gameball-openapi.json
---

# Track events and run a referral program

Grounded in `openapi/gameball-openapi.json`.

## Credentials

`APIKey` on the reads; `APIKey` + `SecretKey` on the writes. Server-side only.

## Behavioral events

1. **Optional preview.**
   `previewEventRewards` — `POST /api/v4.0/integrations/events/reward-preview`
   returns what an event would award without writing it. Use it to show
   "complete this and earn 50 points" in your UI.

2. **Send the event.**
   `sendEvents` — `POST /api/v4.0/integrations/events`
   Events are the trigger for action-based reward campaigns, missions, streaks
   and stamps. The event name must match one configured in the Gameball
   dashboard — the REST API has no operation to read or create the event
   catalog, so a mistyped name silently awards nothing. (The MCP server has a
   `get_events` tool for this; the REST API does not. See
   `mcp/gameball-tool-crosswalk.yml`.)

3. **Read progress back.**
   - `getCustomerCampaignsProgress` — `GET /api/v4.0/integrations/customers/{customerId}/reward-campaigns-progress`
   - `getCustomerStampsProgress` — `GET /api/v4.0/integrations/customers/{customerId}/stamps/{challengeId}`
   - `getCustomerDailyStreakProgress` — `GET /api/v4.0/integrations/customers/{customerId}/streaks/{campaignId}`
   - `getCustomerActivities` — `GET /api/v4.0/integrations/customers/{customerId}/activities` (paginated)
   - `getCustomerAutomationCampaigns` — `GET /api/v4.0/integrations/customers/{customerId}/automation`

`achieveSocialChallenge` — `POST /api/v4.0/integrations/customers/social-challenges`
records a social-share challenge completion.

## Referrals

1. **Validate the code before you create the account.**
   `validateReferrerCode` — `GET /api/v4.0/integrations/referrals/validate`
   Call this at signup, before `createCustomer`, so a bad code becomes a form
   error rather than a half-created customer. Code `7003` / 404 means the
   referral code does not exist.

2. **Create the referred customer** with `createCustomer`, carrying the referrer
   code.

3. **Read the referral graph.**
   `getCustomerReferrals` — `GET /api/v4.0/integrations/customers/{customerId}/referrals` (paginated)
   `getCustomerReferralsCount` — `GET /api/v4.0/integrations/customers/{customerId}/referrals/count`

   Referral rules: `getReferralsConfigurations` —
   `GET /api/v4.0/integrations/configurations/referrals`.

Referral-specific errors: `7002` customer already referred, `7005` duplicate
referral, `7006` invalid referral. All HTTP 422, all caller-fixable.

## Pagination

The paginated reads here (`activities`, `referrals`, `notifications`) use
**cursor** pagination, not offset:

- `startAfter` — the id of the last record on the previous page, exclusive. Omit for page one.
- `limit` — page size.

There is no total in the page envelope and no `Link` header — that is what the
matching `/count` operations are for. See `conventions/gameball-conventions.yml`.

## Localization

A `lang` parameter selects the response language on nine operations — but it is a
**header** on seven of them and a **query parameter** on the other two. Check the
specification per operation; the inconsistency is real.

## Segmentation

`attachCustomerTags` — `POST /api/v4.0/integrations/customers/{customerId}/tags`
and `removeCustomerTags` — `DELETE /api/v4.0/integrations/customers/{customerId}/tags`
(or `removeCustomerTag` for a single tag). The REST API has no operation to list
the account's tag vocabulary, so keep your tag names in your own code.

## Rate limits

Events and customer reads throttle at **100/second, 1200 per 30 seconds** — higher
than transactions. Still HTTP 429 with no rate-limit response headers. For
backfilling events use `POST /api/v4.0/integrations/batch/events` instead of
looping.

## Reacting to what Gameball decides

Events are fire-and-forget; the reward decision is asynchronous. Subscribe to the
webhook topics to learn what was actually awarded — deliveries carry an
`X-GB-Signature` header you must verify, and Gameball retries a non-2xx three
times at 5, 20 and 60 minutes before discarding the event. See
`asyncapi/gameball-webhooks.yml`.
