# Saraswti Mobile — Security review and current limitations

## Changes made in this review
- Removed embedded owner/admin credentials and master-password/backdoor matching.
- Disabled local-only admin elevation. Admin functions remain locked until a trusted backend verifies an administrator.
- Removed automatic demo account, saved addresses, fake offers, sample products and phone-model seeding from fresh Android installs.
- Replaced the desktop HTML sample catalog and default admin account with an empty store state.
- Removed embedded anonymous relay endpoint/topic strings and disabled anonymous ntfy/Bytebin cloud sync. The previous anonymous relay could expose customer passwords, contact details, addresses, order details and product/catalog data, and untrusted messages could modify local data.
- Removed customer password fields from outgoing sync payloads.
- Added one-way SHA-256 password digests for new local Android registrations; this is a stopgap, not a replacement for Firebase Authentication or a modern password-hashing service.
- Disabled Android Auto Backup for local customer/order data.
- Removed invented promotional prices/discounts and unconfigured WhatsApp contact defaults from fresh configuration.
- Added a fail-closed test for local admin authentication.

## Important before real launch
- This ZIP is **not production-ready**: cloud sync and admin login are intentionally disabled rather than pretending insecure public relay sync is safe.
- If a previous version of this project was shared or installed, treat its old public relay topics as compromised and do not reuse them.
- Connect Firebase Authentication and Firestore (or another trusted backend), add strict server-side security rules and role claims, and only then enable admin operations and cross-device sync.
- Android Room database schema still has a legacy `password` column. New passwords are stored as SHA-256 digests, but older installed copies may contain old demo rows/plaintext passwords. For a clean test install, uninstall the old app or clear its app data first. Do not do this if you need to preserve existing shop data.
- The standalone HTML app is a local prototype, not a secure multi-user website. It must not be exposed as a production store or used to collect real customer/payment data until a backend is connected.
- Payment processing, delivery, real inventory, privacy policy, terms, support contact and Play Store release signing are not configured by this patch.
- Build/test execution requires Android SDK and Gradle. This environment did not have a local Gradle executable or Android SDK available, so a successful APK build could not be confirmed here. Use the included GitHub Actions workflow to perform the actual cloud build and review its result.

## Firebase starter added after review
- Added Firebase Auth and Firestore dependencies plus `FirebaseBackend.kt` helper methods for email/password auth, customer profile creation, admin custom-claim checking, public product reads, and customer order creation.
- Added restrictive Firestore rules: public reads only for published products, writes restricted to `admin: true` custom-claim holders, user profiles cannot self-assign admin, and customers can only create/read their own orders.
- Added a trusted-environment example script for assigning the admin custom claim. It must never run inside the Android app and requires Firebase Admin SDK credentials kept outside the repository.
- Added optional GitHub Actions support for a Base64 `google-services.json` secret.
- These changes are scaffolding only: Firebase is not linked to a real project, existing UI/login is not yet wired to Firebase, Firestore rules have not been deployed, and cloud APK build has not been run. Do not treat the project as production-ready until those steps are completed and tested.
- Corrected a remaining product-display fallback in `ShopViewModel`: an empty Room catalogue or flow error now displays no products instead of repopulating fictional sample products and prices.
