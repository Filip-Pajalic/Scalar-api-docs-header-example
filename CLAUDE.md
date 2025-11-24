# Scalar Vue Project Rules

## Core Principles

1. **Native features ONLY** - Always use Scalar's native configuration, slots, and CSS variables
2. **NO CSS hacks** - Never use CSS to override Scalar's internal behavior
3. **If CSS is needed, create a wrapper component** - Encapsulate any necessary styling

---

## CSS Rules (CRITICAL)

### DO NOT use CSS for:
- Hiding/showing Scalar elements (use `hide*` config options)
- Repositioning sidebars, headers, or content areas
- Overriding Scalar's internal classes (`.scalar-*`, `.t-doc__*`)
- Using `!important` on any Scalar elements
- Media queries that change Scalar's layout behavior

### CSS is ONLY acceptable for:
- Setting CSS variables (e.g., `--scalar-custom-header-height`)
- Styling YOUR OWN wrapper components (scoped CSS)
- Basic app layout (`html`, `body`, `.app` container)

### If you need custom behavior:
1. First check if there's a native config option
2. Then check if there's a slot to inject content
3. If CSS is truly needed, create a **wrapper component** with scoped styles
4. NEVER put CSS overrides in App.vue for Scalar internals

---

## Pre-Implementation Checklist

### 1. Native Feature Check
- [ ] Does Scalar have a config option for this? (`hideSearch`, `hideDarkModeToggle`, etc.)
- [ ] Is there a slot? (`sidebar-start`, `sidebar-end`, `content-start`, `content-end`, `footer`)
- [ ] Is there a CSS variable? (`--scalar-custom-header-height`, etc.)
- [ ] Is the feature in the PUBLISHED npm version?

### 2. Source Verification
- [ ] Check the Scalar repo for how they implement similar features
- [ ] Run `grep "featureName" node_modules/@scalar/api-reference/dist/` to verify feature exists
- [ ] guides.scalar.com is made by Scalar - they use native features, not CSS hacks

### 3. Red Flags - STOP if you're doing any of these:
- Writing `!important`
- Targeting `.scalar-*` or `.t-doc__*` classes
- Using `display: none` on Scalar elements
- Writing media queries that affect Scalar layout

---

## Native Features Reference

### CSS Variables (the ONLY CSS you should set)
```css
:root {
  --scalar-custom-header-height: 49px;  /* Your header height */
}

@media (max-width: 1000px) {
  :root {
    --scalar-custom-header-height: 0px;  /* No header on mobile */
  }
}
```

### Slots
| Slot | Purpose |
|------|---------|
| `sidebar-start` | Content at top of sidebar (mobile nav items) |
| `sidebar-end` | Content at bottom of sidebar |
| `content-start` | Content before main docs |
| `content-end` | Content after main docs |
| `footer` | Page footer |

### Configuration Options
```typescript
{
  // Visibility (NATIVE way to show/hide)
  hideModels: boolean
  hideSearch: boolean
  hideClientButton: boolean
  hideDarkModeToggle: boolean
  hideDownloadButton: boolean
  hideTestRequestButton: boolean

  // Theming
  theme: 'default' | 'moon' | 'purple' | 'bluePlanet' | ...
  darkMode: boolean

  // Layout
  layout: 'modern' | 'classic'
  showSidebar: boolean
}
```

---

## Unpublished Features

These exist in the local Scalar repo but NOT in npm:
- `sidebarNavItems` config - Use `#sidebar-start` slot instead
- `SidebarNavItems.vue` - Replicated locally

---

## Correct Implementation Pattern

```vue
<template>
  <div class="app">
    <!-- Your header (hidden on mobile via its own scoped CSS) -->
    <AppHeader />

    <!-- Scalar with native slots only -->
    <ApiReference :configuration="config">
      <template #sidebar-start>
        <YourNavItems />
      </template>
    </ApiReference>
  </div>
</template>

<style>
/* ONLY these CSS rules are acceptable in App.vue */
:root {
  --scalar-custom-header-height: 49px;
}

html, body { margin: 0; padding: 0; }

@media (max-width: 1000px) {
  :root { --scalar-custom-header-height: 0px; }
}
</style>
```

---

## Wrong Implementation Pattern (DO NOT DO)

```vue
<style>
/* BAD - overriding Scalar internals */
.scalar-api-reference .t-doc__sidebar {
  top: 49px !important;
  height: calc(100vh - 49px) !important;
}

/* BAD - hiding Scalar elements with CSS */
.scalar-api-reference button[role="search"] {
  display: none !important;
}

/* BAD - media query affecting Scalar layout */
@media (max-width: 1000px) {
  .scalar-api-reference .t-doc__header {
    height: 100dvh !important;
  }
}
</style>
```

---

## Scalar Source Paths

- Main component: `/home/pajalic/git/scalar/packages/api-reference/src/components/ApiReference.vue`
- Configuration: `/home/pajalic/git/scalar/packages/types/src/api-reference/api-reference-configuration.ts`
- Mobile header: `/home/pajalic/git/scalar/packages/api-reference/src/components/MobileHeader.vue`
