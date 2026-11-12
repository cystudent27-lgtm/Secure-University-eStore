# M1 – System Requirements Document

## 1. Project Purpose

The Secure University e-Store is a web-based e-commerce platform designed to support both Business-to-Consumer (B2C) shopping and Consumer-to-Consumer (C2C) marketplace activities.

The main purpose of the project is to develop an e-commerce system that provides essential shopping services while applying security controls to protect users, products, orders, and payment operations.

## 2. Project Scope

The system will provide the following main capabilities:

- User registration, authentication, and account management.
- Product browsing, searching, and filtering.
- Shopping cart and checkout.
- Simulated payment processing through PayLab.
- Order history and real-time order status tracking.
- Vendor product and inventory management.
- Product image upload and external image URL management.
- Secure product catalogue import from XML and CSV files.
- Product reviews and coupon management.
- Administrative management and security auditing.
- Security testing using a separate intentionally vulnerable LAB environment.

## 3. System User Roles

The system defines four main user roles:

1. Customer
2. Vendor
3. Store Admin
4. Security Auditor

## 4. Functional Requirements

### FR01 – User Registration and Authentication

The system shall allow users to register, log in, log out, reset passwords, and manage their accounts securely.

It shall support:

- User registration with a unique email address.
- Secure password storage using bcrypt or Argon2 hashing.
- Login and logout with secure session management.
- Password reset using a single-use token that expires within 15 minutes.
- Rate limiting and temporary account lockout to reduce brute-force attacks.
- Generic authentication error messages that do not reveal whether an email address exists.
- Session cookies configured with HttpOnly, Secure, and SameSite attributes.
- Session expiration and invalidation after logout.
- TOTP-based multi-factor authentication for vendors and administrators.

All authentication and authorization checks shall be enforced on the server.

### FR02 – Product Catalogue

The system shall allow customers to:

- Browse available products.
- Search for products by relevant keywords.
- Filter products by categories and other available criteria.
- View product details, prices, stock availability, images, and reviews.

Only approved vendors' products shall be publicly available.

### FR03 – Shopping Cart and Checkout

The system shall allow customers to:

- Add products to their shopping carts.
- Update product quantities.
- Remove products from their carts.
- Select a delivery address.
- Review the order summary before confirming checkout.

The server shall calculate all prices, quantities, discounts, and order totals. Client-submitted totals shall never be trusted.

### FR04 – Simulated Payment Processing

The system shall integrate with a separate simulated payment service named PayLab.

It shall:

- Redirect customers to a hosted payment page.
- Receive payment notifications through a signed webhook.
- Verify the webhook signature, timestamp, event ID, and payment amount.
- Prevent duplicate payment processing and replay attacks.
- Update an order to PAID only after successful server-side payment verification.

A browser redirect or return page alone shall not be considered proof of payment.

### FR05 – Order History and Real-Time Tracking

The system shall allow customers to:

- View their own order history.
- View details of their orders.
- Track the current status of their orders.
- Receive real-time order status updates through an authenticated WebSocket connection.

Order tracking shall:

- Authenticate the customer before establishing the WebSocket connection.
- Verify that the requested order belongs to the authenticated customer.
- Ensure that customers can subscribe only to updates for their own orders.
- Send only information that the customer is authorized to access.
- Reject unauthorized subscriptions and connections.
- Close or invalidate connections when the user logs out or the session expires.
- Avoid exposing order information through publicly accessible channels.

Customers shall only be able to access their own orders and related information.

### FR06 – Vendor Product Management and Image Handling

The system shall allow approved vendors to:

- Create, update, and delete their own products.
- Update product descriptions, prices, and stock quantities.
- Upload product images or provide external image URLs.
- Replace or remove product images associated with their own products.
- View orders containing their own products.

Image handling shall:

- Support approved image file uploads and external HTTPS image URLs.
- Validate uploaded file size, extension, actual file type, and image content.
- Generate safe server-side filenames instead of trusting client-provided filenames.
- Store uploaded images in a controlled location that does not allow execution.
- Accept external image URLs only from explicitly approved hosts.
- Pass external image-fetching requests through a separate secure image-fetching component.
- Reject localhost, private, and link-local IP addresses.
- Validate resolved IP addresses before making external requests.
- Prevent redirects to unauthorized destinations.
- Apply connection timeouts and response-size limits.
- Prevent the image-fetching component from accessing internal services and networks.

Vendors shall not modify products belonging to other vendors or change order and payment statuses.

### FR07 – Product Catalogue Import (XML and CSV)

The system shall allow authorized vendors or administrators, according to their permissions, to import product catalogue data using XML or CSV files.

The import functionality shall:

- Accept only supported XML and CSV file formats.
- Validate file extensions, actual content, and file size.
- Validate required product fields and data types.
- Validate product names, descriptions, prices, stock quantities, and category references.
- Reject malformed or invalid files.
- Reject unauthorized attempts to import products for another vendor.
- Apply server-side validation before saving imported records.
- Prevent imported data from overwriting protected fields such as vendor ownership, approval status, or administrative fields.
- Process imports safely and report rejected records without exposing internal errors.
- Use database transactions or an equivalent safe processing strategy to prevent inconsistent partial updates.

XML processing shall:

- Disable Document Type Definitions (DTDs).
- Disable external entity processing.
- Disable external document and schema resolution unless explicitly required and safely controlled.
- Prevent XML External Entity (XXE) attacks.
- Apply limits on document size, nesting depth, and processing resources.

CSV processing shall:

- Validate the expected column structure.
- Handle delimiters and quoted values safely.
- Reject invalid or unexpected fields.
- Prevent formula injection when imported data is later exported or opened in spreadsheet applications.

Only authorized and validated catalogue data shall be stored in the database.

### FR08 – Product Reviews

The system shall allow customers to submit reviews for products they have purchased.

Each review shall:

- Be associated with its product and customer.
- Include a rating from 1 to 5.
- Contain validated and safely handled text.
- Be accessible to other users according to the product's visibility.

Customers shall not be allowed to review products they have not purchased.

### FR09 – Coupon Management

The system shall allow customers to apply valid coupons during checkout.

The system shall:

- Validate coupon codes on the server.
- Check coupon expiration dates and usage limits.
- Calculate discounts on the server.
- Prevent coupon usage beyond the configured maximum.
- Update coupon usage records only through successful server-side operations.
- Enforce usage limits safely during concurrent checkout requests.
- Ensure that the used_count value never exceeds max_uses.

Client-submitted discount values or modified coupon data shall never be trusted.

### FR10 – Administration and Security Auditing

The system shall provide administrative functions for authorized users.

Store Administrators shall be able to:

- Approve or reject vendor accounts.
- Manage user accounts and product categories.
- Manage coupons.
- Process refunds through an authorized and documented workflow.
- Review administrative operations.

Security Auditors shall be able to:

- View security events.
- Read administrative audit records.
- Review Content Security Policy (CSP) reports.

Security and Audit Rules:

- Security Auditors shall have read-only access.
- Security Auditors shall not modify users, products, orders, or payment data.
- Audit records shall be append-only and shall not be modified or deleted.
- Refund operations shall require an authorized administrator, a documented reason, and an audit record.
- Refund amounts shall not exceed the original paid amount.
- Administrators shall not mark orders as PAID without successful server-side verification of a valid PayLab webhook.
- Administrative and security-related activities shall be recorded in appropriate logs.

### FR11 – REST API Documentation

The system shall provide a REST API documented using OpenAPI 3.

The API documentation shall describe:

- Available endpoints and HTTP methods.
- Request and response schemas.
- Authentication and authorization requirements.
- Input validation rules.
- Expected error responses.
- Payment webhook operations.
- Order status retrieval and tracking operations.
- Product image upload and external image URL operations.
- XML and CSV catalogue import operations.

The frontend shall use the documented API endpoints. WebSocket communication shall be documented separately as part of the system's real-time order tracking interface.

### FR12 – Secure and Vulnerable Environments

The project shall provide two separate application environments:

1. **Secure Environment:** The protected application with implemented security controls.
2. **Vulnerable LAB Environment:** An intentionally vulnerable copy used for authorized security testing and comparison with the secure application.

The project shall:

- Keep the vulnerable LAB separate from the secure environment.
- Restrict the vulnerable LAB to the group's local testing environment.
- Prevent public internet exposure of intentionally vulnerable services.
- Support rebuilding both environments using Docker Compose.
- Provide clear setup and safety instructions in the README.
- Maintain separate configurations and test evidence for both environments.

All security testing shall be performed only within the group's own local project environment.

## 5. Business Rules

### BR01 – User Accounts

- Every user must have a unique email address.
- Passwords must be stored as secure password hashes.
- Users must only access functions permitted by their assigned roles.

### BR02 – Vendor Approval

- Vendors must be approved by a Store Administrator before publishing products.
- Vendors can manage only their own products and inventory.
- Catalogue import operations must enforce vendor ownership and authorization.

### BR03 – Product Rules

- Product prices must be greater than zero.
- Product stock quantities must never be negative.
- Customers can only purchase products with sufficient available stock.
- Product images must be uploaded through the secure image upload process or use validated external HTTPS URLs from approved hosts.
- Uploaded images must be validated before storage.
- External image URLs must be validated before retrieval.

### BR04 – Order Calculation

- Order totals must be calculated on the server.
- Each order item must store a snapshot of the product price at the time of purchase.
- Client-submitted prices and totals must never be trusted.

### BR05 – Payment Rules

- Payment card information must not be stored by the application.
- An order can become PAID only after successful verification of a valid payment webhook.
- Each payment event must be processed only once.
- Refund amounts must not exceed the amount originally paid.

### BR06 – Order Status Rules

- Order status changes must follow the defined order state machine.
- Customers and vendors must not directly change payment or order statuses.
- Only authorized server-side operations may perform valid state transitions.
- Customers may receive order status updates through authenticated WebSocket connections.
- Customers must only subscribe to updates for their own orders.

### BR07 – Review Rules

- A customer may review a product only after purchasing it.
- Review ratings must be between 1 and 5.
- Review content must be validated and safely handled.

### BR08 – Coupon Rules

- Coupons must be valid and not expired when applied.
- Coupon usage must not exceed the configured maximum.
- Discounts must be calculated and validated on the server.

### BR09 – Administrative Rules

- Only authorized administrators may perform administrative operations.
- Refund operations must include a documented reason and audit record.
- Audit records must be append-only and must not be modified or deleted by users.

### BR10 – Security Auditor Rules

- Security Auditors have read-only access to security events and administrative audit records.
- Security Auditors must not modify users, products, orders, or payment data.

### BR11 – Catalogue Import Rules

- Only authorized users may import product catalogue files.
- Only XML and CSV formats are accepted.
- Imported product data must pass server-side validation before being stored.
- Imported records must be associated with the authorized vendor or administrative operation.
- XML processing must prevent XXE and unsafe external entity resolution.
- Invalid files and records must be rejected safely.

## 6. Order State Machine

### 6.1 Order States

The system shall support the following order states:

| State | Description |
|---|---|
| CREATED | The order has been created. |
| PENDING_PAYMENT | The order is waiting for payment confirmation. |
| PAID | Payment has been successfully verified. |
| SHIPPED | The order has been shipped to the customer. |
| DELIVERED | The order has been delivered. |
| CANCELLED | The order has been cancelled through an authorized operation. |
| REFUNDED | A refund has been successfully processed. |

### 6.2 Allowed State Transitions

The normal order flow shall be:

CREATED → PENDING_PAYMENT → PAID → SHIPPED → DELIVERED

Additional transitions:

- CREATED → CANCELLED
- PENDING_PAYMENT → CANCELLED
- PAID → REFUNDED

### 6.3 Transition Rules

- Only server-side operations may change order states.
- Payment confirmation must be received through a valid and verified PayLab webhook.
- A browser redirect must never change an order to PAID.
- An order cannot be shipped before reaching PAID.
- An order cannot be marked DELIVERED before reaching SHIPPED.
- Refunds require an authorized Store Administrator and a documented reason.
- A refund amount must not exceed the original paid amount.
- Invalid or unauthorized state transitions must be rejected and logged.
- WebSocket notifications shall be sent only after the corresponding state change has been successfully committed.

### 6.4 State Machine Diagram

The order lifecycle is designed to enforce valid transitions and prevent unauthorized changes to order status.

## 7. Use Cases

### 7.1 Customer Use Cases

The Customer shall be able to:

- Register and log in to the system.
- Browse, search, and filter products.
- View product details, images, and reviews.
- Add products to the shopping cart.
- Complete checkout and select a delivery address.
- Make simulated payments through PayLab.
- View and track personal orders through authenticated WebSocket updates.
- Submit reviews for purchased products.
- Apply valid coupons.
- Manage personal profile and delivery addresses.

**Restrictions:**

- A customer must not access another customer's orders or addresses.
- A customer must not modify product prices or order totals.
- A customer must not review products that have not been purchased.
- A customer must not subscribe to another customer's order updates.

### 7.2 Vendor Use Cases

The Vendor shall be able to:

- Manage personal account information.
- Create and manage personal products.
- Update product prices and stock quantities.
- Upload product images or add and update external image URLs for personal products.
- Import product catalogue data using supported XML or CSV files, subject to authorization and validation.
- View orders containing the vendor's own products.

**Restrictions:**

- A vendor must be approved before publishing products.
- A vendor must not modify products belonging to other vendors.
- A vendor must not access payment details.
- A vendor must not change order or payment statuses.
- A vendor must not bypass image validation or the secure image-fetching component.
- A vendor must not import products under another vendor's ownership.

### 7.3 Store Administrator Use Cases

The Store Administrator shall be able to:

- Approve or reject vendor accounts.
- Manage user accounts.
- Manage product categories.
- Manage coupons.
- Perform authorized catalogue import operations where permitted.
- Process refunds through an authorized and documented workflow.
- Review administrative operations.

**Restrictions:**

- An administrator must not mark an order as PAID without verified payment confirmation.
- An administrator must not modify or delete audit records.
- Refunds must include a reason and an audit record.
- Catalogue imports must follow the same validation and security controls as other imports.

### 7.4 Security Auditor Use Cases

The Security Auditor shall be able to:

- View security events.
- Read administrative audit records.
- Review Content Security Policy (CSP) reports.
- Analyze recorded security activities.

**Restrictions:**

- The Security Auditor has read-only permissions.
- The Security Auditor must not modify users, products, orders, or payment data.
- The Security Auditor must not modify or delete audit records.

## 8. Security Requirements

### SR01 – Authentication and Account-Takeover Protection

The system shall:

- Store passwords using bcrypt or Argon2.
- Apply rate limiting to failed login attempts.
- Temporarily lock accounts after repeated failed login attempts.
- Require TOTP-based two-factor authentication for vendors and administrators.
- Use single-use password-reset tokens that expire within 15 minutes.
- Return the same generic authentication error message for an incorrect email address or password.

### SR02 – Sessions and Cookies

The system shall:

- Configure session cookies with HttpOnly, Secure, and SameSite attributes.
- Generate a new session ID after successful login.
- Enforce idle and absolute session timeouts.
- Destroy the server-side session after logout.

### SR03 – Authorization and Access Control

The system shall:

- Enforce Role-Based Access Control (RBAC) on the server for every endpoint.
- Verify object ownership before allowing access to individual resources.
- Reject unauthorized access attempts, including IDOR attacks.
- Never accept a user's role from client-submitted data.
- Verify ownership before allowing WebSocket subscriptions to order updates.

### SR04 – Cross-Site Scripting (XSS) Protection

The system shall:

- Apply context-aware output encoding.
- Keep template auto-escaping enabled.
- Avoid using innerHTML and document.write with untrusted data; use textContent where appropriate.
- Sanitize permitted review formatting using an allow-list-based sanitization library.
- Address stored, reflected, and DOM-based XSS vulnerabilities.

### SR05 – Content Security Policy (CSP)

The system shall:

- Implement a nonce-based Content Security Policy.
- Define script-src using approved nonces.
- Set object-src to 'none'.
- Set base-uri to 'none'.
- Set frame-ancestors to 'none'.
- Initially deploy CSP in report-only mode before enforcing the policy.
- Provide a CSP reporting endpoint that records reports for Security Auditor review.

### SR06 – Cross-Site Request Forgery (CSRF) Protection

The system shall:

- Require synchronizer tokens or double-submit tokens for every state-changing request.
- Use SameSite cookie protection.
- Validate the Origin or Referer header.
- Never allow state-changing operations through GET requests.

### SR07 – Secure Product Image Upload and External Image Validation

The system shall:

- Validate uploaded image extensions, actual file types, image content, and file sizes.
- Generate safe server-side filenames.
- Store uploaded images in a controlled location that does not allow execution.
- Accept only HTTPS image URLs from explicitly approved external hosts.
- Reject malformed URLs and unsupported URL schemes.
- Validate hostnames and resolved IP addresses before making requests.
- Reject loopback, private, and link-local IP addresses.
- Validate response content types and accept only supported image formats.
- Enforce connection timeouts and response-size limits.
- Reject invalid or unsafe image responses.

### SR08 – Server-Side Request Forgery (SSRF) Protection

The external image-fetching component shall:

- Accept HTTPS URLs from an explicit allow-list of hosts only.
- Resolve hostnames and reject loopback, private, and link-local IP addresses.
- Disable automatic redirect following.
- Apply connection timeouts and response-size limits.
- Have no network route to internal services or networks.
- Reject common SSRF bypass techniques, including decimal IP addresses, alternative loopback representations, redirects, and DNS names resolving to loopback addresses.

### SR09 – API Security

The system shall:

- Keep the OpenAPI documentation consistent with the implemented API.
- Return 404 or 405 for undocumented endpoints or unsupported methods.
- Use explicit Data Transfer Objects (DTOs) or allow-lists for request fields.
- Prevent mass assignment of protected fields such as role, price, and status.
- Validate request schemas and input data.
- Apply API rate limiting.
- Return safe error messages without exposing stack traces or SQL details.
- Verify order ownership when customers retrieve order details.
- Authenticate and authorize WebSocket connections and subscriptions.
- Validate XML and CSV import requests and their contents.

### SR10 – Clickjacking Protection

The system shall:

- Prevent unauthorized websites from embedding sensitive application pages in frames.
- Use the Content Security Policy frame-ancestors directive.
- Configure appropriate X-Frame-Options headers as an additional protection.
- Require confirmation before performing sensitive operations.

### SR11 – Payment and Order Integrity

The system shall:

- Calculate all prices, discounts, and order totals on the server.
- Enforce valid order state transitions through a server-side state machine.
- Verify PayLab webhook signatures using HMAC-SHA256.
- Validate webhook timestamps within a five-minute freshness window.
- Enforce unique event IDs to prevent replay and duplicate processing.
- Verify payment amounts before updating order status.
- Never store or receive payment card numbers.
- Explain how the payment design reduces PCI DSS scope.

### SR12 – Third-Party Scripts and Dependencies

The system shall:

- Use Subresource Integrity (SRI) for external scripts and stylesheets, or host them locally.
- Restrict script sources through Content Security Policy.
- Perform dependency security audits before each milestone Git tag.
- Fix security findings or document justified exceptions.

### SR13 – CORS Security

The system shall:

- Allow only the store's exact origin through the CORS policy.
- Allow credentials only for the authorized origin.
- Never use a wildcard (*) origin with credentials.
- Avoid enabling unnecessary cross-origin methods and headers.

### SR14 – Transport Security, Headers, Secrets, and Logging

The system shall:

- Enforce HTTPS and enable HTTP Strict Transport Security (HSTS).
- Configure X-Content-Type-Options, Referrer-Policy, and a restrictive Permissions-Policy.
- Prevent secrets from being stored in source code or committed to the repository.
- Maintain append-only administrative audit logs.
- Maintain a unified test log containing expected results, actual results, evidence, date, and tester information.

### SR15 – XML and CSV Import Security

The system shall:

- Accept only the explicitly supported XML and CSV formats.
- Validate uploaded catalogue files before processing.
- Enforce file size, parsing time, and resource limits.
- Disable XML external entities and DTD processing.
- Prevent XXE attacks and unauthorized external resource access.
- Validate XML structure and CSV column definitions.
- Reject malformed or unexpected data safely.
- Prevent unauthorized modification of vendor ownership and protected product fields.
- Use safe database operations and transactions during import processing.
- Record import outcomes and relevant security failures without logging sensitive data.

### SR16 – WebSocket Security

The system shall:

- Require authenticated sessions for WebSocket connections.
- Validate the connection origin against the authorized store origin.
- Authorize every order subscription using server-side ownership checks.
- Reject subscriptions to unauthorized order identifiers.
- Avoid transmitting sensitive information through WebSocket messages.
- Close connections when sessions expire or users log out.
- Apply reasonable connection and message limits.
- Ensure that WebSocket notifications are sent only after successful server-side state changes.