# Test Scenarios: Equipment Finance Application

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-BUY-FIN-01  
**Module:** Get Finance / Equipment Financing Application  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Financing_Scenarios`)*  
**Related Business Rules:** `BR-GEN-002`, `BR-BUY-004`, `BR-BUY-005`  

---

## 1. Scope & Multi-Step Wizard Flow
- **Step 1 — Financial Detail:**
  - Time Frame (dropdown e.g. "Within 30 Days").
  - Requested Loan Amount (`*`, positive numeric amount).
  - Loan Term (`*`, dropdown e.g. "48 months").
  - Working Capital interest toggle ("Are you interested in working capital?").
  - Mandatory SunBridge Leasing Corporation third-party data sharing agreement.
- **Step 2 — Business Detail:**
  - Business Name, Business Type (LLC, etc.), Industry.
  - Annual Income (`$`), Time in Business, Estimated Credit Score.
  - Business Phone Number (with dial code), Business Email, Contact Name, Federal Tax ID (EIN).
  - Business Address with Zip Code Auto-fill Location (Country, State/Region, City, Street Address `*`, Street 2 optional).
- **Step 3 — Personal Detail:**
  - Applicant Identity: First Name (`*`), Last Name (`*`), Email Address (`*`), Phone Number (`*`), Ownership % (`*`), SSN (`*` with eye-toggle masking).
  - Residential Home Address (Zip Code Auto-fill).
  - `+ Add Additional Owner` action.
  - Mandatory Legal Consents:
    - Agreement to Terms of Service & Financing Terms & Disclaimer (Credit authorization consent).
    - Agreement to Privacy Policy.
  - Preferred Contact Method: "Any", "Phone", "Email".
- **Verification Rule:**
  - **Registered / Logged-in Buyer:** Submits directly without OTP verification.
  - **Guest Buyer:** Intercepted with Dual OTP Verification popup (Email OTP & SMS OTP within 30 seconds) before application creation.

---

## 2. Test Scenario Matrix (`Financing_Scenarios`)

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_FIN_001** | Equipment Financing | Step 1 - Financial Detail | Positive | High | Verify Step 1 submission with valid Time Frame, Requested Loan Amount, Loan Term, and SunBridge Leasing consent. | Step 1 validates successfully; progress stepper advances to Step 2 ("Business Detail"). |
| **TS_FIN_002** | Equipment Financing | Step 1 - Financial Detail | Negative | High | Verify Step 1 validation when Requested Loan Amount is blank or <= 0, or SunBridge Leasing consent is unchecked. | Form blocks advance; validation errors prompt positive loan amount and mandatory third-party consent. |
| **TS_FIN_003** | Equipment Financing | Step 1 - Working Capital | Functional | Low | Verify toggling the "Are you interested in working capital?" switch toggle. | Switch toggles between active/inactive state and reflects working capital flag in submission payload. |
| **TS_FIN_004** | Equipment Financing | Step 2 - Business Detail | Positive | High | Verify Step 2 submission with valid Business Info (Name, Type, Industry, Income, Time in Business, Credit Score, Phone, Email, Tax ID, Address). | Step 2 passes all field validations; wizard advances to Step 3 ("Personal Detail"). |
| **TS_FIN_005** | Equipment Financing | Step 2 - Zip Auto-fill | Functional | Medium | Verify entering valid Zip Code (e.g., 90001) automatically populates Country, State, and City fields. | State and City dropdowns/inputs auto-populate correctly based on entered Zip Code. |
| **TS_FIN_006** | Equipment Financing | Step 2 - Business Detail | Negative | High | Verify Step 2 validation when mandatory business fields (Business Name, Phone, Email, Tax ID, Street Address) are empty. | Advance to Step 3 is blocked; error indicators appear under each incomplete mandatory field. |
| **TS_FIN_007** | Equipment Financing | Step 3 - Personal Detail | Positive | High | Verify Step 3 data entry with valid Applicant Details (Name, Email, Phone, Ownership %, SSN, Home Address, Preferred Contact Method). | All applicant identity and residential address fields accept valid inputs. |
| **TS_FIN_008** | Equipment Financing | Step 3 - SSN Masking | Security / UI | Medium | Verify Social Security Number (SSN) masking and unmasking toggle icon functionality. | SSN digits mask as dots by default; clicking eye-toggle icon unmasks digits into plain text. |
| **TS_FIN_009** | Equipment Financing | Step 3 - Additional Owners | Functional | Medium | Verify clicking "+ Add Additional Owner" allows entering secondary owners such that total ownership adds up to 100%. | Secondary owner input card appears; validates combined ownership percentages. |
| **TS_FIN_010** | Equipment Financing | Step 3 - Mandatory Consents | Negative | High | Verify "Submit Application" button behavior when Terms of Service / Credit Authorization or Privacy Policy checkboxes are unchecked (`BR-BUY-004`). | Submission is prevented; explicit validation error prompts applicant to agree to terms and credit check consent. |
| **TS_FIN_011** | Equipment Financing | Registered User Flow | Positive | High | Verify end-to-end financing submission for an already registered and logged-in buyer. | Contact fields pre-fill; clicking "Submit Application" successfully submits directly without OTP verification popup. |
| **TS_FIN_012** | Equipment Financing | Guest User Verification | Business Logic | High | Verify end-to-end financing submission for a guest (unauthenticated) user. | Clicking "Submit Application" halts submission and opens the Dual Verification popup (Email OTP & Mobile OTP verification). |
| **TS_FIN_013** | Equipment Financing | Guest User Verification | Positive | High | Verify guest user successfully enters valid 6-digit Email OTP and Mobile SMS OTP within 30 seconds. | Guest identity is verified; financing application is finalized and created; confirmation screen is displayed. |
| **TS_FIN_014** | Equipment Financing | Wizard Navigation | Navigation | Low | Verify "Back" button navigation between Step 3, Step 2, and Step 1 preserves entered form values. | User can navigate back to previous steps without losing previously entered form data. |
