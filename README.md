# Saraswti Mobile — eCommerce Android App

Starter eCommerce Android application for **Saraswti Mobile** electronics store built with Kotlin, Jetpack Compose, Material 3, and Room Database. Fresh installs and empty/error catalogue states start with an empty catalog so no fake stock/prices/customers are shown.

## Important security status

This reviewed ZIP intentionally disables local admin login and anonymous public cloud sync because the previous build included embedded admin credentials and sent customer/order data through an unauthenticated public relay. Admin features and multi-device sync will not work until Firebase Authentication + Firestore security rules (or an equivalent trusted backend) are configured. New local customer passwords are stored as SHA-256 digests, but production login should use Firebase Authentication. The standalone HTML files are local prototypes, not production websites.

For a clean test, install as a fresh app or clear old app data. Older installed copies may retain previously stored demo rows. Do not clear data if you need to keep existing shop information.

## How to Get the Installable APK (`app-debug.apk`)

### Method 1: AI Studio Settings Menu
1. Click the **Settings (⚙️)** or **Export** menu in the top-right bar of Google AI Studio.
2. Select **Generate APK / Download APK**.

### Method 2: GitHub Cloud Build (No PC Needed)
1. Click the **GitHub** icon in the top bar of Google AI Studio to push this repository to your GitHub account.
2. Open your repository on GitHub and tap the **Actions** tab.
3. Click the **Build Saraswti Mobile APK** workflow run and download the **`Saraswti-Mobile-APK`** artifact zip, which contains `app-debug.apk`.

### Method 3: Android Studio (From Project ZIP)
1. Download the project ZIP from AI Studio and extract it.
2. Open the folder in **Android Studio**.
3. Click **Build → Build Bundle(s) / APK(s) → Build APK(s)**.

## Firebase project configuration added (backend integration still incomplete)

This ZIP now includes your `google-services.json` for Firebase project `saraswati-mobile-a1037`, Firebase Auth/Firestore dependencies, `FirebaseBackend.kt`, restrictive `firestore.rules`, and an optional GitHub Actions secret hook. The config package ID matches the app's `applicationId`. Important: this only adds the Android client configuration. The current UI still uses the local Room login flow; `FirebaseBackend.kt` is a starter and is **not yet wired into the UI**, and Firestore rules have not been deployed. Do not use this as a live customer store yet.

### Setup steps
1. Your Firebase project has already been selected: `saraswati-mobile-a1037`.
2. The supplied `google-services.json` is included at `app/google-services.json`, and its package ID (`com.aistudio.saraswtimobile.xkqpzr`) matches `app/build.gradle.kts`. If you later create another Firebase project, replace this file with that project's Android config. Never upload service-account private keys to the Android app.
3. In Firebase Authentication, enable **Email/Password** sign-in. The current starter auth helper uses email/password; the existing phone-based UI has not yet been connected to it.
4. Create a Firestore database. Deploy the included rules from the project root with Firebase CLI (`firebase deploy --only firestore:rules`) after installing and signing into Firebase CLI.
5. Create your owner account using Firebase Authentication. Assign the `admin: true` custom claim only from a trusted server/admin computer using Firebase Admin SDK. Do not add an admin toggle or secret password in the app. Example script is `scripts/set-admin-claim.cjs`; install `firebase-admin` in a separate trusted tools folder and set `GOOGLE_APPLICATION_CREDENTIALS` to a service-account file kept outside this project. Never commit that JSON key.
6. In GitHub repository Settings → Secrets and variables → Actions, optionally add `FIREBASE_GOOGLE_SERVICES_JSON_BASE64`, whose value is the Base64 encoding of the `google-services.json` file. Or omit it if you commit the non-secret Android Firebase config to your private/public repo according to your policy. The service-account key must never be used for this secret.
7. Push the project to GitHub, open Actions, run **Build Saraswti Mobile APK**, then download the `Saraswti-Mobile-APK` artifact after the run succeeds.

### Security notes
- Firebase client configuration is not a server secret; Firestore rules and Auth/claims are the security boundary.
- `firestore.rules` denies access by default. Products must have `published: true` to be public. Customer accounts cannot grant themselves admin privileges.
- Firebase Auth/Firestore helpers are scaffolding only and have not been integrated into the existing screens or tested against your project. The Android UI and web prototype must be integrated and tested before accepting real orders or personal/payment data.
- The example claim script requires `firebase-admin` in a trusted Node.js environment and valid Application Default Credentials. Do not run it on a public computer or distribute service-account credentials.
