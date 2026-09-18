# Universal Data Layer Converter (Web) — by MD Niamul

A Google Tag Manager **Variable Template** for the **Web (browser) container**.
It reads whatever structure your site's `dataLayer` already uses — GA4-style,
Universal Analytics-style, a custom e-commerce schema, a lead-gen form, or
anything else — and converts it into the exact payload shape a specific ad
or analytics platform expects, so you stop hand-writing a Custom JavaScript
variable per platform per client.

No value is ever invented. A field that isn't present in the source stays
empty, or is returned as a placeholder you configure.

For the **server-side (sGTM)** counterpart — which additionally SHA-256
hashes personal data (email, phone, name, address) before it leaves your
server container — see
[Universal Data Layer Converter (Server)](https://github.com/mdniamul0/gtm-universal-datalayer-converter-server).

## What it does

1. Reads your `dataLayer` (in "Auto" mode: a built-in list of common keys —
   `event`, `ecommerce`, `items`, `user_data`, `customer`, `order`,
   `transaction`, `page`, `cart`, `product`, `value`, `currency`,
   `transaction_id` — plus any extra top-level keys you add), or reads a
   single object from another variable ("Variable" mode).
2. Auto-detects the canonical fields inside that object regardless of the
   exact key names your site uses (`orderId`, `order_id`, `transactionId`,
   `ORDER-ID`, … all resolve to the same canonical `transaction_id`), using
   a large alias table and a shallowest-match resolution strategy.
3. Formats the result for the platform you choose: GA4, Google Ads, Meta
   (Facebook) Pixel/CAPI, TikTok, LinkedIn, Snapchat, Pinterest, Microsoft
   Advertising (Bing UET), X (Twitter), Reddit, Quora, Outbrain, Taboola, a
   generic structure, or Stape's server-side pass-through shape — or leave
   it as a platform-independent "Normalized" object.
4. Optionally lets you override any single field with a manual path when
   auto-detection picks the wrong value.

## Parameters

| Parameter | Purpose |
|---|---|
| Data source | Read the data layer automatically, or point at another variable that already returns an object. |
| Extra data layer keys to read | Additional top-level `dataLayer` keys beyond the built-in list (Auto mode only). |
| Output format (platform) | Which platform's payload shape to produce. |
| Return | Full object, just the event name, a single field by path, or the object as a JSON string. |
| Field path | Dot-notation path used with "Single field" return mode. |
| When a value is not found | Omit the key, return an empty string, or return a placeholder — the template never invents data. |
| Advanced → Manual mapping | Per-field overrides (`field` → source `path`) that win over automatic detection. |
| Advanced → Calculate value from items | If no top-level order value is found, sum `price × quantity` across items and mark `value_source: 'calculated_from_items'`. |
| Advanced → Enable debug logging | Logs the platform, the normalized model, and the output via `logToConsole` (scoped to the GTM debug/preview environment only). |
| Action source / Event conversion type | Passed through to platforms that need them (Meta `action_source`, Snapchat `event_conversion_type`). |

## Security & privacy notes

- **This Web variant never hashes personal data.** Any email, phone, or
  name value it maps is passed through unchanged, because hashing PII in
  the browser defeats the purpose (the raw value has already reached the
  page). Hash server-side with the
  [Server variant](https://github.com/mdniamul0/gtm-universal-datalayer-converter-server),
  or let the receiving pixel/tag hash it.
- **Permission:** this template requests `read_data_layer` with a wildcard
  key pattern (`*`). This is required because, in Auto mode, it reads a
  fixed list of common keys *plus* any additional key names you type into
  "Extra data layer keys to read" at tag-configuration time — those names
  aren't known when the permission is granted, so the pattern can't be
  narrowed further. The template only reads the specific keys in that
  combined list; it never scans or dumps the entire data layer.
- **Permission:** `logging`, scoped to the `debug` environment only —
  nothing is logged in a live/production container.

## Testing

The template ships with unit tests under `___TESTS___` (run automatically
by Google's template validator) covering: GA4→Meta mapping, Universal
Analytics→GA4 mapping, a fully custom schema→TikTok, single-field output,
placeholder handling, empty-data-layer behavior, and numeric-ID-to-string
coercion. All scenarios pass. The template was additionally verified with
an independent Node.js test harness that mocks GTM's sandboxed JS APIs and
exercises adversarial edge cases (ambiguous ID disambiguation, calculated
value, manual-mapping overrides, numeric type coercion) beyond the built-in
suite.

## Installation

1. In your GTM Web container, go to **Templates → Variable Templates →
   Search Gallery**, and search for "Universal Data Layer Converter by MD
   Niamul" (once approved), **or**
2. Import `template.tpl` manually: **Templates → New → ⋮ → Import**.
3. Create a new variable from the template, choose your platform and
   options, and use it wherever you'd use a Custom JavaScript variable.

## About the author

Built by **MD Niamul**, founder of Digital Soldier Agency — a conversion
tracking, server-side tagging (sGTM), and web analytics specialist. 10×
Meta Blueprint Certified, 8× Google Ads & Microsoft Ads Certified, HubSpot
Certified Inbound Marketer, and an official Stape partner.

- Website: https://mdniamul.com
- LinkedIn: https://www.linkedin.com/in/mdniamul/

## License

Apache License 2.0 — see [LICENSE](./LICENSE).
