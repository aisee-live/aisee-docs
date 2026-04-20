# Analysis Service API Reference

The Analysis Service is the central engine for task execution, product management, and billing.

**Base URL:** `https://api.aisee.live/` (configurable)  
**Auth:** JWT Bearer token in `Authorization` header

---

## 📦 Product Management

### Check URL Accessibility
`GET /check-url`

Checks if a URL is reachable and accessible. It automatically attempts both `https://` and `http://` protocols, as well as `www.` variants if needed.

**Query Parameters:**
| Parameter | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `url` | string | Yes | The URL to check accessibility for |

**Response Example:**
```json
{
  "accessible": true,
  "status_code": 200,
  "original_url": "example.com",
  "successful_url": "https://example.com",
  "protocol_used": "https",
  "attempts": [
    {
      "url": "https://example.com",
      "accessible": true,
      "error": null
    }
  ]
}
```

---

### Preview Product Info
`POST /product/preview`

Fetches metadata (title, logo, tech stack, social media) from a URL before creating it. Useful for auto-filling the creation form.

**Request Body:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `url` | string | Yes | The URL to scrape information from |

---

### Create Product
`POST /product/create`

Registers a website or application for analysis. If the URL has already been registered by the user, returns the existing product.

**Request Body:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `url` | string | Yes | Root domain (e.g., `https://example.com`) |
| `name` | string | No | Display name |
| `product_type` | enum | Yes | `website`, `application`, `brand`, etc. |
| `logo_url` | string | No | URL to the product logo |
| `tags` | array | No | List of categorization tags |
| `data` | object | No | Custom metadata (tech stack, description, etc.) |

---

### List Products
`GET /product`

Returns a paginated list of products.

**Query Parameters:**
| Parameter | Type | Description |
| :--- | :--- | :--- |
| `page` | integer | Page number (default: 1) |
| `page_size` | integer | Items per page (default: 20) |
| `name` | string | Filter by name or URL |
| `status` | string | Filter by status |

---

### Get Product Detail
`GET /product/{product_id}`

Retrieves detailed information about a specific product, including aggregated analysis results.

---

### Update Product
`PUT /product/{product_id}`

Updates product metadata. Supports partial updates.

---

### Delete Product
`DELETE /product/{product_id}`

Permanently removes a product and its associated task history.

---

## 🚀 Analysis & Tasks (AEO)

### Start Product Analysis
`POST /task/analyze-product`

Triggers a full hierarchical AEO analysis. This is the primary entry point for the analysis engine.
1.  Creates a versioned task tree.
2.  Calculates and freezes the estimated credit cost.
3.  Dispatches analysis agents.

**Request Body:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `product_id` | string | Yes | ID of the product to analyze |
| `task_template_id` | string | No | Specific template to use (default: platform default) |
| `stream` | boolean | No | Enable streaming status updates (via SSE) |
| `use_demo` | boolean | No | Use mock data for testing (doesn't consume credits) |

---

### Re-execute Task (Retry)
`POST /task/analyze-task`

Re-triggers a failed analysis task or a specific branch of the task tree. Preserves history by creating a task copy for the new execution.

**Request Body:**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `task_id` | string | Yes | ID of the task to re-run |

---

### Get Analysis Detail
`GET /task/detail/{task_id}`

Retrieves the status, progress, and results of a task tree. Returns a simplified hierarchical structure optimized for frontend rendering.

---

### Get Task Tree (Technical)
`GET /task/tree/{task_id}`

Returns the full technical task tree including inputs, parameters, schemas, and dependencies. (Full detail available to SuperAdmins).

---

### List Task History
`GET /task`

Returns a paginated list of root analysis tasks.

**Query Parameters:**
| Parameter | Type | Description |
| :--- | :--- | :--- |
| `product_id` | string | Filter by product |
| `status` | string | Filter by status (`pending`, `completed`, `failed`, etc.) |
| `user_id` | string | Filter by user (Admins only) |

---

### Get Task Executions
`GET /task/executions/{task_id}`

Retrieves all execution attempts (original + retries) for a specific task, ordered by sequence.

---

## 🛠️ Action Orchestration
Actions are identified optimization gaps found during analysis. This service manages the playbooks used to fix them.

### List Actions
`GET /action`

Retrieves a paginated list of actions (optimization recommendations) for a specific task or product.

---

### Update Action
`PUT /action/{action_id}`

Updates an action's status or its relative position in the list (using fractional indexing).

---

### Estimate Action Cost
`GET /action/{action_id}/estimate-cost`

Calculates the exact credit cost required to generate the specific "fix" tasks (content, SEO updates, etc.) for an action.

---

### Generate Action Tasks
`POST /action/{action_id}/generate-tasks`

Triggers the AI generation of executable tasks to resolve an action gap.
- Freezes credits.
- Runs specialized agents to generate content or technical fixes.

---

### Poll Generated Tasks
`GET /action/{action_id}/generated-tasks`

Polls the status of an ongoing generation request.

---

## 💳 Billing & Transactions

### Get Credit Balance
`GET /credit-balance/{user_id}/balance`

Returns a detailed breakdown of the user's current credits:
- `subscription_credits`: Monthly plan allowance.
- `topup_credits`: Purchased credits.
- `bonus_credits`: Promotional credits.

---

### List Transactions
`GET /transaction`

Returns a paginated list of all financial and credit operations (purchases, consumption, refunds).

**Query Parameters:**
| Parameter | Type | Description |
| :--- | :--- | :--- |
| `type` | string | Filter by transaction type (e.g., `credit_top_up`, `credit_consumption`) |
| `status` | string | Filter by status (`success`, `pending`, `failed`) |
| `channel` | string | Filter by payment channel (`stripe`, `internal_credit`) |
| `user_id` | string | Filter by user ID |

---

### Create Checkout Session
`POST /subscription/checkout`

Generates a Stripe Checkout URL for purchasing subscriptions or credit top-ups.
