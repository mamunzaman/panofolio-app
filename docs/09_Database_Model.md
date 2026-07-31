# PanoFolio — Database Model

**Document:** `09_Database_Model.md`  
**Status:** Approved  
**Version:** 1.0  
**Database:** PostgreSQL  
**Scope:** PanoFolio Version 1

---

## 1. Purpose

This document converts the approved PanoFolio Domain Model into a physical PostgreSQL database structure.

It defines:

- Tables and columns
- Data types
- Primary and foreign keys
- Unique constraints
- Check constraints
- Indexes
- Deletion rules
- Ownership rules
- Publishing relationships
- Tour project versioning
- Asset references
- Analytics storage
- Security and audit requirements

This document is the source of truth for future database migrations, backend models, API contracts, and data-access policies.

---

## 2. Version 1 Database Principles

### 2.1 PostgreSQL

PanoFolio Version 1 uses PostgreSQL as its relational database.

The database stores structured application data only. Large binary files such as krpano ZIP packages, extracted tour files, panorama tiles, cover images, gallery images, logos, and documents must be stored in object or file storage.

The database stores only file identifiers, storage paths, metadata, checksums, MIME types, file sizes, and processing status.

### 2.2 UUID Primary Keys

All primary business entities use UUID primary keys.

```sql
id UUID PRIMARY KEY DEFAULT gen_random_uuid()
```

UUIDs avoid predictable public identifiers, work well with distributed services, support independent record creation, and are appropriate for API routes.

### 2.3 Naming Conventions

Database objects use `snake_case`.

Examples:

```text
user_accounts
creator_requests
tour_projects
tour_project_versions
portfolio_sections
analytics_events
```

Primary keys use `id`. Foreign keys use names such as `user_id`, `workspace_id`, `portfolio_id`, `tour_project_id`, and `asset_id`.

Timestamp fields use `created_at`, `updated_at`, `deleted_at`, `published_at`, and `processed_at`.

Boolean fields use positive names such as `is_public`, `is_enabled`, `is_featured`, and `is_verified`.

### 2.4 Timestamp Standard

All timestamps use `TIMESTAMPTZ` and are stored in UTC.

```sql
created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
```

`updated_at` is maintained by the application or a shared database trigger.

### 2.5 Soft Deletion

Soft deletion uses:

```sql
deleted_at TIMESTAMPTZ NULL
```

It applies to user accounts, workspaces, portfolios, tour projects, assets, and categories.

It is not required for sessions, verification tokens, password-reset tokens, analytics events, temporary imports, or processing logs.

Normal queries exclude records where `deleted_at IS NOT NULL` unless an Administrator explicitly requests archived or deleted records.

---

## 3. Core Relationship Model

```text
User Account
    │
    ├── Creator Request
    ├── Authentication Sessions
    └── Workspace Membership
            │
            └── Workspace
                    │
                    ├── Portfolio
                    │     ├── Portfolio Sections
                    │     └── Portfolio Social Links
                    │
                    ├── Categories
                    ├── Tour Projects
                    │     ├── Tour Project Versions
                    │     ├── Import Jobs
                    │     └── Analytics
                    │
                    ├── Assets
                    ├── Workspace Settings
                    └── Audit Logs
```

---

## 4. Version 1 Ownership Rule

PanoFolio Version 1 supports:

```text
One Creator account
    ↓
One owned Workspace
    ↓
One Portfolio
```

A Visitor owns no Workspace, Portfolio, or Tour Projects.

An Administrator may manage platform resources but does not automatically require a Creator portfolio.

The Workspace remains the internal ownership boundary even though Version 1 supports only one Creator-owned workspace and one portfolio.

The database enforces:

- One Creator → maximum one owned Workspace
- One Workspace → exactly one Creator owner
- One Workspace → maximum one Portfolio
- One Portfolio → exactly one Workspace

---

## 5. Enumerated Types

### 5.1 User Role

```sql
CREATE TYPE user_role AS ENUM (
    'visitor',
    'creator',
    'administrator'
);
```

Role represents authorisation level and remains separate from account condition.

### 5.2 Account Status

```sql
CREATE TYPE account_status AS ENUM (
    'pending_verification',
    'active',
    'suspended',
    'disabled',
    'deleted'
);
```

### 5.3 Creator Request Status

```sql
CREATE TYPE creator_request_status AS ENUM (
    'pending',
    'approved',
    'rejected',
    'cancelled'
);
```

### 5.4 Project Status

```sql
CREATE TYPE project_status AS ENUM (
    'draft',
    'processing',
    'ready',
    'published',
    'unpublished',
    'failed',
    'archived'
);
```

### 5.5 Project Version Status

```sql
CREATE TYPE project_version_status AS ENUM (
    'uploaded',
    'validating',
    'extracting',
    'processing',
    'ready',
    'failed',
    'archived'
);
```

### 5.6 Import Job Status

```sql
CREATE TYPE import_job_status AS ENUM (
    'pending',
    'uploading',
    'validating',
    'extracting',
    'generating_preview',
    'completed',
    'failed',
    'cancelled'
);
```

### 5.7 Asset Type

```sql
CREATE TYPE asset_type AS ENUM (
    'cover_image',
    'gallery_image',
    'logo',
    'document',
    'brand_resource',
    'other'
);
```

Tour ZIP packages and extracted krpano files are not Assets.

### 5.8 Portfolio Status

```sql
CREATE TYPE portfolio_status AS ENUM (
    'draft',
    'published',
    'unpublished'
);
```

### 5.9 Visibility

```sql
CREATE TYPE visibility_type AS ENUM (
    'private',
    'unlisted',
    'public'
);
```

---

## 6. Authentication and User Tables

### 6.1 `user_accounts`

```sql
CREATE TABLE user_accounts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(320) NOT NULL,
    password_hash TEXT NOT NULL,
    role user_role NOT NULL DEFAULT 'visitor',
    status account_status NOT NULL DEFAULT 'pending_verification',
    full_name VARCHAR(160),
    display_name VARCHAR(120),
    avatar_asset_id UUID,
    email_verified_at TIMESTAMPTZ,
    last_login_at TIMESTAMPTZ,
    locale VARCHAR(10) NOT NULL DEFAULT 'en',
    timezone VARCHAR(80) NOT NULL DEFAULT 'UTC',
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT user_accounts_email_not_blank CHECK (length(trim(email)) > 0)
);

CREATE UNIQUE INDEX user_accounts_email_unique
ON user_accounts (lower(email))
WHERE deleted_at IS NULL;

CREATE INDEX user_accounts_role_idx
ON user_accounts (role)
WHERE deleted_at IS NULL;

CREATE INDEX user_accounts_status_idx
ON user_accounts (status)
WHERE deleted_at IS NULL;
```

Rules:

- New registrations receive `visitor`.
- New registrations receive `pending_verification`.
- A verified account becomes `active`.
- Creator role is assigned only after Administrator approval.
- Suspended or disabled accounts cannot access authenticated Creator functions.
- Password hashes must never contain plaintext passwords.

### 6.2 `email_verification_tokens`

```sql
CREATE TABLE email_verification_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    token_hash TEXT NOT NULL,
    expires_at TIMESTAMPTZ NOT NULL,
    used_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE UNIQUE INDEX email_verification_tokens_hash_unique
ON email_verification_tokens (token_hash);
```

Only the token hash is stored.

### 6.3 `password_reset_tokens`

```sql
CREATE TABLE password_reset_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    token_hash TEXT NOT NULL,
    expires_at TIMESTAMPTZ NOT NULL,
    used_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE UNIQUE INDEX password_reset_tokens_hash_unique
ON password_reset_tokens (token_hash);
```

### 6.4 `auth_sessions`

```sql
CREATE TABLE auth_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    token_hash TEXT NOT NULL,
    ip_address INET,
    user_agent TEXT,
    expires_at TIMESTAMPTZ NOT NULL,
    last_used_at TIMESTAMPTZ,
    revoked_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE UNIQUE INDEX auth_sessions_token_hash_unique
ON auth_sessions (token_hash);

CREATE INDEX auth_sessions_user_active_idx
ON auth_sessions (user_id, expires_at)
WHERE revoked_at IS NULL;
```

Raw session tokens must never be stored.

---

## 7. Creator Approval

### 7.1 `creator_requests`

```sql
CREATE TABLE creator_requests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    full_name VARCHAR(160) NOT NULL,
    proposed_portfolio_name VARCHAR(160),
    existing_website_url TEXT,
    reason TEXT NOT NULL,
    status creator_request_status NOT NULL DEFAULT 'pending',
    reviewed_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    reviewed_at TIMESTAMPTZ,
    rejection_reason TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT creator_requests_reason_not_blank CHECK (length(trim(reason)) > 0),
    CONSTRAINT creator_requests_review_consistency CHECK (
        (status = 'pending' AND reviewed_at IS NULL)
        OR (status IN ('approved', 'rejected') AND reviewed_at IS NOT NULL)
        OR status = 'cancelled'
    )
);

CREATE UNIQUE INDEX creator_requests_one_pending_per_user
ON creator_requests (user_id)
WHERE status = 'pending';

CREATE INDEX creator_requests_status_created_idx
ON creator_requests (status, created_at DESC);
```

Creator approval occurs in one transaction:

1. Lock the pending request.
2. Confirm the user is an active Visitor.
3. Mark the request approved.
4. Change the role to `creator`.
5. Create the Creator-owned Workspace.
6. Create Workspace membership.
7. Create default Workspace settings.
8. Optionally create the initial draft Portfolio.

Any failure rolls back the transaction.

---

## 8. Workspace Tables

### 8.1 `workspaces`

```sql
CREATE TABLE workspaces (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    owner_user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE RESTRICT,
    name VARCHAR(160) NOT NULL,
    slug VARCHAR(180) NOT NULL,
    status VARCHAR(30) NOT NULL DEFAULT 'active',
    storage_bytes_used BIGINT NOT NULL DEFAULT 0,
    storage_bytes_limit BIGINT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT workspaces_name_not_blank CHECK (length(trim(name)) > 0),
    CONSTRAINT workspaces_slug_not_blank CHECK (length(trim(slug)) > 0),
    CONSTRAINT workspaces_storage_non_negative CHECK (storage_bytes_used >= 0),
    CONSTRAINT workspaces_storage_limit_positive CHECK (
        storage_bytes_limit IS NULL OR storage_bytes_limit >= 0
    )
);

CREATE UNIQUE INDEX workspaces_one_owner_unique
ON workspaces (owner_user_id)
WHERE deleted_at IS NULL;

CREATE UNIQUE INDEX workspaces_slug_unique
ON workspaces (lower(slug))
WHERE deleted_at IS NULL;
```

The owner must reference a user whose role is `creator`. Because a PostgreSQL check constraint cannot query another table, this is enforced by a database trigger or secure transactional service.

### 8.2 `workspace_members`

```sql
CREATE TABLE workspace_members (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    membership_role VARCHAR(30) NOT NULL DEFAULT 'owner',
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT workspace_members_role_check CHECK (membership_role IN ('owner'))
);

CREATE UNIQUE INDEX workspace_members_workspace_user_unique
ON workspace_members (workspace_id, user_id);

CREATE UNIQUE INDEX workspace_members_one_owner_per_workspace
ON workspace_members (workspace_id)
WHERE membership_role = 'owner';

CREATE UNIQUE INDEX workspace_members_one_owned_workspace_per_user
ON workspace_members (user_id)
WHERE membership_role = 'owner';
```

Version 1 permits only `owner`. Future migrations may add manager, editor, and viewer roles.

### 8.3 `workspace_settings`

```sql
CREATE TABLE workspace_settings (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL UNIQUE REFERENCES workspaces(id) ON DELETE CASCADE,
    default_locale VARCHAR(10) NOT NULL DEFAULT 'en',
    default_timezone VARCHAR(80) NOT NULL DEFAULT 'UTC',
    default_project_visibility visibility_type NOT NULL DEFAULT 'private',
    analytics_enabled BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

Core settings use typed columns rather than being hidden in an unstructured JSON document.

---

## 9. Portfolio Tables

### 9.1 `portfolios`

```sql
CREATE TABLE portfolios (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL UNIQUE REFERENCES workspaces(id) ON DELETE RESTRICT,
    title VARCHAR(160) NOT NULL,
    slug VARCHAR(180) NOT NULL,
    tagline VARCHAR(240),
    description TEXT,
    profile_image_asset_id UUID,
    logo_asset_id UUID,
    hero_image_asset_id UUID,
    status portfolio_status NOT NULL DEFAULT 'draft',
    visibility visibility_type NOT NULL DEFAULT 'private',
    seo_title VARCHAR(70),
    seo_description VARCHAR(170),
    published_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT portfolios_title_not_blank CHECK (length(trim(title)) > 0),
    CONSTRAINT portfolios_slug_not_blank CHECK (length(trim(slug)) > 0),
    CONSTRAINT portfolios_publish_consistency CHECK (
        status <> 'published' OR published_at IS NOT NULL
    )
);

CREATE UNIQUE INDEX portfolios_slug_unique
ON portfolios (lower(slug))
WHERE deleted_at IS NULL;
```

The unique `workspace_id` constraint enforces one Portfolio per Workspace.

### 9.2 `portfolio_sections`

```sql
CREATE TABLE portfolio_sections (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    portfolio_id UUID NOT NULL REFERENCES portfolios(id) ON DELETE CASCADE,
    section_type VARCHAR(40) NOT NULL,
    title VARCHAR(160),
    position INTEGER NOT NULL,
    is_enabled BOOLEAN NOT NULL DEFAULT true,
    configuration JSONB NOT NULL DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT portfolio_sections_position_non_negative CHECK (position >= 0),
    CONSTRAINT portfolio_sections_type_check CHECK (
        section_type IN (
            'hero', 'about', 'featured_tours', 'tour_grid',
            'categories', 'gallery', 'contact'
        )
    )
);

CREATE UNIQUE INDEX portfolio_sections_position_unique
ON portfolio_sections (portfolio_id, position);
```

### 9.3 `portfolio_social_links`

```sql
CREATE TABLE portfolio_social_links (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    portfolio_id UUID NOT NULL REFERENCES portfolios(id) ON DELETE CASCADE,
    platform VARCHAR(40) NOT NULL,
    url TEXT NOT NULL,
    position INTEGER NOT NULL DEFAULT 0,
    is_enabled BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT portfolio_social_links_url_not_blank CHECK (length(trim(url)) > 0)
);

CREATE UNIQUE INDEX portfolio_social_links_platform_unique
ON portfolio_social_links (portfolio_id, platform);
```

---

## 10. Assets

### 10.1 `assets`

```sql
CREATE TABLE assets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE RESTRICT,
    uploaded_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    asset_type asset_type NOT NULL,
    original_filename TEXT NOT NULL,
    display_name VARCHAR(200),
    storage_provider VARCHAR(40) NOT NULL,
    storage_bucket VARCHAR(120),
    storage_key TEXT NOT NULL,
    mime_type VARCHAR(160) NOT NULL,
    file_extension VARCHAR(20),
    file_size_bytes BIGINT NOT NULL,
    checksum_sha256 CHAR(64),
    width_pixels INTEGER,
    height_pixels INTEGER,
    alt_text VARCHAR(500),
    caption TEXT,
    processing_status VARCHAR(30) NOT NULL DEFAULT 'ready',
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT assets_file_size_non_negative CHECK (file_size_bytes >= 0),
    CONSTRAINT assets_dimensions_positive CHECK (
        (width_pixels IS NULL OR width_pixels > 0)
        AND (height_pixels IS NULL OR height_pixels > 0)
    ),
    CONSTRAINT assets_processing_status_check CHECK (
        processing_status IN ('pending', 'processing', 'ready', 'failed')
    )
);

CREATE UNIQUE INDEX assets_storage_key_unique
ON assets (storage_provider, storage_bucket, storage_key);

CREATE INDEX assets_workspace_type_created_idx
ON assets (workspace_id, asset_type, created_at DESC)
WHERE deleted_at IS NULL;

CREATE INDEX assets_workspace_name_idx
ON assets (workspace_id, lower(display_name))
WHERE deleted_at IS NULL;
```

The following are not reusable Assets:

- krpano source ZIPs
- `tour.html`
- `tour.xml`
- panorama tiles
- krpano skins
- krpano plugins
- extracted project directories

Those belong to a Tour Project Version.

### 10.2 Asset Foreign Keys

```sql
ALTER TABLE portfolios
ADD CONSTRAINT portfolios_profile_image_asset_fk
FOREIGN KEY (profile_image_asset_id) REFERENCES assets(id) ON DELETE SET NULL;

ALTER TABLE portfolios
ADD CONSTRAINT portfolios_logo_asset_fk
FOREIGN KEY (logo_asset_id) REFERENCES assets(id) ON DELETE SET NULL;

ALTER TABLE portfolios
ADD CONSTRAINT portfolios_hero_image_asset_fk
FOREIGN KEY (hero_image_asset_id) REFERENCES assets(id) ON DELETE SET NULL;

ALTER TABLE user_accounts
ADD CONSTRAINT user_accounts_avatar_asset_fk
FOREIGN KEY (avatar_asset_id) REFERENCES assets(id) ON DELETE SET NULL;
```

The application verifies that referenced Assets belong to the appropriate Workspace.

---

## 11. Categories

### 11.1 `categories`

```sql
CREATE TABLE categories (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE RESTRICT,
    name VARCHAR(120) NOT NULL,
    slug VARCHAR(140) NOT NULL,
    description TEXT,
    cover_asset_id UUID REFERENCES assets(id) ON DELETE SET NULL,
    position INTEGER NOT NULL DEFAULT 0,
    is_visible BOOLEAN NOT NULL DEFAULT true,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT categories_name_not_blank CHECK (length(trim(name)) > 0),
    CONSTRAINT categories_slug_not_blank CHECK (length(trim(slug)) > 0),
    CONSTRAINT categories_position_non_negative CHECK (position >= 0)
);

CREATE UNIQUE INDEX categories_workspace_slug_unique
ON categories (workspace_id, lower(slug))
WHERE deleted_at IS NULL;

CREATE UNIQUE INDEX categories_workspace_name_unique
ON categories (workspace_id, lower(name))
WHERE deleted_at IS NULL;
```

---

## 12. Tour Projects

### 12.1 `tour_projects`

```sql
CREATE TABLE tour_projects (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE RESTRICT,
    category_id UUID REFERENCES categories(id) ON DELETE SET NULL,
    created_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    title VARCHAR(180) NOT NULL,
    slug VARCHAR(200) NOT NULL,
    short_description VARCHAR(500),
    description TEXT,
    cover_asset_id UUID REFERENCES assets(id) ON DELETE SET NULL,
    status project_status NOT NULL DEFAULT 'draft',
    visibility visibility_type NOT NULL DEFAULT 'private',
    current_version_id UUID,
    published_version_id UUID,
    is_featured BOOLEAN NOT NULL DEFAULT false,
    position INTEGER NOT NULL DEFAULT 0,
    seo_title VARCHAR(70),
    seo_description VARCHAR(170),
    published_at TIMESTAMPTZ,
    last_imported_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    deleted_at TIMESTAMPTZ,
    CONSTRAINT tour_projects_title_not_blank CHECK (length(trim(title)) > 0),
    CONSTRAINT tour_projects_slug_not_blank CHECK (length(trim(slug)) > 0),
    CONSTRAINT tour_projects_position_non_negative CHECK (position >= 0),
    CONSTRAINT tour_projects_publish_consistency CHECK (
        status <> 'published'
        OR (published_at IS NOT NULL AND published_version_id IS NOT NULL)
    )
);

CREATE UNIQUE INDEX tour_projects_workspace_slug_unique
ON tour_projects (workspace_id, lower(slug))
WHERE deleted_at IS NULL;

CREATE INDEX tour_projects_workspace_status_updated_idx
ON tour_projects (workspace_id, status, updated_at DESC)
WHERE deleted_at IS NULL;

CREATE INDEX tour_projects_workspace_category_idx
ON tour_projects (workspace_id, category_id)
WHERE deleted_at IS NULL;

CREATE INDEX tour_projects_public_idx
ON tour_projects (workspace_id, published_at DESC)
WHERE deleted_at IS NULL
  AND status = 'published'
  AND visibility = 'public';
```

### 12.2 `tour_project_versions`

Each package import creates a separate immutable version.

```sql
CREATE TABLE tour_project_versions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tour_project_id UUID NOT NULL REFERENCES tour_projects(id) ON DELETE RESTRICT,
    version_number INTEGER NOT NULL,
    status project_version_status NOT NULL DEFAULT 'uploaded',
    source_filename TEXT NOT NULL,
    source_storage_key TEXT NOT NULL,
    source_file_size_bytes BIGINT NOT NULL,
    source_checksum_sha256 CHAR(64),
    extracted_storage_prefix TEXT,
    entry_html_path TEXT,
    tour_xml_path TEXT,
    detected_wrapper_folder TEXT,
    scene_count INTEGER,
    extracted_file_count INTEGER,
    extracted_size_bytes BIGINT,
    preview_storage_key TEXT,
    thumbnail_storage_key TEXT,
    manifest JSONB NOT NULL DEFAULT '{}'::jsonb,
    validation_report JSONB NOT NULL DEFAULT '{}'::jsonb,
    import_error_code VARCHAR(80),
    import_error_message TEXT,
    created_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    ready_at TIMESTAMPTZ,
    failed_at TIMESTAMPTZ,
    CONSTRAINT tour_project_versions_number_positive CHECK (version_number > 0),
    CONSTRAINT tour_project_versions_source_size_non_negative CHECK (source_file_size_bytes >= 0),
    CONSTRAINT tour_project_versions_scene_count_non_negative CHECK (scene_count IS NULL OR scene_count >= 0),
    CONSTRAINT tour_project_versions_file_count_non_negative CHECK (
        extracted_file_count IS NULL OR extracted_file_count >= 0
    ),
    CONSTRAINT tour_project_versions_extracted_size_non_negative CHECK (
        extracted_size_bytes IS NULL OR extracted_size_bytes >= 0
    ),
    CONSTRAINT tour_project_versions_ready_consistency CHECK (
        status <> 'ready'
        OR (
            ready_at IS NOT NULL
            AND extracted_storage_prefix IS NOT NULL
            AND entry_html_path IS NOT NULL
            AND tour_xml_path IS NOT NULL
        )
    ),
    CONSTRAINT tour_project_versions_failed_consistency CHECK (
        status <> 'failed' OR failed_at IS NOT NULL
    )
);

CREATE UNIQUE INDEX tour_project_versions_number_unique
ON tour_project_versions (tour_project_id, version_number);

CREATE UNIQUE INDEX tour_project_versions_source_storage_unique
ON tour_project_versions (source_storage_key);

CREATE INDEX tour_project_versions_project_created_idx
ON tour_project_versions (tour_project_id, created_at DESC);
```

Once a version reaches `ready`, its package-related fields are immutable. A replacement upload creates a new version rather than overwriting the existing version.

### 12.3 Version References

```sql
ALTER TABLE tour_projects
ADD CONSTRAINT tour_projects_current_version_fk
FOREIGN KEY (current_version_id)
REFERENCES tour_project_versions(id)
ON DELETE RESTRICT;

ALTER TABLE tour_projects
ADD CONSTRAINT tour_projects_published_version_fk
FOREIGN KEY (published_version_id)
REFERENCES tour_project_versions(id)
ON DELETE RESTRICT;
```

The application or a database trigger verifies that current and published versions belong to the same Tour Project, and that a published version has status `ready`.

### 12.4 Version Numbering

Version numbers are sequential within each project. Allocation must be concurrency-safe by locking the project row before calculating the next version number.

---

## 13. Tour Import Processing

### 13.1 `tour_import_jobs`

```sql
CREATE TABLE tour_import_jobs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE CASCADE,
    tour_project_id UUID REFERENCES tour_projects(id) ON DELETE CASCADE,
    tour_project_version_id UUID REFERENCES tour_project_versions(id) ON DELETE CASCADE,
    requested_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    status import_job_status NOT NULL DEFAULT 'pending',
    progress_percent SMALLINT NOT NULL DEFAULT 0,
    current_step VARCHAR(80),
    original_filename TEXT,
    temporary_storage_key TEXT,
    started_at TIMESTAMPTZ,
    completed_at TIMESTAMPTZ,
    failed_at TIMESTAMPTZ,
    error_code VARCHAR(80),
    error_message TEXT,
    error_details JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT tour_import_jobs_progress_range CHECK (
        progress_percent >= 0 AND progress_percent <= 100
    ),
    CONSTRAINT tour_import_jobs_completion_consistency CHECK (
        status <> 'completed' OR completed_at IS NOT NULL
    ),
    CONSTRAINT tour_import_jobs_failure_consistency CHECK (
        status <> 'failed' OR failed_at IS NOT NULL
    )
);

CREATE INDEX tour_import_jobs_workspace_status_idx
ON tour_import_jobs (workspace_id, status, created_at DESC);

CREATE INDEX tour_import_jobs_project_idx
ON tour_import_jobs (tour_project_id, created_at DESC);
```

Temporary uploaded files are removed after successful import, failed validation, cancellation, or expiration of an abandoned job.

### 13.2 `tour_import_validation_issues`

```sql
CREATE TABLE tour_import_validation_issues (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    import_job_id UUID NOT NULL REFERENCES tour_import_jobs(id) ON DELETE CASCADE,
    severity VARCHAR(20) NOT NULL,
    code VARCHAR(80) NOT NULL,
    path TEXT,
    message TEXT NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT validation_issue_severity_check CHECK (
        severity IN ('info', 'warning', 'error')
    )
);
```

---

## 14. Project Gallery

### 14.1 `tour_project_gallery_assets`

```sql
CREATE TABLE tour_project_gallery_assets (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tour_project_id UUID NOT NULL REFERENCES tour_projects(id) ON DELETE CASCADE,
    asset_id UUID NOT NULL REFERENCES assets(id) ON DELETE CASCADE,
    position INTEGER NOT NULL DEFAULT 0,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT tour_project_gallery_position_non_negative CHECK (position >= 0)
);

CREATE UNIQUE INDEX tour_project_gallery_asset_unique
ON tour_project_gallery_assets (tour_project_id, asset_id);

CREATE UNIQUE INDEX tour_project_gallery_position_unique
ON tour_project_gallery_assets (tour_project_id, position);
```

The Asset and Tour Project must belong to the same Workspace.

---

## 15. Publishing

A Tour Project is publicly accessible only when all conditions are true:

- Project status is `published`.
- Project visibility is `public`.
- `published_version_id` is present.
- The published version status is `ready`.
- The Workspace is active.
- The Portfolio is published.
- The Creator account is active.

### 15.1 `publish_history`

```sql
CREATE TABLE publish_history (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE CASCADE,
    tour_project_id UUID REFERENCES tour_projects(id) ON DELETE CASCADE,
    portfolio_id UUID REFERENCES portfolios(id) ON DELETE CASCADE,
    version_id UUID REFERENCES tour_project_versions(id) ON DELETE SET NULL,
    action VARCHAR(30) NOT NULL,
    performed_by_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT publish_history_action_check CHECK (
        action IN ('publish', 'republish', 'unpublish', 'archive')
    )
);
```

This table is append-only.

---

## 16. Analytics

Version 1 analytics supports portfolio views, tour views, tour launches, referrer information, device category, coarse geographic information, daily aggregation, and unique visitor estimation.

Raw IP addresses are not retained permanently.

### 16.1 `analytics_events`

```sql
CREATE TABLE analytics_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE CASCADE,
    portfolio_id UUID REFERENCES portfolios(id) ON DELETE CASCADE,
    tour_project_id UUID REFERENCES tour_projects(id) ON DELETE CASCADE,
    event_type VARCHAR(40) NOT NULL,
    visitor_hash CHAR(64),
    session_id UUID,
    referrer_host VARCHAR(255),
    country_code CHAR(2),
    device_type VARCHAR(20),
    occurred_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
    CONSTRAINT analytics_events_type_check CHECK (
        event_type IN (
            'portfolio_view', 'tour_card_view', 'tour_open',
            'tour_launch', 'outbound_link_click'
        )
    ),
    CONSTRAINT analytics_events_device_check CHECK (
        device_type IS NULL
        OR device_type IN ('desktop', 'tablet', 'mobile', 'other')
    )
);

CREATE INDEX analytics_events_workspace_time_idx
ON analytics_events (workspace_id, occurred_at DESC);

CREATE INDEX analytics_events_tour_time_idx
ON analytics_events (tour_project_id, occurred_at DESC)
WHERE tour_project_id IS NOT NULL;

CREATE INDEX analytics_events_portfolio_time_idx
ON analytics_events (portfolio_id, occurred_at DESC)
WHERE portfolio_id IS NOT NULL;
```

For large-scale usage, this table may later be partitioned by month.

### 16.2 `analytics_daily`

```sql
CREATE TABLE analytics_daily (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    workspace_id UUID NOT NULL REFERENCES workspaces(id) ON DELETE CASCADE,
    portfolio_id UUID REFERENCES portfolios(id) ON DELETE CASCADE,
    tour_project_id UUID REFERENCES tour_projects(id) ON DELETE CASCADE,
    metric_date DATE NOT NULL,
    event_type VARCHAR(40) NOT NULL,
    total_count BIGINT NOT NULL DEFAULT 0,
    unique_visitor_count BIGINT NOT NULL DEFAULT 0,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    CONSTRAINT analytics_daily_counts_non_negative CHECK (
        total_count >= 0 AND unique_visitor_count >= 0
    )
);

CREATE UNIQUE INDEX analytics_daily_metric_unique
ON analytics_daily (
    workspace_id,
    COALESCE(portfolio_id, '00000000-0000-0000-0000-000000000000'::uuid),
    COALESCE(tour_project_id, '00000000-0000-0000-0000-000000000000'::uuid),
    metric_date,
    event_type
);
```

---

## 17. Notifications

### 17.1 `notifications`

```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES user_accounts(id) ON DELETE CASCADE,
    type VARCHAR(60) NOT NULL,
    title VARCHAR(200) NOT NULL,
    message TEXT NOT NULL,
    action_url TEXT,
    metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
    read_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX notifications_user_unread_idx
ON notifications (user_id, created_at DESC)
WHERE read_at IS NULL;
```

Potential notification types include Creator request approval or rejection, tour import completion or failure, tour publishing, and account suspension.

---

## 18. Audit Logging

### 18.1 `audit_logs`

```sql
CREATE TABLE audit_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    actor_user_id UUID REFERENCES user_accounts(id) ON DELETE SET NULL,
    workspace_id UUID REFERENCES workspaces(id) ON DELETE SET NULL,
    action VARCHAR(100) NOT NULL,
    entity_type VARCHAR(80),
    entity_id UUID,
    ip_address INET,
    user_agent TEXT,
    before_data JSONB,
    after_data JSONB,
    metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX audit_logs_actor_time_idx
ON audit_logs (actor_user_id, created_at DESC);

CREATE INDEX audit_logs_workspace_time_idx
ON audit_logs (workspace_id, created_at DESC);

CREATE INDEX audit_logs_entity_idx
ON audit_logs (entity_type, entity_id, created_at DESC);
```

Audit records are append-only.

---

## 19. Database-Level Ownership Validation

Foreign keys alone do not guarantee that related records belong to the same Workspace.

Same-Workspace validation is required for:

- Project → Category
- Project → Cover Asset
- Category → Cover Asset
- Portfolio → Logo Asset
- Portfolio → Hero Asset
- Portfolio → Profile Image Asset
- Project Gallery → Asset
- Published Version → Project
- Current Version → Project

Recommended approach:

```text
Normal foreign keys
+ backend authorisation
+ database trigger validation for critical ownership links
```

---

## 20. Row-Level Security

When using Supabase or direct PostgreSQL access from authenticated clients, Row-Level Security must be enabled.

A Creator may access a Workspace resource only when the Creator owns that Workspace.

Conceptual policy:

```sql
workspace_id IN (
    SELECT workspace_id
    FROM workspace_members
    WHERE user_id = auth.uid()
)
```

Public users may read only published Portfolios, published public Tour Projects, metadata required for public pages, and the selected ready published Tour Project Version.

Public users must never access source ZIP storage paths, validation reports, import errors, draft projects, private Assets, Creator account details, audit logs, or authentication records.

---

## 21. Delete Behaviour

### 21.1 `CASCADE`

Use `CASCADE` for records that have no meaning without their parent, including sessions, verification tokens, password-reset tokens, Portfolio sections, social links, and import validation issues.

### 21.2 `SET NULL`

Use `SET NULL` when history should remain after an optional reference is removed, including reviewing Administrators, audit actors, optional Categories, and optional Asset references.

### 21.3 `RESTRICT`

Use `RESTRICT` for destructive operations involving major ownership or published records, including Workspaces with content, Tour Projects with Versions, and Versions currently selected for publishing.

The application performs an explicit archival or controlled deletion workflow first.

---

## 22. File Storage Mapping

The database does not store project file contents.

Recommended project structure:

```text
/workspaces/{workspace_id}/projects/{project_id}-{project_slug}/

    source/
        version-1.zip
        version-2.zip

    versions/
        v1/
            extracted/
            preview/
            metadata/

        v2/
            extracted/
            preview/
            metadata/
```

Database mapping uses:

```text
tour_project_versions.source_storage_key
tour_project_versions.extracted_storage_prefix
tour_project_versions.preview_storage_key
tour_project_versions.thumbnail_storage_key
```

Reusable Assets use:

```text
/workspaces/{workspace_id}/assets/{asset_id}/
```

Project files and reusable Assets remain physically and logically separate.

---

## 23. Storage Usage

`workspaces.storage_bytes_used` is a cached value for dashboard and quota checks.

The authoritative total is the sum of active reusable Asset sizes, source ZIP sizes, extracted Tour Project Version sizes, and generated previews and thumbnails.

The platform periodically reconciles cached storage totals with actual object storage.

---

## 24. Search

Version 1 search may use PostgreSQL `ILIKE` queries across project titles and descriptions, category names, and Asset display or original filenames.

Search is always scoped by `workspace_id`.

Generated full-text search vectors may be added later.

---

## 25. Data Integrity Transactions

The following operations require database transactions:

### Creator Approval

```text
Approve request
→ Change role
→ Create Workspace
→ Create membership
→ Create settings
→ Optionally create Portfolio
```

### Project Import Completion

```text
Lock project
→ Create version number
→ Save version metadata
→ Mark version ready
→ Set current version
→ Update project state
→ Update storage usage
```

### Project Publishing

```text
Lock project
→ Confirm selected version is ready
→ Set published version
→ Set project status
→ Set visibility
→ Save published timestamp
→ Add publish history
```

### Project Deletion

```text
Confirm not currently published
→ Soft-delete project
→ Schedule storage cleanup
→ Record audit event
```

---

## 26. Recommended Initial Migration Order

```text
001_extensions
002_enum_types
003_user_accounts
004_authentication_tokens_and_sessions
005_creator_requests
006_workspaces
007_workspace_members
008_workspace_settings
009_assets
010_portfolios
011_portfolio_sections
012_portfolio_social_links
013_categories
014_tour_projects
015_tour_project_versions
016_tour_project_version_references
017_tour_import_jobs
018_tour_import_validation_issues
019_tour_project_gallery_assets
020_publish_history
021_analytics_events
022_analytics_daily
023_notifications
024_audit_logs
025_indexes
026_triggers
027_row_level_security
```

Each migration must be reviewable, tested on a clean database, tested against an existing migrated database, and reversible where safely possible.

---

## 27. Tables Summary

| Table | Purpose |
|---|---|
| `user_accounts` | Registered Visitor, Creator, and Administrator accounts |
| `email_verification_tokens` | Email verification |
| `password_reset_tokens` | Password recovery |
| `auth_sessions` | Active login sessions |
| `creator_requests` | Visitor requests for Creator access |
| `workspaces` | Creator content ownership boundary |
| `workspace_members` | Explicit Workspace access |
| `workspace_settings` | Workspace preferences |
| `portfolios` | Creator public Portfolio |
| `portfolio_sections` | Configurable public sections |
| `portfolio_social_links` | Public social links |
| `assets` | Reusable files |
| `categories` | Workspace tour categories |
| `tour_projects` | Stable tour identity and metadata |
| `tour_project_versions` | Immutable imported krpano versions |
| `tour_import_jobs` | ZIP processing state |
| `tour_import_validation_issues` | Package validation problems |
| `tour_project_gallery_assets` | Project gallery assignments |
| `publish_history` | Publishing history |
| `analytics_events` | Raw visitor analytics |
| `analytics_daily` | Aggregated analytics |
| `notifications` | User notifications |
| `audit_logs` | Administrative and security history |

---

## 28. Main Constraints Summary

The database enforces or reliably validates:

- One Creator owns no more than one Workspace.
- One Workspace has one owner.
- One Workspace has no more than one Portfolio.
- One Portfolio belongs to exactly one Workspace.
- One pending Creator request exists per Visitor.
- Category slugs are unique per Workspace.
- Project slugs are unique per Workspace.
- Version numbers are unique per Tour Project.
- Published projects reference a ready version.
- Current and published versions belong to the same project.
- Assets referenced by a resource belong to the same Workspace.
- Tour Project packages are not reusable Assets.
- Ready Tour Project Versions are immutable.
- Raw authentication tokens are never stored.
- Public access exposes published content only.

---

## 29. Deferred Database Features

The following are deferred beyond Version 1:

- Multiple Creator-owned Workspaces
- Multiple Portfolios per Creator
- Team roles
- Workspace invitations
- Client review accounts
- Comments and annotations
- Project approval workflows
- Native panorama editor data
- Multiple tour-engine package types
- Marketplace listings
- Subscription billing
- Usage invoices
- White-label domains
- Custom domain verification
- Public API keys
- Webhook subscriptions
- AI-generated metadata history

The Workspace and Version models preserve a safe extension path without implementing these features prematurely.

---

## 30. Acceptance Criteria

The Database Model is approved when:

- Every approved Domain Model entity has a database representation.
- One Creator–one Portfolio is enforced.
- Visitor, Creator, and Administrator remain separate roles.
- Account status remains separate from role.
- Workspace ownership is explicit.
- Tour Projects and Assets remain separate.
- Every imported krpano package creates a Version.
- Published projects reference an immutable ready Version.
- Cross-Workspace references are prevented.
- Authentication tokens are stored only as hashes.
- Deletion and cascading behaviour are defined.
- Public and private data boundaries are clear.
- Indexes support normal dashboard and public Portfolio queries.
- The design supports PostgreSQL migrations and future API contracts.

---

## 31. Final Version 1 Relationship

```text
User Account
    │
    ├── Visitor
    │      └── Creator Request
    │
    ├── Creator
    │      └── Owns one Workspace
    │              │
    │              ├── One Portfolio
    │              ├── Categories
    │              ├── Assets
    │              ├── Tour Projects
    │              │      └── Tour Project Versions
    │              ├── Analytics
    │              └── Settings
    │
    └── Administrator
           ├── Manages users
           ├── Reviews Creator requests
           ├── Manages account status
           └── Audits platform activity
```

This database model is the approved technical translation of the PanoFolio Version 1 Domain Model.

The next document is `10_API_Design.md`.
