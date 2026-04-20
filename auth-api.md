# Auth Service API Reference

The Auth Service handles user identity, authentication, and security.

## Authentication Overview

The platform uses a dual-token system:
- **Access Token**: Short-lived JWT Bearer token used in the `Authorization` header.
- **Refresh Token**: Long-lived token stored in an `HttpOnly` cookie for security.

### Header Example
```http
Authorization: Bearer YOUR_ACCESS_TOKEN
```

---

## 🔐 Registration & Login

### User Registration
`POST /register`

Registers a new user account. Requires a pre-verified email via VCode.

**Request Body (JSON):**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `email` | string | Yes | Valid email address |
| `password` | string | Yes | Min 8 characters |
| `username` | string | No | Display name |
| `vcode` | string | Yes | 6-digit verification code |

**Response:**
Returns user details and sets `refresh_token` in a cookie.

**Example:**
```bash
curl -X POST "https://auth.aisee.live/register" \
     -H "Content-Type: application/json" \
     -d '{
       "email": "user@example.com",
       "password": "securepassword123",
       "vcode": "123456"
     }'
```

### User Login
`POST /login`

Authenticates a user and returns an access token.

**Request Body (JSON):**
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| `email` | string | Yes | Registered email |
| `password` | string | Yes | User password |

**Example:**
```bash
curl -X POST "https://auth.aisee.live/login" \
     -H "Content-Type: application/json" \
     -d '{
       "email": "user@example.com",
       "password": "securepassword123"
     }'
```

---

## 🛡️ Token Management

### Refresh Token
`POST /token-refresh`

Used to obtain a new access token when the current one expires.

**Example:**
```bash
curl -X POST "https://auth.aisee.live/token-refresh" \
     -H "Cookie: refresh_token=YOUR_REFRESH_TOKEN_FROM_COOKIE"
```

### Logout
`POST /logout`

Revokes the current session and clears cookies.

---

## 📧 Verification Codes (VCode)

### Request VCode
`POST /vcode`

Sends a 6-digit code to the specified email.

**Request Body:**
- `email`: target email
- `type`: `register` or `password-reset`

**Example:**
```bash
curl -X POST "https://auth.aisee.live/vcode" \
     -H "Content-Type: application/json" \
     -d '{
       "email": "user@example.com",
       "type": "register"
     }'
```

---

## 🌐 OAuth 2.0

### Initiate Login
`GET /oauth/{provider}/login`

Returns the authorization URL for the third-party provider.

**Example:**
```bash
curl "https://auth.aisee.live/oauth/google/login"
```

### List Providers
`GET /oauth/providers`

Returns a list of configured OAuth providers (Google, GitHub, etc.).
