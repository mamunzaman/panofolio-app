# 05 — Screen Specifications

## Purpose

This document is the implementation contract for every PanoFolio screen. Screens are fully specified before coding begins.

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

### Public

| ID | Screen | Primary role | Status |
|---|---|---|---|
| PUB-001 | Home | Public | Planned |
| PUB-002 | Tour List | Public | Planned |
| PUB-003 | Category List | Public | Planned |
| PUB-004 | Category Details | Public | Planned |
| PUB-005 | Tour Details | Public | Planned |
| PUB-006 | Panorama Viewer | Public | Planned |
| PUB-007 | About | Public | Planned |
| PUB-008 | Contact | Public | Planned |
| PUB-009 | Error Pages | Public | Planned |

### Authentication

| ID | Screen | Primary role | Status |
|---|---|---|---|
| AUTH-001 | Login | Public | Planned |
| AUTH-002 | Register | Public | Planned |
| AUTH-003 | Verify Email | Public | Planned |
| AUTH-004 | Forgot Password | Public | Planned |
| AUTH-005 | Reset Password | Public | Planned |

### Visitor

| ID | Screen | Primary role | Status |
|---|---|---|---|
| VIS-001 | Visitor Dashboard | Visitor | Planned |
| VIS-002 | Creator Request | Visitor | Planned |
| VIS-003 | Account | Visitor | Planned |
| VIS-004 | Profile | Visitor | Planned |
| VIS-005 | Security | Visitor | Planned |
| VIS-006 | Appearance | Visitor | Planned |
| VIS-007 | Notifications | Visitor | Planned |

### Creator

| ID | Screen | Primary role | Status |
|---|---|---|---|
| CRE-001 | Creator Dashboard | Creator | Planned |
| CRE-002 | Portfolio Overview | Creator | Planned |
| CRE-003 | Portfolio Settings | Creator | Planned |
| CRE-004 | Homepage Builder | Creator | Planned |
| CRE-005 | Tour List | Creator | Planned |
| CRE-006 | Create Tour | Creator | Planned |
| CRE-007 | Edit Tour | Creator | Planned |
| CRE-008 | Categories | Creator | Planned |
| CRE-009 | Media Library | Creator | Planned |
| CRE-010 | Profile | Creator | Planned |
| CRE-011 | Settings | Creator | Planned |

### Administrator

| ID | Screen | Primary role | Status |
|---|---|---|---|
| ADM-001 | Administrator Dashboard | Administrator | Planned |
| ADM-002 | Creator Requests | Administrator | Planned |
| ADM-003 | Users | Administrator | Planned |
| ADM-004 | User Details | Administrator | Planned |
| ADM-005 | Portfolios | Administrator | Planned |
| ADM-006 | Portfolio Details | Administrator | Planned |
| ADM-007 | Media | Administrator | Planned |
| ADM-008 | Platform Settings | Administrator | Planned |
| ADM-009 | Logs | Administrator | Planned |

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

- `Tour List`, not `Tours`
- `Portfolio Overview`, not `My Portfolio Page`
- `Creator Dashboard`, not `Dashboard Home`

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

# PUB-001 — Home

## Status

Planned. Detailed specification is the next approved documentation task.

## Purpose

Serve as the public entry point for a Creator's Portfolio and guide visitors toward the Creator's work, featured Tours, Categories, profile information, and contact path.

## User Roles

- Unauthenticated Visitor
- Authenticated Visitor
- Creator viewing a published public Portfolio
- Administrator when using the public route

## Route

The final public Portfolio URL structure is not yet approved and must be defined before implementation.

## Navigation Entry

Public navigation: Home.

## Approved boundaries

The Home screen belongs to the public Portfolio experience. It must represent one Creator's single Portfolio and must not behave as a multi-creator marketplace homepage.

## Details still requiring design and approval

- Final route format
- Hero content and actions
- Homepage section model and ordering
- Featured Tour behavior
- Category presentation
- About preview
- Contact call to action
- Empty Portfolio behavior
- Draft and unpublished Portfolio behavior
- SEO metadata
- API contract
- Database dependencies
- Analytics events
- Exact desktop, tablet, and mobile layouts

No implementation should begin until these details are documented and approved using the full screen template.
