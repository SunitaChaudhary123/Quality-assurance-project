# Test Scenarios: Product Wishlist & Watchlist Management

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-BUY-WSH-01  
**Module:** Wishlist / Watchlist — Save & Manage Favorites  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Wishlist_Scenarios`)*  
**Related Business Rules:** `BR-BUY-001`, `BR-BUY-002`  

---

## 1. Scope & Flow Summary
- **Add Action:** Clicking the heart icon on any equipment card turns the icon **orange** and saves the product to the user's Wishlist.
- **Remove Prompt:** Clicking an already orange heart icon opens a confirmation popup: *"Are you sure you want to remove this product in wishlist?"*.
- **Confirmation Options:** Two buttons inside popup:
  - **Remove:** Confirms removal; icon reverts from orange to default outline; item is removed from Wishlist.
  - **Cancel:** Dismisses popup; heart remains orange; item stays in Wishlist.
- **Guest Restriction:** Unauthenticated users clicking heart are prompted to log in.
- **Status Synchronization:** When seller marks saved listing as "Sold", it remains in Wishlist with a "Sold" badge.

---

## 2. Test Scenario Matrix (`Wishlist_Scenarios`)

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_WSH_001** | Wishlist / Watchlist | Add to Wishlist | Positive | High | Verify adding a product to wishlist when clicking the unselected heart icon on an equipment card. | Heart icon turns orange immediately; product is successfully added to user's Wishlist; success toast message appears. |
| **TS_WSH_002** | Wishlist / Watchlist | Removal Confirmation Modal | UI / Functional | High | Verify clicking an already orange (selected) heart icon displays the removal confirmation popup. | Confirmation popup appears asking: "Are you sure you want to remove this product from your wishlist?" with "Cancel" and "Remove" buttons. |
| **TS_WSH_003** | Wishlist / Watchlist | Confirm Removal | Positive | High | Verify confirming removal by clicking the "Remove" button in the confirmation popup. | Product is removed from user's Wishlist; modal closes; heart icon reverts back from orange to default outline state. |
| **TS_WSH_004** | Wishlist / Watchlist | Cancel Removal | Positive | Medium | Verify cancelling removal by clicking the "Cancel" button in the confirmation popup. | Popup closes immediately without removing product; heart icon remains orange; product stays in Wishlist. |
| **TS_WSH_005** | Wishlist / Watchlist | Guest User Prompt | Negative / Auth | High | Verify clicking the heart icon on any equipment card when user is not logged in (guest state). | System intercepts action and prompts a login/signup modal with message: "Please log in to save items to your wishlist". |
| **TS_WSH_006** | Wishlist / Watchlist | Sold / Inactive Status Sync | Business Logic | Medium | Verify wishlist display persistence when a saved equipment listing is marked as "Sold" or archived by the seller (`BR-BUY-002`). | Item remains visible on the Buyer Wishlist page with a prominent "Sold" badge, disabling direct inquiry actions. |
