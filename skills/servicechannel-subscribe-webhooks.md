---
name: Subscribe to ServiceChannel webhooks
description: Register an HTTP notification subscription and signing key to receive work-order event callbacks.
api: openapi/servicechannel-service-automation-openapi-original.json
operations:
  - POSTv3_webhooks_subscribe
  - GETv3_webhooks
  - GETv3_NotificationSubscriptions_SigningKey
  - PUTv3_NotificationSubscriptions_SigningKey
---

# Subscribe to ServiceChannel webhooks

Receive real-time work-order event callbacks instead of polling.

## Auth
- OAuth2 bearer token. Base URL `https://api.servicechannel.com/v3`.

## Steps
1. **Provision a signing key** — `GET /NotificationSubscriptions/SigningKey` to
   read the current key, or `PUT /NotificationSubscriptions/SigningKey` to
   rotate it. Use it to verify inbound callback signatures.
2. **Subscribe** — `POST /webhooks/subscribe` with your HTTPS callback URL and
   the event(s) to receive. (`POST /NotificationSubscriptions/Http` is the
   notification-subscription equivalent.)
3. **List / confirm** — `GET /webhooks` to list active subscriptions and
   `GET /webhooks/{id}` to inspect one.
4. **Unsubscribe** — `POST /webhooks/unsubscribe` when done.

## Conventions & errors
- Callbacks are delivered over HTTPS; verify each payload against the signing
  key before trusting it.
- The 2023 "Webhooks 2x" release doubled the available event set (work-order
  lifecycle, notes, and status families).
- Standard `ErrorResponse` envelope; respect `429` + `Retry-After`.
