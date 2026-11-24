# Requirement Analysis Document (RAD)

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** RAD-02-ANALYSIS  
**Version:** 2.0  
**Status:** Approved for QA Test Design

---

## 1. Document Scope

This document provides a systematic requirement analysis for all confirmed modules of MachineryExpo, maintaining strict separation between **Buyer Side Modules** and **Seller Side Modules**.

Every module is analyzed under:
- Feature Description & Purpose
- User Roles & Access Boundaries
- Input Requirements & Validations
- Expected Behavior
- Technical Dependencies
- Ambiguities & Unclear Requirements (**[NEEDS VERIFICATION]**)

---

## 2. Buyer Side Requirement Analysis

### RA-BUY-01: Buyer Dashboard
- **Feature Description:** Personalized landing hub providing aggregated counters and quick navigation to buyer activities.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** None (read-only analytical view).
- **Expected Behavior:** Renders dynamic count badges for Active Inquiries, Saved Equipment, and Financing Applications upon page load.
- **Validations:** Route protected by `buyerGuard`. Unauthenticated access redirects to login.
- **Dependencies:** Buyer API service, Redis cached user summaries.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Is there a configurable date-range filter for dashboard metrics or is it an all-time aggregate?

### RA-BUY-02: Inquiries (Buyer)
- **Feature Description:** Multi-thread inquiry management interface for buyers to track dialogues with equipment dealers.
- **User Roles:** Authenticated Buyer, Verified Guest.
- **Input Requirements:** Inquiry reply text (String), optional attachments [NEEDS VERIFICATION].
- **Expected Behavior:** Message sent via `/account/Inquiries/:id` appends to thread and dispatches notification to seller.
- **Validations:** Message length 1 to 2000 characters; whitespace-only strings rejected.
- **Dependencies:** Inquiries API, SMTP notification service, Twilio SMS.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does the buyer inquiry interface support real-time WebSocket updates, or does it require polling / manual refresh?

### RA-BUY-03: Wishlist
- **Feature Description:** Bookmark system allowing buyers to save equipment for comparison and price drop tracking.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** Equipment Product ID toggle.
- **Expected Behavior:** Toggling heart button creates or deletes record in `BuyerWishlist` table with instant UI state sync.
- **Validations:** Duplicate saves prevented by composite key `(UserId, ProductId)`.
- **Dependencies:** Buyer Account API, Product Catalog service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - What is the maximum number of items allowed in a buyer's wishlist? *(Assumed unlimited).*

### RA-BUY-04: Financing Inquiries
- **Feature Description:** Historical record portal of credit/loan applications submitted by the buyer.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** Application ID for detail lookup.
- **Expected Behavior:** Displays list of submitted applications with status badges (`Submitted`, `Under Review`, `Approved`, `Declined`).
- **Validations:** Access restricted to applicant's own user token.
- **Dependencies:** Financing Engine API, Financial Syndication Webhooks.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can buyers upload supplementary financial documents (e.g. tax returns) after initial application submission?

### RA-BUY-05: Get Finance
- **Feature Description:** 3-step digital application wizard connecting machinery buyers to commercial lenders.
- **User Roles:** Public Guest, Authenticated Buyer.
- **Input Requirements:** Loan amount, term length, down payment, business legal name, EIN, annual revenue, signer contact info, credit authorization consent.
- **Expected Behavior:** Client-side rate calculator updates monthly payment estimate. Submitting triggers lead generation to partner lenders.
- **Validations:** Loan amount > 0; mandatory legal credit consent check; phone/email validation.
- **Dependencies:** Financing partner API, Twilio OTP (for guest applications).
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does the rate calculator pull live dynamic interest rates from lenders or use static tenant benchmark rates?

### RA-BUY-06: Support Ticket (Buyer)
- **Feature Description:** Helpdesk ticketing system for buyers encountering account, payment, or seller disputes.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** Ticket Subject, Issue Department, Priority, Description, File Attachments.
- **Expected Behavior:** Generates ticket ID (e.g., `TKT-1082`); buyer receives tracking confirmation email.
- **Validations:** Subject 5–100 chars; description mandatory; attachments restricted to image/PDF <= 5MB.
- **Dependencies:** Ticketing service, Platform Admin Helpdesk queue.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can a closed ticket be reopened by the buyer, or must a new ticket be created?

### RA-BUY-07: Alert Preferences (Buyer)
- **Feature Description:** Notification settings management allowing buyers to configure push, email, and SMS triggers.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** Boolean toggles for notification categories.
- **Expected Behavior:** Switches immediately persist via PATCH request to `/api/buyer/notification-settings`.
- **Validations:** None.
- **Dependencies:** User Profile API, Notification dispatch engine.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Are SMS alerts available for all global country codes, or limited to US/Canada numbers?

### RA-BUY-08: Switch to Seller
- **Feature Description:** Upgrade workflow allowing buyers to transition their account into an equipment dealership.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** Seller type (`Individual` vs `Dealer`), Business Name, Shop Name, Address, Subscription tier.
- **Expected Behavior:** Preserves user identity; creates Seller record and provisions tenant privileges without forcing re-login.
- **Validations:** Mandatory business details for dealer type; unique shop slug.
- **Dependencies:** Identity API, Seller Management service, Subscription Billing.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Is admin approval required to activate the seller account post-switch, or is it instantly active upon plan selection?

### RA-BUY-09: Sell Equipment (Buyer)
- **Feature Description:** Entry portal for casual or one-off equipment sellers to post single machinery items.
- **User Roles:** Authenticated Buyer, Public Guest.
- **Input Requirements:** Basic equipment specs (Title, Category, Year, Make, Model, Price, Location, Photos).
- **Expected Behavior:** Submits listing under individual account; prompts upgrade to Dealer tier if user requests commercial multi-listing features.
- **Validations:** Core listing validations (Price > 0, Year <= Current Year + 1, at least 1 photo).
- **Dependencies:** Listing wizard engine, Image uploader service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does a buyer have a free single-listing quota, or is a one-time listing fee charged upon publishing?

### RA-BUY-10: Product Search
- **Feature Description:** Full-text search engine querying equipment titles, descriptions, manufacturers, and models.
- **User Roles:** Public Visitor, Authenticated Buyer.
- **Input Requirements:** Search keyword query string (`q`).
- **Expected Behavior:** Real-time query submission returns catalog results matching keyword with search term highlighting.
- **Validations:** Input sanitization to strip script tags and SQL injections.
- **Dependencies:** Full-text search index / SQL Server catalog query engine.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does search support fuzzy matching/typo tolerance for manufacturer names (e.g., "Komatsu" vs "Kamatsu")?

### RA-BUY-11: Filter
- **Feature Description:** Dynamic category-specific faceted filter sidebar with numeric sliders and multi-select chips.
- **User Roles:** Public Visitor, Authenticated Buyer.
- **Input Requirements:** Categories, Transaction Type (`Sell`, `Rent`, `Auction`), Price Range, Year Range, Hours Range, Location Radius.
- **Expected Behavior:** Changing any filter updates the product catalog grid via asynchronous API call and updates URL query parameters.
- **Validations:** Min values must not exceed Max values.
- **Dependencies:** Catalog Facet API service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Is location radius search calculated from browser GPS or strictly user-entered postal code?

### RA-BUY-12: My Account (Buyer)
- **Feature Description:** Buyer personal identity, security credentials, and communication settings.
- **User Roles:** Authenticated Buyer.
- **Input Requirements:** First Name, Last Name, Phone, Password, MFA Secret.
- **Expected Behavior:** Profile updates save to Identity database; changing password invalidates all other active sessions.
- **Validations:** Password complexity rules; phone verification via OTP on change.
- **Dependencies:** ASP.NET Identity, Twilio OTP.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does changing account email require re-verification on both old and new email addresses?

---

## 3. Seller Side Requirement Analysis

### RA-SEL-01: Seller Dashboard
- **Feature Description:** Dealership mission control center displaying performance metrics, listing health, and operational alerts.
- **User Roles:** Authenticated Seller, Dealer Admin, Staff.
- **Input Requirements:** None (read-only analytical interface).
- **Expected Behavior:** Renders KPI cards: Active Listings, Inquiries Received, Phone Clicks, and Subscription Status.
- **Validations:** Multi-tenant isolation: strict filtering by authenticated user's `TenantId` and `SellerId`.
- **Dependencies:** Analytics API, Telephony service, Listing repository.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - How frequently are seller dashboard metrics refreshed (real-time vs 15-minute background aggregation)?

### RA-SEL-02: Sell Equipment (Seller)
- **Feature Description:** Comprehensive multi-step commercial listing creation wizard with plan-tier restriction enforcement.
- **User Roles:** Dealer Admin, Staff with `Listing.Create` permission.
- **Input Requirements:** Classification (`Sell`, `Rent`, `Auction`), Category, Dynamic Specs, Media Assets, Price/Lease terms, Yard/Location.
- **Expected Behavior:** Guides user through steps; verifies remaining plan balance; blocks Auction/Rent selection if seller plan is Essential.
- **Validations:** All mandatory attributes populated; min 1 photo; price > 0.
- **Dependencies:** Listing API, Plan limit guard (`sellerPlanLimitGuard`), Media upload CDN.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Is there an automated image background removal or watermarking tool applied during image upload?

### RA-SEL-03: Inventory
- **Feature Description:** Full tabular catalog of all inventory items owned by the dealership across all lifecycle statuses.
- **User Roles:** Dealer Admin, Staff with `Inventory.Read` permission.
- **Input Requirements:** Status filter selection, search string, action triggers (Edit, Sold, Archive, Delete).
- **Expected Behavior:** Paginated inventory table with thumbnail, title, stock number, price, status, views, and action dropdown.
- **Validations:** Status transitions require confirmation; items with active bids cannot be marked as sold or deleted [NEEDS VERIFICATION].
- **Dependencies:** Product Inventory service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does the system support bulk inventory actions (e.g. bulk price increase or bulk archive)?

### RA-SEL-04: Yards and Location
- **Feature Description:** Management of physical dealership storage yards, branch locations, and equipment pickup points.
- **User Roles:** Dealer Admin.
- **Input Requirements:** Yard Name, Contact Name, Direct Phone, Street Address, City, State, Postal Code, Country, Operating Hours.
- **Expected Behavior:** Creates yard record in `SellerLocations`; newly created yards become selectable in the listing wizard.
- **Validations:** Yard Name and Address mandatory; valid phone format.
- **Dependencies:** Seller Location service, Geocoding mapping API.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does the plan tier enforce a maximum number of yard locations a dealer can add?

### RA-SEL-05: Boost Listings
- **Feature Description:** Paid listing promotion engine enabling sellers to purchase featured placement and category banners.
- **User Roles:** Dealer Admin with `Payment.Manage` permission.
- **Input Requirements:** Listing selection, Boost package selection, Payment method.
- **Expected Behavior:** Integrates with Stripe; on payment success, adds promotion badge and increases listing ranking in catalog queries.
- **Validations:** Selected listing must be in `Active` status.
- **Dependencies:** Stripe Checkout API, Catalog Search Index.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Do boost durations stack if a seller purchases multiple boosts for the same listing?

### RA-SEL-06: Inquiries (Seller)
- **Feature Description:** Commercial lead management portal for receiving, filtering, and answering prospective buyer messages.
- **User Roles:** Dealer Admin, Staff with `Inquiry.Read` / `Inquiry.Reply` permissions.
- **Input Requirements:** Inquiry reply message, status change dropdown (`Open`, `Contacted`, `Qualified`, `Closed`).
- **Expected Behavior:** Displays inbound buyer leads; reply dispatches email/notification to buyer and updates lead timestamp.
- **Validations:** Reply text required (1–2000 characters).
- **Dependencies:** Inquiries service, Notification dispatcher.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can dealer staff reassign an inquiry lead to another internal team member?

### RA-SEL-07: Call & Line
- **Feature Description:** Virtual call tracking and telephony configuration connecting buyer phone inquiries to dealer sales desks.
- **User Roles:** Dealer Admin.
- **Input Requirements:** Destination forwarding phone number, weekly operating schedule (Mon–Sun open/close times), Timezone.
- **Expected Behavior:** Logs incoming calls via Twilio call webhook; routes audio to forwarding number; logs duration and caller ID.
- **Validations:** Valid E.164 phone number; valid timezone selection.
- **Dependencies:** Twilio Voice API, Telephony DB service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Are voice call audio recordings accessible directly in the call log table?

### RA-SEL-08: Subscription & Billing
- **Feature Description:** Dealer subscription plan governance, tier upgrade/downgrade management, and renewal billing.
- **User Roles:** Dealer Owner / Admin with `Payment.Manage` permission.
- **Input Requirements:** Plan selection (Essential, Pro, Elite), Billing cycle (Monthly/Annual), Credit card credentials.
- **Expected Behavior:** Interacts with Stripe Subscriptions; immediate prorated charge on upgrade; feature quotas updated in real time.
- **Validations:** Card validation via Stripe Elements; downgrade blocked if active listings exceed target plan cap.
- **Dependencies:** Stripe API, Webhook receiver, `sellerPlanLimitGuard`.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - If a downgrade is initiated, does it take effect immediately or at the end of the current billing cycle?

### RA-SEL-09: Invoices
- **Feature Description:** Complete chronological history of all dealership billing transactions, subscription invoices, and boost receipts.
- **User Roles:** Dealer Admin with `Payment.Read` permission.
- **Input Requirements:** None (read-only accounting view).
- **Expected Behavior:** Displays list of past payments; clicking "Download" retrieves authenticated PDF tax invoice.
- **Validations:** Access restricted to dealership organization billing admins.
- **Dependencies:** Stripe Invoicing API, PDF generation service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Are invoices automatically emailed to the dealer's accounts-payable email address upon billing execution?

### RA-SEL-10: Staff Management
- **Feature Description:** Multi-user team delegation permitting dealers to invite and manage secondary staff accounts with granular permissions.
- **User Roles:** Dealer Admin with `Staff.Manage` permission.
- **Input Requirements:** First Name, Last Name, Email, Assigned Role (`Sales Representative`, `Inventory Manager`, `Finance Officer`).
- **Expected Behavior:** Verifies available seat balance on plan (`sellerStaffPlanLimitGuard`); sends invitation email with secure activation link.
- **Validations:** Unique email; mandatory role assignment; blocked if seat quota reached.
- **Dependencies:** Identity User Management API, Invitation email service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can custom roles with arbitrary permission checkboxes be created, or are roles fixed to standard presets?

### RA-SEL-11: My Support Ticket (Seller)
- **Feature Description:** Dedicated B2B support desk for dealers to report technical issues, inventory bugs, or billing disputes.
- **User Roles:** Dealer Admin, Staff.
- **Input Requirements:** Subject, Category (`Technical`, `Billing`, `Listing Issue`), Priority (`Low`, `Medium`, `High`, `Urgent`), Description, Attachments.
- **Expected Behavior:** Generates priority ticket in platform support queue; Elite plan dealers receive accelerated SLA response.
- **Validations:** Subject and description mandatory.
- **Dependencies:** Platform Support Ticketing service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - What are the SLA response time commitments per seller subscription tier?

### RA-SEL-12: Alert Preference (Seller)
- **Feature Description:** Comprehensive notification routing rules for dealership operational events.
- **User Roles:** Dealer Admin, Staff.
- **Input Requirements:** Toggle switches for notification categories (Inquiries, Missed Calls, Listing Approvals, Billing Notices).
- **Expected Behavior:** Saves notification preferences immediately; backend routes alerts according to configured channels (Email, SMS, Dashboard).
- **Validations:** None.
- **Dependencies:** Notification preference service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can alert notifications be routed to multiple email addresses simultaneously (e.g. sales@dealership.com)?

### RA-SEL-13: My Account (Seller)
- **Feature Description:** Dealership business identity configuration and storefront branding settings.
- **User Roles:** Dealer Admin.
- **Input Requirements:** Business Name, Display Name, Dealer License Number, Business Phone, Official Website, Company Bio, Logo, Banner Image.
- **Expected Behavior:** Updates dealer profile in database; changes immediately reflect on public dealer page (`/dealers/:slug`).
- **Validations:** Website must be a valid URL; logo must meet image format and size constraints (<= 5MB).
- **Dependencies:** Seller Profile service, Image CDN.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Is dealer license verification performed manually by platform admins before displaying a "Verified Dealer" badge?

### RA-SEL-14: Financing (Seller)
- **Feature Description:** Seller-side visibility and tracking of buyer financing applications initiated on the dealer's equipment inventory.
- **User Roles:** Dealer Admin, Finance Staff.
- **Input Requirements:** Application lookup filters (Status, Date range, Equipment reference).
- **Expected Behavior:** Displays inbound financing leads; allows dealers to view approval status from lenders to coordinate equipment delivery.
- **Validations:** View restricted to applications referencing listings owned by the logged-in dealer.
- **Dependencies:** Financing Lead service.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Does the dealer receive a notification when a buyer's financing application moves to `Approved`?

### RA-SEL-15: Search and Filter Product (Seller)
- **Feature Description:** Internal inventory search and filtering tool for dealers managing extensive machinery fleets.
- **User Roles:** Dealer Admin, Staff.
- **Input Requirements:** Search keyword (VIN, Stock Number, Title), Filter attributes (Yard Location, Category, Listing Status, Price Range).
- **Expected Behavior:** Filters the dealer's private inventory list instantly; does not mix listings from other dealerships.
- **Validations:** Query input sanitization.
- **Dependencies:** Seller Inventory API.
- **Unclear Requirements [NEEDS VERIFICATION]:**
  - Can inventory search results be exported to CSV or Excel for dealership reporting?
