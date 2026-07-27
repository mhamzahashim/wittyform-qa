# WittyForm QA Daily Quiz

A lightweight static website for running a daily manual QA quiz against the main WittyForm website.

Live GitHub Pages URL:

<https://mhamzahashim.github.io/wittyform-qa/>

Production QA target:

<https://wittyform.com>

## Purpose

This repository gives non-technical testers a simple date-separated QA quiz for WittyForm's most important smoke and regression journeys. Testers answer each daily question with **Pass**, **Fail**, or **Skip**, add notes for failures/setup blockers, then copy or download a daily report.

The page intentionally has no backend and no build step. It is designed to be safe for GitHub Pages and browser-only use.

## How the checklist was derived

The quiz was built after inspecting the WittyForm source repository rather than relying on the README. The audit covered:

- `app/` dashboard, public, auth, and API route structure
- form and quiz builders
- supported field and quiz block types
- public form and quiz rendering/submission flows
- responses, analytics, export, and dashboard data hooks
- authentication, workspaces, team roles, and invitations
- publishing, sharing, embeds, QR, custom slugs, and public access controls
- conditional logic, answer piping, calculations, outcomes, certificates, leaderboards, CRM, ecommerce, offline, translations, integrations, webhooks, branding, white-label, custom domains, billing/plan gates, environment variables, and existing tests

See [`FEATURE_AUDIT.md`](FEATURE_AUDIT.md) for the source-backed inventory and checklist mapping.

## How to edit or add questions

The site is data-driven. Open [`index.html`](index.html) and edit the `CHECKLIST_SECTIONS` JavaScript array.

Each question object supports:

- `id` — stable unique ID used for localStorage
- `title` — short tester-facing question title
- `why` — why the check matters
- `link` — stable WittyForm URL when known
- `data` — required test data
- `setup` — setup warning or external integration note
- `cleanup` — cleanup instructions for destructive tests
- `steps` — numbered manual steps
- `expected` — clear passing result

Do not change a question `id` unless you intentionally want to reset stored browser progress for that question.

## Daily browser storage

Progress is saved in `localStorage` per calendar date using keys like:

```text
wittyform-main-qa:v1:results:YYYY-MM-DD
```

Collapsed section state is saved separately:

```text
wittyform-main-qa:v1:collapsed
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

For the public repo `mhamzahashim/wittyform-qa`, GitHub Pages serves the static files from the `main` branch root.

Typical first-time commands:

```bash
git init -b main
git add index.html README.md FEATURE_AUDIT.md
git commit -m "Create WittyForm QA daily quiz"
gh repo create mhamzahashim/wittyform-qa --public --source=. --remote=origin --push
gh api -X POST repos/mhamzahashim/wittyform-qa/pages -f source[branch]=main -f source[path]=/
```

If Pages is already enabled, update it with:

```bash
gh api -X PUT repos/mhamzahashim/wittyform-qa/pages -f source[branch]=main -f source[path]=/
```

## Safety notes for production QA

Use `https://wittyform.com` with a dedicated production QA account and clearly named test data only.

Do **not**:

- use real customer accounts, emails, forms, responses, or workspaces
- make live purchases or use live payment cards
- send test data to real customer integrations or production customer webhooks
- expose passwords, API keys, tokens, or customer information in notes or reports

For potentially destructive checks, follow the cleanup instructions in the quiz and keep names prefixed with `QA` or `Daily Smoke`.

## Known setup requirements

Some WittyForm features are implemented but require specific accounts, plan tiers, or external services before manual QA can fully exercise them:

- AI generation and translation: AI provider keys and quota
- Team invitations: paid plan plus second verified tester email
- Email notifications/double opt-in: email provider/Resend/SMTP setup and tester-owned inboxes
- Webhooks: Pro/eligible account and safe RequestBin/webhook.site endpoint
- Slack, Google Sheets, Google Drive, CalendarJet, Typeform: OAuth/API setup using QA-owned resources
- Payments/orders: Stripe test-mode keys only
- CAPTCHA: reCAPTCHA or hCaptcha site/secret keys
- Meta Pixel/CAPI and Google Ads: ad platform test configuration
- White-label/custom domains: Enterprise/eligible plan plus DNS/Cloudflare/Dokploy access
- Offline mode and A/B testing: Enterprise/Howdy or eligible plan
- CRM/products/orders/API keys: paid-plan gates as defined in WittyForm plan limits

## Intentionally excluded or marked unavailable

The quiz does not instruct testers to verify features that could not be confirmed as available in the code, or that are explicitly coming soon/hidden:

- HubSpot form creation button: inspected code shows a “Coming Soon” toast.
- Jotform, SurveyMonkey, Wufoo, Microsoft Forms, MakeForms, and Tally imports: import page marks them `coming_soon`.
- Internal screen builder editor: hidden behind `NEXT_PUBLIC_INTERNAL_SCREEN_BUILDER_EDITOR=false` in the environment example.
- Real purchases or live payment cards: payment checks require Stripe test mode only.
- Real customer emails, customer OAuth workspaces, or production customer webhook endpoints.
