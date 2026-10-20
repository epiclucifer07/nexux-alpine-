# Alpine Trip Planner & Hero Continue Action

Implement a secondary frosted-glass "Continue" action beside the primary "Explore Treks" CTA in the hero, opening an interactive alpine expedition planner modal/drawer that allows travellers to customize and preview high-altitude adventures.

### User Review & Critical Decisions

> [!IMPORTANT]
> The placement and primary interaction for the Continue button have been confirmed:
> - **Placement**: Directly beside the primary "Explore Treks" CTA within the hero section, preserving the desktop `--u` coordinate grid and mobile responsive flow.
> - **Interaction**: Opens an interactive frosted-glass trip planner drawer/modal with region selection, expedition style, duration, and live itinerary estimation.

- **Confirmed Decision 1**: Button styling will use a complementary translucent glass aesthetic (`backdrop-filter`, hairline border `rgba(255,255,255,0.36)`, white text) to establish visual hierarchy next to the solid white primary CTA.
- **Confirmed Decision 2**: Interactive trip planner will be built directly into the standalone page with zero external frameworks, smooth entrance animations, keyboard (`Esc`) accessibility, and instant state feedback.

---

## 1. Overview & Core Concept

- **What It Does**: Adds an intuitive second step ("Continue →") for users ready to start configuring their alpine journey. Clicking opens a curated, high-touch expedition planner with interactive destination cards, skill level selectors, and an instant route summary.
- **Target Audience**: Adventure travellers, alpine hikers, mountaineers, and mountain tourism enthusiasts looking to explore itineraries and reserve expedition slots.
- **Key Value**: Bridges the cinematic landing page with actionable tourism discovery without leaving the immersive panorama or reloading the page.

---

## 2. User Experience & Visual Design

### Key User Flows

1. **Discovery**: On initial page reveal, the "Continue" button rises into view alongside "Explore Treks" following the choregraphed entrance timeline.
2. **Engagement**: User clicks "Continue" (or presses Enter on focus).
3. **Planner Modal Reveal**: A refined frosted glass drawer/modal slides in with smooth easing (`cubic-bezier(0.16, 1, 0.3, 1)`), dimming the background slightly while maintaining full visibility of the alpine panorama.
4. **Interactive Configuration**:
   - **Step 1: Destination**: Choose between *Swiss Alps Traverse*, *Patagonia Icefield*, *Dolomites High Route*, or *Mont Blanc Circuit*.
   - **Step 2: Expedition Style**: *Summit Ascent*, *Glacier Traverse*, or *Refuge-to-Refuge Trek*.
   - **Step 3: Duration & Group**: Quick toggles for *3–5 Days*, *7–10 Days*, or *14 Days Epic*.
5. **Dynamic Route Card**: Instantly computes elevation gain, optimal season (e.g. *July–September*), guide-to-guest ratio (1:4), and estimated difficulty rating.
6. **Confirmation / Itinerary Action**: User can submit a trip request or download a PDF-ready summary with instant positive visual feedback.
7. **Dismissal**: Smooth close via close icon (`✕`), clicking the backdrop, or pressing the `Esc` key.

### Visual Identity & Theme

- **Continue Button**:
  - Desktop: Positioned at `left: calc(226.8 * var(--u))`, `top: calc(335.0 * var(--u))`, height `calc(40.6 * var(--u))`.
  - Appearance: Frosted translucent surface (`rgba(255, 255, 255, 0.12)`), hairline border (`1px solid rgba(255, 255, 255, 0.35)`), backdrop blur (`16px`), crisp white typography (`15.8u`), and subtle directional arrow `→`.
  - Hover: Background lightens to `rgba(255, 255, 255, 0.22)`, border brightens, smooth `translateY(-1.5u)` hover lift matching the primary CTA.
- **Trip Planner Modal / Drawer**:
  - Surface: Layered glass background (`rgba(10, 24, 38, 0.82)`) with `backdrop-filter: blur(40px) saturate(1.1)`.
  - Accents: Hairline dividers (`rgba(255, 255, 255, 0.12)`), warm golden-amber badge highlights (`#f59e0b`), Instrument Serif section headings.
  - Anti-Slop Discipline: Clean unboxed metadata, zero mechanical comments, no fake latency tickers.

---

## 3. Key Product Decisions & Trade-Offs

- **Decision 1: Side-by-side Hero CTAs vs. Replacing the existing CTA**
  - *Chosen Approach*: Place "Continue" side-by-side with "Explore Treks".
  - *Why*: Gives users both passive discovery ("Explore Treks" anchors down to destinations) and active planning ("Continue" immediately launches the customized planner).
- **Decision 2: In-Page Glass Modal vs. Multi-page Navigation**
  - *Chosen Approach*: Lightweight in-page modal/drawer with pure vanilla JavaScript and inline CSS.
  - *Why*: Honors the user's strict requirement of single-file standalone architecture with zero build steps or external dependencies.

---

## 4. Technical Architecture & Data Strategy

```
┌────────────────────────────────────────────────────────────────────────┐
│                              .viewport                                 │
│  ┌───────────────────────┐  ┌────────────────────────────────────────┐ │
│  │   .bg (Alpine Video)  │  │   .scrim (Aspect Ratio Gradients)      │ │
│  └───────────────────────┘  └────────────────────────────────────────┘ │
│  ┌───────────────────────────────────────────────────────────────────┐ │
│  │ .stage                                                            │ │
│  │   .eyebrow                                                        │ │
│  │   .headline-mask > .headline                                      │ │
│  │   .lede                                                           │ │
│  │   .cta-group                                                      │ │
│  │     ├─ a.cta ("Explore Treks")                                    │ │
│  │     └─ button.cta-secondary ("Continue →") ──[click]──────────┐   │ │
│  └───────────────────────────────────────────────────────────────│───┘ │
│                                                                  │     │
│  ┌────────────────────────────────────────────────────────────┐  │     │
│  │ .planner-modal (aria-modal="true", role="dialog", hidden)  │◄─┘     │
│  │   ├─ .modal-backdrop (click to close)                      │        │
│  │   └─ .modal-panel (frosted glass, blur(40px))              │        │
│  │        ├─ Header: "Plan Your Alpine Expedition" + Close (✕)│        │
│  │        ├─ Region Selector (Swiss Alps, Patagonia, etc.)    │        │
│  │        ├─ Style & Difficulty Selection                     │        │
│  │        ├─ Dynamic Route Estimate Card (Elevation, Season)  │        │
│  │        └─ Action: "Confirm Expedition Plan"                │        │
│  └────────────────────────────────────────────────────────────┘        │
│  ┌───────────────────────────────────────────────────────────────────┐ │
│  │ footer.footer > .finner (Columns, Legal, Socials)                 │ │
│  └───────────────────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────────────────┘
```

### Interactive Component & State Mapping

- **State Object**:
  - `selectedRegion`: `'swiss-alps'` | `'patagonia'` | `'dolomites'` | `'mont-blanc'`
  - `selectedStyle`: `'climb'` | `'glacier'` | `'refuge'`
  - `selectedDuration`: `'5-days'` | `'9-days'` | `'14-days'`
- **Computed Outputs**:
  - Max altitude: $4,810\text{ m}$ (Mont Blanc) / $3,450\text{ m}$ (Dolomites).
  - Guide Ratio: $1:2$ technical climbing, $1:4$ trekking.
  - Recommended window: *June — September* (Alps) / *December — February* (Patagonia).
- **Accessibility & Keyboard Trap**:
  - `Esc` key listener closes the modal.
  - Focus returns to the Continue button on close.
  - Full ARIA tags: `role="dialog"`, `aria-labelledby`, `aria-modal="true"`.
