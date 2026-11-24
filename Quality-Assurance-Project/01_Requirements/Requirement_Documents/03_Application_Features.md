# Application Features Document (AFD)

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** AFD-03-FEATURES  
**Version:** 2.0  
**Status:** Baseline Specification (Aligned with Confirmed Application Modules)

---

## 1. Modular System Architecture

MachineryExpo is architected as an enterprise multi-tenant marketplace platform designed for trading heavy machinery, construction equipment, commercial trucks, and trailers. The system architecture enforces a strict functional partition between **Buyer Side Modules** and **Seller Side Modules**.

```
+-------------------------------------------------------------------------------------------------------+
|                                    MACHINERYEXPO PLATFORM                                             |
+-------------------------------------------------------------------------------------------------------+
|                                                                                                       |
|  === BUYER SIDE MODULES (12 Modules) ===                   === SELLER SIDE MODULES (15 Modules) ===   |
|  1.  Buyer Dashboard                                       1.  Seller Dashboard                       |
|  2.  Inquiries                                             2.  Sell Equipment                         |
|  3.  Wishlist                                              3.  Inventory                              |
|  4.  Financing Inquiries                                   4.  Yards and Location                     |
|  5.  Get Finance                                           5.  Boost Listings                         |
|  6.  Support Ticket                                        6.  Inquiries                              |
|  7.  Alert Preferences                                     7.  Call & Line                            |
|  8.  Switch to Seller                                      8.  Subscription & Billing                 |
|  9.  Sell Equipment                                        9.  Invoices                               |
|  10. Product Search                                        10. Staff Management                       |
|  11. Filter                                                11. My Support Ticket                      |
|  12. My Account                                            12. Alert Preference                       |
|                                                            13. My Account                             |
|                                                            14. Financing                              |
|                                                            15. Search and Filter Product              |
+-------------------------------------------------------------------------------------------------------+
```

---

## 2. Buyer Side Modules Breakdown

### AF-MOD-BUY-01: Buyer Dashboard
- **Route:** `/account` / `/account/dashboard`
- **Description:** The primary operational dashboard for individual machinery buyers. Aggregates live counters for saved watchlist items, active inquiries, pending financing reviews, and recent alert notifications.
- **Key Features:** Activity overview, one-click shortcuts to active inquiries, quick link to saved equipment, notification stream.

### AF-MOD-BUY-02: Inquiries (Buyer)
- **Route:** `/account/Inquiries`, `/account/Inquiries/:id`
- **Description:** Centralized communication hub for tracking dialogues between the buyer and equipment sellers.
- **Key Features:** Multi-item conversation list with dealer details, read/unread states, chronological message thread view, and reply editor.

### AF-MOD-BUY-03: Wishlist
- **Route:** `/account/watchlist`
- **Description:** Personalized catalog bookmarking engine enabling buyers to track equipment of interest across sessions and devices.
- **Key Features:** One-click save from cards and PDPs, instant removal, visual alerts on price reductions and status changes (e.g., Sold/Under Offer).

### AF-MOD-BUY-04: Financing Inquiries
- **Route:** `/account/finance` (aliases: `/account/finance-inquiries`, `/account/financing-inquiries`)
- **Description:** Status tracking dashboard for commercial loan and leasing applications initiated by the buyer.
- **Key Features:** Submission summary list, loan application detail view (`/account/finance/view/:id`), application status progression badges (`Submitted`, `Under Review`, `Approved`, `Declined`).

### AF-MOD-BUY-05: Get Finance
- **Route:** `/financing`, PDP "Get Financing" modal
- **Description:** Interactive commercial equipment financing application and loan calculation engine.
- **Key Features:** Interactive payment calculator (calculating down payment, term length, and monthly estimates), 3-step digital application wizard, digital credit check authorization consent, encrypted lender routing.

### AF-MOD-BUY-06: Support Ticket (Buyer)
- **Route:** `/account/support-tickets`, `/account/support-tickets/:id`
- **Description:** Dedicated helpdesk communication channel allowing buyers to seek assistance from platform administrators.
- **Key Features:** Ticket creation form with priority levels, department routing, conversational reply thread, resolution status indicator.

### AF-MOD-BUY-07: Alert Preferences (Buyer)
- **Route:** `/account/settings/alerts`, `/account/notifications`
- **Description:** Granular notification configuration governing how and when buyers receive platform communications.
- **Key Features:** Independent toggles for Email, SMS, and In-App notifications; topic toggles (Watchlist price adjustments, inquiry replies, newly listed matching equipment, marketing bulletins).

### AF-MOD-BUY-08: Switch to Seller
- **Route:** `/account/become-seller`
- **Description:** Self-service upgrade path allowing registered buyers to convert their account into an individual seller or commercial dealership.
- **Key Features:** Role elevation wizard, entity type selection (`Individual` vs `Dealer`), business profile data capture, immediate plan onboarding without account re-registration.

### AF-MOD-BUY-09: Sell Equipment (Buyer)
- **Route:** `/create-listing`, `/sell-equipment`
- **Description:** Entry point for casual sellers and individual equipment owners to post equipment for sale on the marketplace.
- **Key Features:** Simplified listing form capturing core equipment metadata, specifications, photos, and price; upgrade prompts to dealer tier for bulk inventory management.

### AF-MOD-BUY-10: Product Search
- **Route:** Header search bar, `/equipment` catalog
- **Description:** High-performance full-text search capability indexing equipment listings across makes, models, categories, and descriptions.
- **Key Features:** Real-time query execution, URL query parameter synchronization, keyword sanitization, relevance-ranked result set generation.

### AF-MOD-BUY-11: Filter
- **Route:** `/equipment` sidebar and category modal
- **Description:** Comprehensive faceted filter engine enabling targeted discovery of equipment assets.
- **Key Features:** Listing type tabs (`Sell`, `Rent`, `Auction`, `All`), multi-tier category directory modal ("Browse all categories"), dynamic numeric range sliders (Price, Year, Hours, Mileage), manufacturer and model pickers, location radius boundaries, and sorting options (Price, Hours, Date).

### AF-MOD-BUY-12: My Account (Buyer)
- **Route:** `/account/settings`, `/account/profile`
- **Description:** Identity, security, and session management center for individual buyers.
- **Key Features:** Personal contact detail maintenance (name, avatar, phone), password changes, Multi-Factor Authentication (MFA / TOTP) setup, active session monitoring [NEEDS VERIFICATION].

---

## 3. Seller Side Modules Breakdown

### AF-MOD-SEL-01: Seller Dashboard
- **Route:** `/seller`, `/seller/dashboard`
- **Description:** Operational command center for equipment sellers and dealerships providing immediate insights into inventory performance and lead flow.
- **Key Features:** High-level KPI summary cards (Active Listings, Total Inquiries, Phone Call Clicks), fast-action buttons ("Add Listing", "Upgrade Plan"), incoming lead previews, and activity audit timeline.

### AF-MOD-SEL-02: Sell Equipment (Seller)
- **Route:** `/seller/product/add` (steps: `/overview`, `/specs`, `/media`, `/pricing`, `/location`, `/review`)
- **Description:** Enterprise multi-step equipment listing creation wizard designed for multi-inventory commercial sellers.
- **Key Features:** Transaction classification toggles (`Sell`, `Rent`, `Auction`) governed by plan tier, category-driven dynamic attribute forms, bulk image upload with primary photo selection, PDF spec sheet attachment, real-time plan quota validation via `sellerPlanLimitGuard`.

### AF-MOD-SEL-03: Inventory
- **Route:** `/inventory`, `/seller/products`
- **Description:** Tabular fleet and inventory management interface providing full control over all stock assets.
- **Key Features:** Categorized status tabs (`All`, `Active`, `Draft`, `Under Offer`, `Sold`, `Archived`), in-table search and filtering, per-listing action menu (Edit, Boost, Mark as Sold, Archive, Delete), view counters and inquiry tally indicators.

### AF-MOD-SEL-04: Yards and Location
- **Route:** `/seller/locations`
- **Description:** Dealership yard and storage facility management system enabling multi-location operations.
- **Key Features:** Interactive list of physical dealer yards, creation and modification modal (`SellerLocationModalComponent`), primary headquarter designation, address geocoding, operating hours configuration, auto-linking of equipment to specific yards.

### AF-MOD-SEL-05: Boost Listings
- **Route:** `/seller/boost`, `/seller/boost-listing`
- **Description:** Monetized listing elevation engine enabling dealers to buy premium visibility packages.
- **Key Features:** Boost package selection (Homepage Featured, Top of Category Banner, Urgent Sale badge), target inventory selection, instant Stripe credit card checkout, automatic expiration scheduling.

### AF-MOD-SEL-06: Inquiries (Seller)
- **Route:** `/seller/Inquiries`, `/seller/Inquiries/:id`
- **Description:** Dealership lead management workspace for handling prospective buyer messages.
- **Key Features:** Lead inbox with buyer contact information, inquiry classification (Price, Inspection, General), thread view with full message history, in-app reply composer with automated buyer notification dispatch.

### AF-MOD-SEL-07: Call & Line
- **Route:** `/seller/calls`
- **Description:** Integrated telephony and virtual tracking line management system powered by Twilio.
- **Key Features:** Chronological Call Log table (Caller ID, Date/Time, Call Duration, Direction, Status: `Completed`, `Missed`, `Voicemail`), destination forwarding phone number configuration, weekly business hours schedule management with timezone controls.

### AF-MOD-SEL-08: Subscription & Billing
- **Route:** `/seller/subscription`
- **Description:** Commercial subscription management portal governing dealer tier membership and billing cycles.
- **Key Features:** Active plan overview (Essential, Pro, Elite), renewal date indicator, live listing and staff seat usage meters, self-service tier upgrade/downgrade workflow, Stripe payment method management.

### AF-MOD-SEL-09: Invoices
- **Route:** `/seller/invoices`
- **Description:** Historical financial accounting repository for dealership records.
- **Key Features:** Chronological ledger of all completed payments (Subscription renewals, Boost fees, Ad campaigns), transaction reference codes, payment status indicators, one-click official PDF tax invoice / receipt downloads.

### AF-MOD-SEL-10: Staff Management
- **Route:** `/seller/team`, `/seller/team/add`, `/seller/team/edit/:id`
- **Description:** Multi-user team management system permitting dealerships to invite and manage team members with role-based permissions.
- **Key Features:** Staff roster table, role assignment (`Sales Rep`, `Inventory Manager`, `Finance Admin`), email invitations, seat quota enforcement via `sellerStaffPlanLimitGuard`, access revocation/suspension.

### AF-MOD-SEL-11: My Support Ticket (Seller)
- **Route:** `/seller/support-tickets`, `/seller/support`
- **Description:** Dedicated B2B support desk providing escalated resolution for dealership operational and technical issues.
- **Key Features:** Support ticket submission form with priority ratings (`Low`, `Medium`, `High`, `Urgent`), departmental categorization, status tracking, conversational thread with platform support engineers, tier-based SLA priority handling.

### AF-MOD-SEL-12: Alert Preference (Seller)
- **Route:** `/seller/settings/alerts`, `/seller/notifications`
- **Description:** Enterprise notification configuration governing inbound business lead alerts.
- **Key Features:** Multi-channel notification controls (Email, SMS, Dashboard Alerts) for critical business events: Inbound Inquiries, Missed Tracking Calls, Listing Expiry Notices, Billing and Card Expiration Alerts.

### AF-MOD-SEL-13: My Account (Seller)
- **Route:** `/seller/profile`, `/seller/account-settings`
- **Description:** Dealership public storefront configuration and administrator credentials management.
- **Key Features:** Dealership commercial profile (Legal Name, Shop Name, Dealer License Number, Phone, Physical Address, Website), branding media (Dealership Logo, Header Banner), public bio, and administrator personal security settings.

### AF-MOD-SEL-14: Financing (Seller)
- **Route:** `/seller/financing`
- **Description:** Dealership visibility module for tracking buyer financing applications linked to the dealer's machinery.
- **Key Features:** Financing leads dashboard, status review of loan approvals from partner lenders, customer readiness coordination for equipment release.

### AF-MOD-SEL-15: Search and Filter Product (Seller)
- **Route:** Integrated within `/inventory` and `/seller/products`
- **Description:** Dealership internal inventory lookup and filtering system.
- **Key Features:** Rapid multi-attribute filtering by Yard Location, Transaction Type (`Sell`, `Rent`, `Auction`), Publication Status, Category, and Price Range; instant text search by VIN, Serial Number, or Internal Stock Number; quick sort controls.
