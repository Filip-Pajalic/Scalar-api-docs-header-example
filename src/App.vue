<script setup lang="ts">
import { ApiReference } from '@scalar/api-reference'
import '@scalar/api-reference/style.css'
import AppHeader from './components/AppHeader.vue'
import SidebarNavItems from './components/SidebarNavItems.vue'

// Navigation items for mobile sidebar (via #sidebar-start slot)
const navItems = [
  { label: 'Checkout Docs', href: '#', active: false },
  { label: 'Checkout API', href: '#', active: true },
  { label: 'Log in', href: 'https://dashboard.scalar.com/login' },
  { label: 'Register', href: 'https://dashboard.scalar.com/register', variant: 'cta' as const },
]

// Native Scalar configuration - no CSS hacks needed
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

    <!-- Scalar ApiReference with native Vue component and slots -->
    <ApiReference :configuration="configuration">
      <!-- Native slot for mobile sidebar content -->
      <template #sidebar-start>
        <SidebarNavItems :items="navItems" />
      </template>
    </ApiReference>
  </div>
</template>

<style>
/*
 * MINIMAL CSS - Only CSS variables for layout integration
 * All other styling is handled by native Scalar features
 */

:root {
  /* Tell Scalar about our custom header height */
  --scalar-custom-header-height: 49px;
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
