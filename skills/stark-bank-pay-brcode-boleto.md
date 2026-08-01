---
name: Pay a Pix BR Code or Boleto with Stark Bank
description: Preview and settle an outbound Pix QR code or boleto payment.
api: openapi/stark-bank-openapi-original.yml
operations: [create-paymentPreview, create-brcodePayment, get-brcodePayment-byId, create-boletoPayment, get-boletoPayment-byId, list-boletoPayment-log]
---

# Pay a Pix BR Code or Boleto with Stark Bank

Settle bills your business owes — a Pix QR code (BR Code) or a Brazilian boleto.

## Auth
ECDSA request signing (SDK-handled). Use the sandbox host to dry-run.

## Steps
1. **Preview (optional)** — `create-paymentPreview` with the `brcode` or boleto
   `line`/`barCode` to resolve the amount, due date and beneficiary before paying.
2. **Pay**
   - Pix QR code: `create-brcodePayment` with `brcode`, `taxId`, a `description`
     and an `externalId`.
   - Boleto: `create-boletoPayment` with the boleto `line` (or `barCode`),
     `taxId`, `description` and an `externalId`.
3. **Confirm** — `get-brcodePayment-byId` / `get-boletoPayment-byId` until
   `status` == `success`; `list-boletoPayment-log` for the state history.

## Rules
- Amounts are integers in cents (BRL); boleto amount may be fixed by the slip.
- Idempotency: reusing an `externalId` returns the original payment.
- Pix settles in seconds 24/7; boleto follows D+1 banking windows.
