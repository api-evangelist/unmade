---
name: Embed the Unmade Editor and create an order from a saved design
description: The core 4-step Unmade integration — embed the customisation editor,
  capture the Design ID, create an ecommerce order after checkout, then track it.
api: openapi/unmade-embed-v2-openapi.json
operations: [getProducts, getDesigns, createOrders, getOrders2, ordersReleaseHold]
generated: '2026-07-21'
method: generated
---

# Embed the Unmade Editor and create an order

Auth: every request needs `Authorization: Token <token>` (token issued by Unmade).
Base URL is your partner-specific subdomain `https://{partner}.embed.unmade.com/`.
Every endpoint URL must end with a trailing slash — a missing slash surfaces as an
unexpected 401.

1. **Embed the editor** — render an iframe at the `getProducts` endpoint
   (`GET /v2/products/{slug}/`, optional `price`, `ccy`, `locale`, `design_id`,
   `init` params). For user asset uploads, pass an HS512 JWT signed with your
   shared key as `init` (payload: a non-PII `user` UUID).
2. **Capture the Design ID** — listen for the `window.postMessage` design-saved
   event from the iframe; its payload matches the `getDesigns` response. Persist
   the Design ID through your native checkout.
3. **Verify the design** — `getDesigns` (`GET /v2/designs/{design_id}/`) returns
   the saved design with rendered preview URLs.
4. **Create the order** — after purchase, call `createOrders`
   (`POST /v2/orders/`) with the Design ID and order details. Optionally set
   `is_test_order: true` for integration testing (ask Unmade about recommended
   test procedures).
5. **Track it** — `getOrders2` (`GET /v2/orders/{order_id}/`) returns state,
   `tracking_data`, and the linked Factory API `manufacturing_order`. If the
   order is on hold, `ordersReleaseHold` (`POST /v2/orders/{order_id}/release_hold/`)
   starts production.

Errors use a `{code, message, detail}` envelope — branch on HTTP status and
`code` (e.g. `authentication_failed`, `not_found`, `throttle_request` on 429),
never on `message` text. Lists paginate DRF-style (`?page=`, envelope
`count/next/previous/results`, 10 per page). No idempotency keys exist: retry
`createOrders` only after checking `getOrders` for an existing order with your
`reference`.
