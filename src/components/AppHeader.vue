<script setup lang="ts">
/**
 * Desktop header component using Scalar CSS variables.
 * Hidden on mobile - Scalar's native mobile header takes over.
 */
</script>

<template>
  <header class="header">
    <div class="header-left">
      <a href="/" class="header-logo">
        <img src="/logo.svg" alt="Logo" height="20" />
      </a>
      <nav class="header-nav">
        <a href="#" class="nav-item">Checkout Docs</a>
        <a href="#" class="nav-item active">Checkout API</a>
      </nav>
    </div>
    <div class="header-actions">
      <a href="https://dashboard.scalar.com/login" class="action-link">Log in</a>
      <a href="https://dashboard.scalar.com/register" class="action-link cta">Register</a>
    </div>
  </header>
</template>

<style scoped>
.header {
  position: sticky;
  top: 0;
  z-index: 100;
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: var(--scalar-custom-header-height);
  padding: 0 18px;
  background: var(--scalar-background-1);
  border-bottom: 1px solid var(--scalar-border-color);
  font-family: var(--scalar-font);
}

.header-left {
  display: flex;
  align-items: center;
  height: 100%;
}

.header-logo {
  display: flex;
  align-items: center;
  padding-right: 12px;
}

.header-nav {
  display: flex;
  height: 100%;
}

.nav-item {
  display: flex;
  align-items: center;
  height: 100%;
  padding: 0 12px;
  color: var(--scalar-color-2);
  text-decoration: none;
  font-size: var(--scalar-font-size-3);
  font-weight: var(--scalar-font-medium);
  position: relative;
}

.nav-item:hover {
  color: var(--scalar-color-1);
}

.nav-item.active {
  color: var(--scalar-color-1);
}

.nav-item.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 12px;
  right: 12px;
  height: 1px;
  background: var(--scalar-color-accent);
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 8px;
}

.action-link {
  padding: 6px 12px;
  color: var(--scalar-color-2);
  text-decoration: none;
  font-size: var(--scalar-font-size-3);
  font-weight: var(--scalar-font-medium);
  border-radius: var(--scalar-radius-lg);
}

.action-link:hover {
  color: var(--scalar-color-1);
}

.action-link.cta {
  background: var(--scalar-color-1);
  color: var(--scalar-background-1);
}

.action-link.cta:hover {
  opacity: 0.9;
}

/* Hide on mobile - let Scalar's native header take over */
@media (max-width: 1000px) {
  .header {
    display: none;
  }
}
</style>
