---
name: Fulfil production orders through the Factory API
description: Work a production order from queue to shipped — list orders and jobs,
  set production states, and attach tracking numbers.
api: openapi/unmade-embed-v2-openapi.json
operations: [factoryGetOrders, factoryGetOrders2, factoryGetOrdersJobs,
  factoryOrdersSetInProduction, factoryOrdersSetReadyToShip,
  factoryOrdersSetShipped, factoryOrdersSetTrackingNumber]
generated: '2026-07-21'
method: generated
---

# Fulfil production orders through the Factory API

The Factory API lives at `https://factory.unmade.com/` with the same
`Authorization: Token <token>` header. Each ecommerce order maps to exactly one
Factory API order — the same order ID works on both APIs. End every URL with a
trailing slash.

1. **Find work** — `factoryGetOrders` (`GET /v2/orders/`) supports `state`,
   `page`, `created_before/after`, `submitted_before/after`, `ecommerce_origin`,
   and `reference` filters (paginated, 10 per page).
2. **Inspect the order** — `factoryGetOrders2` (`GET /v2/orders/{order_id}/`)
   returns the production order; `factoryGetOrdersJobs`
   (`GET /v2/orders/{order_id}/jobs`) lists its production jobs with materials
   data.
3. **Advance states** — orders progress `new` → in production → ready to ship →
   shipped. Call `factoryOrdersSetInProduction`, then
   `factoryOrdersSetReadyToShip`, then `factoryOrdersSetShipped`
   (`POST /v2/orders/{order_id}/set_*/`). Job-level equivalents exist for
   per-job control.
4. **Attach tracking** — `factoryOrdersSetTrackingNumber`
   (`POST /v2/orders/{order_id}/set_tracking_number/`) records the shipment
   tracking number surfaced back on the ecommerce order's `tracking_data`.

State-transition posts are not idempotent-keyed; check the current order state
before re-posting after a timeout. Errors use the `{code, message, detail}`
envelope.
