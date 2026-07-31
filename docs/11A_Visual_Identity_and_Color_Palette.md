# PanoFolio — Visual Identity and Color Palette

**Document:** `11A_Visual_Identity_and_Color_Palette.md`  
**Status:** Approved foundation  
**Version:** 1.0  
**Scope:** PanoFolio Version 1

---

## 1. Purpose

This document defines the visual identity foundation for PanoFolio. It establishes the brand direction, colour system, theme tokens, accessibility rules, and visual principles that all product screens, public portfolio pages, marketing surfaces, and future mobile experiences must follow.

The visual identity is approved before individual screen designs and frontend implementation.

---

## 2. Brand Positioning

PanoFolio is a documentation-first SaaS platform for creators who publish and manage immersive 360° virtual-tour portfolios.

The product should feel:

- Premium but approachable
- Creative but operationally clear
- Immersive without becoming visually distracting
- Modern, reliable, and secure
- Suitable for photographers, real-estate professionals, hospitality creators, agencies, and independent virtual-tour specialists

PanoFolio must not look like a generic admin template. The interface should communicate visual storytelling, spatial media, and professional publishing.

---

## 3. Brand Personality

The PanoFolio personality is:

- **Immersive** — visual media and spatial experiences remain central.
- **Precise** — project status, versions, publishing, and analytics are always clear.
- **Premium** — typography, spacing, imagery, and motion feel deliberate.
- **Calm** — the interface avoids unnecessary colour, clutter, and visual noise.
- **Trustworthy** — publishing, storage, security, and ownership states are explicit.
- **Creator-focused** — media previews and portfolio presentation receive more prominence than raw administration.

---

## 4. Visual Principles

### 4.1 Media first

Tour imagery, panoramic previews, portfolio covers, and project thumbnails should be treated as primary content rather than decoration.

### 4.2 Strong hierarchy

Every screen must clearly communicate:

1. Current location
2. Primary task
3. Content state
4. Important metrics
5. Secondary actions

### 4.3 Controlled depth

Use borders, soft shadows, overlays, and raised surfaces carefully. Avoid excessive glassmorphism or strong shadows.

### 4.4 Purposeful colour

Indigo identifies the PanoFolio brand and primary actions. Cyan is an accent, not a replacement primary colour. Semantic colours are reserved for status and feedback.

### 4.5 Consistent light and dark themes

Both themes must preserve hierarchy, readability, media quality, and component meaning. Dark mode must not merely invert the light palette.

### 4.6 Accessible by default

Text, controls, focus indicators, statuses, and charts must remain understandable without relying only on colour.

---

## 5. Approved Visual Direction

The approved visual direction is **Immersive Indigo**.

### Core brand colours

| Token | Value | Primary use |
|---|---:|---|
| `brand.primary` | `#4F46E5` | Primary actions, selected navigation, active controls |
| `brand.primaryHover` | `#4338CA` | Hover state |
| `brand.primaryPressed` | `#3730A3` | Pressed state |
| `brand.secondary` | `#6366F1` | Supporting brand surfaces and graphics |
| `brand.accent` | `#22D3EE` | Highlights, visual-tour accents, selected data points |
| `brand.accentStrong` | `#06B6D4` | Accent interaction and high-contrast highlights |

### Signature gradient

```css
linear-gradient(135deg, #4F46E5 0%, #6366F1 52%, #22D3EE 100%)
```

The signature gradient may be used for:

- Marketing hero areas
- Authentication artwork
- Selected premium highlights
- Empty-state illustration backgrounds
- Branded loading moments
- Public portfolio visual accents

It must not be applied to every card, navigation item, chart, or primary button. Most product actions should use a solid primary colour for clarity.

---

## 6. Indigo Scale

| Token | Value |
|---|---:|
| `indigo.50` | `#EEF2FF` |
| `indigo.100` | `#E0E7FF` |
| `indigo.200` | `#C7D2FE` |
| `indigo.300` | `#A5B4FC` |
| `indigo.400` | `#818CF8` |
| `indigo.500` | `#6366F1` |
| `indigo.600` | `#4F46E5` |
| `indigo.700` | `#4338CA` |
| `indigo.800` | `#3730A3` |
| `indigo.900` | `#312E81` |
| `indigo.950` | `#1E1B4B` |

`indigo.600` is the default PanoFolio primary colour.

---

## 7. Cyan Accent Scale

| Token | Value |
|---|---:|
| `cyan.50` | `#ECFEFF` |
| `cyan.100` | `#CFFAFE` |
| `cyan.200` | `#A5F3FC` |
| `cyan.300` | `#67E8F9` |
| `cyan.400` | `#22D3EE` |
| `cyan.500` | `#06B6D4` |
| `cyan.600` | `#0891B2` |
| `cyan.700` | `#0E7490` |
| `cyan.800` | `#155E75` |
| `cyan.900` | `#164E63` |
| `cyan.950` | `#083344` |

Cyan should support immersive-media cues, charts, hotspots, focus details, and branded illustration. It should not compete with the indigo primary action system.

---

## 8. Neutral Scale

PanoFolio uses a cool slate neutral family.

| Token | Value |
|---|---:|
| `neutral.0` | `#FFFFFF` |
| `neutral.50` | `#F8FAFC` |
| `neutral.100` | `#F1F5F9` |
| `neutral.200` | `#E2E8F0` |
| `neutral.300` | `#CBD5E1` |
| `neutral.400` | `#94A3B8` |
| `neutral.500` | `#64748B` |
| `neutral.600` | `#475569` |
| `neutral.700` | `#334155` |
| `neutral.800` | `#1E293B` |
| `neutral.900` | `#0F172A` |
| `neutral.950` | `#020617` |

---

## 9. Semantic Colours

| Meaning | Base | Soft background | Strong text/icon |
|---|---:|---:|---:|
| Success | `#10B981` | `#ECFDF5` | `#047857` |
| Warning | `#F59E0B` | `#FFFBEB` | `#B45309` |
| Error | `#EF4444` | `#FEF2F2` | `#B91C1C` |
| Information | `#0EA5E9` | `#F0F9FF` | `#0369A1` |

Semantic colours must be paired with icons, labels, or explanatory text. Colour alone must not communicate status.

---

## 10. Light Theme Tokens

| Token | Value |
|---|---:|
| `background.canvas` | `#F8FAFC` |
| `background.subtle` | `#F1F5F9` |
| `surface.default` | `#FFFFFF` |
| `surface.raised` | `#FFFFFF` |
| `surface.sunken` | `#F1F5F9` |
| `surface.overlay` | `rgba(255, 255, 255, 0.92)` |
| `border.subtle` | `#E2E8F0` |
| `border.default` | `#CBD5E1` |
| `border.strong` | `#94A3B8` |
| `text.primary` | `#0F172A` |
| `text.secondary` | `#475569` |
| `text.muted` | `#64748B` |
| `text.disabled` | `#94A3B8` |
| `text.inverse` | `#FFFFFF` |
| `focus.ring` | `rgba(79, 70, 229, 0.35)` |
| `scrim` | `rgba(2, 6, 23, 0.56)` |

---

## 11. Dark Theme Tokens

| Token | Value |
|---|---:|
| `background.canvas` | `#0B1120` |
| `background.subtle` | `#0F172A` |
| `surface.default` | `#111827` |
| `surface.raised` | `#1E293B` |
| `surface.sunken` | `#0F172A` |
| `surface.overlay` | `rgba(17, 24, 39, 0.92)` |
| `border.subtle` | `#1E293B` |
| `border.default` | `#334155` |
| `border.strong` | `#475569` |
| `text.primary` | `#F8FAFC` |
| `text.secondary` | `#CBD5E1` |
| `text.muted` | `#94A3B8` |
| `text.disabled` | `#64748B` |
| `text.inverse` | `#0F172A` |
| `focus.ring` | `rgba(129, 140, 248, 0.45)` |
| `scrim` | `rgba(2, 6, 23, 0.76)` |

Dark mode should retain indigo as the primary interaction colour, using lighter indigo values where necessary to preserve contrast.

---

## 12. Status Mapping

| Product state | Visual treatment |
|---|---|
| Draft | Neutral badge |
| Uploading | Information badge with progress |
| Validating | Information badge with progress |
| Processing | Indigo badge with progress |
| Ready | Success badge |
| Published | Success badge plus public-visibility icon |
| Failed | Error badge with reason/action |
| Archived | Muted neutral badge |
| Suspended | Warning or error treatment depending on severity |

Status badges must use concise labels and must remain readable in both themes.

---

## 13. Chart Palette

Charts use the following ordered series:

1. `#4F46E5`
2. `#22D3EE`
3. `#8B5CF6`
4. `#0EA5E9`
5. `#14B8A6`
6. `#F59E0B`
7. `#F43F5E`

Rules:

- The primary metric uses indigo.
- Comparison or secondary series use cyan or violet.
- Success, warning, and error colours are not used decoratively when they could imply status.
- Charts require labels, legends, tooltips, and sufficient contrast.
- Do not rely only on colour; use line style, point shape, labels, or direct annotation where necessary.

---

## 14. Typography Direction

The approved typography direction is a clean modern sans-serif system suitable for SaaS interfaces and public portfolios.

Recommended primary family:

```text
Inter
```

Recommended display alternative for selected marketing headings:

```text
Manrope
```

The product application should use one primary family consistently. Marketing surfaces may use a restrained display pairing after visual validation.

Typography must prioritise readability, compact dashboard information, multilingual support, and predictable rendering.

---

## 15. Shape and Radius Direction

| Token | Value | Use |
|---|---:|---|
| `radius.xs` | `4px` | Small badges and compact controls |
| `radius.sm` | `8px` | Inputs and small buttons |
| `radius.md` | `12px` | Default cards and dialogs |
| `radius.lg` | `16px` | Media cards and major panels |
| `radius.xl` | `24px` | Marketing and immersive feature surfaces |
| `radius.full` | `9999px` | Pills, avatars, circular controls |

The product must avoid excessive rounding. Standard application cards should primarily use `12px` or `16px`.

---

## 16. Elevation Direction

Use borders before shadows in dense product interfaces.

### Light theme

```css
--shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.06);
--shadow-md: 0 8px 24px rgba(15, 23, 42, 0.08);
--shadow-lg: 0 20px 50px rgba(15, 23, 42, 0.14);
```

### Dark theme

Dark surfaces use stronger border separation and restrained shadows. Large black glows should be avoided.

---

## 17. Imagery Direction

PanoFolio imagery should feature:

- High-quality architectural and interior photography
- Panoramic and wide-angle compositions
- Hospitality, real estate, cultural spaces, venues, and destinations
- Natural light and credible professional environments
- Strong composition without oversaturated stock-photo treatment

Dashboard media should use consistent aspect ratios and responsive crops. Public portfolio and viewer screens may use edge-to-edge imagery.

---

## 18. Glass and Overlay Usage

Frosted-glass effects may appear over immersive imagery for:

- Tour viewer controls
- Image labels
- Navigation overlays
- Hotspot panels
- Fullscreen actions

Glass effects should not be the general dashboard card style. Overlays must retain readable contrast against both bright and dark imagery.

---

## 19. Motion Direction

Motion should feel calm and responsive.

| Motion | Duration |
|---|---:|
| Small hover/focus response | `120–160ms` |
| Button and control state | `160–200ms` |
| Drawer/dialog transition | `200–280ms` |
| Page-level transition | `240–320ms` |
| Branded loading loop | `1200–2400ms` |

Recommended easing:

```css
cubic-bezier(0.2, 0.8, 0.2, 1)
```

Motion must respect `prefers-reduced-motion`.

---

## 20. Accessibility Requirements

- Normal text must target WCAG AA contrast of at least `4.5:1`.
- Large text and essential non-text graphics must target at least `3:1`.
- Focus indicators must remain visible in both themes.
- Status may not be represented by colour alone.
- Interactive controls require clear hover, focus, active, disabled, and loading states.
- Touch targets should be at least `44 × 44px` on touch interfaces.
- Text embedded inside generated mockup imagery is illustrative only; implementation must use semantic HTML and accessible labels.
- Charts require textual summaries or accessible data alternatives.
- Motion must support reduced-motion preferences.

---

## 21. High-Fidelity Screen Direction

The approved design sequence is:

1. Creator Dashboard
2. Tour Projects
3. Tour Project Detail and Version History
4. ZIP Import and Processing
5. Asset Library
6. Portfolio Editor
7. Analytics
8. Public Portfolio
9. Immersive Tour Viewer
10. Mobile Dashboard
11. Mobile Tour Viewer

Each screen should be produced individually at high fidelity rather than treated only as part of a collage.

Required qualities:

- Production-level information hierarchy
- Realistic content and states
- Strong media presence
- Desktop and mobile adaptation
- Light and dark theme compatibility
- Clear status, versions, publishing, and ownership
- Component consistency
- Developer-ready annotations in the corresponding UI specification

---

## 22. Design Acceptance Criteria

The visual identity is accepted when:

- Immersive Indigo is used consistently as the primary brand direction.
- Light and dark tokens are fully defined.
- Semantic colours have clear meaning.
- Text and interaction contrast meet accessibility targets.
- Media remains visually prominent.
- Screens do not resemble an unmodified generic admin template.
- Public portfolio and tour-viewer experiences feel more immersive than management screens.
- The design can be translated into reusable tokens and components without ad-hoc values.

---

## 23. Implementation Boundary

This document defines visual identity only. It does not authorise frontend implementation.

Implementation begins only after:

1. Visual identity approval
2. UI/UX specification approval
3. Component-library approval
4. High-fidelity screen approval

The approved tokens in this document must later be integrated into `11_UI_UX_Specifications.md` and the component-library documentation.