# Test Scenarios: Buyer Registration & Authentication

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-BUY-REG-01  
**Module:** Buyer Side — Authentication & Onboarding  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Registration_Scenarios`)*  
**Related Business Rules:** `BR-GEN-001`, `BR-GEN-002`, `BR-GEN-003`, `BR-GEN-004`  

---

## 1. Scope & Verification Rules
- **Mandatory Fields:** First Name, Last Name, Email Address, Phone Number, Password, Confirm Password, Terms & Conditions acceptance.
- **Verification Mechanism:** Dual OTP Verification (6-digit Email OTP + 6-digit SMS OTP).
- **OTP Countdown Window:** 30 seconds per cycle with Resend OTP capability upon expiration.
- **Password Complexity:** Minimum 8 characters with at least 1 uppercase, 1 lowercase, 1 number, and 1 special character.
- **Post-Verification Behavior:** Direct automatic authentication and redirect to Buyer Dashboard.

---

## 2. Test Scenario Matrix

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_BUY_REG_001** | Buyer Authentication | Registration | Positive | High | Verify successful buyer registration when all mandatory fields are provided with valid data. | Form submits successfully; Dual OTP modal (Email & SMS) is presented. |
| **TS_BUY_REG_002** | Buyer Authentication | OTP Verification | Positive | High | Verify successful account verification and automatic login upon entering valid 6-digit Email OTP and SMS OTP within 30 seconds. | Account is activated and user is directly redirected to the Buyer Dashboard. |
| **TS_BUY_REG_003** | Buyer Authentication | Registration | Negative | High | Verify system behavior when all mandatory fields (First Name, Last Name, Email, Phone, Password, Confirm Password, Terms) are left empty and submit button is clicked. | Form submission is blocked; mandatory field validation errors appear under each required input. |
| **TS_BUY_REG_004** | Buyer Authentication | Registration | Negative | High | Verify system validation when attempting to register with an already registered/existing email address. | Validation error appears indicating email is already registered. |
| **TS_BUY_REG_005** | Buyer Authentication | Registration | Negative | High | Verify system validation when attempting to register with an already registered/existing mobile phone number. | Validation error appears indicating phone number is already registered. |
| **TS_BUY_REG_006** | Buyer Authentication | Registration | Negative | Medium | Verify system validation when entering an invalid email format (e.g., missing @, missing domain, invalid characters). | Field-level validation error indicates invalid email format. |
| **TS_BUY_REG_007** | Buyer Authentication | Registration | Negative | Medium | Verify system validation when entering an invalid mobile phone number (e.g., less than 10 digits, letters, special characters). | Field-level validation error indicates invalid phone number. |
| **TS_BUY_REG_008** | Buyer Authentication | Registration | Negative | High | Verify password complexity validation when password does not meet criteria (less than 8 chars, missing uppercase, lowercase, number, or special character). | Password policy validation hints/errors are displayed; submission remains blocked. |
| **TS_BUY_REG_009** | Buyer Authentication | Registration | Negative | High | Verify system validation when Confirm Password does not match Password. | Error message displays: "Passwords do not match"; submit button is disabled/blocked. |
| **TS_BUY_REG_010** | Buyer Authentication | Registration | Negative | Medium | Verify registration behavior when Terms & Conditions / Privacy Policy checkbox is left unchecked. | Submit is prevented; validation prompt mandates accepting Terms & Conditions. |
| **TS_BUY_REG_011** | Buyer Authentication | Registration | UI / Usability | Low | Verify password masking and toggle visibility icon (eye icon) functionality on Password and Confirm Password fields. | Characters are masked by default and toggled to plain text upon clicking show/hide icon. |
| **TS_BUY_REG_012** | Buyer Authentication | OTP Verification | Negative | High | Verify system behavior when invalid/incorrect 6-digit OTP is entered for Email verification. | Error message: "Invalid OTP code"; verification fails. |
| **TS_BUY_REG_013** | Buyer Authentication | OTP Verification | Negative | High | Verify system behavior when invalid/incorrect 6-digit OTP is entered for SMS verification. | Error message: "Invalid OTP code"; verification fails. |
| **TS_BUY_REG_014** | Buyer Authentication | OTP Verification | Boundary / Timeout | High | Verify 30-second countdown timer expiration for Dual OTP and error handling when submitting expired OTP. | Timer counts down from 30s to 0; entering OTP after expiry displays: "OTP has expired. Please request a new code." |
| **TS_BUY_REG_015** | Buyer Authentication | OTP Verification | Positive | Medium | Verify that "Resend OTP" button is enabled after the 30-second timer expires and generates fresh 6-digit codes. | Resend button activates at 0s; clicking it restarts 30s timer and dispatches new codes to email & mobile. |
| **TS_BUY_REG_016** | Buyer Authentication | OTP Verification | Boundary / Security | High | Verify maximum retry limit / rate limiting for entering consecutive incorrect OTP attempts. | System temporarily blocks verification attempts (HTTP 429 / lockout warning) after exceeding retry limit. |
| **TS_BUY_REG_017** | Buyer Authentication | Security | Security | High | Verify input sanitization on registration form fields against Cross-Site Scripting (XSS) and SQL Injection payloads. | Payloads are safely encoded/sanitized; no script execution or database errors occur. |
| **TS_BUY_REG_018** | Buyer Authentication | Navigation | Navigation | Low | Verify redirection to Login page when user clicks "Already have an account? Log in" link. | User is redirected smoothly to the Buyer Login screen. |
