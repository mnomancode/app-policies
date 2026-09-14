# app-policies

Privacy policies for apps published by Muhammad Noman, hosted free on GitHub Pages.

Base URL: **https://mnomancode.github.io/app-policies/**

| App | Package | Privacy Policy |
| --- | --- | --- |
| WAPDA Bill | `pk.nomanapps.wapdabill` | https://mnomancode.github.io/app-policies/wapda-bill/privacy-policy/ |

## Folder layout

```
app-policies/
├── .nojekyll                       # tells GitHub Pages to serve files as-is
├── index.html                      # home page listing every app
├── README.md
├── _template/
│   └── privacy-policy/index.html   # starting point for new apps (don't link it in Play Console)
└── wapda-bill/
    └── privacy-policy/index.html   # → /app-policies/wapda-bill/privacy-policy/
```

Every page is `<app-slug>/<page>/index.html`, which gives a clean URL ending in `/`.

---

## Adding a policy for a new app

### 1. Pick a slug

Use lowercase letters and hyphens, and never change it after publishing, because the store listing points at it.
Example: `gas-bill`, `prayer-times`.

### 2. Copy the template

```sh
cd ~/Movies/Development/mobile-apps/app-policies
mkdir -p <app-slug>
cp -R _template/privacy-policy <app-slug>/privacy-policy
```

### 3. Find out what the app really does

The policy must match the app and the Play Console **Data safety** form. Otherwise Play can reject the app.
In the app project, check:

```sh
# Third-party SDKs
grep -E "google_mobile_ads|firebase|posthog|sentry|onesignal|facebook|revenuecat|in_app_purchase|google_sign_in|geolocator|image_picker|camera|contacts" pubspec.yaml
# Android permissions
grep "uses-permission" android/app/src/main/AndroidManifest.xml
# iOS permission prompts
grep -E "UsageDescription" ios/Runner/Info.plist
```

Then map what you find to template sections:

| If the app has... | Keep this section / add this | Data safety form |
| --- | --- | --- |
| No backend, local storage only | §3 Information stored on your device | Local-only data isn't "collected" |
| Calls an external website or API | §4 Online requests (name the URL) | Usually nothing, unless it's your own server that stores data |
| Shows someone else's data (govt portal, etc.) | §2 Disclaimer and data source | — |
| `CAMERA` permission, `camera`, `image_picker` | §5 Camera and photos | Photos: nothing if processed on-device only |
| `google_mobile_ads` | §6 Advertising (AdMob) | Device IDs, approximate location, app interactions, diagnostics — used for advertising, analytics, fraud prevention |
| `NSUserTrackingUsageDescription` in Info.plist | §6, iOS line that says the app **asks** for ATT | — |
| Firebase Analytics / Crashlytics / PostHog / Sentry | §7 Analytics and crash reports | App interactions, crash logs, diagnostics, device IDs |
| Location permission (`geolocator`, etc.) | Add a "Location" section; remove "No precise location" from §8 | Approximate or precise location |
| Sign-in / accounts / your own server | Add sections on what's stored on the server, how long it's kept, and **how to delete the account** (Play also requires an account-deletion URL) | Name, email, user IDs, plus a deletion request |
| In-app purchases | Add a "Payments" section: payments are handled by Google Play / Apple, and we don't see card details | Purchase history |
| Targets children | Rewrite §11; needs Families policy compliance and child-directed ad settings | — |

### 4. Fill it in

- Replace every `{{PLACEHOLDER}}` (app name, package ID, dates, etc.).
- Delete every `<!-- OPTIONAL ... -->` block that doesn't apply, and delete the comment markers of the ones you keep.
- Delete the `<meta name="robots" content="noindex">` line.
- Renumber the `<h2>` headings (1, 2, 3…) and fix any "see Section N" references.
- Check that nothing is left over:

```sh
grep -nE "\{\{|OPTIONAL|noindex" <app-slug>/privacy-policy/index.html   # should print nothing
```

### 5. Preview locally

```sh
python3 -m http.server 8000
# open http://localhost:8000/<app-slug>/privacy-policy/
```

Check it on a narrow (phone-sized) window too.

### 6. Link it

- Add the app to `index.html`:
  ```html
  <h2>New App Name</h2>
  <ul>
    <li><a href="<app-slug>/privacy-policy/">Privacy Policy</a></li>
  </ul>
  ```
- Add a row to the table at the top of this README.

### 7. Publish

```sh
git add .
git commit -m "Add <App Name> privacy policy"
git push
```

GitHub Pages redeploys in about 1–2 minutes. Check progress with `gh run list` or the repo's **Actions** tab. Then open:

```
https://mnomancode.github.io/app-policies/<app-slug>/privacy-policy/
```

### 8. Use it in the stores

- **Google Play Console** → *App content → Privacy policy*: paste the URL. Then fill in *Data safety* using the table in step 3.
- **App Store Connect** → *App Privacy*: paste the same URL and answer the questions the same way.
- Optional: link the policy from the app's settings/about screen too. AdMob and GDPR consent reviewers like to see it.

---

## Updating an existing policy

1. Edit `<app-slug>/privacy-policy/index.html`.
2. Change **Last updated** (keep the original *Effective date*).
3. Commit and push. The URL stays the same, so there's nothing to change in Play Console.
   If the change adds new data collection (a new SDK, a new permission), update the **Data safety** form too **before** releasing that app version.

## Other pages later

The same pattern works for other documents a store might ask for, for example:

- `<app-slug>/terms/index.html` for Terms of Service
- `<app-slug>/delete-account/index.html` for the account-deletion instructions Play requires for apps with sign-in
- `<app-slug>/support/index.html` for a support/contact page
