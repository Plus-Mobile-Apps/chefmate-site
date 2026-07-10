# App-association files (`.well-known`)

These tie the native Chef Mate apps to `chefmate.plusmobileapps.com` so the OS
password managers offer credentials saved for the website inside the app (and
vice-versa) during login autofill.

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

## ⚠️ Before this works: replace the SHA-256 placeholder

`assetlinks.json` has `REPLACE_WITH_PLAY_APP_SIGNING_SHA256`. Use the SHA-256 of
the certificate that signs the **installed** app:

- **Play App Signing (production):** Play Console → your app → *Test and release*
  → *App integrity* → *App signing* → copy the **App signing key certificate**
  SHA-256. (Add the **Upload key certificate** SHA-256 as a second array entry if
  you also sideload upload-key builds.)
- **Local/debug testing:** `keytool -list -v -keystore ~/.android/debug.keystore \
  -alias androiddebugkey -storepass android` and add that SHA-256 too.

Multiple fingerprints are allowed — it's a JSON array.

The iOS file needs no secrets: `Y89258SF5P` is the Team ID and
`com.plusmobileapps.chefmate.ChefMate` is the app's bundle ID.

## App-side counterparts (in the `chef-mate` repo, not here)

- **iOS** requires the `com.apple.developer.associated-domains` entitlement with
  `webcredentials:chefmate.plusmobileapps.com`. Without it iOS never fetches this
  AASA file. (The app currently has no `.entitlements` file.)
- **Android** needs nothing beyond the field autofill hints already in the app —
  the `get_login_creds` relation is declared here, keyed to the package +
  signing cert above.

## Verify after deploy

```bash
# Android — should return your assetlinks JSON, HTTP 200, application/json
curl -i https://chefmate.plusmobileapps.com/.well-known/assetlinks.json

# Google's verifier
# https://developers.google.com/digital-asset-links/tools/generator

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
