# 05 — Screen Specifications

## Purpose

This document is the implementation contract for every PanoFolio screen. Screens are fully specified before coding begins.

## Product priority

The primary product is the desktop-first Workspace portal. Detailed screen specifications therefore begin with Authentication and Workspace management screens.

The public Portfolio is the published output of Workspace content. Public screens remain required, but they are specified after the core management workflow.

## Screen specification template

Every screen must use the following structure:

1. Purpose
2. User Roles
3. Route
4. Navigation Entry
5. Layout
6. Components
7. Actions
8. User Flow
9. Screen States
10. Validation Rules
11. Permissions
12. API Requirements
13. Database Dependencies
14. Notifications
15. Responsive Behaviour
16. Accessibility
17. Analytics Events
18. Future Enhancements
19. Notes

## Screen lifecycle

```text
Navigate
↓
Load Data
↓
Loading
↓
Success | Empty | Error | Permission Denied
```

Every applicable outcome must be intentionally designed and implemented.

## Screen inventory

### Authentication

| ID | Screen | Primary role | Status |
|---|---|---|---|
| AUTH-001 | Login | Public | Planned |
| AUTH-002 | Register | Public | Planned |
| AUTH-003 | Verify Email | Public | Planned |
| AUTH-004 | Forgot Password | Public | Planned |
| AUTH-005 | Reset Password | Public | Planned |

### Visitor account

| ID | Screen | Primary role | Status |
|---|---|---|---|
| VIS-001 | Account Overview | Visitor | Planned |
| VIS-002 | Workspace Access Request | Visitor | Planned |
| VIS-003 | Profile | Visitor | Planned |
| VIS-004 | Security | Visitor | Planned |
| VIS-005 | Appearance | Visitor | Planned |
| VIS-006 | Notifications | Visitor | Planned |

### Workspace portal

| ID | Screen | Primary role | Status |
|---|---|---|---|
| WSP-001 | Workspace Dashboard | Workspace User | Next specification |
| WSP-002 | Portfolio Overview | Workspace User | Planned |
| WSP-003 | Portfolio General Information | Workspace User | Planned |
| WSP-004 | Portfolio Contact Information | Workspace User | Planned |
| WSP-005 | Portfolio SEO and Publishing | Workspace User | Planned |
| WSP-006 | Experience List | Workspace User | Planned |
| WSP-007 | Create Experience | Workspace User | Planned |
| WSP-008 | Experience Editor | Workspace User | Planned |
| WSP-009 | Scene and Panorama Manager | Workspace User | Planned |
| WSP-010 | Categories | Workspace User | Planned |
| WSP-011 | Media Library | Workspace User | Planned |
| WSP-012 | Media Upload | Workspace User | Planned |
| WSP-013 | Media Asset Details | Workspace User | Planned |
| WSP-014 | Branding | Workspace User | Planned |
| WSP-015 | Analytics | Workspace User | Planned |
| WSP-016 | Workspace Settings | Workspace User | Planned |
| WSP-017 | Support | Workspace User | Planned |

### Public Portfolio

| ID | Screen | Primary role | Status |
|---|---|---|---|
| PUB-001 | Portfolio Home | Public | Planned |
| PUB-002 | Experience List | Public | Planned |
| PUB-003 | Category List | Public | Planned |
| PUB-004 | Category Details | Public | Planned |
| PUB-005 | Experience Details | Public | Planned |
| PUB-006 | Immersive Viewer | Public | Planned |
| PUB-007 | About | Public | Planned |
| PUB-008 | Contact | Public | Planned |
| PUB-009 | Error Pages | Public | Planned |

### Administrator

| ID | Screen | Primary role | Status |
|---|---|---|---|
| ADM-001 | Administrator Dashboard | Administrator | Planned |
| ADM-002 | Workspace Access Requests | Administrator | Planned |
| ADM-003 | Accounts | Administrator | Planned |
| ADM-004 | Account Details | Administrator | Planned |
| ADM-005 | Workspaces | Administrator | Planned |
| ADM-006 | Workspace Details | Administrator | Planned |
| ADM-007 | Portfolios | Administrator | Planned |
| ADM-008 | Portfolio Details | Administrator | Planned |
| ADM-009 | Media Administration | Administrator | Planned |
| ADM-010 | Platform Settings | Administrator | Planned |
| ADM-011 | Logs | Administrator | Planned |

## Approved workflow priority

```text
Authentication
↓
Workspace Access Approval
↓
Workspace Dashboard
↓
Portfolio Configuration
↓
Media Upload and Library
↓
Experience Creation
↓
Scene and Interaction Editing
↓
Publishing
↓
Public Portfolio
```

## Status progression

```text
Planned
↓
Designing
↓
Development
↓
Testing
↓
Completed
```

A status change must reflect actual project state. Documentation approval moves a screen from Planned to Designing or an explicitly adopted documentation-approved state; it does not mean development is complete.

## Screen ownership rule

Every screen belongs to one primary navigation area. A screen may link to another area, but it must not have ambiguous ownership.

## Naming standard

Formal screen names must be explicit and consistent:

- `Experience List`, not `Tours`
- `Portfolio Overview`, not `My Portfolio Page`
- `Workspace Dashboard`, not `Dashboard Home`
- `Media Library`, not `Uploads`

## Desktop-first rule

Workspace upload and immersive editing screens are designed for desktop first.

Tablet and mobile behavior must still be documented, but mobile parity must not be assumed for complex operations such as bulk upload, panorama sequencing, or hotspot placement.

## Definition of Done

A screen is complete only when:

- Specification is approved
- UI is approved
- Shared components are reused
- Responsive behavior is verified
- Accessibility is verified
- API integration is complete
- Permissions are enforced
- Loading state is implemented
- Empty state is implemented where applicable
- Error state is implemented
- Permission-denied behavior is implemented where applicable
- Tests pass
- Documentation is updated

---

# WSP-001 — Workspace Dashboard

## Status

Planned. This is the next full screen specification after the Information Architecture and Media model are approved.

## Purpose

Provide the approved Workspace User with a desktop control centre immediately after login.

## Approved Dashboard boundaries

The Dashboard should summarize and link to management work. It must not duplicate full Portfolio, Experience, or Media editing interfaces.

Expected summary areas include:

- Portfolio publishing status
- Draft and published Experience counts
- Recent Media
- Storage summary when supported
- Recent activity
- Quick actions

## Details still requiring design and approval

- Final route
- Exact information hierarchy
- Quick actions
- First-use onboarding state
- Empty Workspace state
- Portfolio status model
- Recent activity model
- API contract
- Database dependencies
- Analytics events
- Exact desktop, tablet, and mobile layouts

No implementation should begin until these details are documented and approved using the full screen template.
