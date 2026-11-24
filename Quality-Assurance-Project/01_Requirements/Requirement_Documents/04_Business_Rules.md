# Business Rules Document (BRD)

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** BRD-04-RULES  
**Version:** 2.0  
**Status:** Baseline Specification (Updated per Confirmed Application Modules)

---

## 1. Document Scope & Policy Framework

This document defines the core business rules, validation logic, system constraints, and operational boundaries governing the **MachineryExpo** platform. The rules are organized into:
1. **General Platform & Registration Rules**
2. **Buyer Side Business Rules (BR-BUY-xxx)**
3. **Seller Side Business Rules (BR-SEL-xxx)**

Rules marked **[CONFIRMED]** represent verified behavior in current application code and test suites. Rules marked **[NEEDS VERIFICATION]** indicate expected business policies that require product management confirmation.

---

## 2. General Platform & Authentication Rules

### BR-GEN-001: Unique Email Identity
- **Status:** [CONFIRMED]
- **Rule Statement:** An email address must be globally unique across users within a specific tenant boundary.
- **Enforcement:** Attempting to register an email already associated with an account triggers an immediate validation conflict error.

### BR-GEN-002: Dual-Channel OTP Identity Verification
- **Status:** [CONFIRMED]
- **Rule Statement:** Any public or casual user performing an account creation or submitting an unauthenticated lead inquiry must complete dual contact verification (Email verification code and SMS OTP code via Twilio) before the action is finalized.
- **Enforcement:** System intercepts submission and launches verification modal; actions remain blocked until valid OTP tokens are verified.

### BR-GEN-003: Password Cryptographic Standards
- **Status:** [CONFIRMED]
- **Rule Statement:** All user passwords must meet strict complexity rules:
  - Minimum 8 characters.
  - At least 1 uppercase letter (`A-Z`).
  - At least 1 lowercase letter (`a-z`).
  - At least 1 digit (`0-9`).
  - At least 1 special character (`!@#$%^&*()_+-=[]{}|;':",./<>?`).
- **Enforcement:** Enforced via Angular Reactive Forms validators on the client and ASP.NET Core Identity on the backend.

### BR-GEN-004: Brute-Force Rate Limiting
- **Status:** [CONFIRMED]
- **Rule Statement:** To safeguard accounts, the system enforces rate limiting on login and verification endpoints.
- **Enforcement:** More than 5 consecutive invalid authentication attempts from an IP or account within 15 minutes triggers HTTP 429 (Too Many Requests).

---

## 3. Buyer Side Business Rules

### BR-BUY-001: Watchlist Idempotency & Ownership
- **Module:** Wishlist
- **Status:** [CONFIRMED]
- **Rule Statement:** A buyer cannot add duplicate instances of the same equipment to their watchlist.
- **Enforcement:** The database enforces a unique composite index `(UserId, ProductId)` on the `BuyerWishlists` table. Subsequent click acts as a removal toggle.

### BR-BUY-002: Watchlist Item Availability Transitions
- **Module:** Wishlist
- **Status:** [CONFIRMED]
- **Rule Statement:** If a bookmarked listing is marked as "Sold" or archived by the seller, the item must not disappear from the buyer's saved equipment page; instead, it must clearly display a "Sold" badge and disable direct inquiry actions.
- **Enforcement:** Checked during catalog entity retrieval; status flag rendered on card component.

### BR-BUY-003: Guest Inquiry Dual-Verification Mandate
- **Module:** Inquiries (Buyer)
- **Status:** [CONFIRMED]
- **Rule Statement:** Unauthenticated buyers submitting an equipment inquiry must verify both email and mobile phone number via static/SMS OTP before the inquiry is recorded in the seller's inbox.
- **Enforcement:** Inquiry submission dialog halts at Step 2 until both verification codes pass backend inspection.

### BR-BUY-004: Mandatory Credit Consent for Financing
- **Module:** Get Finance / Financing Inquiries
- **Status:** [CONFIRMED]
- **Rule Statement:** A financing application cannot be submitted without explicit digital acceptance of credit check authorization terms.
- **Enforcement:** The Step 3 "Submit Financing Application" button remains disabled until the applicant checks the statutory credit disclosure consent box.

### BR-BUY-005: Loan Application Numerical Integrity
- **Module:** Get Finance
- **Status:** [CONFIRMED]
- **Rule Statement:** The requested loan amount must be a positive numeric value and cannot exceed the list price of the selected machinery (plus applicable taxes/fees).
- **Enforcement:** Client-side numeric validator prevents negative or zero loan values; maximum ceiling validated against linked PDP price [NEEDS VERIFICATION].

### BR-BUY-006: Buyer Account Switch to Seller Continuity
- **Module:** Switch to Seller
- **Status:** [CONFIRMED]
- **Rule Statement:** Upgrading an account from Buyer to Seller must retain the original user identity, password credentials, and existing buyer activity history.
- **Enforcement:** Backend attaches new `Seller` and `Tenant` role claims to the existing user record without generating a distinct user ID.

### BR-BUY-007: Single Listing Quota for Casual Buyers
- **Module:** Sell Equipment (Buyer)
- **Status:** [NEEDS VERIFICATION]
- **Rule Statement:** Individual buyer accounts posting equipment via the casual seller portal are restricted to 1 active listing at any time. To post multiple units, the user must upgrade to a commercial dealer plan.
- **Enforcement:** Verified via backend listing count check before opening listing creation wizard.

### BR-BUY-008: Search Query Sanitization & Safe Defaults
- **Module:** Product Search & Filter
- **Status:** [CONFIRMED]
- **Rule Statement:** All search query parameters must be sanitized against SQL injection and script execution. An empty search query returns all active catalog listings sorted by default sort criteria (Newest First).
- **Enforcement:** Parameterized EF Core queries; default sort applied in catalog repository.

---

## 4. Seller Side Business Rules

### BR-SEL-001: Subscription Plan Listing Limit Enforcement (Inventory Quota)
- **Module:** Subscription & Billing / Sell Equipment / Inventory
- **Status:** [CONFIRMED]
- **Rule Statement:** A seller cannot create or hold more active listings in inventory than permitted by their current subscription tier:
  - `Essential Plan` ($0 / Free): Strictly limited to **10 listings** in inventory.
  - `Pro Plan` ($149): Up to **50 listings** in inventory.
  - `Elite Plan` ($249): **Unlimited listings** in inventory.
- **Enforcement:** Enforced at both UI layer via `sellerPlanLimitGuard` and backend API layer; attempting to add/publish listing N+1 beyond the plan quota returns an entitlement limit exception and prompts the seller to upgrade their plan.

### BR-SEL-002: Plan-Based Transaction Type Restrictions
- **Module:** Sell Equipment / Subscription & Billing
- **Status:** [CONFIRMED]
- **Rule Statement:** The ability to publish non-sale transaction types is restricted by subscription tier:
  - `Sell`: Allowed on all tiers (Essential, Pro, Elite).
  - `Rent / Lease`: Restricted to Pro and Elite tiers.
  - `Auction`: Restricted strictly to pro Elite tier.
- **Enforcement:** The listing creation wizard disables or hides Auction/Rent switches for Essential sellers; backend rejects payloads containing `isForAuction: true` from unauthorized tiers.

### BR-SEL-003: Mandatory Listing Attributes
- **Module:** Sell Equipment / Inventory
- **Status:** [CONFIRMED]
- **Rule Statement:** Every equipment listing must contain valid core metadata before it can transition to `Published` status:
  - Product Classification.
  - Category Selection.
  - Manufacturer / Make.
  - Model.
  - Manufacturing Year (Valid past 4-digit year up to Current Year + 1).
  - Asking Price > 0 (unless "Call for Price" flag is true).
  - Physical Location (Associated Yard or valid Address/Zip).
  - Media / Images (Must have at least 1 image uploaded; maximum quota is governed by the seller's active plan per **BR-SEL-014**).
- **Enforcement:** Step-by-step wizard disables the "Publish" button until all mandatory fields and validation conditions are met.

### BR-SEL-014: Plan-Based Media Upload Limits (Images & Video)
- **Module:** Sell Equipment / Inventory / Subscription & Billing
- **Status:** [CONFIRMED]
- **Rule Statement:** The allowed number of product images and video attachments per listing depends strictly on the seller's subscription plan tier:
  - `Essential Plan` ($0 / Free): Maximum **5 images**, **0 videos** (video upload disabled/not allowed).
  - `Pro Plan` ($149): Maximum **20 images**, up to **1 video**.
  - `Elite Plan` ($249): Maximum **50 images**, up to **5 videos**.
- **Enforcement:** Enforced in the listing creation/edit wizard UI (media uploader disables further additions once tier limit is reached) and validated on backend API endpoints.

### BR-SEL-015: SEO Title and Meta Title Customization Privileges
- **Module:** Sell Equipment / Inventory / SEO
- **Status:** [CONFIRMED]
- **Rule Statement:** Customization of SEO Title and Meta Title fields is restricted based on the seller's subscription tier:
  - `Essential Plan`: Locked / read-only (uses platform auto-generated default SEO values).
  - `Pro Plan`: Enabled (sellers can edit and customize SEO Title and Meta Title).
  - `Elite Plan`: Enabled (sellers can edit and customize SEO Title and Meta Title).
- **Enforcement:** UI disables/hides the SEO metadata input fields for Essential Plan users; backend API rejects manual SEO payload updates if the seller account does not hold an active Pro or Elite subscription.

### BR-SEL-016: Post-Creation Listing Publish Options & Promotion Plans
- **Module:** Sell Equipment / Boost Listings
- **Status:** [CONFIRMED]
- **Rule Statement:** Upon clicking the "Publish Listing" button, the seller is presented with three distinct publishing/listing plan options:
  1. **Free Plan ($0):** Standard listing submitted with `Pending Approval` status (requires administrative/moderation review before becoming publicly active).
  2. **Pro Listing Boost Plan ($49.99):** Provides a 30-day top-ranking boost in search/category results ("Top in Products" placement).
  3. **Elite Listing Plan ($99.99):** Premium plan offering enhanced product availability and prime visibility for 14 days ("Best Plan for 14 Days More Product Availability").
- **Enforcement:** After clicking "Publish Listing", a modal/checkout step prompts selection among the three options. Selection of paid plans redirects to secure payment flow before activating the respective boost/availability privileges; Free plan sets listing status to `Pending Approval`.

### BR-SEL-004: Listing Ownership & Modification Rights
- **Module:** Inventory / Sell Equipment
- **Status:** [CONFIRMED]
- **Rule Statement:** A seller or staff member can only view, edit, boost, or delete listings that belong to their specific dealership and tenant organization.
- **Enforcement:** Backend queries enforce `TenantId == CurrentTenantId && SellerId == CurrentSellerId`. Cross-tenant manipulation returns HTTP 403 Forbidden.

### BR-SEL-005: Yards & Location Address Geocoding
- **Module:** Yards and Location
- **Status:** [CONFIRMED]
- **Rule Statement:** Every yard location must possess a unique name within the dealership and include a valid street address, city, state, postal code, and country.
- **Enforcement:** Submissions missing address or postal code fail client-side form validation before API dispatch.

### BR-SEL-006: Primary Yard Deletion Prohibition
- **Module:** Yards and Location
- **Status:** [CONFIRMED]
- **Rule Statement:** A dealership must have at least one active Yard location designated as "Primary / HQ". A yard currently marked as Primary cannot be deleted until another yard is designated as Primary.
- **Enforcement:** Delete action is disabled or triggers confirmation error if attempting to delete the designated primary location.

### BR-SEL-007: Boost Eligibility & Listing Status
- **Module:** Boost Listings
- **Status:** [CONFIRMED]
- **Rule Statement:** Only listings in `Active` / `Published` status are eligible for boost packages. Listings in `Draft`, `Under Offer`, `Sold`, or `Archived` status cannot be boosted.
- **Enforcement:** Boost selection picker filters out all non-active inventory items.

### BR-SEL-008: Staff Seat Limit Enforcement
- **Module:** Staff Management
- **Status:** [CONFIRMED]
- **Rule Statement:** The number of staff accounts an organization can invite or maintain is bounded strictly by the subscription tier plan allowance:
  - `Essential Plan` ($0 / Free): Allows adding only **1 user** (Owner / single user).
  - `Pro Plan` ($149): Allows adding up to **5 users** (staff members).
  - `Elite Plan` ($249): Allows adding **unlimited staff members**.
- **Enforcement:** The "Add Team Member" button is disabled and route `/seller/team/add` is blocked by `sellerStaffPlanLimitGuard` when seat quota is reached; backend API returns HTTP 403 / Plan Limit Exceeded on invitation attempts beyond the plan cap.

### BR-SEL-009: Call Forwarding Number Validation
- **Module:** Call & Line
- **Status:** [CONFIRMED]
- **Rule Statement:** Destination forwarding numbers entered in Call & Line settings must be valid telecommunication numbers in international E.164 format and cannot match the platform's internal Twilio virtual tracking numbers.
- **Enforcement:** Validated on form input; tested against loop prevention rules in telephony service.

### BR-SEL-010: PCI-DSS Payment Isolation & Tokenization
- **Module:** Subscription & Billing / Invoices / Boost Listings
- **Status:** [CONFIRMED]
- **Rule Statement:** Raw payment card account numbers (PAN), CVVs, and expiration dates shall never be processed or stored directly on MachineryExpo application servers.
- **Enforcement:** All payment interactions use Stripe Elements hosted iframes; application servers only store customer IDs, subscription IDs, and payment method tokens.

### BR-SEL-011: Immediate Entitlement Activation on Payment
- **Module:** Subscription & Billing / Boost Listings
- **Status:** [CONFIRMED]
- **Rule Statement:** Upgraded subscription tiers and boost packages must activate immediately upon receipt of a verified Stripe webhook payment confirmation without manual intervention.
- **Enforcement:** Stripe webhook receiver updates `SellerSubscriptions` and flushes Redis entitlement cache within the same transaction.

### BR-SEL-012: Inbound Financing Lead Isolation
- **Module:** Financing (Seller)
- **Status:** [CONFIRMED]
- **Rule Statement:** Dealers can only view financing leads that originated from equipment listings owned by their specific dealership. General platform financing leads not linked to inventory are withheld.
- **Enforcement:** Financing lead database queries enforce `Listing.SellerId == AuthenticatedUser.SellerId`.

### BR-SEL-013: Delinquent Payment Grace Period & Listing Suspension
- **Module:** Subscription & Billing
- **Status:** [NEEDS VERIFICATION]
- **Rule Statement:** When a subscription renewal invoice fails, the seller account enters a 7-day grace period. If unpaid after 7 days, active listings are automatically shifted from `Active` to `Unpublished / Suspended`.
- **Enforcement:** Scheduled daily billing reconciliation background job.

### BR-SEL-017: Subscription Plan Pricing & Billing Cadence
- **Module:** Subscription & Billing
- **Status:** [CONFIRMED]
- **Rule Statement:** Seller subscription plans are offered under Monthly and Annual billing cadences with predetermined pricing structures and annual discounts:
  - **Monthly Billing:**
    - `Essential Plan`: **$0** (Free).
    - `Pro Plan`: **$149 / month**.
    - `Elite Plan`: **$249 / month**.
  - **Annual Billing (Includes ~17% annual discount):**
    - `Essential Plan`: **$0** (Free).
    - `Pro Plan`: **$1,484 / year** (Discounted from standard base price of $1,788 at ~17% off).
    - `Elite Plan`: **$2,480 / year** (Discounted from standard base price of $2,988 at ~17% off).
- **Enforcement:** The subscription checkout UI displays toggleable Monthly/Annual pricing with calculated savings; Stripe Price IDs map strictly to these amounts.

### BR-SEL-018: Promo Code Redemption & Discount Calculation
- **Module:** Subscription & Billing / Checkout
- **Status:** [CONFIRMED]
- **Rule Statement:** Sellers can apply promotional discount coupon codes during checkout for both Monthly and Annual paid plans:
  - Promo codes support two distinct discount mechanisms:
    1. **Percentage Discount (`%`):** Deducts a designated percentage from the recurring invoice total.
    2. **Fixed Amount Discount (`$`):** Deducts a specified flat dollar value from the invoice amount.
  - Promo code discounts can never reduce an invoice total below $0.00.
- **Enforcement:** Checkout wizard validates coupon validity via API call before checkout finalization; discounts are registered and applied at the Stripe subscription/invoice item level.

### BR-SEL-019: Subscription Plan Upgrade, Downgrade & Cancellation Policy
- **Module:** Subscription & Billing
- **Status:** [CONFIRMED]
- **Rule Statement:** Tier transitions and plan cancellations follow clear lifecycle and proration boundaries:
  - **Immediate Upgrade:** A seller can upgrade to any higher tier at any time. The upgrade takes effect immediately upon successful payment verification.
  - **Scheduled Downgrade:** When a seller downgrades to a lower-tier plan, the downgrade does **not** take effect immediately; current plan privileges remain active until the end of the current paid billing period (`effectiveEndOfPeriod`), transitioning at the next billing renewal date.
  - **Instant Cancellation:** The platform provides an option for immediate subscription cancellation (`cancelImmediately`), terminating access/paid entitlements on demand, alongside standard end-of-period cancellation.
- **Enforcement:** Stripe Subscription update API handles immediate pro-rated upgrades and sets `cancel_at_period_end = true` (or updates upcoming subscription schedule) for downgrades; instant cancellation triggers immediate cancellation via Stripe API and shifts seller role entitlements to the Free Essential plan tier.
