# 02 — User Roles

## Overview

PanoFolio Version 1 has three primary roles:

- Visitor
- Creator
- Administrator

Role permissions are explicit and must be enforced in the user interface, API, and database access layer.

## Visitor

### Description

A registered user who can browse public content and manage a personal account.

### Capabilities

- Register an account
- Verify email
- Log in and log out
- Browse public portfolios, categories, tours, and panorama experiences
- Manage personal profile
- Manage security preferences
- Manage appearance preferences
- Manage notification preferences
- Request Creator access
- View the status of a Creator request

### Restrictions

A Visitor cannot:

- Create or own a Portfolio
- Upload portfolio media
- Create Tours or Categories
- Access Creator management screens
- Approve Creator requests
- Manage other users

## Creator

### Description

An approved user who owns and manages exactly one Portfolio.

### Capabilities

All Visitor capabilities, plus:

- Create one Portfolio after approval
- Manage Portfolio information and settings
- Configure the public homepage
- Create, edit, publish, unpublish, and archive Tours
- Create and manage Categories
- Upload and manage Media
- Manage the Creator profile associated with the Portfolio
- Access Creator dashboard information

### Ownership rule

One Creator equals one Portfolio in Version 1.

A Creator cannot create or own a second Portfolio. Any future support for teams, organizations, or multiple portfolios requires a new documented architectural decision.

### Restrictions

A Creator cannot:

- Manage another Creator's Portfolio
- Approve Creator requests
- Manage platform-wide settings
- Access Administrator-only logs and controls

## Administrator

### Description

A platform operator responsible for governance and platform management.

### Capabilities

- Access Administrator dashboard
- Review, approve, or reject Creator requests
- View and manage users
- View user details and role status
- View and manage Portfolios
- View Portfolio details
- Review and manage Media
- Manage platform settings
- View operational and audit Logs
- Suspend or restrict access when supported by an approved workflow

### Restrictions

Administrator actions must be auditable. Administrators must not silently modify Creator-owned content without a defined administrative action and recorded reason where required.

## Role transition

The approved transition is:

```text
Register
↓
Verify Email
↓
Visitor
↓
Request Creator Access
↓
Administrator Review
↓
Approved Creator
↓
Create Portfolio
↓
Publish
```

A rejected request leaves the user as a Visitor.

## Permission enforcement

Permissions must be enforced at all relevant layers:

- Navigation visibility
- Route access
- Screen actions
- API authorization
- Record ownership
- Database policies

Hiding a button is not sufficient authorization.

## Permission-denied behavior

When a user attempts an unauthorized action, the platform must:

- Prevent the action
- Return an appropriate authorization response
- Show a clear permission-denied state or redirect
- Avoid exposing restricted data
- Record security-relevant events where appropriate

## Future roles

Possible future roles include Organization Owner, Team Member, Editor, Reviewer, or Client. They are not part of Version 1 and must not be implemented without approval.
