# 8. QA Risk Areas and Known Issues

Ordered roughly by priority.

## 🔴 High
1. **Payments.** Test full, advance/balance and milestone payments. Cover Razorpay failure and cancel inside the WebView, a webhook that arrives before or after `verify`, a duplicate verify, the `reconciliation_required` state, and refunds.
2. **Authorization boundaries.** The server uses a database client that bypasses RLS, so every route must check ownership itself. Try reading or changing another user's order, cart or draft, or another vendor's booking. Check that staff cannot do owner-only actions.
3. **Booking OTP flow.** Test start OTP and completion OTP: a wrong OTP, an expired OTP, regenerating the OTP, and completing without starting.
4. **Vendor timeout and reassignment.** The cron runs every 15 minutes. Test what the customer sees and what happens when no vendor accepts.
5. **User App auth timeout bug** (`issues.md`). The 4-second safety timeout on the splash marks the user as registered and opens the tabs **before profile verification finishes**. A network failure also sends the user to registration, with no retry.
6. **Dev-only bypasses.** Confirm that the `x-dev-*` headers and `/vendor/auth/dev-token` are rejected outside development. Confirm the `ALLOW_TEST_COUPON` setting in production.

## 🟠 Medium
7. **Incoming-enquiry ringer (Vendor App).** Test with the app in the foreground, in the background and killed. Test Accept/Reject from the notification, `respond_by` expiry, and several enquiries arriving together.
8. **iOS enquiry notification extensions and Live Activity.** The dev handoff doc says the Swift code was never compiled. Test this first on iOS.
9. **Guest-to-login handoff.** Test each pending action (checkout, start booking, custom request): the action must be replayed after login.
10. **Package drafts.** Test slot vendor selection, admin assignment of unassigned slots, and enquiry vs checkout.
11. **KYC.** Test valid and invalid PAN and GST, and Aadhaar flows that are abandoned or time out (the sweep cron runs every 5 minutes).
12. **Service moderation.** Check the vendor submits, admin approves/rejects/edits, and vendor re-accepts terms. The service must stay hidden until it is published.
13. **Realtime.** Test offer and enquiry updates when the connection drops, then reconnects.

## 🟡 Low / hygiene
14. **Dev screens in the User App route tree:** `test-cart`, `test-filter`, `test-occasions`, `test-services`, `test-vendor`. Check whether they can be reached in release builds, for example through a deep link.
15. **Gemini API key shipped in the User App bundle.** This is a security issue to discuss with dev.
16. **Vendor App API fallback.** If `EXPO_PUBLIC_API_URL` is missing, the app points at **production**.
17. **Translations.** Every screen should be checked in English, Hindi and Odia. The empty-address label "Select your address" is already known to be missing in Hindi and Odia.
18. **Home "View more" under Explore by Occasion** renders every remaining card at once, with no pagination.
19. **Leftover placeholder names in the User App:** slug `expo_temp`, scheme `expotemp`.
20. **Force update (Vendor App).** Test that the in-app update prompt and the blocking screen appear when the version is too old.

## Suggested test-suite structure
```
User App:    Auth · Guest mode · Discovery/Search · Cart & Coupons · Checkout & Payment · Orders & Cancellation · Enquiries · Packages · Chat & Brief · Guest Manager · Profile · Notifications · i18n
Vendor App:  Auth · Onboarding/KYC · Catalog & Moderation · Enquiry ringer · Booking lifecycle · Staff/Worker · Calendar · Reports · Chat & Brief · Notifications · i18n · Force update
Admin:       RBAC · Vendor approval · Service/Media reviews · Bookings & assignment · Refunds/Disputes · Notifications/Campaigns · Content (banners, festivals)
Backend:     API contract · AuthZ · Webhooks · Cron jobs · Rate limits
```
