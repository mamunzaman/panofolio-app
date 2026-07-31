# 04 — Navigation

## Purpose

This document defines the approved Version 1 navigation architecture for PanoFolio. Every screen belongs to one primary navigation area. Role-specific navigation must reflect permissions without replacing server-side authorization.

## Product navigation model

PanoFolio contains three distinct navigation contexts:

1. Public Portfolio navigation for viewing published content
2. Creator Workspace navigation for managing Portfolio content and uploaded 360° projects
3. Administrator navigation for platform governance

The Creator Workspace is the primary management interface.

## Approved product terminology

PanoFolio uses the following terms consistently:

- **Portfolio** — the Creator's single public showcase within a Workspace.
- **Category** — a curated public section of the Portfolio with its own hero image, title, description, order, and list of published Tour Projects.
- **Tour Project** — one completed 360° virtual tour package created in third-party software and uploaded to PanoFolio as a ZIP file.
- **Asset** — reusable media such as cover images, logos, videos, documents, and other Portfolio or Project presentation media.
- **Version** — an immutable imported ZIP package belonging to a Tour Project.

PanoFolio is not a panorama editor or tour-authoring tool. Hotspots, scenes, transitions, and navigation are created in third-party software before the ZIP package is uploaded.

## Public Portfolio navigation

A published Portfolio may contain:

- Portfolio Home
- Category pages
- Individual Tour Project pages or direct tour viewer routes
- About
- Contact

Each Category page may contain:

- Hero image
- Category title
- Category description
- Ordered list or grid of published Tour Projects assigned to that Category

Public navigation must never expose draft, processing, failed, archived, private, or unpublished content.

## Shared account navigation

Authenticated users may access:

- My Account
- Profile
- Security
- Appearance
- Notifications
- Logout

These destinations are separate from Workspace-owned content settings.

## Visitor account navigation

Registered Visitors have access to:

- Account Overview
- Creator Access Request
- Profile
- Security
- Appearance
- Notifications

The Creator Access Request destination shows the request form or current request status.

## Creator Workspace navigation

Approved Creators land on `Dashboard` after login.

The approved high-level navigation is:

- Dashboard
- Portfolio
- Categories
- Tour Projects
- Assets
- Analytics
- Settings
- Support

### Dashboard

Owns the operational overview, including:

- Portfolio publishing status
- Published and draft Tour Project summaries
- Recent Tour Projects
- Recent Assets
- Storage summary
- Recent activity
- Quick actions

### Portfolio

Owns the Creator's single public Portfolio configuration:

- Portfolio Overview
- Portfolio hero image
- Portfolio title and description
- About information
- Contact information
- Category ordering
- SEO metadata
- Publishing status
- Public Portfolio preview

Portfolio is not an unrestricted website builder. Version 1 uses structured fields and ordered Categories.

### Categories

Owns public Portfolio organization:

- All Categories
- Create Category
- Category details
- Hero image
- Title
- Description
- Slug
- Display order
- Visibility or publishing state
- Assigned Tour Projects
- Tour Project ordering within the Category

A Category becomes a separate public Portfolio page when published.

Deleting a Category must not delete its Tour Projects. Affected Tour Projects become uncategorized until reassigned.

### Tour Projects

Owns individual uploaded 360° packages:

- All Tour Projects
- Create Tour Project
- Project information
- Category assignment
- Cover image
- ZIP upload
- ZIP validation
- Version history
- Preview
- Publish or unpublish
- Project analytics
- Archive or delete

A Tour Project represents one completed virtual tour created in third-party software. PanoFolio stores, validates, versions, publishes, and serves the package without editing its internal authoring structure.

### Assets

Owns the central reusable Workspace Asset Library:

- All Assets
- Images
- Panoramas used as reusable presentation media
- Videos
- Floor plans
- Logos
- Documents
- Uploads
- Asset details
- Asset usage

Tour Project ZIP packages and extracted package files must not appear in the reusable Asset Library.

### Analytics

Owns approved Portfolio, Category, and Tour Project performance reporting.

Version 1 may include:

- Portfolio views
- Category views
- Tour Project views
- Unique visitors
- Average viewing time
- Device breakdown
- Traffic sources
- Leads where supported

### Settings

Owns Workspace-level configuration not assigned elsewhere:

- Workspace details
- Publishing defaults
- Storage information
- Account preferences
- Integrations when separately approved
- Destructive actions

Branding customization is not a Version 1 priority. Only fields required for the Portfolio presentation should be exposed.

### Support

Owns help, documentation, ZIP upload guidance, and support contact paths.

## Administrator navigation

Administrators have access to:

- Dashboard
- Creator Requests
- Accounts
- Workspaces
- Portfolios
- Categories
- Tour Projects
- Assets
- Platform
- Logs

## Primary Creator workflow

```text
Create or configure Portfolio
        ↓
Create Category
        ↓
Create Tour Project
        ↓
Assign Category
        ↓
Upload completed 360° ZIP package
        ↓
Validate package
        ↓
Preview
        ↓
Publish Tour Project
        ↓
Tour Project appears on its public Category page
```

A Creator may create Tour Projects before Categories, but a published Tour Project should normally be assigned to a published Category to appear in the public Portfolio navigation.

## Navigation rules

- Every screen belongs to one primary navigation area.
- Labels must use approved product terminology.
- `Tour Projects` must be used instead of `Experiences`.
- `Assets` must be used instead of `Media` in the Creator Workspace.
- Portfolio, Categories, and Tour Projects must remain separate concepts.
- Workspace navigation must be optimized for frequent desktop management work.
- Navigation must not expose inaccessible destinations.
- Direct access to restricted routes must still be blocked by authorization.
- Active navigation state must be clear.
- Parent destinations remain active for nested screens.
- Back navigation must not lose unsaved work without warning.
- Public and private navigation contexts must remain visually and technically distinct.

## Formal screen naming

Use explicit screen names:

- `Workspace Dashboard`
- `Portfolio Overview`
- `Category List`
- `Category Details`
- `Tour Project List`
- `Tour Project Details`
- `Create Tour Project`
- `Upload Tour ZIP`
- `Asset Library`

## Responsive navigation

### Desktop

The Creator Workspace uses a persistent sidebar or an equivalently efficient navigation pattern. Desktop is the primary ZIP upload and content-management environment.

### Tablet

Navigation may collapse while preserving hierarchy, active state, and access to primary actions.

### Mobile

Mobile access may support Portfolio browsing, preview, analytics review, and lightweight management. Large ZIP upload and detailed content management remain desktop-first unless explicitly supported later.

## Access outcomes

Navigation or route access may resolve to:

- Screen loaded successfully
- Authentication required
- Creator access required
- Permission denied
- Resource not found
- Portfolio unavailable
- Category unavailable
- Tour Project unavailable

These outcomes must be intentionally designed rather than left to generic browser behavior.
