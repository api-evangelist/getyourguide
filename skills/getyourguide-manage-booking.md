---
name: Retrieve and cancel a booking
description: Look up an existing GetYourGuide booking by its hash and cancel it when needed.
api: openapi/getyourguide-partner-openapi.yml
operations: [BookingsGetByHash, bookingsDeleteByHash, CartsGetByHash]
---

# Retrieve and cancel a booking

## Auth
- Send `X-ACCESS-TOKEN: <your access token>` on every request.
- Base URL: `https://api.getyourguide.com` (production) or `https://api.gygtest.net` (testing).
- Paths carry a `{version}` path parameter (use `1`).

## Steps
1. **Get a booking** — `GET /{version}/bookings/{booking_hash}` (`BookingsGetByHash`). Pass `currency` and `cnt_language` for localized output.
2. **Check a cart** (optional) — `GET /{version}/carts/{shopping_cart_hash}` (`CartsGetByHash`) to inspect a cart before/after confirmation.
3. **Cancel a booking** — `DELETE /{version}/bookings/{booking_hash}` (`bookingsDeleteByHash`).

## Rules
- The `booking_hash` is the server-issued identifier returned when the booking was created; treat it as opaque.
- Errors use the custom envelope (`errors[].errorCode` + `errorMessage`); a 4XX means bad request, invalid token, or not found. See `errors/getyourguide-problem-types.yml`.
- Cancellation eligibility depends on the tour's cancellation policy — a `DELETE` may return an error if the booking is non-refundable or past its cancellation window.
