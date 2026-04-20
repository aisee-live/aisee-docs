# Post Agent Service API Reference

Post Agent is an open-source social media scheduling platform. This document covers all backend REST API endpoints.

**Base URL:** `https://api-post.aisee.live/api` (configurable)  
**Auth:** JWT Bearer token in `Authorization` header

---

### Get Subscription
`GET /user/subscription`

Returns active subscription details. Requires **Admin** role.

---

### Get Subscription Tiers
`GET /user/subscription/tiers`

Returns available pricing tiers. Requires **Admin** role.

---

### Join Organization
`POST /user/join-org`

**Request Body:** `{ "org": "<invite-code>" }`

**Response:** `{ "id": "org-uuid" | null }`

---


### Track Event
`POST /user/t`

Record a frontend analytics event.

**Request Body:**
| Field | Type | Required |
|:---|:---|:---|
| `tt` | TrackEnum | Yes |
| `fbclid` | string | No |
| `additional` | object | No |

---

### Impersonate User *(SuperAdmin only)*
`GET /user/impersonate?name=<name>`  
`POST /user/impersonate` — Body: `{ "id": "user-uuid" }`

---

## Dashboard Endpoints

**Prefix:** `/dashboard`  Requires authentication.

### Summary
`GET /dashboard/summary`

**Query Params:**
| Param | Type | Description |
|:---|:---|:---|
| `startDate` | ISO date | Start of range |
| `endDate` | ISO date | End of range |
| `integrationId` | string | Filter by integration |
| `channel` | string | Filter by channel |

**Response:** Aggregated post and engagement stats.

---

### Posts Trend
`GET /dashboard/posts-trend?period=<period>`

Returns time-series post volume data.

---

### Traffic Analytics
`GET /dashboard/traffics`

**Query Params:** `startDate`, `endDate`, `integrationId`, `channel`

---

### Impressions
`GET /dashboard/impressions`

**Query Params:** `period`, `integrationId`, `channel`, `startDate`, `endDate`

---

### Post Engagement
`GET /dashboard/post-engagement?days=<days>`

Returns per-post engagement metrics over the specified number of days.

---

## Posts Endpoints

**Prefix:** `/posts`  Requires authentication. Some actions require `POSTS_PER_MONTH` policy.

### List Posts
`GET /posts/`

**Query Params:**
| Param | Type |
|:---|:---|
| `status` | string |
| `type` | string |
| `integrationId` | string |
| `page` | number |
| `pageSize` | number |
| `group` | string |

---

### Get Post
`GET /posts/:id`

Returns a single post by ID.

---

### Get Posts by Group
`GET /posts/group/:group`

Returns all posts in a group.

---

### Create Post
`POST /posts/`

Requires `POSTS_PER_MONTH` policy.

**Request Body:**
| Field | Type | Description |
|:---|:---|:---|
| `type` | string | Post type |
| `source` | string | Content source |
| `order` | number | Display order |
| `value` | object | Post content |
| `integration` | string[] | Target integration IDs |
| `group` | string | Group identifier |
| `settings` | object | Platform-specific settings |

---

### Delete Post
`DELETE /posts/:group`

Deletes all posts in a group.

---

### Retry Post
`POST /posts/:id/retry`

Re-schedules a failed post.

---

### Change Post Date
`PUT /posts/:id/date`

**Request Body:** `{ "date": "<ISO datetime>" }`

---

### Find Free Slot
`GET /posts/find-slot`  
`GET /posts/find-slot/:integrationId`

Returns the next available posting time.

**Response:** `{ "date": "2025-01-01T10:00:00Z" }`

---

### Get Post Statistics
`GET /posts/:id/statistics`

---

### Check Shortlink
`POST /posts/should-shortlink`

**Request Body:** `{ "messages": ["<text>", ...] }`

**Response:** `{ "ask": true }`

---

### Separate Posts
`POST /posts/separate-posts`

Split long content into multiple posts.

**Request Body:**
| Field | Type |
|:---|:---|
| `content` | string |
| `len` | number |

**Response:** Array of content strings.

---

### List Tags
`GET /posts/tags`

**Response:** `{ "tags": [...] }`

---

### Create Tag
`POST /posts/tags`

**Request Body:**
| Field | Type |
|:---|:---|
| `label` | string |
| `color` | string (hex) |

---

### Update Tag
`PUT /posts/tags/:id`

Same body as Create Tag.

---

### Post Releases
`GET /posts/release-list`

**Query Params:** `postId`, `page`, `pageSize`

---

### Old Posts
`GET /posts/old?date=<date>`

Returns posts scheduled before the specified date.

---

### Generate Posts (Stream)
`POST /posts/generator`

Requires `POSTS_PER_MONTH` policy. Returns Server-Sent Events.

**Request Body:**
| Field | Type |
|:---|:---|
| `topic` | string |
| `tone` | string |
| `quantity` | number |
| `integrations` | string[] |
| `type` | string |

---

### Generate Draft Posts
`POST /posts/generator/draft`

Same body as Generate Posts. Returns array of draft post objects (non-streaming).

---

### Create Comment
`POST /posts/:id/comments`

**Request Body:** `{ "comment": "text" }`

---

## Integrations Endpoints

**Prefix:** `/integrations`  Requires authentication. Channel creation requires `CHANNEL` policy.

### List All Integrations
`GET /integrations/`

Returns all supported social platform integrations.

---

### List Connected Integrations
`GET /integrations/list`

Returns integrations connected to the current organization.

**Response:** `{ "integrations": [...] }`

---

### Get Single Integration for Order
`GET /integrations/:id?order=<order>`

---

### Get Integration Profile
`GET /integrations/profile/:id`

**Response:**
```json
{
  "integration": { ... },
  "postsCount": 42,
  "stats": { ... },
  "userEmail": "user@example.com"
}
```

---

### Get OAuth URL for Platform
`GET /integrations/social/:integration`

**Query Params:**
| Param | Type |
|:---|:---|
| `refresh` | boolean |
| `externalUrl` | string |
| `onboarding` | boolean |

**Response:** `{ "url": "https://..." }`

---

### Connect Social Platform
`POST /integrations/social/:integration/connect`

**Request Body:**
| Field | Type |
|:---|:---|
| `state` | string |
| `code` | string |
| `timezone` | string |
| `refresh` | boolean |

**Response:** Integration object + `{ "onboarding": true }`

---

### Save Provider Page
`POST /integrations/provider/:id/connect`

Provider-specific connection finalization.

---

### Update Integration Group
`PUT /integrations/:id/group`

**Request Body:** `{ "group": "group-name" }`

---

### Update Customer Name
`PUT /integrations/:id/customer-name`

**Request Body:** `{ "name": "display-name" }`

---

### Update Provider Settings
`POST /integrations/:id/settings`

**Request Body:** `{ "additionalSettings": "<json-string>" }`

---

### Set Nickname / Picture
`POST /integrations/:id/nickname`

**Request Body:**
| Field | Type |
|:---|:---|
| `name` | string |
| `picture` | string (URL) |

---

### Set Posting Times
`POST /integrations/:id/time`

**Request Body:** Posting schedule object.

---

### Enable / Disable Channel
`POST /integrations/enable` — Body: `{ "id": "integration-id" }`  
`POST /integrations/disable` — Body: `{ "id": "integration-id" }`

---

### Delete Channel
`DELETE /integrations/`

**Request Body:** `{ "id": "integration-id" }`

---

### Get Customers
`GET /integrations/customers`

---

### Integration Function Call
`POST /integrations/function`

Call a platform-specific function (e.g., fetch user mentions).

**Request Body:**
| Field | Type |
|:---|:---|
| `id` | string |
| `name` | string |
| `data` | object |

---

### Get Mentions
`POST /integrations/mentions`

Same body as Integration Function Call.

---

### Internal Plugs
`GET /integrations/:identifier/internal-plugs`

---

### Plug List
`GET /integrations/plug/list`

**Response:** `{ "plugs": [...] }`

---

### Get Plugs by Integration
`GET /integrations/:id/plugs`

---

### Create Plug
`POST /integrations/:id/plugs`

**Request Body:**
| Field | Type |
|:---|:---|
| `identifier` | string |
| `alias` | string |
| `hooks` | array |

---

### Toggle Plug Activation
`PUT /integrations/plugs/:id/activate`

**Request Body:** `{ "status": true }`

---

### Telegram Updates
`GET /integrations/telegram/updates?word=<word>&id=<id>`

---

## Analytics Endpoints

**Prefix:** `/analytics`  Requires authentication.

### Integration Analytics
`GET /analytics/:integrationId?date=<days>`

Default `date`: 30 days.

---

### Post Analytics
`GET /analytics/post/:postId?date=<days>`

---

## Media Endpoints

**Prefix:** `/media`  Requires authentication.

### List Media
`GET /media/?page=<page>`

Returns paginated media library items.

---

### Upload File
`POST /media/upload-simple`

Multipart form upload.

**Query:** `preventSave=true` returns a temporary URL without saving to the library.

**Response:** Media object or `{ "path": "<url>" }`

---

### Upload via Endpoint
`POST /media/:endpoint`

Upload through a specific provider endpoint.

---

### Upload to Server
`POST /media/upload-server`

**Request:** `multipart/form-data` with a single file field.

---

### Save Media from URL
`POST /media/save-media`

**Request Body:** `{ "name": "<url-or-name>" }`

---

### Save Media Information
`POST /media/information`

**Request Body:**
| Field | Type |
|:---|:---|
| `id` | string |
| `data` | object |

---

### Delete Media
`DELETE /media/:id`

---

### Generate Image
`POST /media/generate-image`

**Request Body:** `{ "prompt": "description" }`

**Response:** `{ "output": "<base64-string>" }`

---

### Generate Image with Auto-Save
`POST /media/generate-image-with-prompt`

**Request Body:** `{ "prompt": "description" }`

**Response:** Saved media object.

---

### Video Options
`GET /media/video-options`

Returns available video generation templates.

---

### Generate Video
`POST /media/generate-video`

**Request Body:**
| Field | Type |
|:---|:---|
| `identifier` | string |
| `templateId` | string |
| `duration` | number |
| `variables` | object |

---

### Check Video Generation Allowed
`GET /media/generate-video/:type/allowed`

**Response:** `{ "allowed": true }`

---

### Video Function
`POST /media/video/function`

**Request Body:**
| Field | Type |
|:---|:---|
| `identifier` | string |
| `functionName` | string |
| `params` | object |

---

## Settings Endpoints

**Prefix:** `/settings`  Requires authentication.

### Get Team Members
`GET /settings/team`

Requires `TEAM_MEMBERS` or Admin role.

---

### Invite Team Member
`POST /settings/team`

Requires `TEAM_MEMBERS` or Admin role.

**Request Body:**
| Field | Type |
|:---|:---|
| `email` | string |
| `role` | string (`USER` \| `ADMIN`) |

---

### Remove Team Member
`DELETE /settings/team/:id`

Requires `TEAM_MEMBERS` or Admin role.

---

### Get Shortlink Preference
`GET /settings/shortlink`

---

### Update Shortlink Preference
`POST /settings/shortlink`

Requires Admin role.

**Request Body:** `{ "shortlink": "<provider-enum>" }`

---

## Billing Endpoints

**Prefix:** `/billing`  Requires authentication.  
> When `BILL_TYPE=third`, most endpoints return stub responses managed externally.

### Get Current Billing
`GET /billing/`

Returns active subscription details.

---

### Check Status by ID
`GET /billing/check/:id`

**Response:** `{ "status": "active" | "cancelled" | null }`

---

### Check Discount
`GET /billing/check-discount`

**Response:** `{ "offerCoupon": true | "<jwt-coupon>" }`

---

### Apply Discount
`POST /billing/apply-discount`

---

### Subscribe
`POST /billing/subscribe`

**Request Body:**
| Field | Type |
|:---|:---|
| `packageId` | string |
| `period` | string |

**Response:** Stripe checkout session object.

---

### Embedded Checkout
`POST /billing/embedded`

Same body as Subscribe. Returns embedded Stripe checkout data.

---

### Prorate
`POST /billing/prorate`

Preview cost of changing plan mid-cycle.

---

### Customer Portal
`GET /billing/portal`

**Response:** `{ "portal": "<stripe-portal-url>" }`

---

### Cancel Subscription
`POST /billing/cancel`

**Request Body:** `{ "feedback": "reason text" }`

---

### Finish Trial
`POST /billing/finish-trial`

**Response:** `{ "finish": true }`

---

### Trial Status
`GET /billing/is-trial-finished`

**Response:** `{ "finished": false }`

---

### Lifetime Deal
`POST /billing/lifetime`

**Request Body:** `{ "code": "LTD-CODE" }`

---

### Add Subscription (Manual)
`POST /billing/add-subscription`

**Request Body:** `{ "subscription": "sub_xxx" }`

---

### Crypto Payment
`GET /billing/crypto`

Returns a crypto payment page object.

---

## Webhooks Endpoints

**Prefix:** `/webhooks`  Creating webhooks requires `WEBHOOKS` policy.

### List Webhooks
`GET /webhooks/`

---

### Create Webhook
`POST /webhooks/`

**Request Body:**
| Field | Type |
|:---|:---|
| `url` | string |
| `events` | string[] |
| `active` | boolean |

---

### Update Webhook
`PUT /webhooks/`

**Request Body:** Same as Create, plus `id` field.

---

### Delete Webhook
`DELETE /webhooks/:id`

---

### Test Webhook
`POST /webhooks/send?url=<url>`

Sends a test payload to the specified URL.

**Response:** `{ "send": true }`

---

## Autopost Endpoints

**Prefix:** `/autopost`  Creating autoposts requires `WEBHOOKS` policy.

### List Autoposts
`GET /autopost/`

---

### Create Autopost
`POST /autopost/`

**Request Body:**
| Field | Type |
|:---|:---|
| `name` | string |
| `url` | string (RSS feed) |
| `integrations` | string[] |
| `schedule` | object |

---

### Update Autopost
`PUT /autopost/:id`

Same body as Create.

---

### Delete Autopost
`DELETE /autopost/:id`

---

### Toggle Active
`POST /autopost/:id/active`

**Request Body:** `{ "active": true }`

---

### Test Feed
`POST /autopost/send?url=<url>`

Fetches and returns the raw XML from the given RSS URL.

---

## Signatures Endpoints

**Prefix:** `/signatures`  Requires authentication.

### List Signatures
`GET /signatures/`

---

### Get Default Signature
`GET /signatures/default`

---

### Create Signature
`POST /signatures/`

**Request Body:**
| Field | Type |
|:---|:---|
| `name` | string |
| `content` | string |
| `isDefault` | boolean |

---

### Update Signature
`PUT /signatures/:id`

Same body as Create.

---

### Delete Signature
`DELETE /signatures/:id`

---

## Sets Endpoints

**Prefix:** `/sets`  Requires authentication.

Sets are reusable groups of content (e.g., hashtag collections).

### List Sets
`GET /sets/`

---

### Create Set
`POST /sets/`

**Request Body:**
| Field | Type |
|:---|:---|
| `name` | string |
| `description` | string |
| `items` | string[] |

---

### Update Set
`PUT /sets/`

Same body plus `id` field.

---

### Delete Set
`DELETE /sets/:id`

---

## Copilot / AI Endpoints

**Prefix:** `/copilot`  AI features require `AI` policy.

### Chat (Stream)
`POST /copilot/chat`

General AI chat via SSE stream. Accepts OpenAI-compatible GraphQL variables.

---

### AI Agent (Stream)
`POST /copilot/agent`

Requires `AI` policy. CopilotKit agent endpoint. Returns SSE stream with structured agent actions.

---

### List Threads
`GET /copilot/list`

Requires `AI` policy.

**Response:** `{ "threads": [...] }`

---

### Get Thread Messages
`GET /copilot/:thread/list`

Requires `AI` policy.

**Response:** `{ "messages": [...] }`

---

### Calculate AI Credits
`GET /copilot/credits?type=<type>`

**Query:** `type` — `ai_images` or `ai_videos`

**Response:** `{ "credits": 150 }`

---

## Third-Party Integrations Endpoints

**Prefix:** `/third-party`  Requires authentication.

### List Available Providers
`GET /third-party/list`

---

### List Saved Integrations
`GET /third-party/`

---

### Add API Key
`POST /third-party/:identifier`

**Request Body:** `{ "api": "api-key-value" }`

**Response:** `{ "id": "saved-integration-id" }`

---

### Delete Integration
`DELETE /third-party/:id`

---

### Generate Media
`POST /third-party/:id/submit`

Submit a generation request to the third-party provider.

---

### Call Provider Function
`POST /third-party/function/:id/:functionName`

Call a named function on a saved integration.

---

## Notifications Endpoints

**Prefix:** `/notifications`  Requires authentication.

### Notification Counts
`GET /notifications/`

Returns unread counts per category.

---

### Notification List
`GET /notifications/list`

Returns full list of notifications.

---

## Public Unauthenticated Endpoints

**Prefix:** `/public`  No authentication required (some check an API key).

### Post Preview
`GET /public/posts/:id`

Returns published post preview data.

---

### Post Comments
`GET /public/posts/:id/comments`

**Response:** `{ "comments": [...] }`

---

### Agency Listings
`GET /public/agencies-list`  
`GET /public/agencies-list-slug`  
`GET /public/agencies-list-count`  
`GET /public/agencies-information/:agency`

---

### Track Event
`POST /public/t`

**Request Body:** `{ "tt": "EVENT_NAME", "fbclid": "optional", "additional": {} }`

---

### Crypto Webhook
`POST /public/crypto/:path`

---

### Stream Media File
`GET /public/stream?url=<encoded-url>`

Proxies and streams an MP4 file from a remote URL.

---

### Agent Endpoint
`POST /public/agent`

**Request Body:**
| Field | Type |
|:---|:---|
| `text` | string |
| `apiKey` | string |

---

## Public API v1 (API Key Auth)

**Prefix:** `/public/v1`  
**Authentication:** `Authorization: Bearer <api-key>`

All endpoints mirror the core API but are designed for programmatic access via long-lived API keys.

### Upload File
`POST /public/v1/upload`

Multipart file upload.

---

### Upload from URL
`POST /public/v1/upload-from-url`

**Request Body:**
| Field | Type |
|:---|:---|
| `url` | string |
| `name` | string |

---

### Find Free Slot
`GET /public/v1/find-slot/:integrationId`

**Response:** `{ "date": "2025-01-01T10:00:00Z" }`

---

### List Posts
`GET /public/v1/posts`

**Query Params:** `status`, `type`, `integrationId`, `page`, `pageSize`, `group`

---

### Create Post
`POST /public/v1/posts`

Same request body as core `/posts` endpoint.

---

### Delete Post
`DELETE /public/v1/posts/:id`

---

### Check Connection
`GET /public/v1/is-connected`

**Response:** `{ "connected": true }`

---

### List Integrations
`GET /public/v1/integrations`

---

### Generate Video
`POST /public/v1/generate-video`

Same body as `/media/generate-video`.

---

### Video Function
`POST /public/v1/video/function`

---

## Admin API

**Prefix:** `/admin`  Requires JWT + SuperAdmin role.

### Dashboard Overview
`GET /admin/dashboard/`

**Response:** `{ "total_organizations": 500, ... }`

---

### Backfill Data Ticks
`POST /admin/dashboard/data-ticks/backfill`

**Query Params:** `startDate`, `endDate`

**Response:** `{ "backfilled": 1234, "results": [...] }`

---

### User Dashboard Summary
`GET /admin/dashboard/user/summary`

**Query Params:** `organizationId` or `userId`

---

### List Organizations
`GET /admin/organizations/?page=<page>&pageSize=<size>&keyword=<search>`

---

### Get Organization by ID
`GET /admin/organizations/:id`

---

## Stripe Webhook

**Prefix:** `/stripe`  No authentication (verified via Stripe signature).

### Receive Webhook
`POST /stripe/`

Handles Stripe billing events:

| Event | Action |
|:---|:---|
| `invoice.payment_succeeded` | Extend subscription |
| `customer.subscription.created` | Activate subscription |
| `customer.subscription.updated` | Update plan |
| `customer.subscription.deleted` | Cancel subscription |

**Request:** Raw body with `Stripe-Signature` header.  
**Response:** `{ "ok": true }`

---

## Internal API

**Prefix:** `/internal`  Requires `x-internal-secret` header.

### Create Internal User
`POST /internal/users`

**Request Body:**
| Field | Type | Required |
|:---|:---|:---|
| `id` | string | Yes |
| `email` | string | Yes |
| `name` | string | No |

**Response:** `{ "success": true, "userId": "uuid" }`

---

## Health & Monitor

### Root
`GET /`

**Response:** `"App is running!"`

---

### Queue Health
`GET /monitor/queue/:name`

**Response:** `{ "status": "success", "message": "Queue orders is healthy." }`

---

## Error Responses

All endpoints follow standard NestJS error format:

```json
{
  "statusCode": 400,
  "message": "Validation failed",
  "error": "Bad Request"
}
```

| Status | Meaning |
|:---|:---|
| 400 | Bad Request — invalid input |
| 401 | Unauthorized — missing/invalid auth |
| 403 | Forbidden — insufficient permissions |
| 404 | Not Found |
| 429 | Too Many Requests — rate limit exceeded |
| 500 | Internal Server Error |

---

## Rate Limiting

Requests are throttled via `ThrottlerBehindProxyGuard`. The default limit is controlled by the `API_LIMIT` environment variable (requests per hour per IP). Exceeding the limit returns HTTP `429`.

---

## Policy Sections Reference

| Section | Description |
|:---|:---|
| `POSTS_PER_MONTH` | Controls post creation volume |
| `CHANNEL` | Channel/integration connection |
| `TEAM_MEMBERS` | Team management |
| `WEBHOOKS` | Webhook and autopost management |
| `AI` | AI copilot features |
| `ADMIN` | Organization administration |
