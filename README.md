# M4U – Made for Each Other (Capacitor project)

Wraps the M4U web app (`www/index.html`) as a native Android and iOS app.

## Easiest: get an APK with no installs (GitHub)
1. Create a GitHub repo and push this folder to the `main` branch.
2. Open **Actions → Build Android APK → Run workflow**.
3. When it finishes, download **M4U-debug-apk** from the run. Send that APK to users, or install it on an Android phone (allow "install unknown apps").

## Build locally
Needs Node 18+ (Android: Android Studio + JDK 17; iOS: a Mac with Xcode).
```
npm install
npm run add:android      # and/or: npm run add:ios
npm run icons            # generates app icons + splash from /resources
npx cap sync
npm run open:android     # opens Android Studio → Run ▶ or Build → Build APK(s)
npm run open:ios         # opens Xcode → Run ▶
```
Edit the app in `www/index.html`, then run `npx cap sync` again.

## Publishing to stores
- **Google Play:** Android Studio → Build → Generate Signed Bundle (AAB). Needs a Play Console account.
- **App Store:** Xcode → Product → Archive. Needs an Apple Developer account.
- Change the app ID in `capacitor.config.json` (`com.m4u.dating`) to your own before publishing.

## Before real users
The app currently runs on sample data that resets on reload. For real users, add:
sign-up/login, a backend and database (profiles, likes, matches), photo storage, real-time chat, voice-note upload, payments for subscriptions/Boost, and moderation/reporting. Store review also requires a privacy policy and in-app account deletion.

---
## PhonePe payments + registration capture (server/)
`server/` is a small Node server that (1) serves the app, (2) saves every registration, and (3) takes PhonePe payments for paid plans.

1. Get merchant credentials from PhonePe Payment Gateway (client ID, client secret, client version).
2. Deploy `server/` to any Node host (Render, Railway, a VPS). Copy `.env.example` to `.env` (or set the same variables in the host's dashboard).
   `PUBLIC_URL` must be the server's public https address.
3. Test with `PHONEPE_ENV=sandbox`, then switch to `production`.
4. Share the server URL. Users register there; paid plans go to PhonePe checkout and return to the app.
5. See sign-ups: `GET /api/registrations` with header `x-admin-key: <ADMIN_KEY>`.
6. APK/iOS: edit `www/config.js` to `window.M4U_API = "https://your-server"`, then `npx cap sync`.

Notes: prices (₹499 / ₹999) are set in `server/index.js` (`PRICE`, in paise). Registrations are saved to `server/data/registrations.json`; many hosts wipe local files on redeploy, so move this to a real database (Postgres, Supabase) before launch. Add a PhonePe webhook for payments that complete after the user closes the page. This integration was written from PhonePe's published docs but not run against PhonePe (no credentials here), so test the full flow in sandbox first.
