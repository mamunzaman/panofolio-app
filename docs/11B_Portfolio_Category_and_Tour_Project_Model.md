# PanoFolio — Portfolio, Category, and Tour Project Model

**Status:** Approved  
**Version:** 1.0  
**Scope:** Version 1 product and UI model

## 1. Purpose

This document records the approved relationship between the public Portfolio, Portfolio Categories, and uploaded 360° Tour Projects.

It replaces any interpretation of Portfolio as an unrestricted website builder and any interpretation of PanoFolio as a 360° tour-authoring tool.

## 2. Approved structure

```text
User
└── Workspace
    ├── Portfolio
    │   └── Categories
    │       └── Tour Projects
    └── Assets
```

Version 1 rules:

- One Workspace per Creator.
- One Portfolio per Workspace.
- A Portfolio contains ordered Categories.
- Each Category can contain multiple Tour Projects.
- A Tour Project represents one completed 360° ZIP package.
- Assets remain reusable Workspace media and are separate from Tour ZIP package contents.
- Tour Project Versions are immutable.
- Publishing points to a ready Version.

## 3. Portfolio

The Portfolio is the Creator's single public showcase.

It provides the public entry point and may include:

- Hero image
- Portfolio title
- Portfolio description
- About information
- Contact information
- Ordered Category navigation
- Public publishing status
- Basic SEO metadata

The Portfolio is not a drag-and-drop website builder in Version 1.

## 4. Categories

Categories organize published Tour Projects into separate public pages.

Examples:

- Real Estate
- Hotels
- Restaurants
- Museums
- Universities
- Construction
- Luxury Villas
- Commercial Spaces

Each Category may contain:

- Hero image
- Title
- Description
- Slug
- Display order
- Visibility or publishing state
- Ordered Tour Project list

Example public Category page:

```text
Luxury Villas

Hero image
Title
Description

Tour Projects
- Modern Villa in Bali
- Malibu Beach House
- Miami Waterfront Villa
```

A Creator may define custom Categories.

Deleting a Category does not delete its Tour Projects. Projects become uncategorized until reassigned.

## 5. Tour Projects

A Tour Project is one completed 360° virtual tour package created in third-party software.

Examples of external authoring tools may include 3DVista, Pano2VR, krpano-based exporters, or other compatible software.

PanoFolio does not create or edit:

- Panoramas
- Hotspots
- Scenes
- Tour navigation
- Transitions
- Floor-plan interactions
- Internal tour configuration

PanoFolio is responsible for:

- Project metadata
- Category assignment
- Cover image
- ZIP upload
- Package validation
- Version creation
- Preview
- Publishing
- Hosting and delivery
- Analytics
- Archiving

## 6. ZIP upload workflow

```text
Create Tour Project
        ↓
Enter project information
        ↓
Select Category
        ↓
Upload completed ZIP package
        ↓
Validate package structure and safety
        ↓
Create immutable Version
        ↓
Preview
        ↓
Publish
```

Validation may check:

- ZIP format and size
- Safe extraction paths
- Required entry files
- Referenced resources
- Unsupported or dangerous file types
- Package completeness

Validation does not modify the authored tour.

## 7. Public experience

The public hierarchy is:

```text
Portfolio Home
    ↓
Category Page
    ↓
Tour Project
    ↓
360° Tour Viewer
```

Example:

```text
Jane Cooper Studio
    ↓
Luxury Villas
    ↓
Modern Villa in Bali
    ↓
Open 360° Tour
```

A published Tour Project appears in the public Portfolio through its assigned published Category.

## 8. Creator Workspace navigation

Approved primary navigation:

1. Dashboard
2. Portfolio
3. Categories
4. Tour Projects
5. Assets
6. Analytics
7. Settings
8. Support

Portfolio is public-showcase configuration. Categories organize that showcase. Tour Projects manage individual ZIP-based tours.

## 9. UI implications

### Portfolio screen

The Portfolio screen should focus on:

- Public Portfolio overview
- Hero image
- Title and description
- About and contact information
- Category ordering
- Preview
- Publish or unpublish

It should not present a free-form page-builder canvas.

### Category screens

Category management should support:

- Category list
- Create Category
- Edit hero image, title, description, and slug
- Reorder Categories
- Assign Tour Projects
- Reorder Tour Projects within a Category
- Preview Category page
- Publish or hide Category

### Tour Project screens

Tour Project management should support:

- Project list
- Project details
- Cover image
- Category assignment
- ZIP upload
- Validation state
- Version history
- Preview
- Publish or unpublish
- Analytics

### Asset Library

Assets support Portfolio and Project presentation. Extracted Tour ZIP files remain isolated and must not appear in the Asset Library.

## 10. Version 1 exclusions

The following are not part of Version 1 unless separately approved:

- Free-form website builder
- Custom layout canvas
- Theme marketplace
- Advanced branding system
- Panorama editor
- Hotspot editor
- Scene editor
- Tour authoring
- Automatic restructuring of uploaded tours

## 11. Acceptance criteria

This model is correctly implemented when:

- A Workspace owns one Portfolio.
- The Portfolio exposes ordered public Categories.
- Each Category has a hero image, title, description, slug, and ordered Tour Projects.
- A Tour Project stores and publishes an externally authored ZIP package.
- A published Tour Project appears in its assigned public Category.
- Deleting a Category does not delete Tour Projects.
- Tour package contents remain separate from reusable Assets.
- UI terminology consistently uses Portfolio, Categories, Tour Projects, Assets, and Versions.
