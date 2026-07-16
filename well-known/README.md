# App-association files (`.well-known`)

These tie the native Chef Mate apps to `chefmate.plusmobileapps.com` for two
purposes:

1. **Credential autofill** — the OS password managers offer credentials saved for
   the website inside the app (and vice-versa) during login.
2. **Deep links (App Links / Universal Links)** — tapping a
   `https://chefmate.plusmobileapps.com/…` link (e.g. the "Open Chef Mate" button
   in a collaboration-invite email) opens the app straight to the matching screen
   instead of the website. Currently mapped: `/notifications` →
   in-app Notifications; `/recipe/*` → the shared public recipe.

When the app *isn't* installed, `/recipe/<uuid>` has to render on the web
instead. That page is [`../web/404.html`](../web/404.html) — see the note there
and in the deploy workflow for why the fallback lives in `404.html`.

They **cannot** live in `docs/` — MkDocs silently drops any dot-prefixed path.
Instead the deploy workflow (`.github/workflows/deploy.yml`) copies them into the
built `site/.well-known/` and publishes with `.nojekyll` so GitHub Pages doesn't
strip the dot-directory. Edit them here; the workflow does the rest on push to
`main`.

## Files

| File | Platform | Served at |
|------|----------|-----------|
| `assetlinks.json` | Android | `https://chefmate.plusmobileapps.com/.well-known/assetlinks.json` |
| `apple-app-site-association` | iOS (no extension) | `https://chefmate.plusmobileapps.com/.well-known/apple-app-site-association` |

Each file declares two things, one per purpose:

- **`assetlinks.json`** — a `get_login_creds` statement (autofill) and a
  `handle_all_urls` statement (App Links verification). Both target the same
  package + signing-cert fingerprint.
- **`apple-app-site-association`** — a `webcredentials` section (autofill) and an
  `applinks` section (Universal Links) with the path components that open the app.

## Android signing fingerprint

`assetlinks.json` pins the SHA-256 of the certificate that signs the **installed**
app. It must stay in sync with:

- **Play App Signing (production):** Play Console → your app → *Test and release*
  → *App integrity* → *App signing* → the **App signing key certificate** SHA-256.
  (Add the **Upload key certificate** SHA-256 as another array entry if you also
  sideload upload-key builds.)
- **Local/debug testing of App Links:** `keytool -list -v -keystore \
  ~/.android/debug.keystore -alias androiddebugkey -storepass android` and add that
  SHA-256 too.

Multiple fingerprints are allowed — it's a JSON array, and both the
`get_login_creds` and `handle_all_urls` targets should carry the same set.

The iOS file needs no secrets: `Y89258SF5P` is the Team ID and
`com.plusmobileapps.chefmate.ChefMate` is the app's bundle ID.

## App-side counterparts (in the `chef-mate` repo, not here)

The app ships an `iosApp/iosApp/iosApp.entitlements` and
`client/composeApp/src/androidMain/AndroidManifest.xml` that must match these
files:

- **iOS** — `com.apple.developer.associated-domains` entitlement with
  **both** `webcredentials:chefmate.plusmobileapps.com` (autofill) and
  `applinks:chefmate.plusmobileapps.com` (Universal Links). Without the domain
  entry iOS never fetches this AASA file.
- **Android** — the `get_login_creds` autofill needs nothing beyond the field
  hints, but App Links additionally need an `https` `VIEW` intent filter with
  `android:autoVerify="true"` for `chefmate.plusmobileapps.com` (with
  `pathPrefix` entries for `/notifications` and `/recipe` today). The paths the
  app claims are governed by that intent filter; `handle_all_urls` here is
  domain-level — which is why adding a path on Android needs no change to
  `assetlinks.json`, while iOS needs a matching `components` entry in the AASA.

## Verify after deploy

```bash
# Android — should return your assetlinks JSON, HTTP 200, application/json
curl -i https://chefmate.plusmobileapps.com/.well-known/assetlinks.json

# Google's verifier
# https://developers.google.com/digital-asset-links/tools/generator

# Android App Links verification on device:
#   adb shell pm get-app-links com.plusmobileapps.chefmate   # expect "verified"

# iOS — Apple's CDN caches the AASA; check what Apple actually sees:
curl -i https://app-site-association.cdn-apple.com/a/v1/chefmate.plusmobileapps.com
```

**GitHub Pages caveat for iOS:** Pages serves the extension-less AASA as
`application/octet-stream`, not `application/json`. Modern iOS (the Apple-CDN
fetch path) accepts valid JSON over HTTPS regardless of content-type, so this
normally works — but if the Apple CDN check above ever rejects it, move just the
AASA to a host where you can set the `Content-Type` header (Netlify `_headers`,
Cloudflare, S3+CloudFront). Both endpoints must be HTTPS with **no redirects**,
so confirm *Enforce HTTPS* is on in the repo's Pages settings.
