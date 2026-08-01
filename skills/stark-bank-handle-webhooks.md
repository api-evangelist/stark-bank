---
name: Subscribe to and process Stark Bank webhook events
description: Register a webhook, verify signed events, and retry failed deliveries.
api: openapi/stark-bank-openapi-original.yml
operations: [create-webhook, list-webhook, get-webhook-byId, delete-webhook-byId, list-event, get-event-byId, update-event-byId, list-event-attempt]
---

# Subscribe to and process Stark Bank webhook events

Event-driven reconciliation for money movement — react to `invoice`, `transfer`,
`deposit`, `boleto`, `brcode-payment` and other resource state changes instead of
polling.

## Auth
ECDSA request signing on the management calls. Inbound event bodies are signed by
Stark Bank and MUST be verified before you trust them.

## Steps
1. **Register** — `create-webhook` with your public `url` and a `subscriptions`
   array. Valid values: `deposit`, `invoice`, `brcode-payment`, `transfer`,
   `utility-payment`, `boleto`, `boleto-payment`, `darf-payment`,
   `payment-request`, `boleto-holmes`.
2. **Verify every delivery** — validate the `Digital-Signature` header against
   Stark Bank's public key (SDK: `event.parse()`), which raises on mismatch.
   Return 2xx quickly and process asynchronously; tolerate unknown event types.
3. **Reconcile & recover** — `list-event` / `get-event-byId` to fetch events you
   may have missed; `update-event-byId` to mark delivered; `list-event-attempt`
   to inspect failed deliveries (Stark retries with exponential backoff).

## Rules
- Never trust an event body before signature verification passes.
- Manage subscriptions with `list-webhook`, `get-webhook-byId`, `delete-webhook-byId`.
