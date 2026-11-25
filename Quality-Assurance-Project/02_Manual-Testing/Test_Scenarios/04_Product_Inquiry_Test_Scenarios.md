# Test Scenarios: Product Inquiry & Seller Lead Generation

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-BUY-INQ-01  
**Module:** Inquiries — Direct Message to Seller  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Inquiry_Scenarios`)*  
**Related Business Rules:** `BR-BUY-003`, `BR-SEL-012`  

---

## 1. UI Elements Identified from Screen
- **Header:** "Send Inquiry - Direct message to seller" with "Direct Channel" indicator.
- **Mandatory Contact Inputs:**
  - Full Name (`*`)
  - Email Address (`*`)
  - Phone Number (`*`) with country selector dropdown (`+1` USA flag default)
- **Interest Checkboxes / Add-ons:**
  - "I'm interested in financing options" (triggers SunBridge Leasing Corporation disclosure).
  - "I'd like a shipping quote."
  - "I'm interested in an extended warranty."
  - "I want to know more about insurance."
- **Message Area:** Message text box (`*`) with dynamic template auto-fill.
- **Action Button:** "Submit Inquiry".

---

## 2. Test Scenario Matrix (10 Core Scenarios)

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_INQ_001** | Product Inquiries | Inquiry Submission | Positive | High | Verify successful inquiry submission when all mandatory fields (Full Name, Email Address, Phone Number, Message) are valid. | Inquiry is submitted successfully; confirmation toast/message is displayed; seller receives direct lead notification. |
| **TS_INQ_002** | Product Inquiries | Form Validation | Negative | High | Verify validation error when mandatory fields (Full Name, Email, Phone, Message) are left blank and "Submit Inquiry" is clicked. | Form submission is blocked; clear validation messages appear under each mandatory field. |
| **TS_INQ_003** | Product Inquiries | Input Validation | Negative | Medium | Verify field validation when an invalid email format (e.g. missing "@" or domain) is entered in the Email Address field. | System shows error: "Please enter a valid email address"; submit button remains blocked. |
| **TS_INQ_004** | Product Inquiries | Input Validation | Negative | Medium | Verify phone number validation when entering an invalid or incomplete phone number for the selected country code. | System displays validation error indicating an invalid or incomplete phone number. |
| **TS_INQ_005** | Product Inquiries | Country Code | Functional | Medium | Verify country code selector dropdown functionality (flag icon & dial code like +1) in the Phone Number field. | User can change country dial code; phone number formatting dynamically updates according to selected country. |
| **TS_INQ_006** | Product Inquiries | Add-on Options | Positive | High | Verify checking "I'm interested in financing options" displays third-party provider notice (SunBridge Leasing) and auto-populates/appends financing text to the Message box. | Informational disclosure appears; message box auto-appends relevant financing inquiry request text. |
| **TS_INQ_007** | Product Inquiries | Add-on Options | Positive | Medium | Verify selecting multiple service checkboxes ("Shipping quote", "Extended warranty", "Insurance"). | Checkboxes toggle independently; selected service lead tags are saved and transmitted with the inquiry payload. |
| **TS_INQ_008** | Product Inquiries | Guest OTP Verification | Business Logic | High | Verify OTP verification prompt triggers for unauthenticated / guest buyers submitting an inquiry (`BR-BUY-003`). | Guest user is intercepted with a verification modal (Email/SMS OTP) before the inquiry is dispatched to the seller. |
| **TS_INQ_009** | Product Inquiries | Pre-fill (Logged In) | Usability | Medium | Verify that Full Name, Email Address, and Phone Number are auto-prefilled when an authenticated buyer opens the inquiry form. | Account profile contact details are automatically populated in the respective input fields. |
| **TS_INQ_010** | Product Inquiries | Buyer & Seller History | End-to-End | High | Verify end-to-end inquiry delivery: inquiry appears in Buyer's "My Inquiries" history and in Seller's "Inquiries Inbox". | Inquiry is successfully recorded in Buyer sent inquiries and immediately visible in the seller organization dashboard. |
