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

    <aside class="sidebar" :class="{ 'sidebar-open': isDrawerOpen, 'sidebar-collapsed': showCollapsed }">
      <div class="brand">
        <template v-if="!showCollapsed">
          <h1 class="brand-name">{{ t('nav.companyName') }}</h1>
          <span class="brand-subtitle">{{ t('nav.subtitle') }}</span>
        </template>
        <div v-else class="brand-monogram" :title="t('nav.companyName')">
          {{ t('nav.companyName').charAt(0) }}
        </div>
        <button
          class="collapse-toggle"
          type="button"
          @click="toggleCollapsed"
          :aria-label="collapsed ? 'Expand sidebar' : 'Collapse sidebar'"
        >
          <svg
            class="collapse-icon"
            :class="{ 'collapse-icon-flipped': collapsed }"
            width="14"
            height="14"
            viewBox="0 0 14 14"
            fill="none"
          >
            <path d="M9 3L4.5 7L9 11" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/>
          </svg>
        </button>
      </div>

      <nav class="sidebar-nav">
        <router-link
          to="/"
          class="nav-link"
          :class="{ active: $route.path === '/' }"
          :title="t('nav.overview')"
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
          :title="t('nav.inventory')"
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
          :title="t('nav.orders')"
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
          :title="t('nav.demandForecast')"
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
          :title="t('nav.finance')"
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
          :title="t('nav.restocking')"
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
          :title="t('nav.reports')"
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
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'
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

const SIDEBAR_COLLAPSED_KEY = 'sidebar-collapsed'

const collapsed = ref(false)
// Once the user explicitly clicks the toggle, their choice is persisted and
// we stop auto-deriving the collapsed state from viewport width on resize.
const hasExplicitOverride = ref(false)
// Tracks whether we're currently below the 768px mobile-drawer breakpoint,
// so the icon-only collapsed appearance (a >=768px concept only) never
// leaks into the mobile off-canvas drawer's template output.
const isMobileViewport = ref(typeof window !== 'undefined' && window.innerWidth < 768)

// The collapsed appearance only ever applies at >=768px; below that the
// mobile drawer always renders full labels regardless of `collapsed`.
const showCollapsed = computed(() => collapsed.value && !isMobileViewport.value)

// Tablet range (768-1023px) defaults to collapsed; >=1024px defaults expanded.
// Below 768px the mobile drawer takes over, so this value is irrelevant there.
const getWidthDefault = () => {
  const width = window.innerWidth
  return width >= 768 && width < 1024
}

const handleResize = () => {
  isMobileViewport.value = window.innerWidth < 768
  // Only auto-adjust while the user hasn't made an explicit choice, and
  // only within the >=768px range (below that, the mobile drawer applies).
  if (hasExplicitOverride.value) return
  if (window.innerWidth < 768) return
  collapsed.value = getWidthDefault()
}

const toggleCollapsed = () => {
  collapsed.value = !collapsed.value
  hasExplicitOverride.value = true
  try {
    localStorage.setItem(SIDEBAR_COLLAPSED_KEY, String(collapsed.value))
  } catch (err) {
    // localStorage can throw in some browser contexts (e.g. private mode
    // with storage disabled); the in-memory state still updates correctly.
    console.error('Failed to persist sidebar-collapsed preference:', err)
  }
}

onMounted(() => {
  let stored = null
  try {
    stored = localStorage.getItem(SIDEBAR_COLLAPSED_KEY)
  } catch (err) {
    stored = null
  }

  if (stored !== null) {
    collapsed.value = stored === 'true'
    hasExplicitOverride.value = true
  } else {
    collapsed.value = getWidthDefault()
  }

  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
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
  transition: width 0.2s ease;
}

.sidebar.sidebar-collapsed {
  width: 72px;
}

.brand {
  position: relative;
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
  padding: 1.5rem 1.25rem;
  border-bottom: 1px solid var(--color-border, #e2e8f0);
}

.sidebar-collapsed .brand {
  padding: 1.5rem 0.75rem;
  align-items: center;
}

.brand-monogram {
  width: 36px;
  height: 36px;
  border-radius: var(--radius-sm, 6px);
  background: var(--color-accent-soft, #eff6ff);
  color: var(--color-accent, #2563eb);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 1rem;
  text-transform: uppercase;
}

.collapse-toggle {
  position: absolute;
  top: 0.5rem;
  right: 0.5rem;
  width: 22px;
  height: 22px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: var(--color-surface, #ffffff);
  border: 1px solid var(--color-border, #e2e8f0);
  border-radius: var(--radius-sm, 6px);
  color: var(--color-muted, #64748b);
  cursor: pointer;
  padding: 0;
  flex-shrink: 0;
}

.collapse-toggle:hover {
  background: var(--color-border-light, #f1f5f9);
  color: var(--color-ink, #0f172a);
}

.sidebar-collapsed .collapse-toggle {
  position: static;
  margin-top: 0.5rem;
}

.collapse-icon {
  transition: transform 0.2s ease;
}

.collapse-icon-flipped {
  transform: rotate(180deg);
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

.sidebar-collapsed .sidebar-nav {
  padding: 0.75rem 0.5rem;
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

.sidebar-collapsed .nav-link {
  justify-content: center;
  gap: 0;
  padding: 0.625rem;
}

.sidebar-collapsed .nav-link span {
  display: none;
}

.sidebar-footer {
  margin-top: auto;
  padding: 1rem 0.75rem;
  border-top: 1px solid var(--color-border, #e2e8f0);
  display: flex;
  flex-direction: column;
  gap: 0.625rem;
}

.sidebar-collapsed .sidebar-footer {
  padding: 1rem 0.5rem;
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

/* Collapsed footer: shrink the LanguageSwitcher/ProfileMenu buttons to
   square icon-only controls by hiding their label/chevron text (owned by
   the child components) via :deep() rather than editing those files. */
.sidebar-collapsed .sidebar-footer :deep(.language-button),
.sidebar-collapsed .sidebar-footer :deep(.profile-button) {
  justify-content: center;
  padding: 0.5rem;
}

.sidebar-collapsed .sidebar-footer :deep(.language-label),
.sidebar-collapsed .sidebar-footer :deep(.profile-name),
.sidebar-collapsed .sidebar-footer :deep(.language-button > .chevron),
.sidebar-collapsed .sidebar-footer :deep(.profile-button > .chevron) {
  display: none;
}

/* Dropdown contents (opened menus) must keep their full label/chevron
   text — only the closed-button labels above are hidden. */
.sidebar-collapsed .sidebar-footer :deep(.dropdown-menu .chevron),
.sidebar-collapsed .sidebar-footer :deep(.dropdown-menu span) {
  display: inline;
}

/* The dropdown-menu is normally anchored with `right: 0` relative to its
   (now much narrower, 72px) trigger button, which pushes the 160-280px
   wide menu mostly off-screen to the left. Anchor it to the left edge of
   the trigger instead so it opens into the visible content area. */
.sidebar-collapsed .sidebar-footer :deep(.dropdown-menu) {
  left: 0;
  right: auto;
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

  /* Icon-only collapse is a >=768px concept only — the off-canvas drawer
     always shows the full-width, fully-labelled sidebar regardless of the
     `collapsed` state (which may have been true before the viewport
     shrank into mobile range). */
  .sidebar.sidebar-collapsed {
    width: 260px;
  }

  .sidebar-collapsed .brand {
    padding: 1.5rem 1.25rem;
    align-items: stretch;
  }

  /* The manual collapse toggle is a >=768px desktop concept only. */
  .collapse-toggle {
    display: none;
  }

  .sidebar-collapsed .sidebar-nav {
    padding: 0.75rem;
  }

  .sidebar-collapsed .nav-link {
    justify-content: flex-start;
    gap: 0.75rem;
    padding: 0.625rem 0.75rem;
  }

  .sidebar-collapsed .nav-link span {
    display: inline;
  }

  .sidebar-collapsed .sidebar-footer {
    padding: 1rem 0.75rem;
  }

  .sidebar-collapsed .sidebar-footer :deep(.language-button),
  .sidebar-collapsed .sidebar-footer :deep(.profile-button) {
    justify-content: flex-start;
    padding: 0.5rem 0.875rem;
  }

  .sidebar-collapsed .sidebar-footer :deep(.language-label),
  .sidebar-collapsed .sidebar-footer :deep(.profile-name),
  .sidebar-collapsed .sidebar-footer :deep(.language-button > .chevron),
  .sidebar-collapsed .sidebar-footer :deep(.profile-button > .chevron) {
    display: inline;
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
