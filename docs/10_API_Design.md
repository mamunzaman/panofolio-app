# PanoFolio — API Design

**Document:** `10_API_Design.md`  
**Status:** Approved  
**Version:** 1.0  
**API Style:** REST  
**Scope:** PanoFolio Version 1

---

## 1. Purpose

This document defines the Version 1 API contract for PanoFolio. It translates the approved Domain Model and Database Model into HTTP endpoints used by the public portfolio website, Creator Workspace, Administrator interface, background workers, and future approved clients.

The API must not introduce business entities or workflows that are not already approved.

---

## 2. API Principles

- REST-oriented resource design
- JSON request and response bodies
- UUID public identifiers
- Explicit authentication and permission checks
- Workspace-scoped Creator resources
- Consistent errors
- Pagination for collections
- Idempotency for sensitive write operations
- Asynchronous ZIP processing
- Immutable Tour Project Versions
- Public access only to published content
- API versioning from the first release

---

## 3. Base URL

Production:

```text
https://api.panofolio.com/api/v1
```

Development:

```text
http://localhost:3000/api/v1
```

The exact production domain may change before deployment, but `/api/v1` remains the Version 1 prefix.

---

## 4. Transport and Content Types

All production traffic uses HTTPS.

Normal requests use:

```http
Content-Type: application/json
Accept: application/json
```

Large ZIP and media uploads use temporary direct-to-storage upload URLs. The API initiates and completes the upload while binary transfer happens directly between the client and the storage provider.

---

## 5. Identifiers and Field Naming

All business identifiers use UUIDs.

Authenticated management routes use IDs:

```text
GET /workspace/tour-projects/{projectId}
```

Public routes may use slugs:

```text
GET /public/portfolios/{portfolioSlug}
```

API JSON uses `camelCase`; database fields use `snake_case`.

Dates use ISO 8601 UTC:

```json
{
  "createdAt": "2026-07-31T12:30:00Z"
}
```

---

## 6. Authentication

Version 1 uses email and password authentication.

Implementation may use secure HTTP-only sessions or short-lived access tokens with rotating refresh tokens. Raw session and refresh tokens must never be stored.

Bearer authentication:

```http
Authorization: Bearer <access_token>
```

Cookie authentication must use `HttpOnly`, `Secure` in production, an appropriate `SameSite` policy, and CSRF protection for unsafe requests.

Long-lived authentication tokens must not be stored in browser local storage.

---

## 7. Standard Responses

Single resource:

```json
{
  "data": {
    "id": "3df45776-a1ed-4a06-a73b-e75bbdf3fbd2",
    "name": "Example"
  }
}
```

Collection:

```json
{
  "data": [],
  "meta": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 0,
    "totalPages": 0
  }
}
```

Asynchronous operation:

```json
{
  "data": {
    "jobId": "a9c993ff-9f92-43ea-ab38-60cb1186fd61",
    "status": "pending"
  }
}
```

---

## 8. Error Format

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The request contains invalid fields.",
    "details": {
      "fields": {
        "title": ["Title is required."]
      }
    },
    "requestId": "req_31dd6485"
  }
}
```

The API must never return raw database errors, framework exceptions, storage errors, or stack traces.

Standard codes include:

- `BAD_REQUEST`
- `UNAUTHENTICATED`
- `FORBIDDEN`
- `RESOURCE_NOT_FOUND`
- `RESOURCE_CONFLICT`
- `FILE_TOO_LARGE`
- `UNSUPPORTED_FILE_TYPE`
- `VALIDATION_ERROR`
- `RATE_LIMITED`
- `INTERNAL_ERROR`
- `SERVICE_UNAVAILABLE`

Domain codes include:

- `EMAIL_ALREADY_REGISTERED`
- `EMAIL_NOT_VERIFIED`
- `ACCOUNT_SUSPENDED`
- `ACCOUNT_DISABLED`
- `CREATOR_REQUEST_PENDING`
- `CREATOR_ACCESS_REQUIRED`
- `WORKSPACE_NOT_FOUND`
- `PORTFOLIO_ALREADY_EXISTS`
- `PROJECT_NOT_READY`
- `VERSION_NOT_READY`
- `PROJECT_ALREADY_PUBLISHED`
- `IMPORT_VALIDATION_FAILED`
- `ASSET_IN_USE`
- `STORAGE_LIMIT_REACHED`

Every request receives an `X-Request-ID` header. The same value is used in logs and error responses.

---

## 9. Pagination, Sorting, and Filtering

Version 1 uses page-based pagination.

```text
?page=1&pageSize=20
```

Defaults:

- `page = 1`
- `pageSize = 20`
- Maximum `pageSize = 100`

Sorting uses a leading hyphen for descending order:

```text
?sort=-updatedAt
```

Unsupported sort fields are rejected.

Search uses:

```text
?search=hotel
```

All Creator searches remain scoped to the authenticated Workspace.

---

# 10. Authentication Endpoints

## Register

```http
POST /auth/register
```

Request:

```json
{
  "email": "creator@example.com",
  "password": "StrongPassword123!",
  "fullName": "Example Creator",
  "locale": "en",
  "timezone": "Europe/Berlin"
}
```

Rules:

- New accounts receive role `visitor`.
- New accounts receive status `pending_verification`.
- Registration creates no Workspace or Portfolio.
- Email uniqueness is case-insensitive.

## Verify and resend email

```text
POST /auth/verify-email
POST /auth/resend-verification
```

Verification tokens are single-use and time-limited. Resend responses must not disclose whether an email address exists.

## Login and logout

```text
POST /auth/login
POST /auth/logout
POST /auth/logout-all
```

Login is rejected for unverified, suspended, disabled, or deleted accounts. Failure messages must not reveal whether the email or password was incorrect.

## Password recovery

```text
POST /auth/forgot-password
POST /auth/reset-password
```

Successful password reset should revoke existing sessions.

## Current user

```http
GET /auth/me
```

Visitors receive `workspace: null`. Creators receive a summary of their owned Workspace.

---

# 11. Account Endpoints

```text
PATCH /account/profile
POST  /account/change-password
POST  /account/change-email
```

Users cannot change their own role or account status. A new email must be verified before replacement.

---

# 12. Creator Request Endpoints

```text
POST /creator-requests
GET  /creator-requests/me
POST /creator-requests/{requestId}/cancel
```

Only active Visitors may submit a Creator request. A Visitor may have only one pending request. Only the requesting Visitor may cancel their own pending request.

---

# 13. Workspace Endpoints

Version 1 Creators own one Workspace, so management routes use `/workspace` without requiring the Workspace ID in every path.

```text
GET   /workspace
PATCH /workspace
GET   /workspace/settings
PATCH /workspace/settings
```

The backend resolves the owned Workspace and verifies ownership server-side. Ownership transfer is not supported in Version 1.

---

# 14. Portfolio Endpoints

```text
GET   /workspace/portfolio
POST  /workspace/portfolio
PATCH /workspace/portfolio
POST  /workspace/portfolio/publish
POST  /workspace/portfolio/unpublish
```

Only one Portfolio may exist per Workspace. Attempting to create another returns `409 PORTFOLIO_ALREADY_EXISTS`.

Publishing validates required metadata, referenced Assets, Creator status, and Workspace status. Unpublishing removes public access without deleting projects or versions.

## Portfolio sections

```text
GET    /workspace/portfolio/sections
POST   /workspace/portfolio/sections
PATCH  /workspace/portfolio/sections/{sectionId}
DELETE /workspace/portfolio/sections/{sectionId}
PUT    /workspace/portfolio/sections/order
```

## Social links

```text
GET    /workspace/portfolio/social-links
POST   /workspace/portfolio/social-links
PATCH  /workspace/portfolio/social-links/{socialLinkId}
DELETE /workspace/portfolio/social-links/{socialLinkId}
```

Version 1 permits one link per platform.

---

# 15. Asset Endpoints

```text
GET    /workspace/assets
GET    /workspace/assets/{assetId}
POST   /workspace/assets/uploads
POST   /workspace/assets/uploads/{uploadId}/complete
PATCH  /workspace/assets/{assetId}
GET    /workspace/assets/{assetId}/usage
DELETE /workspace/assets/{assetId}
```

Tour Project ZIP packages and extracted krpano files must never appear as reusable Assets.

Upload initiation returns a temporary direct-to-storage upload URL. Completion verifies object existence, size, MIME type, checksum where available, and expected Workspace storage path.

Asset deletion rules:

- Unused Assets may be soft-deleted.
- Assets used by published content cannot be deleted.
- Draft usage requires safe removal or explicit resolution.
- Storage cleanup may be asynchronous.

Blocked deletion returns `409 ASSET_IN_USE`.

---

# 16. Category Endpoints

```text
GET    /workspace/categories
POST   /workspace/categories
GET    /workspace/categories/{categoryId}
PATCH  /workspace/categories/{categoryId}
DELETE /workspace/categories/{categoryId}
PUT    /workspace/categories/order
```

Deleting a Category does not delete its Tour Projects; affected projects receive `categoryId = null`.

---

# 17. Tour Project Endpoints

```text
GET    /workspace/tour-projects
POST   /workspace/tour-projects
GET    /workspace/tour-projects/{projectId}
PATCH  /workspace/tour-projects/{projectId}
DELETE /workspace/tour-projects/{projectId}
POST   /workspace/tour-projects/{projectId}/archive
```

A draft project may be created before its first ZIP import. Metadata updates cannot modify imported Version contents.

Published projects must be unpublished before archive or deletion. Deletion is soft deletion with controlled asynchronous storage cleanup.

---

# 18. Tour Import API

ZIP import is asynchronous.

```text
POST /workspace/tour-projects/{projectId}/imports
POST /workspace/tour-projects/{projectId}/imports/{importJobId}/complete-upload
GET  /workspace/tour-projects/{projectId}/imports/{importJobId}
POST /workspace/tour-projects/{projectId}/imports/{importJobId}/cancel
```

Processing stages:

```text
pending
↓
uploading
↓
validating
↓
extracting
↓
generating_preview
↓
completed
```

Validation must cover:

- Valid ZIP structure
- File-size, extraction-size, and file-count limits
- Path traversal protection
- Absolute path rejection
- Symlink escape protection
- Required `tour.html`
- Required `tour.xml`
- Approved single wrapper-directory removal
- Safe referenced paths
- Prohibited and executable file rejection
- Isolation by Workspace, Project, and Version

A failed import does not replace the current ready Version.

---

# 19. Tour Project Version Endpoints

```text
GET  /workspace/tour-projects/{projectId}/versions
GET  /workspace/tour-projects/{projectId}/versions/{versionId}
POST /workspace/tour-projects/{projectId}/versions/{versionId}/preview-session
POST /workspace/tour-projects/{projectId}/versions/{versionId}/set-current
POST /workspace/tour-projects/{projectId}/versions/{versionId}/archive
```

Only a ready Version may become current. Setting current does not automatically publish it.

A Version cannot be archived while it is current or published. Ready Version package data is immutable.

Preview access is temporary and authorised. Internal storage paths are not exposed.

---

# 20. Publishing Endpoints

```text
POST /workspace/tour-projects/{projectId}/publish
POST /workspace/tour-projects/{projectId}/unpublish
```

Publish request includes a ready `versionId` and visibility.

Validation includes:

- Creator account is active
- Workspace is active
- Portfolio exists and is published
- Project is not deleted or archived
- Version belongs to the Project
- Version status is `ready`
- Required metadata exists
- Referenced Assets belong to the same Workspace and are valid

Republishing with another ready Version uses the same endpoint. The previous Version remains preserved.

---

# 21. Project Gallery Endpoints

```text
GET    /workspace/tour-projects/{projectId}/gallery
POST   /workspace/tour-projects/{projectId}/gallery
DELETE /workspace/tour-projects/{projectId}/gallery/{galleryItemId}
PUT    /workspace/tour-projects/{projectId}/gallery/order
```

Gallery Assets must belong to the same Workspace and use compatible types.

---

# 22. Analytics Endpoints

```text
GET  /workspace/analytics/overview
GET  /workspace/tour-projects/{projectId}/analytics
POST /public/analytics/events
```

Public event ingestion must:

- Confirm referenced content is public
- Apply rate limits
- Restrict metadata keys and size
- Derive trusted values server-side
- Avoid permanent raw IP storage
- Prevent arbitrary database insertion

Clients must not submit trusted Workspace IDs, roles, visitor hashes, country codes, or storage paths.

---

# 23. Notifications

```text
GET  /notifications
POST /notifications/{notificationId}/read
POST /notifications/read-all
```

Users may access only their own notifications.

---

# 24. Administrator Endpoints

All Administrator routes use `/admin`, require active `administrator` role, and produce Audit Logs.

## Users

```text
GET  /admin/users
GET  /admin/users/{userId}
POST /admin/users/{userId}/suspend
POST /admin/users/{userId}/reactivate
POST /admin/users/{userId}/disable
```

Suspension revokes active sessions and preserves data.

## Creator requests

```text
GET  /admin/creator-requests
GET  /admin/creator-requests/{requestId}
POST /admin/creator-requests/{requestId}/approve
POST /admin/creator-requests/{requestId}/reject
```

Creator approval must run in one transaction:

```text
Lock request
↓
Confirm pending request and active Visitor
↓
Approve request
↓
Change role to Creator
↓
Create Workspace
↓
Create owner membership
↓
Create Workspace settings
↓
Optionally create draft Portfolio
↓
Create notification and Audit Log
```

Rejection requires a reason.

## Audit Logs

```text
GET /admin/audit-logs
```

Audit Logs are read-only through Version 1 APIs.

---

# 25. Public Portfolio Endpoints

```text
GET /public/portfolios/{portfolioSlug}
GET /public/portfolios/{portfolioSlug}/tour-projects
GET /public/portfolios/{portfolioSlug}/tour-projects/{projectSlug}
```

Public responses may expose only published Portfolio metadata, enabled sections, public categories, published Tour Projects, and approved public asset derivatives.

They must never expose:

- Workspace internal settings
- Creator email
- Storage keys
- Draft content
- Internal Version history
- Import details
- Audit records

A public Tour Project response references exactly the selected published Version.

---

# 26. Viewer Delivery

The public API must never expose internal extracted storage paths.

Two delivery strategies remain acceptable until the storage specification selects one:

1. Publish an immutable controlled public prefix.
2. Provide signed access to protected viewer files.

Regardless of strategy:

- Draft Versions remain private.
- Old published Versions remain preserved.
- A Project points to exactly one published Version.
- Switching published Version is atomic.
- Cross-Workspace path access is impossible.

---

# 27. Idempotency

Sensitive operations should support:

```http
Idempotency-Key: client-generated-unique-value
```

Recommended for registration, Creator requests, upload initiation, Tour import initiation, publishing, and Creator approval.

Keys are scoped by authenticated user, endpoint, and request-body fingerprint.

---

# 28. Concurrency Control

High-conflict resources may use `ETag` and `If-Match`.

A stale update returns:

```text
409 RESOURCE_VERSION_CONFLICT
```

Concurrency protection is especially important for Portfolio updates, ordering, Project metadata, publishing, Version selection, and Administrator review actions.

---

# 29. Rate Limiting

Rate limits apply to:

- Registration
- Login
- Password reset
- Verification resend
- Creator request submission
- Upload and import initiation
- Public analytics ingestion
- Administrator operations

Rate-limited requests return HTTP 429.

---

# 30. Validation Rules

Server-side validation is mandatory.

- Emails: valid format, max 320 characters, case-insensitive uniqueness
- Slugs: lowercase letters, digits, hyphens, safe reserved-word handling, scoped uniqueness
- URLs: approved `http` or `https` only
- Text: trimmed, maximum lengths enforced, required strings not blank
- UUIDs: valid format and accessible in the current Workspace

---

# 31. Permission Matrix

| Operation | Visitor | Creator | Administrator | Public |
|---|---:|---:|---:|---:|
| Register | Yes | — | — | Yes |
| Submit Creator request | Yes | No | No | No |
| Manage own Workspace | No | Yes | Support-only | No |
| Manage Portfolio | No | Yes | Support-only | No |
| Manage Assets | No | Yes | Support-only | No |
| Import Tour ZIP | No | Yes | Support-only | No |
| Publish Project | No | Yes | Support-only | No |
| View public Portfolio | Yes | Yes | Yes | Yes |
| Review Creator request | No | No | Yes | No |
| Manage account status | No | No | Yes | No |
| Read Audit Logs | No | No | Yes | No |

Administrator support access must remain explicit and auditable.

---

# 32. Security Boundaries

The API must prevent:

- Cross-Workspace access
- Insecure direct object references
- Unauthorised Asset access
- Draft Tour access through guessed URLs
- ZIP path traversal and unsafe extracted files
- Arbitrary metadata injection
- Ordinary-user role or status changes
- Publishing another Project's Version
- Cross-Workspace Asset references
- Reuse of expired upload URLs
- Disclosure of password or token hashes
- Disclosure of internal storage paths

Ownership decisions use trusted server-side data, not request-supplied Workspace IDs.

---

# 33. Logging

Logs should include Request ID, endpoint, method, status, duration, authenticated user ID, Workspace ID, error code, and processing job ID where applicable.

Logs must not include plaintext passwords, authentication tokens, reset or verification tokens, signed upload URLs, or sensitive complete request bodies.

---

# 34. OpenAPI

The implemented API must have a machine-readable OpenAPI contract:

```text
docs/api/openapi-v1.yaml
```

It defines paths, methods, security, parameters, request and response schemas, errors, enumerations, and pagination metadata.

This document defines approved behaviour; the OpenAPI file becomes the implementation contract.

---

# 35. Testing Requirements

Each endpoint requires tests for successful requests, unauthenticated access, wrong-role access, cross-Workspace access, invalid input, missing resources, state conflicts, transaction rollback, rate limiting, and idempotent retries where applicable.

Critical integration tests:

- Visitor cannot access Workspace endpoints.
- Creator cannot access another Workspace.
- Creator cannot create a second Portfolio.
- Creator cannot publish a failed Version.
- Creator cannot publish another Project's Version.
- Creator cannot reference another Workspace's Asset.
- Published Project returns only its published Version.
- Failed import does not replace the current Version.
- Administrator approval creates role, Workspace, membership, and settings atomically.
- Suspension revokes active sessions.
- Public routes never expose draft content.

---

# 36. Deferred API Features

Not included in Version 1:

- Team invitations
- Multiple Workspace selection
- Multiple portfolios per Creator
- Public API keys
- Webhooks
- GraphQL
- Marketplace and billing APIs
- Comments and client approvals
- Native Tour editing
- AI generation
- Multiple viewer engines
- Custom domains
- Real-time collaboration

These require separate business, database, permission, and API approval.

---

# 37. Acceptance Criteria

The API Design is approved when:

- Every approved Version 1 database entity has a safe API boundary.
- Visitor, Creator, and Administrator permissions remain separate.
- Role and account status remain separate.
- Creator access is scoped to one owned Workspace.
- One Workspace–one Portfolio is preserved.
- Tour Project packages remain separate from reusable Assets.
- Imports are asynchronous.
- Every valid import creates an immutable Version.
- Publishing explicitly selects a ready Version.
- Public routes expose published content only.
- Cross-Workspace references are prevented.
- Administrator actions are audited.
- Errors, pagination, validation, and response formats are consistent.
- File uploads use protected temporary upload flows.
- Internal storage paths and sensitive authentication data are not exposed.
- The contract can be converted into OpenAPI documentation.

---

# 38. Next Document

The next phase is:

```text
11_UI_UX_Specifications
```

The approved workflow remains:

```text
Business Domain
↓
Database Model
↓
API Design
↓
UI/UX Specifications
↓
Implementation
```
