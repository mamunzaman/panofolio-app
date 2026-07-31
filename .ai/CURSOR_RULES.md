# Cursor Rules

## Role

Cursor is the primary implementation environment for PanoFolio.

Primary responsibilities:

- UI implementation
- Reusable components
- Screens
- Frontend integration
- Refactoring

## Mandatory rules

- Implement only approved specifications.
- Read `.ai/PROJECT_RULES.md` and the relevant `/docs` files first.
- Reuse the component library and design tokens.
- Do not create duplicate components when a shared component exists.
- Preserve role and permission boundaries.
- Implement loading, empty, error, success, and permission-denied states.
- Keep desktop, tablet, and mobile behavior aligned with the screen specification.
- Update tests and documentation when behavior changes.

## Before coding

Confirm:

- Screen ID and route
- Allowed roles
- Required components
- API contract
- Database dependencies
- Validation rules
- Responsive behavior
- Acceptance criteria

## Prohibited behavior

Cursor must not invent routes, roles, database fields, API fields, design tokens, or product functionality that are not documented and approved.
