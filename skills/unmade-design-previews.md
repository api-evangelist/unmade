---
name: Render design previews across products
description: Retrieve a saved design, transfer-preview it onto other products for
  cross-sell imagery, and show its 3D view.
api: openapi/unmade-embed-v2-openapi.json
operations: [getDesigns, transferPreviews, getDesigns3d]
generated: '2026-07-21'
method: generated
---

# Render design previews across products

Auth: `Authorization: Token <token>` on your partner subdomain
`https://{partner}.embed.unmade.com/`; end every URL with a trailing slash.

1. **Retrieve the design** — `getDesigns` (`GET /v2/designs/{design_id}/`)
   returns the saved design and its rendered preview image URLs.
2. **Transfer previews** — `transferPreviews` (`POST /v2/transfer_previews/`)
   with the design ID and target product slug returns preview objects with
   full-resolution and ~400px thumbnail URLs, rendered on demand. The first
   request can take ~5 seconds — show a placeholder/loading state.
3. **3D view** — `getDesigns3d`
   (`GET /v2/designs/{design_id}/3d/?scene=&autosize=&interactive=&autorotate=`)
   serves the interactive 3D visualisation of the design.

Handle the `{code, message, detail}` error envelope; a 429 with
`throttle_request` means back off and retry.
