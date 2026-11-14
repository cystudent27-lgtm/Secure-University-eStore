# Secure University e-Store — M1 Technology Stack

## 1. Selected Stack

The project will use one consistent stack across the secure store and its intentionally vulnerable lab variant, except where a deliberately vulnerable component is required for the lab exercise.

| Component | Selected technology | M1 rationale / intended use |
|---|---|---|
| Backend | Node.js 22 LTS with Express | JavaScript backend for REST endpoints, business rules, session handling, authorization, and WebSocket integration. |
| Frontend | React with Vite | Component-based storefront and role-specific interfaces for customers, vendors, administrators, and security auditors. |
| Database | MySQL 8.0.16 or later | Relational storage; compatible with the current ERD and its MySQL data types, constraints, and `AUTO_INCREMENT` keys. |
| API description | OpenAPI 3.0.3 | Documents the REST API contract in `api/openapi.yaml`. |
| Real-time updates | WebSocket over WSS | Authenticated order-status notifications; subscriptions are authorized against the signed-in user and order ownership. |
| Reverse proxy | Nginx | HTTPS termination, security headers, and reverse proxying, including WebSocket upgrade forwarding. |
| Containers | Docker Desktop with WSL2 and Docker Compose v2 | Repeatable local development environment and isolated service networks. |
| Payment simulation | PayLab (group-built service) | Hosted test-payment flow and signed HMAC-SHA256 webhook; the store does not receive or store card numbers. |
| Email testing | Mailpit | Local capture of password-reset and authentication-related emails. |
| Version control | Git and GitHub | Source history, collaboration, M1 release/tag, and milestone archive. |
| Security testing | OWASP ZAP, Burp Suite Community, browser DevTools, Postman/curl | API inspection, security testing, and evidence collection. |
| Backend tests | Jest and Supertest | Automated backend and HTTP API tests. |

## 2. Architecture and Security Notes

- The browser communicates with the application through Nginx; it must not connect directly to MySQL or internal-only services.
- The backend owns validation, authorization, price calculations, order-state transitions, and payment verification.
- MySQL is not exposed to the public network.
- WebSocket connections use WSS in the deployed environment, validate the authenticated session and `Origin`, and authorize each order subscription.
- PayLab payment confirmation is accepted only after server-side webhook verification, including HMAC-SHA256, timestamp freshness, event uniqueness, payment reference, and amount checks.
- External image URLs are fetched only through a restricted image-fetching component with SSRF protections.
- XML catalogue imports must disable external entity resolution (XXE) and validate imported fields before database writes.
- Secrets and real payment-card data must not be committed to the repository.

## 3. Milestone Boundary

This document declares the technology choices for M1. It does not claim that the application, database, PayLab, containers, or security tests have already been implemented. Their implementation and verification are planned for the relevant later milestones.

## 4. Consistency

MySQL 8.0.16+ remains the database choice used by the ERD. The backend, frontend, proxy, container, and supporting-service choices above are within the technologies permitted by the course specification.
