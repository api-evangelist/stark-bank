---
name: Receive a Pix payment with a Stark Bank Invoice
description: Issue a Pix Invoice (dynamic QR code), share it, and confirm the money landed.
api: openapi/stark-bank-openapi-original.yml
operations: [create-invoice, get-invoice-byId, get-invoice-byId-qrcode, get-invoice-byId-payment, list-invoice-log]
---

# Receive a Pix payment with a Stark Bank Invoice

Use this to charge a customer over Pix and confirm settlement. Received funds land
directly in your Stark Bank account balance — there is no PSP wallet or payout step.

## Auth
Sign every request with your ECDSA private key (secp256k1 / SHA-256). The official
SDKs add the `Access-Id`, `Access-Time` and `Access-Signature` headers automatically.
Use `https://sandbox.api.starkbank.com` while testing.

## Steps
1. **Create the invoice** — `create-invoice` with `amount` (in cents), `taxId`
   (payer CPF/CNPJ) and `name`. Set an `externalId` so retries never duplicate the
   charge. Optionally set `due`, `expiration`, `fine`, `interest`, `tags`.
2. **Share the QR / BR Code** — read `brcode` from the create response, or call
   `get-invoice-byId-qrcode` for the QR image and `get-invoice-byId` for the link.
3. **Confirm payment** — prefer a webhook subscription on `invoice` (see the
   webhook skill) over polling. To reconcile on demand, call
   `get-invoice-byId-payment` for payer details or `list-invoice-log` /
   `get-invoice-byId` and check `status` == `paid`.

## Rules
- Amounts are integers in cents (BRL).
- Idempotency: the same `externalId` returns the original invoice, so retry freely.
- Errors come back as `{ "errors": [ { "code", "message" } ] }` with HTTP 400.
