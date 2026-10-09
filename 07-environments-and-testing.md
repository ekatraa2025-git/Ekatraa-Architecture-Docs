# 7. Environments, Builds and Existing Tests

## Environments
| Env | Backend | Database |
|---|---|---|
| Local | `npm run dev` on `localhost:3000` | Local Postgres (docker-compose) or dev Supabase |
| Dev / staging | GCP VM `ekatraa-test` (asia-south1), deployed with OpenShip | Self-hosted Supabase |
| Production | Vercel | Supabase |

*Ask the dev team for the actual URLs and test accounts. They are deliberately not stored in this repo.*

## Mobile build profiles (`eas.json`, both apps)
| Profile | Use |
|---|---|
| `development` | Dev client, internal distribution |
| `preview` | Internal test build (User App: Android APK) |
| `production` | Store build; build number increments automatically |

The API host each build talks to comes from `EXPO_PUBLIC_API_URL`. **QA: always confirm which backend a build points to.**
- If this variable is missing, the Vendor App falls back to **production**.
- In the same case, a User App release build throws an error.

## Environment variable names
**User App:** `EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY`, `EXPO_PUBLIC_GOOGLE_MAPS_API_KEY`, `EXPO_PUBLIC_GEMINI_API_KEY`, `EXPO_PUBLIC_INTERAKT_INVITATION_TEMPLATE`, `GOOGLE_SERVICES_JSON`

**Vendor App:** `EXPO_PUBLIC_API_URL`, `EXPO_PUBLIC_CLERK_PUBLISHABLE_KEY`, `EXPO_PUBLIC_GOOGLE_MAPS_API_KEY`, `EXPO_PUBLIC_POSTHOG_API_KEY`, `EXPO_PUBLIC_POSTHOG_HOST`, `GOOGLE_SERVICES_JSON`

**Backend groups:**
- Supabase: `SUPABASE_*`
- Clerk: `CLERK_*`, `USER_CLERK_*`, `VENDOR_CLERK_*`
- Payments: `RAZORPAY_*`
- Messaging: `INTERAKT_*`
- KYC: `SANDBOX_*`
- Maps and AI: `GOOGLE_*`, `GEMINI_*`, `ANTHROPIC_API_KEY`, `OPENROUTER_*`, `MASTRA_*`
- Redis: `UPSTASH_*`
- Secrets: `ADMIN_JWT_SECRET`, `CRON_SECRET`, `BANK_DETAILS_ENCRYPTION_KEY`, `BOOKING_OTP_SALT`, `PRICING_TOKEN_SECRET`
- Feature flags: `MARKETPLACE_V2_ENABLED`, `VENDOR_AI_ENABLED`, `VENDOR_AI_WRITES_ENABLED`, `ALLOW_TEST_COUPON`

## Existing automated tests
| Component | Type | How to run |
|---|---|---|
| Backend | API integration tests (`tests/api/*.mjs`): cart and order lifecycle, payment, Razorpay webhook, KYC, chat, vendor RFP, vendor end-to-end, AI guardrails | Start `npm run dev`, then `npm run test:api` |
| Backend | Unit and module tests (`node:test` + tsx) | `npm run test:unit`, `test:foundations`, `test:identity`, `test:customer-profiles` |
| Backend | Everything | `npm test` (the custom runner `tests/run-all-tests.mjs`) |
| User App | Unit tests (`tests/*.test.cjs`) | `node --test tests/` |
| Vendor App | Standalone scripts (`tests/*.cjs`) | `node tests/<file>.cjs` |

**Gaps:**
- Neither app has **end-to-end UI automation** (no Detox, Maestro or Appium).
- Neither app has an `npm test` script.

Mobile E2E coverage is the main area where QA can add value.
