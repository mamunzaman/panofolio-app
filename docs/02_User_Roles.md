# 02 — User Roles

## Overview

PanoFolio Version 1 has three primary access roles:

- Visitor
- Workspace User
- Administrator

Role permissions are explicit and must be enforced in the user interface, API, and database access layer.

`Workspace User` is the approved Version 1 management role. Product copy may later use an industry-specific label where appropriate, but internal architecture and permissions should use Workspace ownership rather than assuming every user identifies as a Creator.

## Visitor

### Description

A registered or unauthenticated user who can browse published content. A registered Visitor may also manage a personal Account and request Workspace access.

### Capabilities

- Browse published Portfolios and Experiences
- Register an Account
- Verify email
- Log in and log out
- Manage personal profile and security settings
- Request Workspace access
- View the status of an access request

### Restrictions

A Visitor cannot:

- Own or manage a Workspace
- Create or manage a Portfolio
- Upload Workspace Media
- Create Experiences or Categories
- Access Workspace management screens
- Approve access requests
- Manage other users

## Workspace User

### Description

An approved Account that owns and manages one Workspace in Version 1.

The Workspace is the management boundary for Portfolio content, Experiences, Media, Categories, Branding, Analytics, and settings.

### Capabilities

All registered Visitor capabilities, plus:

- Enter the Workspace Dashboard after login
- Configure one Workspace
- Create and manage one public Portfolio
- Configure Portfolio information, Branding, contact details, and publishing settings
- Create, edit, publish, unpublish, and archive Experiences
- Manage Experience types supported in the approved Version 1 scope
- Create and manage Categories
- Upload, organize, select, reuse, and remove Media according to the Media specification
- View Workspace analytics when implemented
- Manage Workspace settings

### Ownership rule

One approved Account equals one Workspace in Version 1.

One Workspace owns one public Portfolio in Version 1.

A Workspace User cannot create a second Workspace or second Portfolio. Future support for teams, organizations, additional Workspaces, or multiple Portfolios requires a documented architectural decision.

### Restrictions

A Workspace User cannot:

- Manage another Account's Workspace
- Access another Workspace's private Media
- Approve access requests
- Manage platform-wide settings
- Access Administrator-only audit records and controls

## Administrator

### Description

A platform operator responsible for governance and platform management.

### Capabilities

- Access the Administrator Dashboard
- Review, approve, or reject Workspace access requests
- View and manage Accounts and role status
- View and manage Workspaces
- View and manage public Portfolios
- Review Media where an approved moderation or support workflow permits it
- Manage platform settings
- View operational and audit Logs
- Suspend or restrict access when supported by an approved workflow

### Restrictions

Administrator actions must be auditable. Administrators must not silently modify Workspace-owned content without a defined administrative action and recorded reason where required.

## Role transition

The approved transition is:

```text
Register Account
↓
Verify Email
↓
Visitor
↓
Request Workspace Access
↓
Administrator Review
↓
Approved Workspace User
↓
Workspace Provisioned
↓
Dashboard
↓
Configure Portfolio and Publish
```

A rejected request leaves the Account as a Visitor.

## Login destination

- A Visitor lands on the Visitor account area or the route originally requested.
- An approved Workspace User lands on the Workspace Dashboard by default.
- An Administrator lands on the Administrator Dashboard by default.

## Permission enforcement

Permissions must be enforced at all relevant layers:

- Navigation visibility
- Route access
- Screen actions
- API authorization
- Workspace ownership
- Record ownership
- Storage access
- Database policies

Hiding a button is not sufficient authorization.

## Permission-denied behavior

When a user attempts an unauthorized action, the platform must:

- Prevent the action
- Return an appropriate authorization response
- Show a clear permission-denied state or redirect
- Avoid exposing restricted data or asset URLs
- Record security-relevant events where appropriate

## Future roles

Possible future roles include Workspace Owner, Organization Owner, Team Member, Editor, Reviewer, and Client. They are not part of Version 1 and must not be implemented without approval.
