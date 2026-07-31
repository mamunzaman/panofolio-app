# 04 — Navigation

## Purpose

This document defines the approved Version 1 navigation architecture. Every screen belongs to one primary navigation area. Role-specific navigation must reflect permissions without replacing server-side authorization.

## Public navigation

The public portfolio experience contains:

- Home
- Tours
- Categories
- About
- Contact

Public navigation is available to unauthenticated and authenticated users unless a Portfolio or item is not published.

## Shared account navigation

Authenticated users may access:

- My Account
- Profile
- Security
- Appearance
- Notifications
- Logout

These destinations are shared by Visitors and Creators, subject to any documented role-specific differences.

## Visitor dashboard navigation

Visitors have access to:

- Dashboard
- Creator Access
- Account

The Creator Access destination shows the request form or the current request status.

## Creator dashboard navigation

Creators have access to:

- Dashboard
- Portfolio
- Homepage Builder
- Tours
- Categories
- Media
- Profile
- Settings

### Navigation ownership

- Dashboard owns the Creator summary and operational overview.
- Portfolio owns portfolio overview and portfolio-level configuration.
- Homepage Builder owns public homepage composition.
- Tours owns Tour listing, creation, and editing.
- Categories owns Category management.
- Media owns uploaded media and related management.
- Profile owns public Creator profile information.
- Settings owns Creator and Portfolio settings not assigned elsewhere.

## Administrator navigation

Administrators have access to:

- Dashboard
- Creator Requests
- Users
- Portfolios
- Media
- Platform
- Logs

### Navigation ownership

- Creator Requests owns approval workflows.
- Users owns user listing and user details.
- Portfolios owns portfolio listing and portfolio details.
- Media owns platform-level media review and administration.
- Platform owns platform-wide settings.
- Logs owns operational and audit records.

## Navigation rules

- Every screen belongs to one primary navigation area.
- Labels must use approved product terminology.
- Navigation must not expose inaccessible destinations.
- Direct access to a restricted route must still be blocked by authorization.
- Active navigation state must be clear.
- Parent destinations remain active for nested screens.
- Back navigation must not lose unsaved work without warning.

## Naming rules

Use explicit names:

- `Tour List`, not merely `Tours`, when identifying a screen specification.
- `Portfolio Overview`, not `My Portfolio Page`.
- `Creator Dashboard`, not `Dashboard Home`.

Navigation labels may remain concise, while formal screen names follow the screen inventory.

## Responsive navigation

### Desktop

Creator and Administrator areas should use a persistent or clearly accessible dashboard navigation pattern suitable for frequent management work.

### Tablet

Navigation may collapse while preserving hierarchy and clear active state.

### Mobile

Navigation must prioritize primary actions, avoid overcrowding, and provide an accessible menu for secondary destinations.

Detailed responsive behavior is defined per screen and later in the Design System and UI Guidelines.

## Access outcomes

Navigation or route access may resolve to:

- Screen loaded successfully
- Authentication required
- Permission denied
- Resource not found
- Portfolio or content unavailable

These outcomes must be designed rather than left to generic browser behavior.
