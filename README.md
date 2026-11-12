# Secure University e-Store

## Project Information

- **Course:** Secure Electronic Commerce (SEC 906425)
- **University:** Al al-Bayt University
- **Semester:** First Semester 2026/2027
- **Group Number:** 1
- **Project Status:** Milestone 1 – Requirements and Design

## Team Members

1. رتاج ثائر محمود صافي
2. ليان أحمد عبدالله البرماوي

## Project Overview

The Secure University e-Store is a university e-commerce platform that supports both Business-to-Consumer (B2C) shopping and a Consumer-to-Consumer (C2C) marketplace.

The platform is designed to allow customers to browse products, manage shopping carts, place orders, make simulated payments, track orders, and review purchased products.

Vendors can manage their products and inventory, while store administrators manage users, vendors, categories, and coupons. Security auditors have read-only access to security events and audit records.

## Main Security Objectives

The project focuses on identifying, demonstrating, and mitigating common web application security vulnerabilities, including:

- Cross-Site Scripting (XSS)
- Cross-Site Request Forgery (CSRF)
- XML External Entity (XXE)
- Server-Side Request Forgery (SSRF)
- API security vulnerabilities
- Clickjacking
- Price tampering
- Payment callback forgery
- Account takeover

A separate intentionally vulnerable LAB environment will be developed for authorized security testing and comparison with the secured application.

## Project Scope

The project will include:

- E-commerce application and relational database.
- REST API documented using OpenAPI 3.
- Simulated payment service (PayLab).
- Reverse proxy and HTTPS configuration.
- Internal services and email testing environment.
- Separate vulnerable LAB environment.
- Security testing, documentation, and evidence.

## Repository Structure

The repository will organize application code, API documentation, database scripts, payment services, security testing, and milestone documentation.

## Development Approach

All project components will be developed from scratch according to the approved project specification.

Implementation details and technology selections will be documented as the project progresses.

## Current Progress

- [x] Development environment prepared.
- [x] Project directory created.
- [x] Initial M1 documentation structure created.
- [ ] Requirements analysis.
- [ ] Database ERD design.
- [ ] Architecture and Data Flow Diagram.
- [ ] STRIDE threat analysis.
- [ ] OpenAPI 3 draft.
- [ ] Team work plan.

## Safety Notice

All security experiments and vulnerability testing will be conducted only within the group's own local project environment.
