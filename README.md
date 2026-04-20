# AISee Documentation

This directory contains the official documentation for the AISee platform, a sophisticated AI Engine Optimization (AEO) system designed for hierarchical website and application analysis.

## 📖 Documentation Index

### 1. [User Manual](./user-manual.md)
**Target Audience:** Users and Product Managers.
A comprehensive guide on how to use the platform, from registration and product creation to running deep AEO analyses and managing subscriptions.

### 2. [Auth Service API](./auth-api.md)
**Target Audience:** Developers.
Technical specifications for the `aisee_auth` service, covering JWT authentication, OAuth integrations, and secure user management.

### 3. [Orchestrator Service API](./orchestrator-api.md)
**Target Audience:** Developers.
Technical details for the `aisee_orchestrator` service, the core engine that handles task trees, credit consumption, Stripe payments, and product data.

## 🏗️ Project Architecture

AISee is built on a microservices-inspired architecture:

- **Auth Service**: Manages identity, permissions, and security.
- **Orchestrator Service**: The "brain" of the platform. It coordinates complex task hierarchies, manages the billing engine, and interfaces with AI analyzers.
- **Shared Library (`aisee_shared`)**: Common models, database utilities, and middleware used across services.

## 🚀 Key Features

- **Hierarchical Task Trees**: Complex analyses are broken down into manageable sub-tasks for parallel execution and detailed reporting.
- **Credit-Based Billing**: Granular consumption tracking based on the specific AI models used.
- **Seamless Stripe Integration**: Self-service subscription management and checkout.
- **Interactive Results**: Deep insights into how AI engines perceive digital products.

---
*Last updated: April 17, 2026*
