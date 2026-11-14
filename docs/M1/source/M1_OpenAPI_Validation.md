# OpenAPI Specification Validation Report

## 1. Project Information

- **Project Name:** Secure University e-Store
- **Course:** Secure Electronic Commerce (SEC 906425)
- **University:** Al al-Bayt University
- **Group Number:** 1
- **Team Members:**
  - رتاج ثائر محمود صافي
  - ليان أحمد عبدالله البرماوي
- **API Specification:** OpenAPI 3.0.3
- **API Version:** 1.2.0
- **Validation Date:** November 12, 2026

## 2. Validation Tool

The API specification was reviewed using Swagger Editor:

https://editor.swagger.io/

## 3. Validation Results

The OpenAPI specification was successfully imported into Swagger Editor.

The following checks were performed:

- The OpenAPI document was loaded successfully.
- API paths and HTTP methods were displayed.
- API tags and endpoint documentation were displayed.
- Request and response schemas were recognized.
- No visible YAML parsing errors were reported.
- No visible red validation errors were observed during the review.

## 4. API Coverage

The specification documents the following API areas:

- Authentication and customer registration.
- Login, logout, password reset, and TOTP MFA.
- Public product browsing.
- Vendor product management.
- Shopping cart operations.
- Order creation and order status retrieval.
- Simulated PayLab payment processing.
- Payment webhook verification.
- Product reviews.
- Administrative user, vendor, category, coupon, and refund operations.
- Security Auditor access to audit logs and security events.

## 5. Security Considerations

The API specification documents the following security controls:

- Secure session cookies using HttpOnly, Secure, and appropriate SameSite attributes.
- CSRF protection for authenticated state-changing requests.
- TOTP MFA for vendor and administrator accounts.
- Role-based authorization and resource ownership validation.
- Server-side price and order-total calculations.
- HMAC-SHA256 payment webhook signature verification.
- Webhook timestamp freshness and duplicate-event protection.
- External product image URL validation and SSRF protections.
- Restricted access to administrative and audit operations.

## 6. Implementation Notes

The API specification is a design-stage document and does not demonstrate that the backend has been implemented or functionally tested.

The following points must be addressed during implementation:

- Payment refunds must be processed for the full verified payment amount.
- Refund status must be updated only after successful PayLab confirmation.
- Refund requests must be protected against duplicate processing.
- PayLab refund confirmation handling must be defined and implemented.
- Runtime API, database, and security testing will be performed in later implementation stages.

## 7. Conclusion

The Secure University e-Store OpenAPI specification, version 1.2.0, was successfully imported and visually reviewed using Swagger Editor.

No visible YAML parsing or validation errors were observed during this review.

This validation confirms the specification's successful loading and visual structure only. It does not constitute runtime, backend, database, or security functional testing.

## M1 API Coverage Update

The OpenAPI 3.0.3 draft also documents:
- `POST /vendor/products/import` for authorized XML and CSV catalogue imports.
- `POST /vendor/products/{productId}/image` for product image uploads.
- `POST /vendor/products/{productId}/image-from-url` for validated external HTTPS image URLs.
- An `x-websocket` root extension describing the authenticated order-status WebSocket channel, subscription authorization, and event payload. OpenAPI 3.0 does not natively model WebSocket operations, so this interface is documented separately from REST paths.

The YAML syntax was revalidated after these additions. This is an M1 API design draft; endpoint implementation and runtime testing are deferred to later milestones.
