---
name: Search a tour and create a booking
description: Search the GetYourGuide marketplace for a tour, inspect its options and availability, then create and confirm a booking via the two-step cart flow.
api: openapi/getyourguide-partner-openapi.yml
operations: [ToursQuery, ToursByIdQuery, ToursByIdOptions, tours-by-id-availability, tours-by-id-price-breakdown, BookingsCreate, CartsConfirm]
---

# Search a tour and create a booking

Use the GetYourGuide Partner API to find a tour and book it.

## Auth
- Send `X-ACCESS-TOKEN: <your access token>` on every request.
- Base URL: `https://api.getyourguide.com` (production) or `https://api.gygtest.net` (testing).
- All paths carry a `{version}` path parameter (use `1`).

## Steps
1. **Search tours** — `GET /{version}/tours` (`ToursQuery`). Pass `limit`/`offset` to page; read `_metadata.totalCount` for the total. Supply `currency` and `cnt_language` as needed.
2. **Inspect the tour** — `GET /{version}/tours/{tour_id}` (`ToursByIdQuery`).
3. **List options** — `GET /{version}/tours/{tour_id}/options` (`ToursByIdOptions`) to pick a bookable option.
4. **Check availability** — `GET /{version}/tours/{tour_id}/availability` (`tours-by-id-availability`).
5. **Get price** (optional) — `POST /{version}/tours/{tour_id}/price-breakdown` (`tours-by-id-price-breakdown`).
6. **Create booking / cart** — `POST /{version}/bookings` (`BookingsCreate`). Send either `bookable` or `coupon` in the body. The response returns a `shopping_cart_hash`.
7. **Confirm the cart** — `POST /{version}/carts` (`CartsConfirm`) with the `shopping_cart_hash` to finalize.

## Rules
- There is **no idempotency key**. Safety comes from the two-step cart/confirm model — do not retry `BookingsCreate` blindly; if unsure, `GET /{version}/carts/{shopping_cart_hash}` (`CartsGetByHash`) to check state before confirming.
- Errors return a custom envelope: `errors[].errorCode` (integer) + `errorMessage`. `errorCode: 25` means the access token is invalid. See `errors/getyourguide-problem-types.yml`.
- Test against `https://api.gygtest.net` with a test token before going live.
