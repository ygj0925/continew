# Spec: Authentication

## Login Methods

### 1. Account Login
- Fields: username, password, captcha
- Password encrypted with jsencrypt (RSA) before sending
- Captcha fetched from `GET /captcha` → returns image + uuid
- Submit: `POST /auth/login` with `{ username, password, captcha, uuid, authType: 'ACCOUNT' }`

### 2. Phone Login
- Fields: phone, captcha (SMS code)
- SMS sent via separate API call
- Submit: `POST /auth/login` with `{ phone, captcha, authType: 'PHONE' }`

### 3. Email Login
- Fields: email, captcha (email code)
- Submit: `POST /auth/login` with `{ email, captcha, authType: 'EMAIL' }`

### 4. Social/OAuth Login
- Buttons for: Gitee, GitHub, WeChat
- Click → `GET /auth/{source}` → returns `{ authorizeUrl }` → redirect
- Callback: `/social/callback?code=xxx` → POST to backend → get token

## Response

```typescript
interface LoginResp {
  token: string       // JWT
  tenantId: number    // Tenant ID for X-Tenant-Id header
}
```

## Post-Login Flow

1. Store token + tenantId in `useUserStore`
2. Fetch `GET /auth/user/info` → store user info, roles, permissions
3. Fetch `GET /auth/user/route` → register dynamic routes
4. Redirect to `redirect` query param or `/dashboard/workplace`

## Auth Guard

- Every route under `/` (except `/login`, `/social/callback`, `/403`, `/404`) requires auth
- Guard checks `useUserStore.token`
- If no token → redirect to `/login?redirect=<current_path>`
- If token exists but no userInfo → fetch userInfo + routes first

## Token Lifecycle

- Stored in localStorage via Zustand persist
- Attached to every request as `Authorization: Bearer <token>`
- On 401 response: clear store, show "登录已过期" modal, redirect to login
- On logout: `POST /auth/logout`, clear store, redirect to login

## Password Expiry

If backend returns a special code indicating password expired:
- Redirect to `/pwdExpired` page
- User must set new password before proceeding
- After reset, redirect to login

## Tenant Handling

- Login response includes `tenantId`
- Stored in user store
- Sent as `X-Tenant-Id` header on all subsequent requests
- If tenant is enabled, login page shows tenant code input or detects from domain
