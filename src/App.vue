<script setup lang="ts">
import AppHeader from './components/AppHeader.vue'
import ScalarApiReferenceWrapper from './components/ScalarApiReferenceWrapper.vue'
import SidebarNavItems from './components/SidebarNavItems.vue'

// Navigation items for mobile sidebar (via #sidebar-start slot)
const navItems = [
  { label: 'Checkout Docs', href: '#', active: false },
  { label: 'Checkout API', href: '#', active: true },
  { label: 'Log in', href: 'https://dashboard.scalar.com/login' },
  { label: 'Register', href: 'https://dashboard.scalar.com/register', variant: 'cta' as const },
]

// Native Scalar configuration
const configuration = {
  url: 'https://registry.scalar.com/@scalar/apis/galaxy?format=yaml',

  // Native display options
  hideModels: true,
  hideDarkModeToggle: false,
  hideDownloadButton: true,
  hideClientButton: true,

  // Native sorting
  tagsSorter: 'alpha' as const,
  operationsSorter: 'alpha' as const,

  // Native search
  searchHotKey: 'k' as const,

  // Native HTTP client default
  defaultHttpClient: {
    targetKey: 'shell' as const,
    clientKey: 'curl' as const,
  },
}
</script>

<template>
  <div class="app">
    <!-- External header for desktop (hidden on mobile via CSS variable) -->
    <AppHeader />

    <!-- Wrapper component encapsulates sidebar positioning CSS for custom header -->
    <ScalarApiReferenceWrapper :configuration="configuration">
      <!-- Native slot for mobile sidebar content -->
      <template #sidebar-start>
        <SidebarNavItems :items="navItems" />
      </template>
    </ScalarApiReferenceWrapper>
  </div>
</template>

<style>
/*
 * Global CSS variables for custom header integration
 * These are read by Scalar's internal calculations
 */

:root {
  /* Scalar reads this natively for viewport calculations */
  --scalar-custom-header-height: 49px;
  --full-height: 100dvh;
  --document-height: calc(var(--full-height) - var(--scalar-custom-header-height));
}

/* Basic app layout */
html, body {
  margin: 0;
  padding: 0;
}

.app {
  min-height: 100vh;
}

/* On mobile, no custom header - Scalar handles everything natively */
@media (max-width: 1000px) {
  :root {
    --scalar-custom-header-height: 0px;
  }
}
</style>
