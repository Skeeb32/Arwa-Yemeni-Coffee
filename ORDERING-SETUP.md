# Ordering activation

The deployed interface is deliberately in preview mode. No real charge or order is accepted until the required server configuration is supplied. This is not a completed Stripe onboarding or production-tested payment integration.

## Required business inputs

- Approved prices in integer USD cents for `sand-dune`, `adeni`, `matcha`, and `biscoff`.
- Delivery fee and supported ZIP codes (the delivery area is postal-code based, not a driving-radius or live GPS service).
- Staff responsible for receiving orders, marking preparation and completion, and arranging delivery.
- The business's Stripe account, configured Stripe Tax registration/product tax treatment, receipts, refunds, and fulfillment procedures.

## Server secrets/settings

Use Sites environment settings, never client JavaScript or Git:

- `MENU_PRICES_JSON`: approved integer-cent prices keyed by the four IDs.
- `DELIVERY_FEE_CENTS`: approved nonnegative integer cents.
- `DELIVERY_ZIPS`: comma-separated ZIP codes.
- `STRIPE_SECRET_KEY`: initially the business's test-mode secret.
- `STRIPE_WEBHOOK_SECRET`: signature secret for `/api/stripe/webhook`.
- `ORDER_TOKEN_SECRET`: cryptographically random secret for private order links.
- `ORDER_ADMIN_TOKEN`: a different cryptographically random staff access key.
- `TAX_READY=true`: only after checking Stripe Tax settings and sample pickup/delivery tax calculations.
- `FULFILLMENT_READY=true`: only after the shop has agreed to monitor paid orders and arrange delivery.
- `ORDERING_ENABLED=true`: final activation switch after sandbox end-to-end validation.

The fixed convenience fee is $2 once per nonempty order. Pickup has zero delivery fee. All totals are recomputed on the server; client prices are ignored. Stripe calculates tax before payment. Delivery name, address, city, state and ZIP are required; approved ZIPs are checked server-side. Delivery shipping details are attached to the Stripe customer for tax calculation and to the order metadata for staff. Pickup uses the business address as fulfillment location. Validate these tax treatments with the actual Stripe account before enabling live payments.

## Fulfillment

Stripe is the durable payment and order record; no browser draft is authoritative. Staff open `/staff.html` and enter their private access key. The key stays in memory, never browser storage. The order desk shows paid Arwa orders within the latest 50 Stripe Checkout Sessions. Older orders remain in Stripe Dashboard. Staff view the purchased line items in the Stripe session, arrange delivery, and advance the next status. Status changes are stored on the Stripe Checkout Session. This is a basic manual order desk, not an integrated POS or courier system; concurrent staff operations should be coordinated.

Delivery: Order received → Making your order → Ready for delivery → On the way → Arrived.
Pickup: Order received → Making your order → Ready for pickup → Picked up.

The customer tracker retrieves verified payment/status from the server every 15 seconds while the tracker is open and the page visible. It never simulates real progress. The customer must keep/bookmark the private return URL to revisit their order. Do not share that link. No personal details are returned by the customer status endpoint.

## Notifications

On-page order confirmation is emitted only after Stripe reports payment paid. Optional browser notifications require the visitor's permission and work while the page remains open. The demo only shows labeled demo messages. Enable payment receipt emails in Stripe Dashboard if the business wants emailed receipts. SMS, background push, and courier GPS are not integrated.

Register `checkout.session.completed` at `/api/stripe/webhook`. The handler verifies the raw-body HMAC with a five-minute timestamp tolerance and records confirmation idempotently. Only card payments are accepted by this implementation. Fulfillment changes are staff-driven; a successful return URL alone never confirms payment.

## Validation before live activation

Use Stripe test mode to validate successful payment, declined payment, cancelled checkout, idempotent retries, webhook retries/signatures, pickup and delivery taxes, unsupported ZIPs, staff order visibility, all status transitions, order-link authorization, browser notifications, and receipt settings. Automated local tests use synthetic amounts solely as test fixtures, never displayed customer prices. Then switch credentials and webhook to live mode and explicitly enable ordering. No real payment was made during this implementation.

Official API references: https://docs.stripe.com/api/checkout/sessions/create, https://docs.stripe.com/api/checkout/sessions/retrieve, https://docs.stripe.com/api/checkout/sessions/update.
