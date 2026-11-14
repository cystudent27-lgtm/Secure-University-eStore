# Secure University e-Store – STRIDE Threat Analysis

## 1. Introduction

This document identifies and analyzes security threats affecting the Secure University e-Store using the STRIDE threat modeling methodology.

The analysis is based on the system requirements, database design, architecture, and purchase and payment data flows defined in Milestone 1 (M1).

The purpose is to identify potential threats, evaluate their impact, and define security controls that will be implemented and verified during later project milestones.

## 2. STRIDE Methodology

STRIDE is a threat modeling methodology that classifies security threats into six categories:

| Category | Description |
|---|---|
| Spoofing | Pretending to be another user or system. |
| Tampering | Unauthorized modification of data or requests. |
| Repudiation | Denying an action without sufficient evidence to verify it. |
| Information Disclosure | Exposing confidential or sensitive information. |
| Denial of Service | Making a system or service unavailable. |
| Elevation of Privilege | Gaining permissions beyond those authorized. |

## 3. Risk Assessment Method

Each identified threat is evaluated according to:

- **Likelihood:** The estimated probability of the threat occurring.
- **Impact:** The potential damage to the system, its users, or its data.
- **CIA Impact:** The effect on Confidentiality, Integrity, and Availability.

Risk levels are determined using the following qualitative approach:

| Likelihood | Description |
|---|---|
| Low | Requires unusual conditions or significant effort. |
| Medium | Possible under realistic conditions. |
| High | Relatively easy to exploit or likely to occur. |

| Impact | Description |
|---|---|
| Low | Limited damage with minimal operational consequences. |
| Medium | Noticeable damage affecting specific users or functions. |
| High | Significant damage involving sensitive data, financial transactions, or critical services. |

## 4. Identified Security Threats

### T01 – Account Spoofing and Account Takeover

- **STRIDE Category:** Spoofing.
- **Affected Assets:** User accounts, authentication credentials, sessions, and personal information.
- **Threat Description:** An attacker may obtain or misuse a user's credentials to impersonate a legitimate customer, vendor, or administrator and gain unauthorized access.
- **CIA Impact:**
  - Confidentiality: High – Unauthorized access to personal and account information.
  - Integrity: High – Unauthorized actions performed using the compromised account.
  - Availability: Medium – The legitimate user may lose access to their account.
- **OWASP Top 10 Mapping:** A07 – Identification and Authentication Failures.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Store passwords using bcrypt or Argon2 password hashing.
  - Apply login rate limiting and temporary account lockout.
  - Use generic authentication error messages.
  - Require TOTP multi-factor authentication for vendors and administrators.
  - Use secure session cookies and session expiration.
  - Provide single-use, time-limited password reset tokens.
- **Future Verification:**
  - Test repeated failed login attempts.
  - Verify that invalid and expired password reset tokens are rejected.
  - Verify that vendor and administrator accounts require MFA.
  - Test session expiration and invalidation after logout.

### T02 – Broken Access Control and Unauthorized Data Modification

- **STRIDE Category:** Elevation of Privilege.
- **Affected Assets:** User accounts, products, orders, addresses, payments, and administrative functions.
- **Threat Description:** An attacker may access another user's resources or perform operations outside their assigned role by manipulating API requests or resource identifiers.
- **CIA Impact:**
  - Confidentiality: High – Unauthorized access to other users' private information.
  - Integrity: High – Unauthorized modification of products, orders, or account data.
  - Availability: Medium – Unauthorized actions may disrupt application operations.
- **OWASP Top 10 Mapping:** A01 – Broken Access Control.
- **Likelihood:** High.
- **Impact:** High.
- **Security Controls:**
  - Enforce server-side role-based access control.
  - Verify resource ownership for customer and vendor operations.
  - Apply authorization checks to every protected API endpoint.
  - Restrict Security Auditors to read-only access.
  - Prevent vendors from modifying products owned by other vendors.
  - Prevent users from accessing other customers' orders and addresses.
- **Future Verification:**
  - Test access to another customer's order using a modified order ID.
  - Attempt to modify another vendor's product.
  - Attempt to access administrative endpoints using a customer account.
  - Verify that Security Auditors cannot modify application data.

### T03 – SQL Injection

- **STRIDE Category:** Tampering.
- **Affected Assets:** MySQL 8 Database, user accounts, products, orders, and payment records.
- **Threat Description:** An attacker may submit malicious input to manipulate database queries and potentially read, modify, or delete unauthorized data.
- **CIA Impact:**
  - Confidentiality: High – Unauthorized access to sensitive database information.
  - Integrity: High – Unauthorized modification or deletion of database records.
  - Availability: High – Malicious queries may disrupt database operations.
- **OWASP Top 10 Mapping:** A03 – Injection.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Use parameterized queries or prepared statements.
  - Avoid constructing SQL queries through unsafe string concatenation.
  - Validate and constrain user input.
  - Apply least-privilege database accounts.
  - Avoid exposing detailed database errors to users.
- **Future Verification:**
  - Test search, login, and product-related inputs with SQL injection payloads.
  - Verify that malicious input is treated as data rather than executable SQL.
  - Confirm that database accounts cannot perform unauthorized operations.

### T04 – Cross-Site Scripting (XSS)

- **STRIDE Category:** Tampering.
- **Affected Assets:** Customer browser sessions, product descriptions, reviews, and user interface content.
- **Threat Description:** An attacker may inject malicious JavaScript into user-controlled content, such as product descriptions or reviews, causing it to execute in another user's browser.
- **CIA Impact:**
  - Confidentiality: High – Malicious scripts may expose accessible session information or user data.
  - Integrity: Medium – The attacker may manipulate displayed content or perform actions through the victim's browser.
  - Availability: Low – Malicious scripts may disrupt the user interface.
- **OWASP Top 10 Mapping:** A03 – Injection.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Apply context-aware output encoding.
  - Use secure template rendering and automatic escaping.
  - Sanitize user-generated content when rich text is permitted.
  - Implement Content-Security-Policy.
  - Use HttpOnly and Secure session cookies.
- **Future Verification:**
  - Submit harmless XSS test payloads in product descriptions and reviews.
  - Verify that payloads are displayed as text or safely sanitized.
  - Confirm that the Content-Security-Policy blocks unauthorized script execution.

### T05 – Cross-Site Request Forgery (CSRF)

- **STRIDE Category:** Spoofing.
- **Affected Assets:** User sessions, account settings, shopping carts, and administrative operations.
- **Threat Description:** An attacker may trick an authenticated user into submitting an unwanted request to the e-store using the user's active session.
- **CIA Impact:**
  - Confidentiality: Low – CSRF may indirectly expose information through unauthorized actions.
  - Integrity: High – Unauthorized changes may be made to account or application data.
  - Availability: Medium – Unauthorized actions may disrupt user operations.
- **OWASP Top 10 Mapping:** A01 – Broken Access Control.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Implement CSRF tokens for state-changing requests.
  - Configure session cookies with appropriate SameSite settings.
  - Validate request origins where applicable.
  - Avoid using GET requests for state-changing operations.
  - Require additional authorization for sensitive administrative actions.
- **Future Verification:**
  - Attempt state-changing requests without valid CSRF tokens.
  - Test requests originating from an untrusted website.
  - Verify that invalid or missing tokens cause requests to be rejected.

### T06 – Server-Side Request Forgery (SSRF)

- **STRIDE Category:** Information Disclosure.
- **Affected Assets:** Secure Image Fetcher, internal services, MySQL network, and internal application resources.
- **Threat Description:** An attacker may manipulate an image URL supplied to the application to make the image-fetching component access localhost, private IP addresses, or internal services.
- **CIA Impact:**
  - Confidentiality: High – Internal resources or sensitive information may be exposed.
  - Integrity: Medium – Unauthorized requests may affect internal services.
  - Availability: Medium – Internal services may be disrupted by malicious requests.
- **OWASP Top 10 Mapping:** A10 – Server-Side Request Forgery (SSRF).
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Allow requests only to approved external hosts.
  - Reject localhost, private, and link-local IP ranges.
  - Validate resolved IP addresses before making requests.
  - Disable automatic redirects or revalidate every redirect destination.
  - Apply connection timeouts and response-size limits.
  - Prevent the image-fetching component from accessing internal networks.
- **Future Verification:**
  - Test image URLs pointing to localhost and private IP addresses.
  - Test URLs that resolve to blocked addresses.
  - Test redirect attempts to internal destinations.
  - Verify that the image-fetching component can access only approved external hosts.

### T07 – Price and Order Total Tampering

- **STRIDE Category:** Tampering.
- **Affected Assets:** Product prices, shopping carts, order items, coupons, and order totals.
- **Threat Description:** An attacker may manipulate client-submitted prices, quantities, discounts, or order totals to purchase products at unauthorized prices or bypass payment rules.
- **CIA Impact:**
  - Confidentiality: Low – The attack does not primarily target confidential information.
  - Integrity: High – Unauthorized changes may affect financial and order records.
  - Availability: Low – Limited direct impact on service availability.
- **OWASP Top 10 Mapping:** A04 – Insecure Design.
- **Likelihood:** High.
- **Impact:** High.
- **Security Controls:**
  - Calculate all prices, discounts, and order totals on the server.
  - Retrieve product prices from the database during checkout.
  - Validate quantities and stock availability.
  - Store unit-price snapshots in order items.
  - Validate coupon eligibility and usage limits on the server.
  - Use database transactions to prevent inconsistent checkout operations.
- **Future Verification:**
  - Modify product prices and order totals in browser requests.
  - Submit unauthorized discount values.
  - Test negative, zero, and excessive quantities.
  - Verify that the backend recalculates totals using trusted database values.

### T08 – Forged Payment Webhook

- **STRIDE Category:** Spoofing.
- **Affected Assets:** PayLab integration, payment records, order statuses, and financial transaction records.
- **Threat Description:** An attacker may send a forged payment callback pretending to be PayLab, attempting to mark an unpaid order as successfully paid.
- **CIA Impact:**
  - Confidentiality: Low – The main target is payment integrity rather than confidential information.
  - Integrity: High – Payment and order statuses may be changed fraudulently.
  - Availability: Medium – Incorrect payment processing may disrupt order handling.
- **OWASP Top 10 Mapping:** A08 – Software and Data Integrity Failures.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Verify webhook signatures using HMAC-SHA256.
  - Store the webhook secret securely.
  - Validate the payment reference and associated order.
  - Verify the payment amount and currency.
  - Reject requests with invalid signatures.
  - Allow payment status changes only through verified server-side processing.
- **Future Verification:**
  - Send webhook requests with invalid or missing signatures.
  - Modify the payment amount and payment reference.
  - Verify that invalid requests cannot change payment or order statuses.
  - Confirm that browser redirects alone cannot mark an order as PAID.

### T09 – Webhook Replay Attack

- **STRIDE Category:** Spoofing.
- **Affected Assets:** Payment records, webhook processing, and order statuses.
- **Threat Description:** An attacker may resend a previously valid payment webhook to repeat a payment-related operation or cause inconsistent transaction processing.
- **CIA Impact:**
  - Confidentiality: Low – The primary target is transaction integrity.
  - Integrity: High – Duplicate processing may affect payment and order records.
  - Availability: Medium – Repeated requests may disrupt payment processing.
- **OWASP Top 10 Mapping:** A08 – Software and Data Integrity Failures.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Assign unique identifiers to webhook events.
  - Enforce uniqueness of processed event identifiers.
  - Validate webhook timestamps within a five-minute window.
  - Reject previously processed events.
  - Make payment processing idempotent.
- **Future Verification:**
  - Submit the same valid webhook multiple times.
  - Submit an expired webhook with an old timestamp.
  - Verify that only the first valid event is processed.
  - Confirm that duplicate events do not create additional payment updates.

### T10 – Clickjacking

- **STRIDE Category:** Spoofing.
- **Affected Assets:** Customer and administrator browser sessions, account settings, and sensitive application actions.
- **Threat Description:** An attacker may embed the e-store inside a malicious webpage using an iframe and trick an authenticated user into clicking hidden or misleading interface elements.
- **CIA Impact:**
  - Confidentiality: Low – The attack may expose information indirectly through user interactions.
  - Integrity: Medium – Users may unknowingly perform unauthorized actions.
  - Availability: Low – Limited direct impact on service availability.
- **OWASP Top 10 Mapping:** A05 – Security Misconfiguration.
- **Likelihood:** Medium.
- **Impact:** Medium.
- **Security Controls:**
  - Configure the Content-Security-Policy frame-ancestors directive.
  - Apply X-Frame-Options where appropriate.
  - Use secure session cookie settings.
  - Require confirmation for sensitive operations.
- **Future Verification:**
  - Attempt to load the e-store inside an unauthorized iframe.
  - Verify that browser framing protections block the attempt.
  - Confirm that sensitive actions require appropriate user interaction.

### T11 – Audit Log Tampering and Repudiation

- **STRIDE Category:** Repudiation.
- **Affected Assets:** AUDIT_LOG, SECURITY_EVENTS, administrative activity records, and payment-related security evidence.
- **Threat Description:** A malicious user or compromised privileged account may attempt to modify, delete, or avoid generating audit records to hide unauthorized actions or deny responsibility.
- **CIA Impact:**
  - Confidentiality: Medium – Audit records may contain sensitive operational information.
  - Integrity: High – Modified or deleted records undermine the reliability of security evidence.
  - Availability: Medium – Missing records may affect security investigations.
- **OWASP Top 10 Mapping:** A09 – Security Logging and Monitoring Failures.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Apply append-only audit logging.
  - Prevent administrators and auditors from modifying or deleting audit records through application functions.
  - Record important authentication, administrative, and payment events.
  - Restrict access to audit records using role-based permissions.
  - Record timestamps and relevant actor identifiers.
- **Future Verification:**
  - Attempt to modify or delete audit records using application accounts.
  - Verify that important administrative and payment actions generate audit events.
  - Confirm that Security Auditors can read records but cannot modify them.

### T12 – Denial of Service and Resource Exhaustion

- **STRIDE Category:** Denial of Service.
- **Affected Assets:** Backend Application, authentication endpoints, database connections, image-fetching component, and payment-related endpoints.
- **Threat Description:** An attacker may send excessive requests, oversized inputs, or resource-intensive operations to exhaust application resources and make the e-store unavailable.
- **CIA Impact:**
  - Confidentiality: Low – The attack does not primarily target confidential data.
  - Integrity: Low – Data modification is not the primary objective.
  - Availability: High – Legitimate users may be unable to access the application.
- **OWASP Top 10 Mapping:** A04 – Insecure Design.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Apply rate limiting to sensitive API endpoints.
  - Set request body and upload size limits.
  - Configure connection and processing timeouts.
  - Limit image-fetching response sizes.
  - Apply database connection limits and appropriate resource controls.
  - Monitor repeated failed requests and unusual traffic patterns.
- **Future Verification:**
  - Perform controlled rate-limit tests in the local environment.
  - Submit oversized requests and verify that they are rejected.
  - Test image-fetching timeout and response-size restrictions.
  - Verify that normal application functions remain available under controlled test loads.

### T13 – XML External Entity (XXE) Attack

- **STRIDE Category:** Information Disclosure.
- **Affected Assets:** XML catalogue import functionality, backend application, internal files, internal services, and sensitive application information.
- **Threat Description:** An attacker may submit a malicious XML catalogue file containing an external entity declaration. If the XML parser processes external entities, the application may disclose local files, access internal resources, or make unauthorized requests to internal services.
- **CIA Impact:**
  - Confidentiality: High – Sensitive local files or internal information may be exposed.
  - Integrity: Medium – Malicious XML processing may affect imported catalogue data or trigger unauthorized internal requests.
  - Availability: Medium – Recursive entity expansion or resource-intensive XML input may consume application resources.
- **OWASP Top 10 Mapping:** A05 – Security Misconfiguration.
- **Likelihood:** Medium.
- **Impact:** High.
- **Security Controls:**
  - Disable DTD processing and external entity resolution in the XML parser.
  - Disable external general entities and external parameter entities.
  - Disable XInclude and external resource access where supported by the parser.
  - Reject XML documents containing prohibited DOCTYPE declarations.
  - Use a securely configured and maintained XML parsing library.
  - Apply XML file size, nesting depth, and processing limits.
  - Validate imported XML against the expected catalogue structure and allowed fields.
  - Ensure that XML parsing cannot access local files or internal network resources.
- **Future Verification:**
  - Submit harmless XXE test files in the isolated local testing environment.
  - Verify that XML documents containing external entity declarations are rejected or safely processed without resolving the entities.
  - Test attempts to reference local files and internal network addresses.
  - Verify that the secure application parser does not disclose file contents or access internal services.
  - Test entity expansion limits to ensure that resource exhaustion is prevented.
  - Perform any intentionally vulnerable XXE demonstrations only in the isolated LAB environment.

## 5. STRIDE Threat Summary

| Threat ID | STRIDE Category | Threat | Likelihood | Impact |
|---|---|---|---|---|
| T01 | Spoofing | Account Spoofing and Account Takeover | Medium | High |
| T02 | Elevation of Privilege | Broken Access Control | High | High |
| T03 | Tampering | SQL Injection | Medium | High |
| T04 | Tampering | Cross-Site Scripting (XSS) | Medium | High |
| T05 | Spoofing | Cross-Site Request Forgery (CSRF) | Medium | High |
| T06 | Information Disclosure | Server-Side Request Forgery (SSRF) | Medium | High |
| T07 | Tampering | Price and Order Total Tampering | High | High |
| T08 | Spoofing | Forged Payment Webhook | Medium | High |
| T09 | Spoofing | Webhook Replay Attack | Medium | High |
| T10 | Spoofing | Clickjacking | Medium | Medium |
| T11 | Repudiation | Audit Log Tampering | Medium | High |
| T12 | Denial of Service | Resource Exhaustion | Medium | High |
| T13 | Information Disclosure | XML External Entity (XXE) Attack | Medium | High |

## 6. Security Analysis and Future Testing

The identified threats demonstrate that the most critical security areas of the Secure University e-Store are:

- **Authentication and Authorization:** Protecting user accounts and enforcing role-based access control.
- **Data Integrity:** Preventing unauthorized modification of product prices, order totals, and database records.
- **Payment Security:** Ensuring that only verified PayLab webhooks can confirm successful payments.
- **Application Security:** Preventing injection attacks, XSS, CSRF, SSRF, XXE, and clickjacking.
- **XML Import Security:** Ensuring that XML catalogue files are parsed safely without allowing external entity resolution or unauthorized access to local and internal resources.
- **Logging and Monitoring:** Maintaining reliable audit records for security investigations.
- **Service Availability:** Protecting application components from resource exhaustion.

The security controls listed in this document represent planned protections. Their effectiveness will be verified through controlled security testing during the later project milestones.

All security experiments will be conducted within the group's isolated local testing environments.

## 7. Conclusion

The STRIDE analysis identifies thirteen security threats affecting the Secure University e-Store and maps each threat to its affected assets, CIA impact, OWASP Top 10 category, likelihood, impact, security controls, and future verification activities.

The analysis covers authentication, authorization, database security, web application attacks, payment integrity, audit logging, service availability, and secure XML catalogue processing.

This analysis provides a foundation for implementing security controls and conducting practical security testing during the subsequent project milestones.