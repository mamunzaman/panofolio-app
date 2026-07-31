# 01 — Product Overview

## Product name

PanoFolio

## Product summary

PanoFolio is a desktop-first content management platform for building, managing, publishing, and sharing immersive portfolio experiences.

The primary product is the private Workspace portal. Approved users use the portal from a desktop browser to upload media, create immersive Experiences, configure a Portfolio, manage publishing, and maintain their public presence.

The public Portfolio website is the published output of Workspace content. It is not the primary management product and Version 1 is not a multi-creator marketplace.

## Product vision

Enable professionals and organizations to manage immersive content through a structured Workspace and publish a polished public Portfolio without building or maintaining a custom website.

## Primary product: Workspace portal

The Workspace portal is the operational centre of PanoFolio.

It provides:

- Dashboard
- Portfolio management
- Experience management
- Central Media library
- Categories
- Branding
- Analytics
- Settings

After login, an approved Workspace user lands on the Dashboard.

## Secondary product: Public Portfolio

The public Portfolio is generated from published Workspace content.

Visitors may:

- View the Portfolio
- Browse published Experiences
- Open panorama and immersive viewers
- View profile and contact information
- Share public links

The public Portfolio does not provide content-management controls.

## Core domain model

```text
Account
└── Workspace
    ├── Dashboard
    ├── Portfolio
    ├── Experiences
    ├── Media
    ├── Categories
    ├── Branding
    ├── Analytics
    └── Settings
```

A Workspace owns and manages the content used by its public Portfolio.

## Version 1 ownership rule

- An Account may browse public content and manage authentication details.
- An Account may request access to create and manage immersive content.
- An Administrator approves or rejects the access request.
- An approved Account receives one Workspace in Version 1.
- One Workspace owns one public Portfolio in Version 1.
- A Workspace contains Experiences, Media, Categories, Branding, and settings.

The architecture should not prevent multiple Workspaces or team members in a future version, but these capabilities are not approved for Version 1.

## Experience model

`Experience` is the primary content concept.

An Experience may represent:

- A virtual tour
- A single panorama
- A museum walkthrough
- A hotel or property tour
- A campus or venue tour
- Construction progress
- An interactive story
- A future immersive format

A Tour is one Experience type, not the entire domain model.

## Media model

Media is a central Workspace asset library rather than a collection of files owned only by one Experience.

The intended workflow is:

```text
Upload Media
↓
Create Experience
↓
Attach Media
↓
Add structure and interactions
↓
Publish
```

Media may be reused where permitted by the approved Media specification.

## Version 1 scope

Version 1 includes:

- Authentication and account management
- Access request and administrator approval
- One Workspace per approved Account
- Creator Workspace Dashboard
- One public Portfolio per Workspace
- Portfolio configuration
- Experience creation and management
- Panorama and immersive scene management
- Central Media library
- Categories
- Branding and contact information
- Publishing workflow
- Administrator governance

## Version 1 exclusions

Version 1 is not a marketplace and does not include:

- Buyer and seller transactions
- Multi-vendor commerce
- Public bidding
- Booking commissions
- Paid creator ranking
- Multiple Workspaces per Account
- Multiple public Portfolios per Workspace
- Team membership or collaboration
- Subscription billing unless separately approved

## Target users

### Public visitors

People viewing published Portfolios and immersive Experiences.

### Workspace users

Approved professionals or organizations that upload, organize, publish, and maintain immersive content.

Examples include photographers, real-estate professionals, hotels, museums, architects, venues, campuses, and construction teams.

### Administrators

Platform operators responsible for access approval, users, Workspaces, Portfolios, Media governance, platform settings, and audit records.

## Product principles

- Documentation first
- Desktop-first management workflow
- Workspace-centred architecture
- Clear role and ownership boundaries
- Central reusable Media library
- One source of truth
- Reusable components
- Accessible and responsive interfaces
- Scalable domain architecture
- No unapproved feature invention
- Consistent terminology across product, code, and documentation

## Success criteria

PanoFolio succeeds when an approved user can:

1. Log in and enter the Workspace Dashboard.
2. Configure one Workspace and public Portfolio.
3. Upload and organize reusable Media.
4. Create and manage Experiences.
5. Attach Media and configure immersive content.
6. Publish Experiences and Portfolio updates.
7. Maintain all content from a desktop browser without developer assistance.

## Future expansion

The architecture should support later additions without redesigning the core domain, including:

- Multiple Workspaces
- Organization accounts
- Team collaboration and granular permissions
- Multiple public Portfolios
- Industry-specific Portfolio templates
- Advanced analytics
- Leads and contact management
- Embeddable Experiences
- Subscription plans
- Custom domains
- White-label Experiences
- Additional Experience types

These are future considerations and are not approved Version 1 functionality unless separately documented.
