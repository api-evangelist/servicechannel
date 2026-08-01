---
name: Review and approve a ServiceChannel invoice
description: Retrieve an invoice for a work order, check discrepancies, and approve, reject, or hold it.
api: openapi/servicechannel-service-automation-openapi-original.json
operations:
  - GETv3_invoices_{invoiceId}
  - GETv3_invoices_{invoiceId}_discrepancy
  - PUTv3_invoices_{invoiceId}_approve
  - PUTv3_invoices_{invoiceId}_reject
  - PUTv3_invoices_{invoiceId}_onhold
---

# Review and approve a ServiceChannel invoice

## Auth
- OAuth2 bearer token (see the create-work-order skill for the token flow).
- Base URL `https://api.servicechannel.com/v3`.

## Steps
1. **Get the invoice** — `GET /invoices/{invoiceId}`
   (`GETv3_invoices_{invoiceId}`).
2. **Check discrepancies** — `GET /invoices/{invoiceId}/discrepancy`
   (`GETv3_invoices_{invoiceId}_discrepancy`) to compare invoiced rates against
   the contract before deciding.
3. **Decide** — one of:
   - Approve: `PUT /invoices/{invoiceId}/approve`
     (`PUTv3_invoices_{invoiceId}_approve`) with `approvalCode`/`comments`.
   - Reject: `PUT /invoices/{invoiceId}/reject`
     (`PUTv3_invoices_{invoiceId}_reject`) with `comments`.
   - Hold: `PUT /invoices/{invoiceId}/onhold`
     (`PUTv3_invoices_{invoiceId}_onhold`) with `comments`.

## Conventions & errors
- `ErrorResponse` envelope with numeric codes. Invoice-domain codes include
  `1104` (Invoice Total > NTE), `1107` (duplicate invoice number), `1130` (user
  not assigned to the approval level), `1178` (cannot be paid before invoice
  dates), `1116` (invoicing disabled for the account).
- This flow targets non-MLI (single-level) invoices; multilevel-approval
  invoices follow the MLI configuration.
- Respect `429` + `Retry-After`.
