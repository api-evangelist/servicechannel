---
name: Create a ServiceChannel work order
description: Authenticate and create a maintenance work order for a location, then track its status.
api: openapi/servicechannel-service-automation-openapi-original.json
operations:
  - POSTv3_workorders
  - GETv3_workorders
  - PUTv3_workorders_{workorderId}_status
  - POSTv3_workorders_{workorderId}_resolution
---

# Create a ServiceChannel work order

Use the Service Automation API (v3) to open a maintenance work order at a
location and track it to completion.

## Auth
- OAuth2 authorization-code flow. Obtain a bearer token from
  `https://login.servicechannel.com/oauth/token` (sandbox:
  `https://sb2login.servicechannel.com/oauth/token`).
- Send `Authorization: Bearer <token>` on every call.
- Base URL: `https://api.servicechannel.com/v3` (sandbox
  `https://sb2api.servicechannel.com/v3`).

## Steps
1. **Create the work order** — `POST /workorders` (`POSTv3_workorders`) with the
   location, trade, category, priority, NTE, and problem description in the JSON
   body. A `201 Created` returns the work order id / tracking number.
2. **Confirm / search** — `GET /workorders` (`GETv3_workorders`) filtered by
   `locationId`/`storeId` to verify the order and read its current status. Use
   `page` / `pageSize` to page results.
3. **Advance status** — `PUT /workorders/{workorderId}/status`
   (`PUTv3_workorders_{workorderId}_status`) to move the order through its
   lifecycle.
4. **Resolve** — `POST /workorders/{workorderId}/resolution`
   (`POSTv3_workorders_{workorderId}_resolution`) to record the resolution code
   and root cause on completion.

## Conventions & errors
- Errors return the `ErrorResponse` envelope: `ErrorCodes[]`, `ErrorCode`,
  `ErrorMessage`. Common codes: `504` (call rejected by security permissions),
  `502` (work order not found), `503` (request data is empty), `904` (invalid
  work order status), `921/924` (work order already invoiced — cannot change).
- Honor `429 Too Many Requests` by waiting for the `Retry-After` header.
- No idempotency key: do not blindly retry a `POST /workorders`; search first to
  avoid duplicates (error `9100` flags duplicate PO numbers).
