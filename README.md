# AISee: All-in-One GEO Toolkit

AISee is a sophisticated **Generative Engine Optimization (GEO)** platform designed to empower brands and businesses in the AI-first internet era. Unlike traditional SEO, AISee focuses specifically on optimizing digital presence for AI engines such as **ChatGPT, Claude, Gemini, and Perplexity**.

AISee helps you understand how AI models perceive your brand, identifies visibility gaps, and provides actionable insights to ensure your products are discoverable in AI-generated answers.

---

## 🚀 Key Value Propositions

- **AI Visibility Analysis**: Deep-dive reports on how different LLMs interpret and rank your brand or project.
- **Competitor Comparison**: Benchmark your AI presence against industry rivals.
- **Optimization Roadmap**: Step-by-step guidance to improve recognition in AI search results.
- **Automated Distribution**: Integrated social media orchestration to amplify your optimized content.

---

## 🏗️ System Architecture

AISee is built on a modular, microservices-oriented architecture to ensure scalability and reliability.

### 1. Core Services (`aisee-core`)
The "brain" of the platform, built with FastAPI and PostgreSQL.
- **Auth Service**: Secure JWT-based identity management and OAuth (Google/GitHub) integration.
- **Orchestrator Service**: Manages complex hierarchical task trees, product metadata, and the credit-based billing system.
- **Agent Service**: Executes asynchronous website analysis using specialized AI agents.
- **MCP Server**: Provides Model Context Protocol (MCP) tools for direct integration with AI clients like Claude Desktop.

### 2. User Interfaces
- **Main App (`aisee-app`)**: The primary web portal for users to manage products, view analysis reports, and handle subscriptions.
- **Management Console (`aisee-manage`)**: Administrative dashboard for system monitoring, user management, and configuration.
- **Agent Interface (`aisee-agent`)**: A specialized interface for interacting with and monitoring the AI analysis agents.

### 3. Social Orchestration (`postiz-app`)
A comprehensive social media scheduling and distribution suite (based on Postiz).
- **Multi-Platform Support**: Schedule content across Twitter, LinkedIn, and more.
- **AI-Assisted Creation**: Transform AEO analysis insights directly into social media posts.
- **Analytics**: Track engagement and performance of your distributed content.

---

## 📖 Documentation Guide

### For Users & Product Managers
- **[User Manual](./user-manual.md)**: A complete guide to getting started, managing products, and interpreting AEO results.
- **[AEO Checklist](https://aisee.live/blog/the-aeo-checklist-what-to-fix-today-to-show-up-in-ai-search)**: Practical steps to optimize your website for AI.

### For Developers
- **[Auth API](./auth-api.md)**: Integration guide for identity and security.
- **[Orchestrator API](./orchestrator-api.md)**: Technical specs for task management and analysis orchestration.
- **[Postiz Backend API](./postiz-api.md)**: Reference for the social media distribution layer.
- **[Deployment Guide](./deployment.md)**: Instructions for setting up the full AISee stack.

---

## 🌐 Resources
- **Official Website**: [https://aisee.live](https://aisee.live)
- **Technical Blog**: [https://aisee.live/blog](https://aisee.live/blog)
- **Support**: [contact@aisee.live](mailto:contact@aisee.live)

---
*Last updated: April 20, 2026*
