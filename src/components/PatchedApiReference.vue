<script setup lang="ts">
/**
 * Patched ApiReference Component
 *
 * Wraps @scalar/api-reference and injects mobile header actions
 * (search icon + dark mode toggle) using Vue Teleport after the
 * component renders.
 *
 * This is the cleanest approach without rebuilding Scalar from source.
 */
import { ApiReference } from '@scalar/api-reference'
import '@scalar/api-reference/style.css'
import type { ReferenceConfiguration } from '@scalar/types/api-reference'
import { onMounted, onUnmounted, ref } from 'vue'

const props = defineProps<{
  configuration: ReferenceConfiguration
}>()

defineSlots<{
  'sidebar-start'?(): unknown
  'sidebar-end'?(): unknown
  'content-start'?(): unknown
  'content-end'?(): unknown
  footer?(): unknown
}>()

// State for mobile header actions injection
const mobileHeaderReady = ref(false)
const isDark = ref(false)

// Wait for Scalar to render, then prepare for teleport
onMounted(() => {
  const checkMobileHeader = () => {
    // MobileHeader renders: <div class="flex h-6 items-center gap-1 pl-1"><slot name="actions" /></div>
    const target = document.querySelector('.t-doc__header header .flex.h-6.items-center.gap-1.pl-1')

    if (target) {
      mobileHeaderReady.value = true
      isDark.value = document.documentElement.classList.contains('dark-mode')

      // Listen for dark mode changes
      const observer = new MutationObserver(() => {
        isDark.value = document.documentElement.classList.contains('dark-mode')
      })
      observer.observe(document.documentElement, {
        attributes: true,
        attributeFilter: ['class']
      })

      // Store observer for cleanup
      ;(window as any).__darkModeObserver = observer
    } else {
      // Retry
      setTimeout(checkMobileHeader, 50)
    }
  }

  checkMobileHeader()
})

onUnmounted(() => {
  // Cleanup observer
  const observer = (window as any).__darkModeObserver
  if (observer) {
    observer.disconnect()
    delete (window as any).__darkModeObserver
  }
})

// Handle search click by simulating the keyboard shortcut
const handleSearchClick = () => {
  // Scalar's search listens for Cmd/Ctrl+K by default
  // Simulate the keyboard event to trigger the search modal
  const event = new KeyboardEvent('keydown', {
    key: props.configuration.searchHotKey || 'k',
    metaKey: true, // For Mac
    ctrlKey: true, // For Windows/Linux
    bubbles: true,
    cancelable: true
  })
  window.dispatchEvent(event)
}

// Handle dark mode toggle by manipulating document classes
const handleDarkModeToggle = () => {
  const htmlEl = document.documentElement
  const currentMode = htmlEl.classList.contains('dark-mode') ? 'dark' : 'light'
  const newMode = currentMode === 'dark' ? 'light' : 'dark'

  // Toggle classes (Scalar uses 'dark-mode' class)
  if (newMode === 'dark') {
    htmlEl.classList.add('dark-mode')
    htmlEl.classList.remove('light-mode')
  } else {
    htmlEl.classList.add('light-mode')
    htmlEl.classList.remove('dark-mode')
  }

  // Update localStorage to persist the preference
  localStorage.setItem('scalar-color-mode', newMode)

  // Update our ref for the icon
  isDark.value = newMode === 'dark'
}
</script>

<template>
  <div class="patched-api-reference">
    <!-- Teleport mobile header actions into Scalar's MobileHeader -->
    <Teleport
      v-if="mobileHeaderReady"
      to=".t-doc__header header .flex.h-6.items-center.gap-1.pl-1">
      <!-- Search Icon -->
      <button
        type="button"
        class="mobile-header-action-button"
        aria-label="Search"
        @click="handleSearchClick">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256" fill="currentColor" width="16" height="16">
          <path d="M229.66,218.34l-50.07-50.06a88.11,88.11,0,1,0-11.31,11.31l50.06,50.07a8,8,0,0,0,11.32-11.32ZM40,112a72,72,0,1,1,72,72A72.08,72.08,0,0,1,40,112Z" />
        </svg>
      </button>

      <!-- Dark Mode Toggle -->
      <button
        type="button"
        class="mobile-header-action-button mobile-header-darkmode-toggle"
        :aria-label="isDark ? 'Set light mode' : 'Set dark mode'"
        @click="handleDarkModeToggle">
        <svg v-if="isDark" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256" fill="currentColor" width="16" height="16">
          <!-- Moon -->
          <path d="M235.54,150.21a104.84,104.84,0,0,1-37,52.91A104,104,0,0,1,32,120,103.09,103.09,0,0,1,52.88,57.48a104.84,104.84,0,0,1,52.91-37,8,8,0,0,1,10,10,88.08,88.08,0,0,0,109.8,109.8,8,8,0,0,1,10,10Z" />
        </svg>
        <svg v-else xmlns="http://www.w3.org/2000/svg" viewBox="0 0 256 256" fill="currentColor" width="16" height="16">
          <!-- Sun -->
          <path d="M120,40V16a8,8,0,0,1,16,0V40a8,8,0,0,1-16,0Zm72,88a64,64,0,1,1-64-64A64.07,64.07,0,0,1,192,128Zm-16,0a48,48,0,1,0-48,48A48.05,48.05,0,0,0,176,128ZM58.34,69.66A8,8,0,0,0,69.66,58.34l-16-16A8,8,0,0,0,42.34,53.66Zm0,116.68-16,16a8,8,0,0,0,11.32,11.32l16-16a8,8,0,0,0-11.32-11.32ZM192,72a8,8,0,0,0,5.66-2.34l16-16a8,8,0,0,0-11.32-11.32l-16,16A8,8,0,0,0,192,72Zm5.66,114.34a8,8,0,0,0-11.32,11.32l16,16a8,8,0,0,0,11.32-11.32ZM48,128a8,8,0,0,0-8-8H16a8,8,0,0,0,0,16H40A8,8,0,0,0,48,128Zm80,80a8,8,0,0,0-8,8v24a8,8,0,0,0,16,0V216A8,8,0,0,0,128,208Zm112-88H216a8,8,0,0,0,0,16h24a8,8,0,0,0,0-16Z" />
        </svg>
      </button>
    </Teleport>

    <!-- Render Scalar ApiReference -->
    <ApiReference
      :configuration="configuration"
      v-bind="$attrs">
      <!-- Pass through all slots -->
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
/* Mobile header action buttons styling */
.mobile-header-action-button {
  appearance: none;
  background: transparent;
  border: none;
  border-radius: var(--scalar-radius, 6px);
  color: var(--scalar-color-3, #8e8e8e);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 32px;
  width: 32px;
  padding: 8px;
  transition: background-color 0.15s ease, color 0.15s ease;
}

.mobile-header-action-button:hover {
  background: var(--scalar-background-2, #f6f6f6);
  color: var(--scalar-color-1, #1b1b1b);
}

.mobile-header-action-button:active {
  color: var(--scalar-color-1, #1b1b1b);
}

/* Hide on desktop */
@media (min-width: 1001px) {
  .mobile-header-action-button {
    display: none;
  }
}

/* Sidebar positioning for custom header integration */
.patched-api-reference :deep(.scalar-api-reference .t-doc__sidebar),
.patched-api-reference :deep(.scalar-api-reference .layout-aside-left .layout-aside-content) {
  top: var(--scalar-custom-header-height) !important;
  position: sticky !important;
  height: var(--document-height) !important;
  max-height: var(--document-height) !important;
}

/* Mobile: Reset to Scalar's defaults */
@media (max-width: 1000px) {
  .patched-api-reference :deep(.scalar-api-reference .t-doc__sidebar) {
    top: 0 !important;
    height: auto !important;
    max-height: none !important;
  }

  /* Hide search in sidebar on mobile */
  .patched-api-reference :deep(.scalar-api-reference button[role="search"]),
  .patched-api-reference :deep(.scalar-api-reference .bg-sidebar-b-search) {
    display: none !important;
  }

  /* Hide dark mode toggle when burger is expanded */
  .patched-api-reference :deep(.scalar-api-reference.references-sidebar-mobile-open .mobile-header-darkmode-toggle) {
    display: none;
  }
}
</style>
