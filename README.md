# WittyForm QA Daily Checklist

A lightweight static website for running a daily manual QA checklist against WittyForm staging.

Expected GitHub Pages URL after publication:

<https://mhamzahashim.github.io/wittyform-qa/>

## Purpose

This repository gives non-technical testers a date-separated checklist for WittyForm's most important manual smoke and regression journeys. It is designed for daily QA on `https://beta.wittyform.com` and intentionally avoids a backend or build step.

## How the checklist was derived

The checklist was built after inspecting the WittyForm source repository rather than relying on the README. The audit covered:

- `app/` dashboard, public, auth, and API route structure
- form and quiz builders
- supported field and quiz block types
- public form and quiz rendering/submission flows
- responses, analytics, export, and dashboard data hooks
- authentication, workspaces, team roles, and invitations
- publishing, sharing, embeds, QR, custom slugs, and public access controls
- conditional logic, answer piping, calculations, outcomes, certificates, leaderboards, CRM, ecommerce, offline, translations, integrations, webhooks, branding, white-label, custom domains, billing/plan gates, environment variables, and existing tests

See [`FEATURE_AUDIT.md`](FEATURE_AUDIT.md) for the source-backed inventory and checklist mapping.

## How to edit or add tests

The site is data-driven. Open [`index.html`](index.html) and edit the `CHECKLIST_SECTIONS` JavaScript array.

Each test object supports:

- `id` — stable unique ID used for localStorage
- `title` — short tester-facing title
- `why` — why the test matters
- `link` — stable WittyForm URL when known
- `data` — required test data
- `setup` — setup warning or external integration note
- `cleanup` — cleanup instructions for destructive tests
- `steps` — numbered manual steps
- `expected` — clear expected result

Do not change a test `id` unless you intentionally want to reset stored browser progress for that test.

## Daily browser storage

Progress is saved in `localStorage` per calendar date using keys like:

```text
wittyform-qa:v1:results:YYYY-MM-DD
```

Collapsed section state is saved separately:

```text
wittyform-qa:v1:collapsed
```

The page restores the selected date's Pass/Fail/Skip results and notes after refresh. Choosing a different date loads a separate storage record.

## Run locally

No install is required. From this repository:

```bash
python3 -m http.server 8080
```

Then open:

```text
http://127.0.0.1:8080/
```

## GitHub Pages deployment

For a public repo named `mhamzahashim/wittyform-qa`, GitHub Pages can serve the static files from the `main` branch root.

Typical commands:

```bash
git init -b main
git add index.html README.md FEATURE_AUDIT.md
git commit -m "Create WittyForm QA daily checklist"
gh repo create mhamzahashim/wittyform-qa --public --source=. --remote=origin --push
gh api -X POST repos/mhamzahashim/wittyform-qa/pages -f source[branch]=main -f source[path]=/
```

If Pages is already enabled, update it with:

```bash
gh api -X PUT repos/mhamzahashim/wittyform-qa/pages -f source[branch]=main -f source[path]=/
```

## Known setup requirements

Some WittyForm features are implemented but require specific accounts, plan tiers, or external services before manual QA can fully exercise them:

- AI generation and translation: AI provider keys and quota
- Team invitations: paid plan plus second verified tester email
- Email notifications/double opt-in: email provider/Resend/SMTP setup
- Webhooks: Pro/eligible account and safe RequestBin/webhook.site endpoint
- Slack, Google Sheets, Google Drive, CalendarJet, Typeform: OAuth/API setup
- Payments/orders: Stripe test-mode keys only
- CAPTCHA: reCAPTCHA or hCaptcha site/secret keys
- Meta Pixel/CAPI and Google Ads: ad platform test configuration
- White-label/custom domains: Enterprise/eligible plan plus DNS/Cloudflare/Dokploy access
- Offline mode and A/B testing: Enterprise/Howdy or eligible plan
- CRM/products/orders/API keys: paid-plan gates as defined in WittyForm plan limits

## Intentionally excluded or marked unavailable

The checklist does not instruct testers to verify features that could not be confirmed as available in the code, or that are explicitly coming soon/hidden:

- HubSpot form creation button: inspected code shows a “Coming Soon” toast.
- Jotform, SurveyMonkey, Wufoo, Microsoft Forms, MakeForms, and Tally imports: import page marks them `coming_soon`.
- Internal screen builder editor: hidden behind `NEXT_PUBLIC_INTERNAL_SCREEN_BUILDER_EDITOR=false` in the environment example.
- Real purchases or live payment cards: payment tests require Stripe test mode only.
- Real customer emails, customer OAuth workspaces, or production webhook endpoints.
