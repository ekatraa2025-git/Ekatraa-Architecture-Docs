# 5. API Reference (route map)

All routes are on the backend host (`EXPO_PUBLIC_API_URL`). Each folder holds a `route.ts`. HTTP methods are not listed per route; read the actions from the path, or check the backend docs `docs/API_FLOWS.md`.

**Columns:** U = called by the User App · V = called by the Vendor App · A = Admin panel

## Auth
| Route | Client |
|---|---|
| `/api/v1/user/auth/sync` | U |
| `/api/v1/user/auth/pin/{status,lookup,login,enroll}` | U |
| `/api/v1/vendor/auth/{login,request-otp,verify-otp}` | V |
| `/api/v1/vendor/auth/pin/{status,lookup,login,enroll}` | V |
| `/api/v1/vendor/auth/dev-token` | dev only ⚠️ |
| `/api/v1/admin/auth/{login,logout,change-password,forgot-password,reset-password}` | A |

## User: `/api/v1/user/*` (U)
| Domain | Routes |
|---|---|
| Profile | `profile`, `account/delete`, `push-tokens` |
| Addresses | `addresses`, `addresses/[id]`, `autocomplete`, `place-details`, `reverse-geocode` |
| Browse | `categories`, `services`, `services/[id]`, `services/search`, `services/recently-viewed`, `special-services`, `packages`, `addons`, `offers`, `home/{popular-categories,popular-vendors,trending-services}`, `vendors/preview`, `wishlist` |
| Cart | `cart`, `cart/[id]`, `cart/items`, `cart/enquiry`, `cart/enquiry/offers/[offerId]/claim`, `coupons/{apply,remove}` |
| Checkout / pay | `checkout`, `payment/{create-order,verify,create-balance-order,verify-balance,create-milestone-order,verify-milestone}` |
| Orders | `orders`, `orders/[id]`, `…/cancel`, `…/coupon`, `…/resubmit`, `…/review` |
| Bookings | `bookings/[id]/{alternative-vendors,regenerate-event-otp}`, `cancellation-requests/[id]/{accept,select-vendor}` |
| Enquiries | `service-requests`, `[id]`, `[id]/send`, `realtime`, `offers/[offerId]/add-to-cart`, `items/[itemId]/vendor/[vendorId]/addons` |
| Other | `notifications` (+read, mark-all-read), `e-invites/{dispatch,templates}`, `ai/chat` |

## User V2: `/api/v2/user/*` (U)
`cart`, `cart/items/[id]`, `checkout`, `packages`, `drafts`, `drafts/[id]/enquiry`, `drafts/[id]/slots/[slot_id]`, `drafts/[id]/vendors`, `kyc/aadhaar/{initiate,status}`

## Gift cash (U)
`/api/gift-cash`, `[id]`, `summary`, `finalize`, `export/gift-ledger-pdf`, `export/guest-list-pdf` · WhatsApp: `/api/v1/whatsapp/send-invitation`

## Vendor: `/api/v1/vendor/*` (V)
| Domain | Routes |
|---|---|
| Profile | `me`, `profile`, `profile/bank-details`, `gallery`, `reviews`, `account/delete`, `push-tokens`, `notifications` |
| Onboarding | `onboarding/{register,verification-status}`, `kyc-documents` |
| Availability / staff | `availability`, `availability/unavailable`, `staff`, `staff/[id]` |
| Bookings (current) | `me/bookings/[id]/{accept,decline,start,complete,request-end-otp,assign-staff,drop-staff,reassign-staff,staff-logs}` |
| Bookings (older) | `bookings`, `bookings/[id]/{accept,decline,complete,assign-staff,request-cancellation}` |
| Enquiries | `service-requests`, `service-requests/offers/[offerId]/respond`, `respond-token`, `realtime` |
| Orders | `orders/[id]/deliverables` |
| Services | `services`, `services/[id]`, `accept-terms`, `decline-terms` |
| Reports | `me/reports` |
| Vendor AI | `ai/{chat,context,threads,threads/[id],threads/[id]/archive,actions/[id]/confirm,actions/[id]/cancel}` |

## Vendor V2: `/api/v2/vendor/*` (V)
| Domain | Routes |
|---|---|
| Onboarding | `onboarding/register`, `onboarding/addresses/{autocomplete,place-details,reverse-geocode}` |
| KYC | `kyc/pan/verify`, `kyc/gst/verify`, `kyc/aadhaar/{initiate,status}` |
| Catalog | `categories`, `services` (+media, reorder, submit, accept-terms, variant/option price), `variants`, `packages`, `bundles` (+accept/decline-terms) |
| Media | `media/upload`, `profile/media` (+reorder, submit) |
| Booking requests | `booking-requests/[id]`, `…/respond` |
| Venue combos | `venue-combos`, `eligible-venues`, `batches`, `batches/…/results`, `…/work` |

## Staff (V, worker role)
`/api/v1/staff/tasks`, `/api/v1/staff/tasks/[id]/status`

## Chat and briefs (U + V)
`/api/v1/chat`, `[roomId]`, `messages`, `read`, `upload`, `brief`, `brief/generate`, `brief/queries/[id]/{accept,decline}`, `brief/versions/[id]/{submit,decision}`

## Public, no auth (U)
- v1: `/api/v1/public/{catalog,search,vendors,vendors/[id],service-cards,service-categories/[id]/vendors,event-cards/[id]/vendors,festivals,occasions,keywords,reels,trending-searches,app-config/[app]}`, `/api/v1/categories`, `/api/v1/testimonials`
- v2: `/api/v2/public/{catalog,services,vendors,vendors/[id]/portfolio,vendors/[id]/venue-combos,banners,event-discovery,event-discovery/[id],…/packages,…/upcoming,pricing/preview}`

## Admin (A)
| Domain | Routes |
|---|---|
| RBAC | `/api/v1/admin/{admins,roles,permissions}` |
| Overview | `dashboard`, `reports` |
| Bookings | `bookings`, `bookings/[id]/{accept,decline,cancel,assign-vendor}`, `unassigned-package-slots` |
| Money | `cancellation-requests/[id]/review`, `refund-requests` (+approve/deny), `disputes/[id]` (+resolve), `coupons` |
| Catalog | `addons`, `packages`, `special-services` |
| Vendors | `vendors` (+approve, suspend, bank-details/unmasked, reviews, sync-interakt, bulk) |
| Enquiries | `service-requests`, `service-requests/stream` |
| Notifications | `notifications/{publish,campaigns/[id]/deliveries,delivery-status,deliveries/[id]/retry}` |
| V2 | `/api/v2/admin/{configuration,event-discovery,services,memberships/[id]/review,service-reviews/*,media-reviews/*}` |
| Legacy | `/api/admin/{media/upload,services/[id],vendors/[id]/media,vendors/[id]/services,vendor-bundles,vendor-packages}` |

## Webhooks and system (not called by the apps)
| Route | Source |
|---|---|
| `/api/webhooks/clerk`, `/api/webhooks/clerk/user`, `/api/v1/webhooks/clerk/{sms,user/sms}` | Clerk |
| `/api/v1/webhooks/razorpay` | Razorpay |
| `/api/v1/webhooks/{user-created,vendor-created,vendor-categories-changed,referral-qualified}` | Supabase DB *(inferred)* |
| `/api/webhooks/sandbox` | KYC provider |
| `/api/webhooks/whatsapp`, `/webhook/interakt` | WhatsApp / Interakt |
| `/api/internal/voice-responses` | Voice worker |
| `/api/cron/*` | Vercel cron (`CRON_SECRET`) |
