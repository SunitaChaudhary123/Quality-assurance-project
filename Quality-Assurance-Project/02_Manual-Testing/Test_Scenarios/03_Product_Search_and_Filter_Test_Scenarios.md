# Test Scenarios: Product Search, Filter & Sort

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-BUY-FLT-01  
**Module:** Product Catalog — Search, Filters & Sorting  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Search_and_Filter_Scenarios`)*  
**Related Requirements:** `FR-BUY-009`, `BR-BUY-008`  

---

## 1. UI Elements & Scope Identified from Screenshots
- **Transaction Tabs:** Sell, Rent, Auction, All.
- **Left Filter Sidebar:**
  - Category (Search categories, checkboxes, count tags, "Browse all 162 categories").
  - Manufacturer (Checkboxes, count tags, "Browse all 13 manufacturers").
  - Condition (Good, Like New, Refurbished, "Browse all 6 conditions").
  - Cab Style (OROPS, Enclosed with AC).
  - State & Country filters.
  - Specifications Accordions: Year, Odometer, Price, Published Date, Horsepower, Gross Weight, VIN.
  - Action Buttons: "Clear all", "More Filters", Quick search buttons (By Category, By Manufacturer, By State, By Specs).
- **Sort By Modal:**
  - Quick Sort: Nearest Location, Recently Added.
  - Attributes: Price (Lowest/Highest), Mileage, Equipment Age (Newest/Oldest), Hours, Horsepower, Lift Capacity.
  - Actions: "Clear All", "Apply".
- **View Toggles:** Grid View icon vs List View icon.

---

## 2. Test Scenario Matrix (`Search_and_Filter_Scenarios`)

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_FLT_001** | Product Catalog | Transaction Tabs | Positive | High | Verify filtering listings by transaction type tabs ("Sell", "Rent", "Auction", "All"). | Listing grid and count refresh to display only equipment matching the active transaction tab. |
| **TS_FLT_002** | Product Catalog | Category Filter | Positive | High | Verify filtering by Category checkbox (e.g., Knuckle Boom Cranes) and keyword search within category box. | Results grid shows only items in selected category; search input dynamically filters category list. |
| **TS_FLT_003** | Product Catalog | Manufacturer Filter | Positive | High | Verify multi-select filtering by Manufacturer (e.g., Caterpillar, Volvo Trucks). | Listings display only equipment manufactured by selected brands, matching counts on checkboxes. |
| **TS_FLT_004** | Product Catalog | Condition & Specs | Positive | Medium | Verify filtering by Condition ("Good", "Like New", "Refurbished") and Cab Style ("OROPS", "Enclosed with AC"). | Listings update to display only machinery meeting the specified condition and cab criteria. |
| **TS_FLT_005** | Product Catalog | Location Filter | Positive | Medium | Verify geographical filtering by State and Country accordions. | Equipment cards filter by selected state/country; card location tags match selected location. |
| **TS_FLT_006** | Product Catalog | Numerical Filters | Boundary / Positive | High | Verify range filtering across Year, Price, Odometer, and Horsepower accordion filters. | Only listings within specified numeric ranges (min/max) are returned in the catalog view. |
| **TS_FLT_007** | Product Catalog | More Filters Modal | Functional | Medium | Verify opening and applying extended parameters via the "More Filters" modal dialog. | Extended filter modal opens smoothly; applying filters updates the listing grid and total count. |
| **TS_FLT_008** | Product Catalog | Combined Filters | Positive | High | Verify applying multiple simultaneous filters (e.g., Sell tab + Caterpillar + 2018-2023 + Good condition). | Results satisfy all combined filter conditions concurrently (AND condition); listing count updates accurately. |
| **TS_FLT_009** | Product Catalog | Clear Filters | Positive | Medium | Verify "Clear all" button functionality in the filter header and Sort modal. | All selected checkboxes, ranges, and sort options reset to default; full listing catalog reloads. |
| **TS_FLT_010** | Product Catalog | Sort Options | Positive | High | Verify sorting by Location & Recency ("Nearest Location", "Recently Added") in the Sort By dropdown modal. | Listings reorder according to user proximity (Nearest) or publication timestamp (Recently Added). |
| **TS_FLT_011** | Product Catalog | Sort Options | Positive | High | Verify multi-attribute sorting: Price (Lowest/Highest), Hours, Mileage, Age, Horsepower, and Lift Capacity. | Catalog sorts accurately in ascending/descending order based on the selected metric. |
| **TS_FLT_012** | Product Catalog | View Toggle | UI / Usability | Low | Verify layout switching between Grid View and List View icons next to the Sort By button. | Equipment cards toggle between multi-column grid layout and horizontal row list layout without data loss. |
| **TS_FLT_013** | Product Catalog | Zero Results State | Negative | Medium | Verify system behavior when mutually exclusive filter combinations yield zero matching products. | Shows zero listings counter ("0 listings") and user-friendly "No matching equipment found" state with option to reset. |
| **TS_FLT_014** | Product Catalog | Accordion Usability | UI / Usability | Low | Verify expanding, collapsing, and "Browse all" link navigation across filter accordion sections. | Accordions expand/collapse smoothly with arrow indicators; "Browse all" opens full option lists. |
