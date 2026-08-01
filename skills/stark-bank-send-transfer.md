---
name: Send money with a Stark Bank Transfer
description: Pay out via Pix or TED and confirm the transfer settled.
api: openapi/stark-bank-openapi-original.yml
operations: [create-transfer, get-transfer-byId, get-transfer-byId-pdf, list-transfer-log, list-balance]
---

# Send money with a Stark Bank Transfer

Move funds out of your Stark Bank balance to any bank account over Pix (seconds,
24/7) or TED (D+1 banking window). Prefer Pix when the recipient can accept it.

## Auth
ECDSA request signing (SDK-handled). Test against `sandbox.api.starkbank.com`.

## Steps
1. **Check funds** — `list-balance` to confirm the available balance covers the
   amount (integer cents).
2. **Create the transfer** — `create-transfer` with `amount`, recipient `name`,
   `taxId`, `bankCode`, `branchCode`, `accountNumber` and `accountType`. Set an
   `externalId` for safe retries. Pix vs TED is inferred from the recipient / rails.
3. **Confirm settlement** — subscribe a webhook on `transfer`, or poll
   `get-transfer-byId` until `status` == `success`. Use `list-transfer-log` for the
   full state history and `get-transfer-byId-pdf` for the receipt.

## Rules
- Amounts are integers in cents (BRL).
- Idempotency: reusing an `externalId` returns the original transfer.
- Large or rule-governed payouts can be routed through `create-paymentRequest`
  for Web Banking approval instead of executing immediately.
