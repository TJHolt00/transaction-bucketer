# Design System Master File — Transaction Bucketer

> **LOGIC:** When building a specific page, first check `design-system/transaction-bucketer/pages/[page-name].md`.
> If that file exists, its rules **override** this Master file.
> If not, strictly follow the rules below.

> **SOURCE OF TRUTH:** This file documents the design system **as actually implemented** in
> `index.html` (the `:root` and `body.dark-mode` token blocks). It was authored by reading the
> real CSS — it is NOT a generated recommendation.
>
> **Do not replace this system with generated suggestions.** Tools that propose a palette from a
> product-type query will suggest something different (e.g. dark-OLED + trust-blue). That is wrong
> for this app. Use those tools to *audit against* this system, not to redesign it.
>
> If a token changes in `index.html`, update this file in the same commit.

---

**Project:** Transaction Bucketer
**Documented:** 2026-07-18
**Category:** Personal finance / transaction categorization dashboard
**Stack:** Single-file vanilla HTML/CSS/JS (no build step) · Chart.js 4.4.1 · Supabase JS v2 · Phosphor Icons 2.1.1
**Theme model:** Light "FRESH" theme is the **default** (`:root`). Dark theme opt-in via `body.dark-mode`.

---

## Global Rules

### Color Palette — FRESH (light, default)

| Role | Hex | CSS Variable |
|------|-----|--------------|
| Background | `#F4F5FB` | `--bg` |
| Surface | `#FFFFFF` | `--surface` |
| Surface alt | `#F4F4FA` | `--surface2` |
| Border | `#ECEDF5` | `--border` |
| Text | `#1A1B2E` | `--text` |
| Text muted | `#8A8DA6` | `--text2` |
| Text secondary | `#55586F` | `--text3` |
| **Accent (brand)** | `#5B4CE0` | `--accent` |
| Accent soft | `#EEECFC` | `--accent-soft` |
| Accent border | `#E0DCFA` | `--accent-border` |
| Positive | `#12B76A` | `--pos` |
| Negative | `#E5484D` | `--neg` |
| Warning | `#E0912B` | `--warn` |
| Warning soft | `#FFF7EA` | `--warn-soft` |
| Warning border | `#F6E2BE` | `--warn-border` |
| Warning text | `#8A5A12` | `--warn-text` |
| Card shadow | `0 10px 26px -20px rgba(26,27,46,.30)` | `--shadow-card` |

### Color Palette — Dark (`body.dark-mode`)

| Role | Hex | CSS Variable |
|------|-----|--------------|
| Background | `#0f0f14` | `--bg` |
| Surface | `#1a1a24` | `--surface` |
| Surface alt | `#22222f` | `--surface2` |
| Border | `#2e2e3e` | `--border` |
| Text | `#e8e8f0` | `--text` |
| Text muted | `#888899` | `--text2` |
| Text secondary | `#a6a6bd` | `--text3` |
| **Accent (brand)** | `#66c2fa` | `--accent` |
| Accent soft | `#16273a` | `--accent-soft` |
| Accent border | `#2a4a63` | `--accent-border` |
| Positive | `#34d399` | `--pos` |
| Negative | `#f87171` | `--neg` |
| Warning | `#fbbf24` | `--warn` |
| Warning soft | `#2a2410` | `--warn-soft` |
| Warning border | `#5a4a1e` | `--warn-border` |
| Warning text | `#fbbf24` | `--warn-text` |
| Card shadow | `0 10px 26px -18px rgba(0,0,0,.55)` | `--shadow-card` |

**Color Notes:** The accent shifts hue between themes by design — violet `#5B4CE0` in light,
sky blue `#66c2fa` in dark. Always reference `var(--accent)`, never a literal, so both themes work.

> **Rule:** Components must use semantic tokens (`var(--accent)`, `var(--surface)`, …).
> Raw hex in a component rule is a bug — it will break one of the two themes.
> Historical note: an earlier dark-only design left hardcoded hex in the base layer; those are
> neutralized by a later override block (~lines 289–310). Don't reintroduce the pattern.

### Typography

- **Display/Heading Font:** `Bricolage Grotesque` (500–800) → `--font-display`
- **Body Font:** `Plus Jakarta Sans` (400–700), fallback `Inter`, `system-ui` → `--font`
- **Mono Font:** `IBM Plex Mono` (400–600) → `--font-mono` (raw transaction descriptors, numeric detail)
- **Base size:** 14px body, line-height 1.5
- **Display treatment:** `font-weight:700–800; letter-spacing:-.02em` (used on metric values, brand word, donut center)

**CSS Import (already in `<head>`):**
```css
@import url('https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:wght@500;600;700;800&family=Plus+Jakarta+Sans:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap');
```

### Radius Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--radius` | `18px` | Cards, panels, upload zone, modals |
| `--radius-ctl` | `12px` | Controls: buttons, inputs, selects, txn rows |
| — | `8px` | Small inline elements (samples, note fields, cat buttons) |
| — | `10–11px` | Brand tiles, nav items, avatars |

### Shadow

Single semantic token rather than a scale: `--shadow-card`, applied to `.metric`,
`.review-card`, `.txn-card`, `.modal-box`, `.sync-bar`, `.rule-row`.
Accent glow for active nav/FAB: `0 8px 18px -8px var(--accent)`.

### Motion

- **Standard transition:** `.15s` (controls, hovers); `.2s` (toasts); `.3s` (progress fill)
- **Hover convention:** `filter:brightness(1.07)` on accent-filled elements — never a hue change
- **Reduced motion:** respected globally via `@media (prefers-reduced-motion:reduce)`

### Responsive Breakpoints

| Breakpoint | Behavior |
|-----------|----------|
| `max-width:700px` | `.dash-layout` collapses from `230px 1fr` sidebar grid to single column |
| `max-width:760px` | Mobile header + bottom tab bar; toast lifts above tab bar |
| `print` | Print stylesheet present |

---

## Component Specs (as implemented)

### Buttons

```css
/* Primary — accent filled */
.btn-primary {
  background: var(--accent);
  color: #fff;
  font-weight: 700;
  padding: 8px 18px;
  border-radius: var(--radius-ctl);
  border: none;
  cursor: pointer;
  transition: all .15s;
}
.btn-primary:hover { background: var(--accent); filter: brightness(1.07); }

/* Secondary — surface + border */
.btn-secondary {
  background: var(--surface);
  border: 1px solid var(--border);
  color: var(--text3);
  border-radius: var(--radius-ctl);
}
.btn-secondary:hover { border-color: var(--text2); filter: none; }
```

### Cards

```css
.card {              /* .metric / .review-card / .txn-card / .modal-box */
  background: var(--surface);
  border-radius: var(--radius);       /* 18px */
  padding: 24px;
  border: none;
  box-shadow: var(--shadow-card);
}
```

### Inputs

```css
.input {
  padding: 8px 10px;
  border: 1px solid var(--border);
  border-radius: var(--radius-ctl);
  background: var(--surface2);
  color: var(--text);
  font-size: 13px;
  outline: none;
}
.input:focus { border-color: var(--accent); }
```

### Focus (accessibility — required)

```css
/* Keyboard-only focus ring. Must remain; overrides the scattered outline:none. */
button:focus-visible, a:focus-visible, input:focus-visible, select:focus-visible,
textarea:focus-visible, [tabindex]:focus-visible, .tab:focus-visible,
.cat-btn:focus-visible, .nav-item:focus-visible, .upload-zone:focus-visible,
.txn-check:focus-visible {
  outline: 2px solid var(--accent) !important;
  outline-offset: 2px;
}
```

### Navigation

```css
.nav-item { padding:10px 12px; border-radius:11px; color:var(--text3); font-size:13px; font-weight:500; gap:11px; }
.nav-item:hover  { background: var(--surface2); color: var(--text); }
.nav-item.active { background: var(--accent); color:#fff; font-weight:700;
                   box-shadow: 0 8px 18px -8px var(--accent); }
```

---

## Chart & Data Visualization

**Library:** Chart.js 4.4.1. Primary viz is a category donut with a `.donut-center` overlay
(`--font-display`, 20px, weight 700).

**Category color palette (23 buckets)** — defined in the `catMap` array in `index.html`.
These are the canonical per-category colors; reuse them anywhere a category is represented
(dots, bars, donut slices, rule tags) so color meaning stays consistent across the app:

| Category | Hex | | Category | Hex |
|---|---|---|---|---|
| Home | `#ec4899` | | Entertainment | `#22d3ee` |
| Food — Groceries | `#1d9e75` | | Personal Care | `#e879f9` |
| Food — Fast Food | `#f97316` | | Subscriptions | `#34d399` |
| Food — Restaurant | `#fb923c` | | Pets | `#fb7185` |
| Food — Food at Work | `#c47b2b` | | Business | `#10b981` |
| Auto & Insurance | `#60a5fa` | | Taxes | `#64748b` |
| Ram Restoration | `#7c3aed` | | Cash | `#84cc16` |
| Kids | `#f472b6` | | Fees | `#dc2626` |
| TJ Soccer | `#38bdf8` | | Transfers | `#94a3b8` |
| Shopping | `#fbbf24` | | Income | `#06b6d4` |
| Utilities | `#a78bfa` | | Other | `#6b7280` |
| Debt Payments | `#ef4444` | | | |

**Budget rail colors:** Needs `#378add` · Wants `#fbbf24` · Savings/debt paydown `#34d399`

**Chart rules:**
- Never rely on color alone — always pair a slice/bar with its label or a legend row.
- The category list beside the donut serves as the legend; keep dot color in sync with slice color.
- ⚠️ *Unaudited:* this 23-color set has not been checked for adjacent-slice contrast or
  color-blind safety. Audit before adding more categories.

---

## Anti-Patterns (Do NOT Use)

- ❌ **Raw hex in component rules** — use semantic tokens; literals break one theme
- ❌ **Hue-shifting hovers** — hover is `filter:brightness(1.07)`, not a different color
- ❌ **Emojis as icons** — this app uses Phosphor Icons (`<i class="ph …">`); keep it consistent
- ❌ **Removing the focus ring** — `outline:none` without a `:focus-visible` replacement
- ❌ **Ignoring `prefers-reduced-motion`**
- ❌ **Assuming dark mode** — light FRESH is the default; verify both themes on every change
- ❌ **Color-only meaning in charts**

---

## Known Gaps (open work, not yet fixed)

- [ ] Div-based clickable elements (`.nav-item`, `.txn-row`, `.cat-row`) have `onclick` but no
      `tabindex`/`role`/key handler — they are **not keyboard reachable**. The focus-ring CSS
      already covers them once they become focusable.
- [ ] The 23 category chart colors are unaudited for contrast / color-blind safety.
- [ ] No skip-to-main-content link (nav-heavy layout).
- [ ] Dead hardcoded dark hex remains in the base CSS layer (harmless — overridden — but noise).

---

## Pre-Delivery Checklist

Before shipping any UI change, verify:

- [ ] Both themes checked — light FRESH (default) **and** `body.dark-mode`
- [ ] Only semantic tokens used; no new raw hex in component rules
- [ ] Icons from Phosphor set; no emoji as icons
- [ ] `cursor:pointer` on all clickable elements
- [ ] Transitions 150–300ms; hover uses brightness, not hue change
- [ ] Text contrast ≥ 4.5:1 in both themes
- [ ] Keyboard: tab reaches the control and the focus ring is visible
- [ ] `prefers-reduced-motion` still respected
- [ ] Responsive checked at 375px, 700px, 760px, 1024px, 1440px
- [ ] No horizontal scroll on mobile
