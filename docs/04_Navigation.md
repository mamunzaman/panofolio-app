# 04 — Navigation

## Purpose

This document defines the approved Version 1 navigation architecture. Every screen belongs to one primary navigation area. Role-specific navigation must reflect permissions without replacing server-side authorization.

## Product navigation model

PanoFolio contains three distinct navigation contexts:

1. Public Portfolio navigation for viewing published content
2. Workspace portal navigation for desktop content management
3. Administrator navigation for platform governance

The Workspace portal is the primary product interface.

## Public Portfolio navigation

A published Portfolio may contain:

- Portfolio Home
- Experiences
- Categories
- About
- Contact

The exact visible items may depend on published content and Portfolio configuration. Public navigation must never expose draft or private content.

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
- Workspace Access Request
- Profile
- Security
- Appearance
- Notifications

The Workspace Access Request destination shows the request form or current request status.

## Workspace portal navigation

Approved Workspace Users land on `Dashboard` after login.

The approved high-level navigation is:

- Dashboard
- Portfolio
- Experiences
- Media
- Categories
- Branding
- Analytics
- Settings
- Support

### Dashboard

Owns the operational overview, including:

- Portfolio status
- Draft and published Experience summaries
- Recent Media
- Storage summary when supported
- Recent activity
- Quick actions

### Portfolio

Owns the public Portfolio configuration:

- Portfolio Overview
- General Information
- Contact Information
- SEO
- Publishing status

A separate generic website or Homepage Builder is not part of the approved architecture. Public Portfolio composition must be defined as structured Portfolio configuration rather than an unrestricted website builder unless separately approved.

### Experiences

Owns immersive content management:

- All Experiences
- Create Experience
- Draft Experiences
- Published Experiences
- Archived Experiences
- Experience Editor
- Scene or Panorama Manager
- Hotspot and interaction configuration where approved

`Experience` is the primary domain term. A virtual Tour is one Experience type.

### Media

Owns the central Workspace asset library:

- All Media
- Panoramas
- Images
- Videos
- Documents
- Uploads
- Asset details and usage

Media is uploaded to the Workspace library and may then be attached to Experiences or Portfolio content according to the Media specification.

### Categories

Owns Experience categorization and ordering.

### Branding

Owns public visual identity:

- Logo
- Colours
- Typography options where supported
- Cover and social images
- Brand presentation settings

### Analytics

Owns approved Portfolio and Experience performance reporting. Analytics may be limited or deferred in Version 1 according to the Roadmap.

### Settings

Owns Workspace-level configuration not assigned elsewhere:

- Workspace details
- Publishing defaults
- Storage information
- Integrations or API keys when separately approved
- Destructive actions

### Support

Owns help, documentation, and support contact paths.

## Administrator navigation

Administrators have access to:

- Dashboard
- Access Requests
- Accounts
- Workspaces
- Portfolios
- Media
- Platform
- Logs

### Navigation ownership

- Access Requests owns Workspace access approval workflows.
- Accounts owns user listing and Account details.
- Workspaces owns Workspace listing and management.
- Portfolios owns public Portfolio listing and status.
- Media owns approved platform-level Media review and administration.
- Platform owns platform-wide settings.
- Logs owns operational and audit records.

## Navigation rules

- Every screen belongs to one primary navigation area.
- Labels must use approved product terminology.
- Workspace navigation must be optimized for frequent desktop management work.
- Navigation must not expose inaccessible destinations.
- Direct access to a restricted route must still be blocked by authorization.
- Active navigation state must be clear.
- Parent destinations remain active for nested screens.
- Back navigation must not lose unsaved work without warning.
- Public and private navigation contexts must remain visually and technically distinct.

## Naming rules

Use explicit formal screen names:

- `Experience List`, not merely `Experiences`, when identifying a screen specification.
- `Portfolio Overview`, not `My Portfolio Page`.
- `Workspace Dashboard`, not `Dashboard Home`.
- `Media Library`, not `Uploads` when referring to the complete asset-management screen.

Navigation labels may remain concise, while formal screen names follow the screen inventory.

## Responsive navigation

### Desktop

The Workspace portal should use a persistent sidebar or an equivalently efficient navigation pattern. Desktop is the primary content-upload and editing environment.

### Tablet

Navigation may collapse while preserving hierarchy, active state, and access to primary actions.

### Mobile

Mobile access may support review and lightweight management, but complex upload and immersive editing workflows are desktop-first. Mobile navigation must avoid implying that every desktop workflow is fully supported unless documented per screen.

Detailed responsive behavior is defined per screen and later in the Design System and UI Guidelines.

## Access outcomes

Navigation or route access may resolve to:

- Screen loaded successfully
- Authentication required
- Workspace access required
- Permission denied
- Resource not found
- Portfolio or content unavailable

These outcomes must be intentionally designed rather than left to generic browser behavior.
