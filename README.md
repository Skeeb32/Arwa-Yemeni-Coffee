# Arwa Yemeni Coffee

One-page Chicago coffeehouse website with the original hero artwork, atmospheric motion, four hover/tap drink previews, a shopping bag, pickup/delivery totals, and an interactive order tracker.

**Ordering is currently a preview.** Real payments require the business's approved menu prices, delivery fee/service area, Stripe configuration, and a staffed fulfillment process. See [ORDERING-SETUP.md](ORDERING-SETUP.md). No prices or delivery fee were invented. The convenience fee is $2; pickup has no delivery charge.

## Stack and commands

Semantic HTML, CSS, vanilla JavaScript, and a Cloudflare-compatible ESM Worker. No package installation or external runtime dependencies. Node.js 22+:

```sh
npm run dev   # http://127.0.0.1:4187
npm test      # commerce validation tests
npm run build
```

`public/` contains the website and optimized artwork. `server/commerce.mjs` holds server-only Stripe checkout, order authorization, webhook verification and staff status APIs. `scripts/build.mjs` emits `dist/server/index.js` with embedded assets. `.openai/hosting.json` retains the existing Sites identity. Deploy the built Worker through Sites.

## Experience

- Pointer hover and keyboard focus preview drinks on desktop; tap expands images on mobile.
- Draft bag quantities persist on this device. It is not a real order until paid and verified by the server.
- Fees and prices come from server configuration. Unknown amounts display as pending, never zero-priced products.
- Stripe-hosted checkout keeps card information off this site.
- Staff manually advance paid orders through `/staff.html`; customers see real status updates.
- Tracker demo is clearly labeled and never submits an order.
- Notifications are on-page, plus opt-in browser notifications while the page is open. No SMS or background push.
- Motion respects reduced-motion settings and has a pause control.

## Media and sources

The original hero image is unchanged. Four generated drink previews are explicitly illustrative serving suggestions, not verified product photography. Prompts are in `MENU-MEDIA-PROMPTS.json` and `MEDIA-PROMPTS.txt`. Verified business details and exact review excerpts are documented in `SOURCES.md`.

The requested generated cinematic video remains unavailable. The site uses the original custom still image with parallax and light motion. Source repository: https://github.com/Skeeb32/Arwa-Yemeni-Coffee.

## Deployment

https://arwa-yemeni-coffee-chicago.shaqib-dev.chatgpt.site

The site retains owner-private access. Stripe end-to-end payment testing and public customer launch remain pending configuration.
