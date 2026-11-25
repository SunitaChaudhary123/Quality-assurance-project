# Test Scenarios: User Login & Authentication

**Project Name:** MachineryExpo Multi-Tenant Marketplace Platform  
**Target Application:** https://ourmachineryexpo.com  
**Document Reference:** TS-AUTH-LOG-01  
**Module:** Authentication — Login  
**Corresponding Excel Workbook:** [Buyer_Test_Scenarios.xlsx](file:///d:/Workspace/MachineryExpo/Quality-Assurance%20-Project/Quality-Assurance-Project/02_Manual-Testing/Test_Scenarios/Buyer_Test_Scenarios.xlsx) *(Sheet: `Login_Scenarios`)*  
**Related Business Rules:** `BR-GEN-001`, `BR-GEN-003`, `BR-GEN-004`  

---

## 1. UI Elements Identified from Screen
- **Inputs:** Username or Email, Password (with eye toggle icon).
- **Checkboxes:** Keep me logged in.
- **Action Buttons:** ACCESS DASHBOARD (Primary CTA), Continue with Google (Social Login).
- **Secondary Links:** Login with OTP, Forgot Password?, Create an account.
- **Navigation:** Back button (top-left arrow).
- **Footer Links:** Privacy Policy, Terms of Service, Support.

---

## 2. Test Scenario Matrix (`Login_Scenarios`)

| Scenario ID | Module | Sub-Module | Testing Type | Priority | Test Scenario Description | Expected Result |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TS_AUTH_LOG_001** | Authentication | Login | Positive | High | Verify successful login when entering valid registered Username and valid Password, then clicking "ACCESS DASHBOARD". | User is authenticated and smoothly redirected to the User Dashboard. |
| **TS_AUTH_LOG_002** | Authentication | Login | Positive | High | Verify successful login when entering valid registered Email address and valid Password, then clicking "ACCESS DASHBOARD". | User is authenticated and smoothly redirected to the User Dashboard. |
| **TS_AUTH_LOG_003** | Authentication | Login | Negative | High | Verify validation behavior when Username/Email and Password fields are left completely blank and "ACCESS DASHBOARD" is clicked. | Form submission is blocked; mandatory validation messages appear under both fields. |
| **TS_AUTH_LOG_004** | Authentication | Login | Negative | High | Verify validation behavior when valid Username/Email is entered but Password field is left empty. | Submission blocked; "Password is required" validation message is displayed. |
| **TS_AUTH_LOG_005** | Authentication | Login | Negative | High | Verify validation behavior when Username/Email field is left empty but Password is entered. | Submission blocked; "Username or Email is required" validation message is displayed. |
| **TS_AUTH_LOG_006** | Authentication | Login | Negative | High | Verify system behavior when non-registered / non-existing Username or Email is entered with any password. | Displays error message: "Invalid credentials or account does not exist". |
| **TS_AUTH_LOG_007** | Authentication | Login | Negative | High | Verify system behavior when valid registered Username/Email is entered with an incorrect/wrong password. | Authentication fails; displays error message: "Invalid username/email or password". |
| **TS_AUTH_LOG_008** | Authentication | Login | Security / Boundary | High | Verify account lockout / rate limiting after 5 consecutive failed login attempts (`BR-GEN-004`). | Account or IP is temporarily locked with HTTP 429 / warning: "Too many failed attempts. Please try again after 15 minutes." |
| **TS_AUTH_LOG_009** | Authentication | Login | UI / Usability | Medium | Verify password masking and eye-toggle icon functionality on the Password field. | Password characters are masked by default (dots/asterisks); clicking the eye icon toggles characters to readable plain text and back. |
| **TS_AUTH_LOG_010** | Authentication | Session Management | Functional | Medium | Verify "Keep me logged in" checkbox functionality across browser sessions. | When checked, auth token/cookie persists across browser closure; user remains logged in upon reopening browser. |
| **TS_AUTH_LOG_011** | Authentication | Session Management | Functional | Medium | Verify session behavior when "Keep me logged in" checkbox is left unchecked. | Auth session terminates when browser window/tab is closed; user must log in again. |
| **TS_AUTH_LOG_012** | Authentication | OTP Login | Positive | High | Verify navigation to OTP login flow when user clicks "Login with OTP" link. | Screen transitions/prompts for registered contact (Email/Mobile) to receive a one-time login OTP code. |
| **TS_AUTH_LOG_013** | Authentication | Password Recovery | Positive | High | Verify navigation to password recovery wizard when user clicks "Forgot Password?" link. | User is redirected to the Forgot Password / Reset Password request page. |
| **TS_AUTH_LOG_014** | Authentication | Social Login | Positive | High | Verify Google OAuth authentication when clicking "Continue with Google" button. | Opens Google authentication prompt; upon successful Google account selection, logs in and redirects to Dashboard. |
| **TS_AUTH_LOG_015** | Authentication | Navigation | Navigation | Medium | Verify navigation to registration page when user clicks "Create an account" link. | User is redirected smoothly to the Registration / Sign up screen. |
| **TS_AUTH_LOG_016** | Authentication | Navigation | Navigation | Low | Verify back button (arrow icon in top-left) navigation. | Redirects user back to the previous referring page or home page. |
| **TS_AUTH_LOG_017** | Authentication | Navigation / Legal | Navigation | Low | Verify that footer links ("Privacy Policy", "Terms of Service", "Support") open their respective target pages. | Each link navigates to the valid corresponding document/support contact page without 404 errors. |
| **TS_AUTH_LOG_018** | Authentication | Security | Security | High | Verify SQL Injection and Cross-Site Scripting (XSS) input sanitization on Username/Email and Password input fields. | Inputs are sanitized/parameterized; malicious payloads are rejected without system vulnerability. |
