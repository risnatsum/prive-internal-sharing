# Privé UI Design Principles

> Internal reference. Designers + devs. Brand + platform standards combined.

---

## Contents

1. [Core Principles](#1-core-principles)
2. [Colours](#2-colours)
3. [Typography](#3-typography)
4. [Spacing & Sizing](#4-spacing--sizing)
5. [Layout & Navigation](#5-layout--navigation)
6. [Buttons](#6-buttons)
7. [Form Elements](#7-form-elements)
8. [Cards & Surfaces](#8-cards--surfaces)
9. [Data Tables](#9-data-tables)
10. [Charts & Data Visualisation](#10-charts--data-visualisation)
11. [Dialogs & Notifications](#11-dialogs--notifications)
12. [Empty States](#12-empty-states)
13. [Iconography](#13-iconography)
14. [Advanced Frameworks](#14-advanced-frameworks)
15. [View Modes](#15-view-modes)

---

## 1. Core Principles

- Use `prive-core-ui` components — never raw HTML or overridden MUI.
- React SPA on MUI v4. Custom theme extends defaults with `colors`, `dimensions`, `stepWizard`, `progressIndicator`.
- 8px base grid. All spacing multiples of 4 or 8.
- No drop shadows. Depth = background contrast only.
- Themes, fonts, palettes load per client/tenant.

---

## 2. Colours

### Primary

| Name | Hex | Usage |
|---|---|---|
| Teal Blue | `#00557e` | Nav, headings, primary buttons |
| Green | `#60c094` | Accents, active states, checkboxes, CTA |
| Black | `#000000` | Body text, dark surfaces |

### Secondary

| Name | Hex | Usage |
|---|---|---|
| Red | `#ed212c` | Errors, destructive actions |
| Dark Slate | `#3D505D` | Muted data, secondary text |
| Amber | `#ffa921` | Caution, expense data in charts |

### Tints (over white)

| | 75% | 50% | 25% |
|---|---|---|---|
| Teal Blue | `#3f7f9e` | `#7faabf` | `#bfd4df` |
| Green | `#88d0af` | `#afdfc9` | `#d7efe4` |
| Black | `#404040` | `#808080` | `#bfbfbf` |

### Colour scale

`000`–`900` scale for `technologyBlue`, `priveGreen`, `greenGray`, `gray` — maps to code tokens.

### Rules

- Teal + Green never compete as fills in same component. One leads, one accents.
- Red = error/destructive only. Never decorative.
- Amber = expense/caution only.
- Backgrounds: `#ffffff` or `#f4f5f7` only.

### Semantic

| State | Family | Example |
|---|---|---|
| Success | Green | `#4caf50` |
| Error | Red | `#f44336` |

### Tenant overrides

HSBC: custom reds + blacks. Prudential: red primary, blue-grey secondary. Generali TH: `#c21b17`. UOB: Hero New font. Principal: Century Gothic.

---

## 3. Typography

### Proxima Nova — sole product typeface

```
Stack: 'Proxima Nova', Helvetica, sans-serif
Base: 16px
```

| Style | Weight | Size | Usage |
|---|---|---|---|
| H1 | 700 | 28–32px | Page titles, modal headings |
| H2 | 600 | 20–24px | Section headings, card titles |
| H3 | 500 | 16–18px | Sub-section labels |
| Body | 400 | 14–15px | UI text, form labels |
| Caption | 400 | 12–13px | Table data, timestamps |
| Micro | 400 | 11px | Footnotes, axis labels |

Line height: 1.5 body / 1.2 headings. Letter spacing: normal body, +0.02em uppercase labels.

### Casing

| Context | Case |
|---|---|
| Page headings | Title |
| Nav labels | Title |
| Buttons | Sentence |
| Form labels | Sentence |
| Table headers | Sentence |
| All-caps | Micro-labels + tags only, with tracking |

### Nordique Pro

Marketing only (print, banners, events). **Never in product UI.**

---

## 4. Spacing & Sizing

### Tokens

| Token | Value | Usage |
|---|---|---|
| `space-1` | 4px | Icon gap, tag padding |
| `space-2` | 8px | Component internal |
| `space-3` | 12px | Related elements |
| `space-4` | 16px | Card padding, field gap |
| `space-5` | 24px | Section + content padding |
| `space-6` | 32px | Major sections |
| `space-8` | 48px | Page vertical rhythm |

### Border radius

| Token | Value | Usage |
|---|---|---|
| `radius-sm` | 4px | Tags, badges, chips |
| `radius-md` | 6–8px | Inputs, cards, cells |
| `radius-lg` | 12px | Large cards, panels, modals |
| `radius-pill` | 100px | **All buttons — non-negotiable** |

### Structural dimensions

| Element | Value |
|---|---|
| Main menu width | 180px |
| Sidebar widget width | 280px |
| Widget header height | 72px |
| Widget container padding | 32px |
| Top bar height | ~56px |
| Nav sidebar width | ~72px |
| Right panels | 280–320px fixed |
| Content padding | 24px |

---

## 5. Layout & Navigation

### Page

```
Background: #f4f5f7
Content panels: #ffffff cards
Padding: 24px
```

### Widget container

Use `Widget` + `WidgetHeader` + `WidgetContent`. (`WidgetContainer` deprecated.)  
Controls global scrollbar (`g-scrollbar`). CSS Grid: `grid-template-rows: 1fr auto`.

### Top bar

```
Background: #ffffff
Height: ~56px
Border-bottom: 1px solid #eeeeee
Client name: 18–20px SemiBold #1a1a1a
Avatars: 32px circular, teal or grey
Action icons (right): 20px #555555
```

### Nav sidebar

```
Width: ~72px
Background: #1a2535
Icons + labels: white, stacked
Active: #60c094 accent
Icon size: 20–24px
Label: 10–11px Regular
```

### Tabs

```
Font: 14px Medium
Inactive: #555555
Active: #00557e + 2px bottom border #00557e
Bar border: 1px solid #eeeeee
Tab gap: 24–32px
```

---

## 6. Buttons

Pill shape (`border-radius: 100px`) on all buttons. Non-negotiable brand signature.

### Primary (filled)

```
Background: #00557e
Text: #ffffff
Height: 36–40px
Padding: 0 24px
Font: Medium 14px
Border-radius: 100px
```

Variants: default `#00557e` / success `#60c094` / destructive `#ed212c` / dark `#1a1a1a`.

### Secondary (outlined)

```
Background: transparent
Border: 1.5px solid [colour]
Height: 36–40px
Padding: 0 24px
Font: Medium 14px
Border-radius: 100px
```

Variants mirror primary set.

### Disabled

```
Background: #9e9e9e
Text: #ffffff
Opacity: 0.6
Cursor: not-allowed
```

### Hover

Primary: 10% darker. Secondary: 10% tint of stroke as fill.

`ActionButton` supports `loading` state with circular progress indicator.

---

## 7. Form Elements

### Architecture

Data-driven via `PriveForm` / `usePriveFormMaker` + `react-hook-form`:

1. **FormSchema** — JSON defining fields + responsive grid (`xs`, `sm` spans)
2. **UISchema** — wraps `InputBase`, `DropDown`, `Label`, `DatePicker`, `RenderPropsAutocomplete`; renders asterisks + `ErrorRenderer`
3. **ValidationSchema** — `yup` rules + localised messages

### Inputs

```
Height: 36–40px
Border: 1px solid #d0d0d0
Border-radius: 6px
Background: #ffffff
Padding: 0 12px
Font: Regular 14px #333333
Placeholder: #aaaaaa
Focus: 1.5px solid #00557e
```

### Dropdowns

Same as inputs. Chevron (▼) right-aligned. Open panel: `border 1px #d0d0d0`, `shadow 0 4px 12px rgba(0,0,0,0.08)`, `radius 6px`.

### Checkboxes

```
Size: 18×18px
Checked: #60c094 fill, white checkmark
Unchecked: 1.5px solid #d0d0d0
Border-radius: 4px
```

### Numeric spinners

Up/down inside right edge. Arrows: `#00557e` on hover.

### Layout

- Two-column grid default.
- Labels above fields: 12px Regular `#666666`.
- Related fields same row, 12px gap.
- Read-only/computed: muted background.
- Save: bottom-right. Cancel: plain text link top-right.

---

## 8. Cards & Surfaces

```
Background: #ffffff
Border: 0.5–1px solid #e0e0e0
Border-radius: 8–12px
Padding: 16–20px
Shadow: none
```

Card titles: 14–16px SemiBold `#1a1a1a`. Dividers: `1px solid #f0f0f0`.

Shaded variant: `border 1px #90a4ae`, bg `#eceff1`.

### Elevation via contrast

| Layer | Colour |
|---|---|
| Page bg | `#f4f5f7` |
| Card | `#ffffff` |
| Sidebar | `#1a2535` |
| Modal overlay | `rgba(0,0,0,0.4)` |

---

## 9. Data Tables

`ag-grid-enterprise` via `V2DataGrids`. CSS scoped to `.ag-theme-prive`.

```
Row height: 40px
Line height: 16px
Divider: 1px solid #f0f0f0
No zebra-striping
```

Headers: 12px Medium `#888888`. Cells: 13–14px Regular `#333333`. Numbers right-aligned, text left-aligned.

Custom renderers: `puiIconCell`, `puiChipCell`, `puiCurrencyCell`, `puiNumberCell`.

Hooks: `useQuickSearch`, `useExpand`, `useCustomizeColumns`. Top panels for "Manage Views" + custom pagination.

Row actions: `...` + delete for user rows. Read-only rows: `...` + ↗ only.

Generated rows (from Assets & Liabilities): link icon prefix, separate section, no delete.

Warnings: amber (`#ffa921`) triangle inline next to Type label.

---

## 10. Charts & Data Visualisation

Recharts + custom SVG. Colours map to client theme via JSON.

Background always white. Gridlines: `#f0f0f0`, 1px. No grid fill colours.

### Series colours

| Series | Colour | Hex |
|---|---|---|
| Income / positive | Teal blue | `#00557e` |
| Expenses / negative | Amber | `#ffa921` |
| Net income | Green | `#60c094` |
| Bar (general) | Green | `#60c094` |
| Donut 1 | Purple | `#7B4FA6` |
| Donut 2 | Navy | `#00557e` |
| Donut 3 | Cyan | `#3ab5b0` |
| Donut 4 | Teal | `#1d9e75` |
| Donut 5 | Yellow-green | `#9ab429` |

### Chart types

**Accumulated Net Income:** Navy line `#00557e`, 2px. Age on X-axis. Draggable range selector below.

**Net Income Progression:** Grouped bars (navy + amber) + green overlay line 1.5px. Legend below.

**Breakdown donut:** 180° arc. Centre: category + total. Legend below. `1m`/`1yr` toggle top-right.

**Bar:** Fill `#60c094`, 60–70% column width, no border.

**Progress bar:** Fill `#60c094`, track `#e0e0e0`, height 6–8px. Name left, % right above bar.

**Pie:** Green shades dark (`#0f6e56`) → light (`#afdfc9`). No segment stroke.

Date controls: `Month` + `DefinedRanges` for filtering.

---

## 11. Dialogs & Notifications

Modals (`ConfirmDialog`, `AdvancedSearchDialog`):

```
Border-radius: 8px
Action area padding: 16px 32px
Button gap: 16px
```

Badges: `isNotification` → white text on red. `isOutline` → coloured border, white bg.

Snackbars: transient success/error messages.

---

## 12. Empty States

```
Illustration: centred, ~120px, greyscale SVG
Text: "This client has not added any [item]." — 16px Regular #555555
CTA: single primary pill button #00557e
Alignment: vertically centred in panel
```

No secondary actions. No extra copy. Minimal + actionable.

---

## 13. Iconography

Product UI:

```
Style: outline, 1.5–2px stroke, no fill
Colour: white (sidebar) / #555555 (light) / #00557e (active)
Size: 20–24px nav, 16–18px inline
```

Favicon (the colon `∶`):

```
Background: #00557e, border-radius ~22%
Top dot: #60c094, ~28% icon width
Bottom dot: #60c094, ~28%, ~60% vertical
```

---

## 14. Advanced Frameworks

### StepWizard

Guided multi-step journeys (onboarding, risk profiling).

- State: `privateWidgetContext` (step) + `sharedWidgetContext` (global)
- Hooks: `onBeforeNextStep`, `onBeforePreviousStep` via `useWizardContext` / `useWizardEvent`
- Block nav: `ev.preventDefault()` on validation fail
- Session modes: `transient` (client-side) / `session` (browser) / `persistent` (API-synced)

### DataGrids

See [Data Tables](#9-data-tables).

### FormMaker

See [Form Elements](#7-form-elements).

---

## 15. View Modes

| Mode | Description |
|---|---|
| `PRIVE` | New design, all updated features |
| `FUSION` | Container view without prive-system |
| `MATERIAL` / `DEFAULT` | Standard Material Design |
| `BASIC` | Old classic menu design |

---

*Source: Platform Design Notebook (prive-core-ui) + Privé Brand Guidelines (Feb 2018, updated Apr 2026).*
