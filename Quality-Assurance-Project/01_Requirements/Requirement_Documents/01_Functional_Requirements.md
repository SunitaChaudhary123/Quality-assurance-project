# Functional Requirements Document (FRD)

**Project Name:** MachineryExpo Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** FRD-01-REQ  
**Version:** 2.0  
**Status:** Baseline Specification (Updated per Confirmed Application Modules)

---

## 1. Document Overview & Structure

This document specifies the verified functional requirements of the **MachineryExpo** web application. Requirements are strictly organized into two separate operational domains:
1. **Buyer Side Modules (12 Modules)**
2. **Seller Side Modules (15 Modules)**

Requirement status conventions used:
- **[CONFIRMED]**: Verified by existing frontend routes, backend services, or active QA test suites.
- **[NEEDS VERIFICATION]**: Deduced from system workflows but requires explicit backend/business confirmation for specific parameters or edge rules.

---

## 2. Buyer Side Functional Requirements

### 2.1 Buyer Dashboard
- **Requirement ID:** `FR-BUY-001`
- **Module Name:** Buyer Dashboard
- **User Role:** Authenticated Buyer
- **Purpose:** Centralized landing hub providing account-level overview of buyer activities, active watchlist items, inquiry count, and recent alerts.
- **Available Functionality:**
  - View overall account summary metrics.
  - Shortcut navigation to saved equipment, recent inquiries, and financing submissions.
  - View recent notifications and status changes.
- **Inputs & Validations:**
  - View-only dashboard; interactive filter or date-range selection [NEEDS VERIFICATION].
- **Expected Behavior:**
  - Authenticated buyers landing on `/account` or `/account/dashboard` see personalized counters (e.g., Active Inquiries, Saved Equipment).
  - Unauthenticated users are redirected to login by `buyerGuard`.

### 2.2 Inquiries (Buyer)
- **Requirement ID:** `FR-BUY-002`
- **Module Name:** Inquiries
- **User Role:** Authenticated Buyer, Verified Guest Buyer
- **Purpose:** Management and conversation tracking of equipment inquiries initiated by the buyer to equipment sellers.
- **Available Functionality:**
  - Access inquiry workspace at `/account/Inquiries` in `viewMode: 'buyer'`.
  - View list of initiated inquiries with thumbnail, equipment title, seller name, date, and current status (`Open`, `In Progress`, `Closed`).
  - Open detailed conversation thread (`/account/Inquiries/:id`) and send follow-up text messages.
- **Inputs & Validations:**
  - Reply message text: Non-empty string, maximum 2000 characters.
  - Optional file attachments (e.g. proof of funds or spec queries) [NEEDS VERIFICATION].
- **Expected Behavior:**
  - Buyer message is appended to the thread and triggers an email/dashboard alert to the seller.

### 2.3 Wishlist
- **Requirement ID:** `FR-BUY-003`
- **Module Name:** Wishlist
- **User Role:** Authenticated Buyer
- **Purpose:** Personal collection of bookmarked equipment items for comparison and future purchase monitoring.
- **Available Functionality:**
  - Dedicated page at `/account/watchlist`.
  - Real-time removal of saved items.
  - Display of price drops, sold badges, or availability changes.
  - Direct navigation link to Product Detail Page (PDP).
- **Inputs & Validations:**
  - Listing ID toggle action (`Save` / `Remove`).
- **Expected Behavior:**
  - Toggling bookmark button updates database via `BuyerWishlist` API immediately.
  - Removing an item reflects instantly in the UI list without a page refresh.

### 2.4 Financing Inquiries
- **Requirement ID:** `FR-BUY-004`
- **Module Name:** Financing Inquiries
- **User Role:** Authenticated Buyer
- **Purpose:** Historical tracking and status review of commercial equipment loan applications submitted by the buyer.
- **Available Functionality:**
  - Access dashboard at `/account/finance` (aliases: `/account/finance-inquiries`, `/account/financing-inquiries`).
  - View application records with submission date, requested loan amount, equipment reference, and review status (`Submitted`, `Under Review`, `Approved`, `Declined`).
  - View comprehensive application detail modal/page at `/account/finance/view/:id`.
- **Inputs & Validations:**
  - Read-only review of previously submitted loan parameters.
- **Expected Behavior:**
  - System pulls submitted application details matching the buyer's user ID.
  - Real-time status update indicators when lender status changes.

### 2.5 Get Finance
- **Requirement ID:** `FR-BUY-005`
- **Module Name:** Get Finance
- **User Role:** Public Guest, Authenticated Buyer
- **Purpose:** Multi-step digital loan application wizard enabling buyers to apply for asset financing on selected machinery.
- **Available Functionality:**
  - Accessible via `/financing` or PDP "Get Financing" button.
  - Interactive payment calculator (calculates monthly estimates based on down payment, term, and rate).
  - 3-step application wizard:
    - Step 1: Requested Loan Amount, Term (12–72 months), Estimated Down Payment.
    - Step 2: Business Information (Legal Name, Years in Business, Annual Revenue, EIN/Tax ID).
    - Step 3: Contact details & Digital Credit Check Authorization consent.
- **Inputs & Validations:**
  - Requested Amount: Numeric, minimum $1,000 [NEEDS VERIFICATION].
  - Mandatory Credit Consent checkbox.
  - Valid contact email and phone.
- **Expected Behavior:**
  - For unauthenticated users: Requires contact verification before dispatch.
  - Application dispatches encrypted payload to lending syndication partners.

### 2.6 Support Ticket (Buyer)
- **Requirement ID:** `FR-BUY-006`
- **Module Name:** Support Ticket
- **User Role:** Authenticated Buyer
- **Purpose:** Helpdesk communication channel for buyers to submit complaints, account queries, or platform issues.
- **Available Functionality:**
  - List support tickets at `/account/support-tickets`.
  - View conversation log and agent replies at `/account/support-tickets/:id`.
  - Create new support ticket with subject, department/category, priority, and description.
- **Inputs & Validations:**
  - Subject: Required, 5–100 characters.
  - Category: Dropdown selection (Account, Listing Dispute, Technical Issue).
  - Message Description: Required.
- **Expected Behavior:**
  - Generates unique ticket reference number.
  - Notifies platform support desk; updates ticket status (`Open`, `In Progress`, `Resolved`, `Closed`).

### 2.7 Alert Preferences (Buyer)
- **Requirement ID:** `FR-BUY-007`
- **Module Name:** Alert Preferences
- **User Role:** Authenticated Buyer
- **Purpose:** Configuration of buyer notification channels and frequency.
- **Available Functionality:**
  - Managed under `/account/settings/alerts` (or `/account/notifications`).
  - Toggles for Email, SMS, and In-App notifications.
  - Category-specific alerts: New listings matching preferences, price drops on watchlist, inquiry replies, newsletter/marketing.
- **Inputs & Validations:**
  - Boolean toggle switches.
- **Expected Behavior:**
  - Immediate persistence of user notification preferences in backend user profile.

### 2.8 Switch to Seller
- **Requirement ID:** `FR-BUY-008`
- **Module Name:** Switch to Seller
- **User Role:** Authenticated Buyer
- **Purpose:** Seamless account transition allowing an existing buyer to unlock dealer/seller capabilities without re-registering.
- **Available Functionality:**
  - Accessible at `/account/become-seller`.
  - Selection of seller type (`Individual Seller` or `Equipment Dealership`).
  - Provision of business name, shop details, physical address, and subscription tier selection.
- **Inputs & Validations:**
  - Business Name & Shop Name (mandatory for dealer type).
  - Physical address and postal code.
  - Acceptance of Seller Policy terms.
- **Expected Behavior:**
  - Retains existing user ID and credentials.
  - Associates user with new Seller/Tenant entity and grants `sellerGuard` permissions.

### 2.9 Sell Equipment (Buyer)
- **Requirement ID:** `FR-BUY-009`
- **Module Name:** Sell Equipment
- **User Role:** Authenticated Buyer, Public Guest
- **Purpose:** Direct entry path for casual sellers and individual equipment owners to post single machinery items for sale.
- **Available Functionality:**
  - Accessible via `/create-listing` or `/sell-equipment`.
  - Simplified listing wizard with basic category selection, photos, pricing, and specs.
- **Inputs & Validations:**
  - Standard listing attributes (Category, Title, Year, Make, Model, Price, Location, Photos).
- **Expected Behavior:**
  - Authenticated buyers can submit listing within allowable single-listing limits [NEEDS VERIFICATION].
  - Prompts buyer-to-seller upgrade if multi-listing dealership capabilities or advanced options (rent/auction) are chosen.

### 2.10 Product Search
- **Requirement ID:** `FR-BUY-010`
- **Module Name:** Product Search
- **User Role:** Public Visitor, Authenticated Buyer
- **Purpose:** Keyword-driven full-text discovery of machinery across the marketplace catalog.
- **Available Functionality:**
  - Global search bar on header and `/equipment` catalog page.
  - Real-time search suggestions / autocomplete [NEEDS VERIFICATION].
  - Keyword matching across Title, Description, Make, Model, and Serial Number.
- **Inputs & Validations:**
  - Search term: String, sanitized against injection.
- **Expected Behavior:**
  - Returns matching products sorted by relevance or newest first.
  - Updates browser URL query param (`?q=keyword`) for shareable links.

### 2.11 Filter
- **Requirement ID:** `FR-BUY-011`
- **Module Name:** Filter
- **User Role:** Public Visitor, Authenticated Buyer
- **Purpose:** Multi-faceted categorization and narrowing of equipment inventory.
- **Available Functionality:**
  - Interactive sidebar filters on `/equipment`.
  - Transaction Type tabs (`Sell`, `Rent`, `Auction`, `All`).
  - Category hierarchy selection modal ("Browse all categories").
  - Range sliders / inputs: Price (Min/Max), Year (Min/Max), Hours (Min/Max).
  - Facets for Manufacturer / Make, Model, Condition (New/Used), and Location radius.
  - Sorting: Lowest Price, Highest Price, Newest First, Lowest Hours, Highest Hours.
- **Inputs & Validations:**
  - Numeric validation on min/max ranges (Min <= Max).
- **Expected Behavior:**
  - Asynchronously re-fetches products without page refresh.
  - Displays active filter badges/chips with one-click clear options.

### 2.12 My Account (Buyer)
- **Requirement ID:** `FR-BUY-012`
- **Module Name:** My Account
- **User Role:** Authenticated Buyer
- **Purpose:** Management of core buyer profile, personal contact information, security credentials, and session controls.
- **Available Functionality:**
  - Located at `/account/settings` and `/account/profile`.
  - Update First Name, Last Name, Primary Phone Number, and Avatar.
  - Change Password and manage Multi-Factor Authentication (MFA / TOTP).
  - View login history and active devices [NEEDS VERIFICATION].
- **Inputs & Validations:**
  - Password change requires current password + new password meeting complexity rules.
  - Phone update requires re-verification via SMS OTP.
- **Expected Behavior:**
  - Profile changes persist to Identity store; cached claims update upon next refresh.

---

## 3. Seller Side Functional Requirements

### 3.1 Seller Dashboard
- **Requirement ID:** `FR-SEL-001`
- **Module Name:** Seller Dashboard
- **User Role:** Authenticated Seller, Dealer Admin, Staff
- **Purpose:** High-level operational command center for dealers and sellers.
- **Available Functionality:**
  - Accessible at `/seller` and `/seller/dashboard`.
  - Key performance indicators: Total Active Listings, Total Leads / Inquiries, Phone Call Clicks, Profile Views [NEEDS VERIFICATION].
  - Quick action links: "Add Listing", "Manage Inventory", "Upgrade Plan".
  - Recent activity feed and incoming inquiry lead previews.
- **Inputs & Validations:**
  - View-only dashboard with role-based component rendering.
- **Expected Behavior:**
  - Data isolated strictly to the seller's specific tenant and dealership ID.

### 3.2 Sell Equipment (Seller)
- **Requirement ID:** `FR-SEL-002`
- **Module Name:** Sell Equipment
- **User Role:** Authenticated Seller / Dealer with `Listing.Create` permission
- **Purpose:** Commercial equipment creation wizard tailored for multi-inventory dealers.
- **Available Functionality:**
  - Accessible at `/seller/product/add` (multi-step wizard: `/overview`, `/specs`, `/media`, `/pricing`, `/location`, `/review`).
  - Multi-classification selection: `Sell`, `Rent`, `Auction` (enforced by plan tier).
  - Dynamic category-specific technical attributes (e.g. Engine Model, Operating Weight, Tire Tread %).
  - Multi-image drag-and-drop upload, primary image selector, PDF spec sheet attachment.
- **Inputs & Validations:**
  - Mandatory fields: Title, Category, Manufacturer, Model, Year, Price, Equipment Location.
  - Image requirement: Minimum 1 photo.
- **Expected Behavior:**
  - Auto-saves draft progress.
  - Checks remaining plan quota via `sellerPlanLimitGuard`; blocks progression if quota exhausted.

### 3.3 Inventory
- **Requirement ID:** `FR-SEL-003`
- **Module Name:** Inventory
- **User Role:** Seller, Dealer Admin, Staff with `Inventory.Read` permission
- **Purpose:** Comprehensive tabular listing management interface for monitoring, filtering, and bulk managing stock.
- **Available Functionality:**
  - Located at `/inventory` and `/seller/products`.
  - Status tabs: `All`, `Active`, `Draft`, `Under Offer`, `Sold`, `Archived`.
  - Search within inventory by Title, Serial Number, or Stock ID.
  - Action menu per listing: Edit, View Live PDP, Boost Listing, Mark as Sold, Archive, Delete.
- **Inputs & Validations:**
  - Status transition confirmation dialogs.
- **Expected Behavior:**
  - Marking as Sold keeps listing in seller inventory but tags or unpublishes it from public search results.
  - Deleting shifts item to archive or performs soft-delete.

### 3.4 Yards and Location
- **Requirement ID:** `FR-SEL-004`
- **Module Name:** Yards and Location
- **User Role:** Seller, Dealer Admin
- **Purpose:** Management of multiple physical equipment storage facilities, dealership branches, and display yards.
- **Available Functionality:**
  - Located at `/seller/locations`.
  - Add, edit, and delete physical yard locations.
  - Set Primary / Headquarter yard location.
  - Yard details: Yard Name, Contact Person, Direct Phone, Street Address, City, State/Province, Postal Code, Country, Operating Hours.
- **Inputs & Validations:**
  - Yard Name and Street Address: Required.
  - Postal Code: Validated against country format.
- **Expected Behavior:**
  - When creating equipment listings, sellers can select from saved Yards to automatically populate equipment location.

### 3.5 Boost Listings
- **Requirement ID:** `FR-SEL-005`
- **Module Name:** Boost Listings
- **User Role:** Seller, Dealer Admin
- **Purpose:** Paid listing promotion module to increase visibility and lead generation.
- **Available Functionality:**
  - Located at `/seller/boost` (alias: `/seller/boost-listing`).
  - Selection of boost packages (e.g. 7-Day Homepage Featured, Top of Category Banner, Urgent Badge).
  - Selection of target listing from seller inventory.
  - Integration with Stripe checkout for boost package settlement.
- **Inputs & Validations:**
  - Target listing must be currently `Active`.
- **Expected Behavior:**
  - Upon successful payment, promotional badge is displayed on card and search algorithm ranking is elevated until boost duration expires.

### 3.6 Inquiries (Seller)
- **Requirement ID:** `FR-SEL-006`
- **Module Name:** Inquiries
- **User Role:** Seller, Dealer Admin, Staff with `Inquiry.Read` / `Inquiry.Reply` permissions
- **Purpose:** Lead management workspace for reviewing and responding to prospective buyers.
- **Available Functionality:**
  - Located at `/seller/Inquiries` in `viewMode: 'seller'`.
  - Table of incoming leads: Buyer Name, Equipment Name, Inquiry Type (Price, Inspection, General), Date Received, Read/Unread state.
  - Detailed conversation thread view at `/seller/Inquiries/:id`.
  - In-app text reply capability with message timestamps.
- **Inputs & Validations:**
  - Reply text: Mandatory, non-empty.
- **Expected Behavior:**
  - Seller reply dispatches an immediate notification to the buyer and marks the lead as `Responded`.

### 3.7 Call & Line
- **Requirement ID:** `FR-SEL-007`
- **Module Name:** Call & Line
- **User Role:** Seller, Dealer Admin
- **Purpose:** Telephony integration and call-tracking settings connecting buyer phone inquiries to dealer sales desks.
- **Available Functionality:**
  - Located at `/seller/calls`.
  - Call Log table displaying Call Date/Time, Caller Phone, Duration, Call Status (`Completed`, `Missed`, `Voicemail`), Call Direction (`Inbound`).
  - Forwarding phone number configuration (Twilio-backed virtual tracking number routing to dealer line).
  - Business hours schedule management (Monday–Sunday open/closed hours and timezone selection).
  - Call recording toggle [NEEDS VERIFICATION].
- **Inputs & Validations:**
  - Forwarding Phone Number: Valid E.164 phone format.
  - Timezone: Valid IANA timezone identifier.
- **Expected Behavior:**
  - Inbound calls to tracking numbers log call records and route audio according to business hours schedule.

### 3.8 Subscription & Billing
- **Requirement ID:** `FR-SEL-008`
- **Module Name:** Subscription & Billing
- **User Role:** Dealer Owner / Admin with `Payment.Read` and `Payment.Manage` permissions
- **Purpose:** Monetization management, tier subscription selection, and renewal handling.
- **Available Functionality:**
  - Located at `/seller/subscription`.
  - View current active plan (Essential, Pro, Elite), renewal date, and billing frequency (Monthly/Annual).
  - Usage meters: Active Listings vs Plan Limit, Staff Seats Used vs Plan Limit.
  - Upgrade/Downgrade plan workflow with Stripe Checkout / Customer Portal integration.
  - Cancel subscription workflow.
- **Inputs & Validations:**
  - Credit card tokenization via Stripe Elements.
- **Expected Behavior:**
  - Plan changes immediately recalculate feature entitlements and listing quotas.

### 3.9 Invoices
- **Requirement ID:** `FR-SEL-009`
- **Module Name:** Invoices
- **User Role:** Dealer Admin with `Payment.Read` permission
- **Purpose:** Accounting ledger and receipt repository for tax reconciliation.
- **Available Functionality:**
  - Located at `/seller/invoices`.
  - Chronological list of billing charges (Subscription renewals, Boost purchases, Ad campaigns).
  - Attributes: Invoice Number, Date, Description, Amount, Payment Method, Status (`Paid`, `Pending`, `Failed`).
  - Download official PDF invoice / receipt.
- **Inputs & Validations:**
  - Read-only historical ledger.
- **Expected Behavior:**
  - Invoice PDF download pulls verified tax invoice from Stripe or billing service.

### 3.10 Staff Management
- **Requirement ID:** `FR-SEL-010`
- **Module Name:** Staff Management
- **User Role:** Dealer Admin with `Staff.Read` / `Staff.Create` / `Staff.Update` permissions
- **Purpose:** Multi-user team delegation within a single dealership organization.
- **Available Functionality:**
  - Located at `/seller/team`.
  - View active staff roster with Name, Email, Assigned Role, Status (`Active`, `Invited`, `Suspended`).
  - Add new staff member at `/seller/team/add` (guarded by `sellerStaffPlanLimitGuard`).
  - Edit staff details and permissions at `/seller/team/edit/:id`.
  - Revoke or suspend staff access.
- **Inputs & Validations:**
  - First Name, Last Name, Email, Assigned Role.
  - Cannot add staff if organization has reached the plan seat limit.
- **Expected Behavior:**
  - System sends invitation email with activation link to the newly added staff member.

### 3.11 My Support Ticket (Seller)
- **Requirement ID:** `FR-SEL-011`
- **Module Name:** My Support Ticket
- **User Role:** Seller, Dealer Admin, Staff
- **Purpose:** Dedicated dealer support desk to resolve technical issues, billing disputes, or listing questions.
- **Available Functionality:**
  - Located at `/seller/support-tickets` (alias: `/seller/support`).
  - Submit support ticket with Priority (`Low`, `Medium`, `High`, `Urgent`).
  - View status (`Open`, `Awaiting Seller Response`, `Closed`).
  - Reply to support tickets with attachments.
- **Inputs & Validations:**
  - Ticket Subject, Issue Type, Description.
- **Expected Behavior:**
  - Dedicated queue prioritized by seller subscription tier (Elite tier receives priority SLA).

### 3.12 Alert Preference (Seller)
- **Requirement ID:** `FR-SEL-012`
- **Module Name:** Alert Preference
- **User Role:** Seller, Dealer Admin, Staff
- **Purpose:** Operational alert settings for business leads and platform updates.
- **Available Functionality:**
  - Managed via `/seller/settings/alerts` or `/seller/notifications`.
  - Notification triggers: New Buyer Inquiries, Missed Calls, Listing Expirations, Subscription Billing notices.
  - Delivery channels: In-app banner, Email, SMS alerts to sales manager.
- **Inputs & Validations:**
  - Toggle switches per event category.
- **Expected Behavior:**
  - System immediately routes inbound leads based on configured seller alert rules.

### 3.13 My Account (Seller)
- **Requirement ID:** `FR-SEL-013`
- **Module Name:** My Account
- **User Role:** Seller, Dealer Admin, Staff
- **Purpose:** Dual-layer profile management covering personal credentials and dealership public storefront settings.
- **Available Functionality:**
  - Located at `/seller/profile` and `/seller/account-settings`.
  - User Profile: Personal name, login email, password, MFA security.
  - Dealership Profile: Dealership Legal Name, Display Name, Dealer License / Tax ID, Official Website URL, Company Bio, Logo and Banner media.
- **Inputs & Validations:**
  - Website URL: Valid URL format.
  - Dealership Name: Required, 2–100 characters.
- **Expected Behavior:**
  - Updates to Dealership Profile immediately update the public dealer storefront page (`/dealers/:slug`).

### 3.14 Financing (Seller)
- **Requirement ID:** `FR-SEL-014`
- **Module Name:** Financing
- **User Role:** Seller, Dealer Admin
- **Purpose:** Seller-side visibility into buyer financing applications tied to the seller's specific inventory.
- **Available Functionality:**
  - Located at `/seller/financing` or `/seller/finance-leads` [NEEDS VERIFICATION].
  - View financing leads generated from equipment listings owned by this dealer.
  - Monitor status of loan approvals to facilitate machinery handover.
  - Opt-in / Opt-out of offering platform financing on specific listings [NEEDS VERIFICATION].
- **Inputs & Validations:**
  - Read-only lead tracking interface.
- **Expected Behavior:**
  - When a buyer submits a financing application referencing a seller's listing, a finance lead record appears in the seller dashboard.

### 3.15 Search and Filter Product (Seller)
- **Requirement ID:** `FR-SEL-015`
- **Module Name:** Search and Filter Product
- **User Role:** Seller, Dealer Admin, Staff
- **Purpose:** Internal catalog search and inventory filtering within the seller's private stock and cross-tenant listings.
- **Available Functionality:**
  - Integrated into `/inventory` and `/seller/products`.
  - Filter inventory by Category, Yard / Location, Transaction Type (`Sell`, `Rent`, `Auction`), Price Range, and Publication Status.
  - Instant text filter by SKU, Stock Number, or VIN.
  - Quick-sort by Date Added, Asking Price, and Total Leads Generated.
- **Inputs & Validations:**
  - Filter parameters matching inventory columns.
- **Expected Behavior:**
  - Allows rapid retrieval of equipment records for editing or customer phone inquiry lookups.
