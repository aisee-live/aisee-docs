# AISee Platform User Manual

Welcome to the AISee Platform. This manual provides a comprehensive guide to using the AISee services, including user management, product analysis, and credit systems.

## 1. Introduction
AISee is an AI-powered platform designed to analyze websites and applications (AEO - AI Engine Optimization). It provides deep insights through hierarchical task orchestration, helping you understand how AI engines perceive and interact with your digital products.

## 2. Getting Started

### 2.1 Authentication
To use most AISee features, you must be authenticated.

1.  **Request a Verification Code**: Use the `/vcode` endpoint to receive a code via email for registration or password reset.
2.  **Registration**: Register using the `/register` endpoint with your email, password, and the verification code.
3.  **Login**: Obtain an `access_token` via the `/login` endpoint.
4.  **Token Management**: Use the `access_token` in the `Authorization: Bearer <token>` header for subsequent requests. The system uses refresh tokens (via cookies) to keep you logged in.

### 2.2 OAuth Login
You can also sign in using third-party providers like Google or GitHub via the `/oauth` routes.

---

## 3. Core Workflows

### 3.1 Managing Products
Before running an analysis, you must define a "Product" (e.g., your website).

*   **Preview**: Use `/product/preview` with a URL to auto-fetch metadata (title, description, logo).
*   **Create**: Use `/product/create` to save your website/app to the platform.
*   **List**: View all your products at `/product`.

### 3.2 Running Analysis (AEO)
This is the core feature of AISee.

1.  **Start Analysis**: Call `/task/analyze-product` with your `product_id`.
    *   This creates a **Task Tree** (a root task with multiple sub-tasks).
    *   The system checks if you have sufficient credits.
    *   Analysis runs asynchronously.
2.  **Monitoring**: Use `/task/detail/{task_id}` to check the status of your analysis.
    *   **Status Levels**: `pending` -> `in_progress` -> `completed` (or `failed`).
3.  **Retrying**: If a specific part of the analysis fails, you can use `/task/analyze-task` to re-execute that specific branch.

### 3.3 Understanding Task Results
Results are hierarchical. A root task represents the overall analysis, while child tasks represent specific modules (e.g., "SEO Audit", "Content Analysis").
*   Results are typically returned in JSON format within the task detail.
*   Final scores (if applicable) are aggregated at the product level.

---

## 4. Social Media Management (Post Agent)
AISee integrates with Post Agent to provide a comprehensive social media scheduling and orchestration suite. This allows you to transform AEO analysis insights into actionable social content.

### 4.1 Connecting Social Channels
Before you can post, you must connect your social media profiles.
*   **Browse Platforms**: Use `GET /integrations/` to see all supported social networks.
*   **Authorization**: Use `GET /integrations/social/:provider` to obtain an OAuth URL for the target platform (e.g., Twitter, LinkedIn).
*   **Finalize Connection**: After the OAuth flow, use `POST /integrations/social/:provider/connect` to save the channel to your organization.

### 4.2 Content Creation and Scheduling
*   **Manual Creation**: Use `POST /posts/` to create and schedule content. You can specify target integrations, content value, and scheduling dates.
*   **AI Generation**: Use `POST /posts/generator` to generate social media posts based on a specific topic, tone, and set of integrations.
*   **Auto-Scheduling**: Use `GET /posts/find-slot` to let the system automatically find the next available posting time based on your configured schedule.
*   **Drafts**: Use `POST /posts/generator/draft` to generate content for review before scheduling.

### 4.3 Media Management
Manage your visual assets in the integrated media library.
*   **Upload**: Use `POST /media/upload-simple` for direct file uploads.
*   **AI Image Generation**: Use `POST /media/generate-image` to create custom visuals from text prompts using AI models.

### 4.4 Analytics & Reporting
Monitor the performance of your social media campaigns.
*   **Dashboard Summary**: Use `GET /dashboard/summary` for a high-level view of engagement metrics and post volume.
*   **Deep Analytics**: Access detailed time-series data via `GET /analytics/:integrationId`.

---

## 5. Credits and Subscriptions

### 5.1 Credit System
AISee uses a credit-based billing system. Credits are consumed based on the AI models used during analysis.
*   **Subscription Credits**: Monthly/Yearly allowance from your plan.
*   **Top-up Credits**: Purchased separately.
*   **Bonus Credits**: Granted through promotions.

Check your balance at `/credit-balance/{user_id}/balance`.

### 5.2 Subscriptions
*   **Purchase**: Use `/subscription/checkout` to be redirected to a Stripe payment page.
*   **Manage**: Use `/subscription/customer-portal` to access the Stripe-hosted portal where you can:
    *   Cancel your subscription.
    *   Upgrade/Downgrade your plan.
    *   Update payment methods.

---

## 6. Administrative Features (Superusers Only)
Admin users have access to system-wide management tools:
*   **User Management**: Create, update, and deactivate users.
*   **Task Monitoring**: View system-wide task health and manually fix stuck tasks via `/task/health-check`.
*   **Configuration**: Set model pricing and system secrets.

---

## 7. Troubleshooting & FAQ

*   **Task is "Stuck"**: If a task remains `pending` despite all children being `completed`, the system usually auto-fixes it within minutes. You can also refresh the detail page to trigger a check.
*   **Insufficient Credits**: If your analysis won't start, check your balance. You may need to purchase a top-up or upgrade your plan.
*   **Verification Code Not Received**: Check your spam folder. Codes are valid for a limited time.

For further assistance, please contact support or refer to the technical API documentation.
