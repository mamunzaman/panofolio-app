# PanoFolio Project Rules

## Purpose

This file defines the mandatory working rules for every human developer and AI assistant contributing to PanoFolio.

## Source of truth

The documentation in `/docs` is the Product Bible and the authoritative source for product behavior, roles, navigation, screens, data, APIs, design, and implementation decisions.

When source code and documentation conflict, the conflict must be reviewed. Code must not silently redefine the product.

## Required workflow

Every feature follows this sequence:

1. Discuss
2. Design
3. Document
4. Review
5. Approve
6. Implement
7. Test
8. Release

Implementation must not begin before the relevant specification is approved.

## Product constraints

- PanoFolio is a creator portfolio platform for immersive 360-degree virtual tours.
- Version 1 is not a marketplace.
- One Creator owns exactly one Portfolio.
- Visitors may request Creator access.
- Creator access requires Administrator approval.
- Architecture must support future verticals without redesigning the core domain.

## AI behavior

- No AI may invent product functionality independently.
- All AIs must use the same Product Bible.
- Missing requirements must be identified rather than silently assumed.
- Architectural decisions must be recorded in `docs/13_Decision_Log.md`.
- Shared terminology must follow `docs/14_Glossary.md`.
- Existing reusable components must be preferred over duplicate components.

## Quality requirements

A feature is not complete until its specification, implementation, responsive behavior, accessibility, permissions, loading state, empty state, error state, tests, and documentation are aligned.

## Change control

Any change affecting roles, permissions, navigation, database structure, APIs, shared components, or product behavior requires documentation review before implementation.
