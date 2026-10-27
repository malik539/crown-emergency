# Crown Dental Care Rockville – Emergency Dental Treatment (PPC landing page)

A single-file, dependency-free landing page for Google Ads traffic targeting
**Emergency Dental Treatment in Rockville, MD**.

```
index.html            # the page (inline CSS + a small inline script, no external JS/CSS/fonts)
assets/img/           # optimized WebP images + favicons
```

Open `index.html` directly or serve the folder with any static host.

## Page structure (6 sections + header + mobile sticky CTA)

1. Urgency bar + compact header (logo, phone, Call Now)
2. Hero + emergency form (55/45 desktop split) + trust strip
3. Emergency Dental Services We Provide (7 approved services in a card grid)
4. See Real Patient Reviews – Google 4.8 (310 reviews) badge + an auto-scrolling ticker of 10 real Google reviews (CSS transform animation, pauses on hover/focus, has a Pause button, pauses while a review is expanded, stops when off-screen, and becomes a static swipeable row under `prefers-reduced-motion`)
5. Meet Dr. Thy Nguyen + Why Trust Us badges
6. Emergency Dentist in Rockville, MD / Can I Wait Until I See a Dentist?
7. Final CTA + financing statement + responsive Google Map

CTAs appear in the hero, after services, in the doctor section, in the urgency callout,
in the final section and in the mobile sticky bar. Every phone link uses `tel:2408513012`.

## Required setup before going live

All integration settings live in one block at the top of `index.html`:

```js
window.CDC_CONFIG = {
  gtmId: "",            // e.g. "GTM-XXXXXXX"
  formEndpoint: "",     // URL that accepts a JSON POST (GHL / LeadConnector inbound webhook, CRM, Zapier…)
  formTag: "Emergency Dental - Rockville PPC"
};
```

* **gtmId** – when set, Google Tag Manager is loaded (script + noscript). Google Ads
  conversions, GA4 and any pixels should be configured inside that container.
* **formEndpoint** – the form POSTs JSON to this URL. Until it is set, submitting shows an
  inline error asking the visitor to call, so leads are never silently lost.

### Form payload

```json
{
  "first_name": "", "last_name": "", "phone": "", "email": "", "message": "",
  "consent": true,
  "consent_text": "I agree to terms & conditions provided by the company. By providing my phone number, I agree to receive text messages from the business.",
  "source": "Emergency Dental Treatment – Rockville PPC Landing Page",
  "tags": "Emergency Dental - Rockville PPC",
  "gclid": "…", "utm_source": "…", "utm_medium": "…", "utm_campaign": "…",
  "page_url": "…", "referrer": "…"
}
```

`gclid`, `gbraid`, `wbraid` and `utm_*` are included only when present in the page URL.
A hidden honeypot field (`website`) is used for spam filtering; submissions that fill it are dropped client-side.

If the practice prefers to keep its existing GoHighLevel form widget, replace the `<form>`
inside `#emergency-form` with the GHL embed iframe. The surrounding card, heading and
success/error styling can stay.

### dataLayer events (for GTM triggers)

| Event                    | When                                   | Extra keys                      |
|--------------------------|----------------------------------------|---------------------------------|
| `phone_click`            | any `tel:` link / button is clicked    | `cta_location`, `link_url`      |
| `form_cta_click`         | a "Get Emergency Help Now" anchor click| `cta_location`                  |
| `form_submit_click`      | the form's submit button is clicked    |                                 |
| `emergency_form_submit`  | the endpoint returned 2xx (lead sent)  | `form_name`                     |
| `emergency_form_error`   | validation / no endpoint / network     | `reason`                        |

Recommended Google Ads conversions: `phone_click` (primary) and `emergency_form_submit` (secondary).

## Content sources

* Copy, services, reviews, doctor bio, trust points and financing statement come from the
  approved content document (`Crown Dental Care - Emergency Dental Treatment`).
* The Google 4.8 rating, 310-review count and the 5-star rows come from the verified Google Reviews screenshots supplied by the client. The two reviews that appear only in the approved content document (Kathy Yerovi, patricia cleavenger) are shown without a star row but keep their link to the original Google review.
* Review text is reproduced verbatim, including the reviewers' own spelling.
* Images: supplied logo and Dr. Thy Nguyen photo only. No stock imagery.
* Privacy policy: https://dentistinrockville.com/privacy-policy/

## QA checklist (verified with Playwright at 390px, 820px and 1366px)

- One `<h1>`, logical H2/H3 hierarchy, labelled form fields, visible focus states, skip link
- Phone number `240-851-3012` everywhere; all 14 call links use `tel:2408513012`
- No horizontal overflow at any breakpoint; all tap targets ≥ 44px
- Form: required-field validation, consent required, error state, success state, honeypot
- Mobile sticky CTA appears after scrolling, hides while the form is on screen or focused,
  respects `env(safe-area-inset-bottom)`
- Map iframe is responsive (4:3, 100% width) and unchanged in destination
- `prefers-reduced-motion` disables the ticker, reveal and image fade-in animations
- Subtle "lazy" effects: sections fade/slide in as they enter the viewport (IntersectionObserver, one-time), service cards stagger, and lazy-loaded images fade in once decoded. All effects are opacity/transform only (compositor-friendly, no layout shift).
