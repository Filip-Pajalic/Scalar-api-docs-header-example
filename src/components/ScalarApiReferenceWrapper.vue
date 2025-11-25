<script setup lang="ts">
import { ApiReference } from '@scalar/api-reference'
import '@scalar/api-reference/style.css'
import type { ReferenceConfiguration } from '@scalar/types/api-reference'

defineProps<{
  configuration: ReferenceConfiguration
}>()

defineSlots<{
  'sidebar-start'?(): unknown
  'sidebar-end'?(): unknown
  'content-start'?(): unknown
  'content-end'?(): unknown
  footer?(): unknown
}>()
</script>

<template>
  <div class="scalar-api-reference-wrapper">
    <ApiReference :configuration="configuration">
      <template #sidebar-start>
        <slot name="sidebar-start" />
      </template>
      <template #sidebar-end>
        <slot name="sidebar-end" />
      </template>
      <template #content-start>
        <slot name="content-start" />
      </template>
      <template #content-end>
        <slot name="content-end" />
      </template>
      <template #footer>
        <slot name="footer" />
      </template>
    </ApiReference>
  </div>
</template>

<style scoped>
/*
 * Wrapper component for Scalar API Reference with custom header integration
 *
 * These overrides are necessary because:
 * 1. Scalar's sidebar uses hardcoded `sticky top-0 h-dvh` positioning (MobileHeader.vue:40)
 * 2. Sidebar doesn't natively account for external headers
 */

.scalar-api-reference-wrapper :deep(.scalar-api-reference .t-doc__sidebar),
.scalar-api-reference-wrapper :deep(.scalar-api-reference .layout-aside-left .layout-aside-content) {
  top: var(--scalar-custom-header-height) !important;
  position: sticky !important;
  height: var(--document-height) !important;
  max-height: var(--document-height) !important;
}

/* Mobile: Reset to Scalar's defaults */
@media (max-width: 1000px) {
  .scalar-api-reference-wrapper :deep(.scalar-api-reference .t-doc__sidebar) {
    top: 0 !important;
    height: auto !important;
    max-height: none !important;
  }

  /*
   * Mobile Header Actions (guides.scalar.com behavior):
   * - Hide search bar in sidebar (we have search icon in mobile header)
   * - Hide dark mode toggle when burger menu is expanded
   */

  /* Hide search button in sidebar on mobile - only use search icon in header */
  .scalar-api-reference-wrapper :deep(.scalar-api-reference button[role="search"]),
  .scalar-api-reference-wrapper :deep(.scalar-api-reference .bg-sidebar-b-search) {
    display: none !important;
  }

  /* Hide dark mode toggle when burger menu is expanded (matches guides.scalar.com) */
  .scalar-api-reference-wrapper :deep(.scalar-api-reference.references-sidebar-mobile-open .mobile-header-darkmode-toggle) {
    display: none;
  }
}
</style>
