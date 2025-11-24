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
 * Based on Scalar's official nextjs-api-reference example
 *
 * These overrides are necessary because Scalar's sidebar uses hardcoded
 * `sticky top-0 h-dvh` positioning (see MobileHeader.vue:40) and doesn't
 * natively handle external custom headers.
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
}
</style>
