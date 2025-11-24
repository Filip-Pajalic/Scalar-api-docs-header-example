<script setup lang="ts">
/**
 * Renders custom navigation items at the top of the mobile sidebar.
 * Only visible on mobile (when burger menu is open).
 */
import { ref, onMounted, onUnmounted } from 'vue'

defineProps<{
  items: Array<{
    label: string
    href: string
    active?: boolean
    variant?: 'default' | 'cta'
  }>
}>()

// Simple mobile detection (matches Scalar's lg breakpoint at 1000px)
const isMobile = ref(false)

const checkMobile = () => {
  isMobile.value = window.innerWidth <= 1000
}

onMounted(() => {
  checkMobile()
  window.addEventListener('resize', checkMobile)
})

onUnmounted(() => {
  window.removeEventListener('resize', checkMobile)
})
</script>

<template>
  <!-- Only render on mobile -->
  <div v-if="items?.length && isMobile" class="sidebar-nav-items">
    <a
      v-for="item in items"
      :key="item.href"
      :class="[
        'sidebar-nav-item',
        { active: item.active },
        item.variant === 'cta' ? 'sidebar-nav-cta' : '',
      ]"
      :href="item.href">
      {{ item.label }}
    </a>
  </div>
</template>

<style scoped>
.sidebar-nav-items {
  display: flex;
  flex-direction: column;
  padding: 12px;
  border-bottom: 1px solid var(--scalar-border-color);
  gap: 4px;
}

.sidebar-nav-item {
  display: block;
  padding: 8px 12px;
  color: var(--scalar-color-2);
  text-decoration: none;
  font-size: var(--scalar-font-size-3, 14px);
  font-weight: 600;
  border-left: 2px solid transparent;
  margin-left: -2px;
  border-radius: var(--scalar-radius, 4px);
}

.sidebar-nav-item:hover {
  color: var(--scalar-color-1);
  background: var(--scalar-background-2);
}

.sidebar-nav-item.active {
  color: var(--scalar-color-1);
  border-left-color: var(--scalar-color-accent, #006965);
}

.sidebar-nav-cta {
  display: block;
  padding: 8px 12px;
  margin-top: 4px;
  background: var(--scalar-color-1);
  color: var(--scalar-background-1);
  text-decoration: none;
  font-size: var(--scalar-font-size-3, 14px);
  font-weight: 600;
  border-radius: var(--scalar-radius-lg, 6px);
  text-align: center;
  border-left: none;
  margin-left: 0;
}

.sidebar-nav-cta:hover {
  opacity: 0.9;
  color: var(--scalar-background-1);
  background: var(--scalar-color-1);
}
</style>
