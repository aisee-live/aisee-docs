# Orchestrator Service API Reference

The Orchestrator is the central engine for task execution, product management, and billing.

## 📦 Product Management

### Create Product
`POST /product/create`

Registers a website or application for analysis.

**Request Body (JSON):**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `url` | string | Yes | Root domain (e.g., `https://example.com`) |
| `name` | string | No | Display name |
| `product_type` | enum | No | `website`, `application`, `brand`, etc. |
| `tags` | array | No | List of categorization tags |

**Example:**
```bash
curl -X POST "https://api.aisee.live/product/create" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "url": "https://myaisee.com",
       "name": "My AISee Project",
       "product_type": "website"
     }'
```

### Preview Product Info
`POST /product/preview`

Fetches metadata (title, logo, tech stack) from a URL before creating it.

---

## 🚀 Task & Analysis (AEO)

### Start Product Analysis
`POST /task/analyze-product`

Triggers a full hierarchical analysis of a product.

**Request Body (JSON):**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `product_id` | string | Yes | ID of the product to analyze |
| `task_template_id`| string | No | Specific template to use |
| `stream` | boolean| No | Enable streaming response |

**Example:**
```bash
curl -X POST "https://api.aisee.live/task/analyze-product" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "product_id": "prod_123",
       "stream": false
     }'
```

### Get Task Detail
`GET /task/detail/{task_id}`

Retrieves the current status and results of a task tree.

**Example:**
```bash
curl "https://api.aisee.live/task/detail/task_abc123" \
     -H "Authorization: Bearer YOUR_TOKEN"
```

---

## 💳 Credits & Billing

### Get Balance
`GET /credit-balance/{user_id}/balance`

Returns a breakdown of subscription, top-up, and bonus credits.

**Example:**
```bash
curl "https://api.aisee.live/credit-balance/user_999/balance" \
     -H "Authorization: Bearer YOUR_TOKEN"
```

### Create Subscription Checkout
`POST /subscription/checkout`

Generates a Stripe Checkout URL for a subscription plan.

**Request Body (JSON):**
| Field | Type | Description |
| :--- | :--- | :--- |
| `product_code` | string | e.g., `starter-month`, `pro-year` |
| `interval` | string | `month` or `year` |
| `success_url` | string | Frontend redirect after payment |
| `cancel_url` | string | Frontend redirect after cancellation |

**Example:**
```bash
curl -X POST "https://api.aisee.live/subscription/checkout" \
     -H "Authorization: Bearer YOUR_TOKEN" \
     -H "Content-Type: application/json" \
     -d '{
       "product_code": "pro-month",
       "interval": "month",
       "success_url": "https://app.aisee.live/success",
       "cancel_url": "https://app.aisee.live/pricing"
     }'
```

---

## 🛠️ System & Health

### Health Check
`GET /health`

**Response:**
```json
{
  "status": "healthy",
  "service": "AISee Orchestrator",
  "version": "1.0.0"
}
```
