# Zomunk Domain Taxonomy

The shared vocabulary of how Zomunk operates. Harvested from engineering sessions; this is the backbone of the company bot's domain knowledge. Format: `Term` — what it means here.

# Products
1. `Zomunk Points` — flight search product (points/miles angle)
   - Used via: none (internal)
2. `Zomunk Deals` — curated flight-deal discovery platform
   - Used via: executor toolkit_mcp, executor pouch_cms
3. `Premium` — paid subscriber status; unlocks all deals/features
   - Used via: none (internal)
4. `Plan` — subscription tier (Flights, Deals, full Premium) with per-provider pricing
   - Used via: none (internal)
5. `Paywall` — gate restricting free users from premium content
   - Used via: none (internal)
6. `Giveaway` — weekly destination promotional contest with entries
   - Used via: none (internal)

# Billing & Subscription Lifecycle
1. `Activate` — grant premium after successful payment; sets premiumUntil
   - Used via: raw API — packages/app-db/src/data/billing.ts:307
2. `Renew` — successful recharge for the next billing cycle
   - Used via: raw API — packages/app-db/src/data/billing.ts:457
3. `Expire` — auto-renew off; access continues until period end, then revoked
   - Used via: raw API — packages/app-db/src/data/billing.ts:526
4. `Cancel` — subscription ended after access period runs out
   - Used via: raw API — packages/app-db/src/data/billing.ts:526
5. `Halt` — involuntary access revocation on payment/mandate failure (Razorpay)
   - Used via: raw API — packages/app-db/src/data/billing.ts:798
6. `Past Due` — Stripe payment failed; triggers immediate downgrade
   - Used via: raw API — packages/app-db/src/data/billing.ts:814
7. `Reactivation` — lapsed or past_due subscriber returns to active
   - Used via: raw API — packages/app-db/src/data/billing.ts:1007
8. `Downgrade` — revoke premium; premiumUntil set to null
   - Used via: raw API — packages/app-db/src/data/billing.ts:814
9. `Trial` — free evaluation period before first charge (Stripe)
   - Used via: raw API — packages/app-utils/src/billing/stripe.ts:12
10. `Trial Conversion` — trialing → active transition granting paid access
   - Used via: raw API — apps/webhook-worker/src/workflows/stripe.checkout-session-completed.ts:58
11. `Refund` — reverse a payment, optionally revoke access (7-day money-back)
   - Used via: raw API — packages/app-db/src/data/billing.ts:1245
12. `ShortChange` — user paid but received less access than entitled; see ADR-016
   - Used via: none (internal)
13. `Subscription Status` — active | pending | trialing | halted | past_due | expired | cancelled
   - Used via: none (internal)
14. `premiumUntil` — timestamp on user record granting premium; null = not premium (ISO text in better-auth tables, unix ms in billing tables)
   - Used via: none (internal)
15. `renewAt` — next billing/access-end timestamp; must stay in sync with premiumUntil
   - Used via: none (internal)
16. `Activation` — first-ever subscription payment (metric)
   - Used via: none (internal metric)
17. `Renewal` — subsequent-period payment (metric, Razorpay-tracked)
   - Used via: none (internal metric)
18. `Churn` — subscribers who didn't renew; tracked as annual cohort rate
   - Used via: none (internal metric)
19. `Involuntary Churn` — loss from payment failure, not user choice
   - Used via: none (internal metric)
20. `Mandate` — UPI auto-debit authorization behind a Razorpay subscription
   - Used via: raw API — packages/app-db/src/data/billing.ts:307
21. `Auto-renew` — toggle controlling whether a subscription recharges
   - Used via: raw API — packages/app-db/src/data/billing.ts:457

# Payments & Providers
1. `Razorpay` — Indian payment gateway (UPI/cards/netbanking), INR subscriptions
   - Used via: raw API — packages/app-db/src/data/billing.ts:307
2. `Stripe` — international payment processor, USD/SGD/etc., supports trials
   - Used via: raw API — packages/app-utils/src/billing/stripe.ts:12
3. `Apple IAP` — iOS in-app purchase subscriptions
   - Used via: raw API — packages/app-utils/src/apple.ts:145
4. `Android Billing` — Google Play subscriptions
   - Used via: raw API — apps/webhook-worker/src/workflows/android.subscription-activated.ts:165
5. `Cashfree` — payout gateway for referral disbursements (beneficiaries, transfers)
   - Used via: raw API — apps/web-worker/src/routes/user/referral/_service.ts:18
6. `UPI / VPA` — Indian payment method / virtual payment address (user@bank)
   - Used via: raw API — packages/app-db/src/data/billing.ts:307 (Razorpay)
7. `RRN` — Razorpay Retrieval Reference Number for looking up a payment
   - Used via: raw API — packages/app-db/src/data/billing.ts:798
8. `Coupon` — discount code validated and applied at checkout
   - Used via: none (internal)
9. `Embedded Checkout` — Stripe hosted payment UI mounted in-page
   - Used via: raw API — packages/app-utils/src/billing/stripe.ts:12
10. `Provider Meta` — raw provider-side subscription/payment JSON kept for debugging
   - Used via: none (internal)
11. `Provider Subscription ID` — external subscription ID used for webhook lookups
   - Used via: none (internal)
12. `geoState` — user's Indian state, propagated through billing for regional pricing
   - Used via: none (internal)

# Deals
1. `Deal` — priced flight offer: route, dates, airline, cabin, old/new price
   - Used via: none (internal)
2. `Best Deals` — curated top deals on landing pages, country-filtered (Pouch CMS)
   - Used via: executor pouch_cms
3. `Masked Deal` — partial deal shown to non-premium users behind the paywall
   - Used via: none (internal)
4. `Publish` — make a sourced deal live on the platform
   - Used via: executor toolkit_mcp
5. `Source` — where a deal came from (scraper or creator-submitted)
   - Used via: executor toolkit_mcp
6. `Departure City` — origin city; core deal filter and user preference
   - Used via: none (internal)
7. `Seat Class` — economy / premium economy / business / first
   - Used via: none (internal)
8. `Rarity` — how exceptional a deal's price is
   - Used via: none (internal)
9. `PriceLine` — low/typical/high price bar UI on deal pages
   - Used via: none (internal)
10. `Deal Score` — AI-scored deal quality metric
   - Used via: executor toolkit_mcp
11. `Deal Slug` — human-readable URL from city names
   - Used via: none (internal)
12. `Similar Deals` — related deals on a deal detail page
   - Used via: none (internal)
13. `Deal Share` — native share with tracking
   - Used via: none (internal)
14. `Wishlist` — saved deals/routes a user monitors for price changes
   - Used via: none (internal)
15. `PickMedia` — deterministic deal image selection by hashing deal ID
   - Used via: none (internal)
16. `Deal Search` — filter deals by region/country/city, month, budget, cabin
   - Used via: none (internal)

# Flights & Alerts
1. `FlightAware` — external API for flight status and tracking
   - Used via: raw API — apps/flights-worker/src/router/flight/service.search.ts:54
2. `Alert` — FlightAware alert tracking one flight's lifecycle
   - Used via: raw API — apps/flights-worker/src/router/flight/service.search.ts:54
3. `Flight Event` — webhook payload describing a status change
   - Used via: raw API — apps/flights-worker/src/router/flight/service.search.ts:54
4. `Terminal Event` — arrival/cancellation/diversion ending a flight's active state; triggers cleanup
   - Used via: raw API — apps/flights-worker/src/router/flight/service.search.ts:54
5. `Push Token` — device token for APNs notifications
   - Used via: raw API — packages/app-utils/src/mobile-notifications/apns.ts:85
6. `Live Activity` — iOS Dynamic Island live flight tracking
   - Used via: raw API — packages/app-utils/src/mobile-notifications/apns.ts:130
7. `Live Activity Start Token` — push-to-start registration token
   - Used via: raw API — packages/app-utils/src/mobile-notifications/apns.ts:130
8. `faFlightId` — FlightAware's flight identifier
   - Used via: raw API — apps/flights-worker/src/router/flight/service.search.ts:107
9. `Home Airport` — user's preferred departure airport
   - Used via: none (internal)
10. `Price Matrix` — grid view of fares across dates/routes (Points product)
   - Used via: none (internal)

# Users & Auth
1. `OTP` — one-time password login (stored bcrypt-hashed)
   - Used via: raw API — packages/app-utils/src/gupshup.ts:22
2. `Phone Login` — India-only OTP auth via Gupshup SMS
   - Used via: raw API — packages/app-utils/src/gupshup.ts:22
3. `Email OTP` — primary auth outside India
   - Used via: none (internal)
4. `Google / Apple OAuth` — social login providers
   - Used via: raw API — packages/app-auth/src/index.ts:437
5. `Auth Form` — country-specific login: India phone+email, rest email+OAuth
   - Used via: none (internal)
6. `Session` — better-auth session with role
   - Used via: none (internal)
7. `Role` — user | admin | support (sudo-worker access levels)
   - Used via: none (internal)
8. `Country` — user's billing/locale country code driving plans and deals shown
   - Used via: none (internal)
9. `Search Profile` — user preferences: departure cities, country, home airport
   - Used via: none (internal)
10. `Onboarding` — post-signup guided setup funnel
   - Used via: none (internal)
11. `Banned` — suspended account with reason and expiry
   - Used via: none (internal)

# Referrals
1. `Referral Code` — unique per-user invite code
   - Used via: none (internal)
2. `Referral Payout` — cash reward disbursed via Cashfree to UPI
   - Used via: raw API — apps/web-worker/src/routes/user/referral/_service.ts:18
3. `Cashfree Beneficiary` — payout bank account setup for earnings
   - Used via: raw API — apps/web-worker/src/routes/user/referral/_service.ts:60
4. `Referral Stats` — pending / paid / awaiting-approval earnings
   - Used via: none (internal)
5. `Partnero` — former external referral tracker (removed from prod)
   - Used via: none (removed)

# Analytics & Messaging
1. `Tracker` — unified analytics client fanning events out to providers
   - Used via: raw API — packages/app-utils/src/tracker/client.ts:104
2. `Canonical Event` — normalized event name mapped to provider-specific names
   - Used via: none (internal)
3. `Event Routing` — table mapping canonical events to destinations
   - Used via: none (internal)
4. `Identify` — user-property enrichment sent to all providers
   - Used via: raw API — packages/app-utils/src/tracker/client.ts:147
5. `Mixpanel` — product analytics
   - Used via: executor mixpanel_mcp, raw API — packages/app-utils/src/mixpanel.ts:73
6. `PostHog` — product analytics (newer events, funnel metrics)
   - Used via: raw API — scripts/finance/deck-v2/generate-03-funnel.ts:45
7. `Customer.io` — lifecycle email/push messaging
   - Used via: raw API — packages/app-utils/src/tracker/adapters/customer-io.ts:36, apps/sudo-worker/src/router/user.ts:520
8. `Facebook Pixel / Conversions API` — client fbq + server-side deduped events
   - Used via: raw API — apps/web-worker/src/client/lib/track.ts:46, apps/web-worker/src/deps.ts:113
9. `Billing Events` — Trial:Started, Trial:Converted, Subscription:{Renewed,Halted,Cancelled,Resumed}, Payment:{Started,Success,Failure}
   - Used via: none (internal)
10. `Daily Notification` — scheduled workflow sending top deals per country
   - Used via: raw API — apps/webhook-worker/src/lib/slack/client.ts:17
11. `Wishlist Notification` — workflow matching new deals to wishlist criteria
   - Used via: raw API — apps/webhook-worker/src/lib/slack/client.ts:17
12. `Broadcast` — batch email send from notification workflows
   - Used via: raw API — packages/app-utils/src/tracker/adapters/customer-io.ts:36
13. `Crisp` — support chat platform
   - Used via: raw API — packages/app-utils/src/crisp.ts:35
14. `Liquid Template` — email template language with filters like rounded currency
   - Used via: none (internal)

# Retention CRM
1. `CRM Contacts` — Pouch Git collection with current retention state per user
   - Used via: executor pouch_git
2. `CRM Call Log` — Pouch Git collection logging each win-back call attempt
   - Used via: executor pouch_git
3. `Caller` — retention person calling churned users
   - Used via: none (internal)
4. `Call Outcome` — result of a call (no answer, reactivated, etc.)
   - Used via: none (internal)
5. `Smart Filters` — preset filters for retention call lists
   - Used via: none (internal)
6. `Win-back` — re-engagement of churned subscribers
   - Used via: none (internal)

# Content & CMS
1. `Pouch CMS` — headless CMS: faqs, best_deals, landing_hero, landing_pages, testimonials
   - Used via: executor pouch_cms
2. `Pouch Git` — Git-backed Pouch: crm_contacts, crm_call_log, findings, reviews
   - Used via: executor pouch_git
3. `Collection` — CMS content type with JSON schema; admin vs runtime API keys
   - Used via: executor pouch_cms, executor pouch_git
4. `OG Image` — 1200x630 social preview from og-worker (deal / static templates)
   - Used via: raw API — apps/og-worker (satori/workers-og)
5. `Wishlist Image` — deterministic map-rendered image replacing stock photos
   - Used via: raw API — apps/map-worker
6. `Sitemap / JsonLd / canonicalUrl` — SEO surface (FAQPage, BreadcrumbList)
   - Used via: none (internal)

# Metrics & Finance
1. `ARPU / ARPPU` — average revenue per user / per paying user
   - Used via: none (internal)
2. `CAC` — customer acquisition cost (blended includes reactivations)
   - Used via: none (internal)
3. `LTV` — customer lifetime value
   - Used via: none (internal)
4. `Cohort` — subscribers grouped by activation month for churn analysis
   - Used via: executor mixpanel_mcp
5. `Retention Rate` — percentage of a cohort that renewed
   - Used via: executor mixpanel_mcp
6. `DAU / WAU / MAU` — active-user counts
   - Used via: executor mixpanel_mcp, raw API — scripts/finance/deck-v2/generate-03-funnel.ts:45 (PostHog historical)
7. `NPS` — net promoter score
   - Used via: none (internal)
8. `Conversion Rate` — signups → paid users
   - Used via: executor mixpanel_mcp
9. `Ad Spend` — Meta advertising budget ingested hourly to Axiom
   - Used via: executor axiom_mcp, exe axiom-zomunk
10. `Unit Economics` — per-user metrics bundle: ARPU, ARPPU, LTV, conversion
   - Used via: none (internal)
11. `Segments` — revenue markets: India, ME Dubai, ME Saudi, ME Rest, Global Points, US/Canada
   - Used via: none (internal)
12. `Subscriber Model` — cohort projection with tapering growth phases and churn
   - Used via: none (internal)
13. `P&L / Cash Flow / MIS` — financial statements; MIS = monthly source-of-truth
   - Used via: none (internal)
14. `Forecast vs Actuals` — 5-year projection vs real historicals
   - Used via: none (internal)
15. `Terminal Value` — end-of-projection metrics (e.g. Mar-29: 9.63L subs, 383 Cr ARR)
   - Used via: none (internal)
16. `Deck` — investor report compiled from PostHog/Mixpanel via CSV pipeline
   - Used via: raw API — scripts/finance/deck-v2/*
17. `Gross / Net Revenue` — before vs after refunds and fees
   - Used via: none (internal)

# Data Layer & Platform Entities
1. `DataLayer (DL)` — the only DB access path; methods return neverthrow ResultAsync
   - Used via: none (internal)
2. `DL.billing` — billing namespace: subscriptions, payments, activateSubscription
   - Used via: none (internal)
3. `Audit Log` — structured event log, queue-flushed via ctx.waitUntil
   - Used via: raw API — apps/api-worker/src/lib/axiom.ts:38
4. `ApiCache` — KV-backed cache with namespaced prefixes, TTL, clearKeys
   - Used via: none (internal)
5. `AppConfig` — KV-backed configuration storage
   - Used via: none (internal)
6. `Reconciliation` — cross-source comparison to find billing/data discrepancies
   - Used via: none (internal)
7. `forInsert / forUpdate` — base helpers stamping id, created_at, updated_at
   - Used via: none (internal)
8. `Meta Column` — JSON column for flexible non-queried data
   - Used via: none (internal)
9. `Keyset Pagination` — cursor pagination over sort-column values
   - Used via: none (internal)
10. `Timestamps` — unix ms everywhere except better-auth tables (ISO text) — never compare across formats in SQL
   - Used via: none (internal)

# Webhooks & Workflows
1. `Webhook` — inbound provider event (Stripe, Razorpay, Apple, Android, FlightAware, Cashfree, Crisp)
   - Used via: raw API — apps/webhook-worker/src/app/routes/*
2. `Workflow` — Cloudflare Workflows multi-step automation, mostly billing, in webhook-worker
   - Used via: none (internal)
3. `checkout.session.completed` — Stripe event → activate (handles trial start + conversion)
   - Used via: raw API — apps/webhook-worker/src/workflows/stripe.checkout-session-completed.ts:58
4. `customer.subscription.updated / deleted` — Stripe events → renew, convert, cancel
   - Used via: raw API — apps/webhook-worker/src/workflows/stripe.subscription-updated.ts:29, apps/webhook-worker/src/workflows/stripe.subscription-deleted.ts:26
5. `invoice.payment_succeeded / payment_failed` — Stripe events → renewal / downgrade
   - Used via: raw API — apps/webhook-worker/src/workflows/stripe.invoice-payment-succeeded.ts:28, apps/webhook-worker/src/workflows/stripe.checkout-payment-failed.ts:33
6. `ALLOWED_EVENTS` — whitelist of accepted webhook event types
   - Used via: none (internal)
7. `NonRetryableError` — workflow error that must not be retried
   - Used via: none (internal)
8. `Ingest` — write raw webhook payloads to audit table before processing
   - Used via: raw API — apps/api-worker/src/lib/axiom.ts:38
9. `Idempotent` — safe to re-run without duplicates (dispatch keys, backfills)
   - Used via: none (internal)
10. `Backfill` — import historical data to fill gaps
   - Used via: none (internal)
