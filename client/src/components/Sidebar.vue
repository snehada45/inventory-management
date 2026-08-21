<template>
  <div class="sidebar-shell">
    <!-- Mobile top bar (visible < 768px via CSS) -->
    <div class="mobile-topbar">
      <button
        class="hamburger-btn"
        @click="isDrawerOpen = !isDrawerOpen"
        aria-label="Toggle navigation"
      >
        <svg width="22" height="22" viewBox="0 0 22 22" fill="none">
          <path d="M3 6H19" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/>
          <path d="M3 11H19" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/>
          <path d="M3 16H19" stroke="currentColor" stroke-width="1.8" stroke-linecap="round"/>
        </svg>
      </button>
      <div class="mobile-brand">
        <span class="mobile-brand-name">{{ t('nav.companyName') }}</span>
      </div>
      <div class="mobile-actions">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="$emit('show-profile-details')"
          @show-tasks="$emit('show-tasks')"
        />
      </div>
    </div>

    <!-- Backdrop scrim for mobile drawer -->
    <div
      v-if="isDrawerOpen"
      class="drawer-scrim"
      @click="isDrawerOpen = false"
    ></div>

    <aside class="sidebar" :class="{ 'sidebar-open': isDrawerOpen }">
      <div class="brand">
        <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
        <span class="brand-subtitle">{{ t('nav.subtitle') }}</span>
      </div>

      <nav class="sidebar-nav">
        <router-link
          to="/"
          class="nav-link"
          :class="{ active: $route.path === '/' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 8L9 2L16 8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M4 7V15C4 15.5523 4.44772 16 5 16H13C13.5523 16 14 15.5523 14 15V7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <router-link
          to="/inventory"
          class="nav-link"
          :class="{ active: $route.path === '/inventory' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 5.5L9 2L16 5.5V12.5L9 16L2 12.5V5.5Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M2 5.5L9 9L16 5.5" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M9 9V16" stroke="currentColor" stroke-width="1.5"/>
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          to="/orders"
          class="nav-link"
          :class="{ active: $route.path === '/orders' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M4 2H14V16L11 14L9 16L7 14L4 16V2Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round"/>
            <path d="M6.5 6H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M6.5 9H11.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          to="/demand"
          class="nav-link"
          :class="{ active: $route.path === '/demand' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 16L2 12L6 8L9.5 11L16 4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <path d="M12 4H16V8" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          to="/spending"
          class="nav-link"
          :class="{ active: $route.path === '/spending' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 5C2 3.89543 2.89543 3 4 3H14C15.1046 3 16 3.89543 16 5V13C16 14.1046 15.1046 15 14 15H4C2.89543 15 2 14.1046 2 13V5Z" stroke="currentColor" stroke-width="1.5"/>
            <path d="M2 7H16" stroke="currentColor" stroke-width="1.5"/>
            <path d="M11.5 11H13.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          to="/restocking"
          class="nav-link"
          :class="{ active: $route.path === '/restocking' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M2 4H4L4.5 6M4.5 6L5.5 12H14L15.5 6H4.5Z" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            <circle cx="6.5" cy="15" r="1" stroke="currentColor" stroke-width="1.5"/>
            <circle cx="13" cy="15" r="1" stroke="currentColor" stroke-width="1.5"/>
          </svg>
          <span>{{ t('nav.restocking') }}</span>
        </router-link>

        <router-link
          to="/reports"
          class="nav-link"
          :class="{ active: $route.path === '/reports' }"
          @click="isDrawerOpen = false"
        >
          <svg class="nav-icon" width="18" height="18" viewBox="0 0 18 18" fill="none">
            <path d="M3 15V9" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M8 15V4" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M13 15V11" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            <path d="M2 15H16" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
          </svg>
          <span>{{ t('nav.reports') }}</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="$emit('show-profile-details')"
          @show-tasks="$emit('show-tasks')"
        />
      </div>
    </aside>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useRoute } from 'vue-router'
import { useI18n } from '../composables/useI18n'
import LanguageSwitcher from './LanguageSwitcher.vue'
import ProfileMenu from './ProfileMenu.vue'

defineEmits(['show-profile-details', 'show-tasks'])

const { t } = useI18n()
const route = useRoute()

const isDrawerOpen = ref(false)

// Close the drawer whenever navigation occurs (covers cases where the
// click handler on the link itself doesn't fire, e.g. browser back/forward)
watch(() => route.path, () => {
  isDrawerOpen.value = false
})
</script>

<style scoped>
/* The component root has no visual box of its own — its children
   (mobile top bar, scrim, sidebar/drawer) become direct flex items of
   .app in App.vue, so each can be sized/positioned independently
   (fixed-width sidebar column on desktop, full-width top bar + off-canvas
   drawer on mobile) without an extra wrapping box distorting the flex math. */
.sidebar-shell {
  display: contents;
}

.sidebar {
  width: 260px;
  flex-shrink: 0;
  height: 100vh;
  background: var(--color-surface, #ffffff);
  border-right: 1px solid var(--color-border, #e2e8f0);
  display: flex;
  flex-direction: column;
  position: sticky;
  top: 0;
}

.brand {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  padding: 1.5rem 1.25rem;
  border-bottom: 1px solid var(--color-border, #e2e8f0);
}

.brand-name {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--color-ink, #0f172a);
  letter-spacing: -0.025em;
}

.brand-subtitle {
  font-size: 0.813rem;
  color: var(--color-muted, #64748b);
  font-weight: 400;
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: 0.125rem;
  padding: 0.75rem;
  overflow-y: auto;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.625rem 0.75rem;
  color: var(--color-muted, #64748b);
  text-decoration: none;
  font-weight: 500;
  font-size: 0.875rem;
  border-radius: var(--radius-sm, 6px);
  border-left: 3px solid transparent;
  transition: all 0.2s ease;
}

.nav-icon {
  flex-shrink: 0;
  color: inherit;
}

.nav-link:hover {
  background: var(--color-border-light, #f1f5f9);
  color: var(--color-ink, #0f172a);
}

.nav-link.active {
  background: var(--color-accent-soft, #eff6ff);
  color: var(--color-accent, #2563eb);
  border-left-color: var(--color-accent, #2563eb);
}

.sidebar-footer {
  margin-top: auto;
  padding: 1rem 0.75rem;
  border-top: 1px solid var(--color-border, #e2e8f0);
  display: flex;
  flex-direction: column;
  gap: 0.625rem;
}

.sidebar-footer :deep(.language-switcher),
.sidebar-footer :deep(.profile-menu) {
  width: 100%;
}

.sidebar-footer :deep(.language-button),
.sidebar-footer :deep(.profile-button) {
  width: 100%;
}

/* LanguageSwitcher/ProfileMenu dropdowns default to opening downward
   (top: calc(100% + 0.5rem)), which works when mounted in a top nav bar.
   Here they sit at the bottom of the sidebar, so flip them to open
   upward to avoid being clipped by the viewport. */
.sidebar-footer :deep(.dropdown-menu) {
  top: auto;
  bottom: calc(100% + 0.5rem);
}

/* Mobile top bar - hidden by default on desktop */
.mobile-topbar {
  display: none;
}

.drawer-scrim {
  display: none;
}

@media (max-width: 768px) {
  .mobile-topbar {
    display: flex;
    align-items: center;
    gap: 0.75rem;
    height: 64px;
    padding: 0 1rem;
    background: var(--color-surface, #ffffff);
    border-bottom: 1px solid var(--color-border, #e2e8f0);
    position: sticky;
    top: 0;
    z-index: 110;
  }

  .hamburger-btn {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 36px;
    height: 36px;
    background: none;
    border: 1px solid var(--color-border, #e2e8f0);
    border-radius: var(--radius-sm, 6px);
    color: var(--color-ink, #0f172a);
    cursor: pointer;
    flex-shrink: 0;
  }

  .mobile-brand {
    flex: 1;
    min-width: 0;
  }

  .mobile-brand-name {
    font-weight: 700;
    font-size: 1rem;
    color: var(--color-ink, #0f172a);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .mobile-actions {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    flex-shrink: 0;
  }

  .sidebar {
    position: fixed;
    top: 0;
    left: 0;
    height: 100vh;
    transform: translateX(-100%);
    transition: transform 0.25s ease;
    z-index: 120;
  }

  .sidebar.sidebar-open {
    transform: translateX(0);
    box-shadow: 4px 0 16px rgba(0, 0, 0, 0.12);
  }

  .drawer-scrim {
    display: block;
    position: fixed;
    inset: 0;
    background: rgba(15, 23, 42, 0.4);
    z-index: 115;
  }

  /* Bottom footer (LanguageSwitcher/ProfileMenu) already lives in the sidebar
     drawer; the mobile top bar renders its own copies so those controls stay
     reachable without opening the drawer. */
}
</style>
