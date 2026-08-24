<template>
  <div class="filters-bar">
    <div class="filters-container">
      <div class="filters-grid">
        <div class="filter-group">
          <label>{{ t("filters.timePeriod") }}</label>
          <select v-model="selectedPeriod" class="filter-select">
            <option value="all">{{ t("filters.allMonths") }}</option>
            <option value="2025-01">{{ t("months.january") }}</option>
            <option value="2025-02">{{ t("months.february") }}</option>
            <option value="2025-03">{{ t("months.march") }}</option>
            <option value="2025-04">{{ t("months.april") }}</option>
            <option value="2025-05">{{ t("months.may") }}</option>
            <option value="2025-06">{{ t("months.june") }}</option>
            <option value="2025-07">{{ t("months.july") }}</option>
            <option value="2025-08">{{ t("months.august") }}</option>
            <option value="2025-09">{{ t("months.september") }}</option>
            <option value="2025-10">{{ t("months.october") }}</option>
            <option value="2025-11">{{ t("months.november") }}</option>
            <option value="2025-12">{{ t("months.december") }}</option>
          </select>
        </div>

        <div class="filter-group">
          <label>{{ t("filters.location") }}</label>
          <select v-model="selectedLocation" class="filter-select">
            <option value="all">{{ t("filters.all") }}</option>
            <option value="San Francisco">
              {{ t("warehouses.sanFrancisco") }}
            </option>
            <option value="London">{{ t("warehouses.london") }}</option>
            <option value="Tokyo">{{ t("warehouses.tokyo") }}</option>
          </select>
        </div>

        <div class="filter-group">
          <label>{{ t("filters.category") }}</label>
          <select v-model="selectedCategory" class="filter-select">
            <option value="all">{{ t("filters.all") }}</option>
            <option value="circuit boards">
              {{ t("categories.circuitBoards") }}
            </option>
            <option value="sensors">{{ t("categories.sensors") }}</option>
            <option value="actuators">{{ t("categories.actuators") }}</option>
            <option value="controllers">
              {{ t("categories.controllers") }}
            </option>
            <option value="power supplies">
              {{ t("categories.powerSupplies") }}
            </option>
          </select>
        </div>

        <div class="filter-group">
          <label>{{ t("filters.orderStatus") }}</label>
          <select v-model="selectedStatus" class="filter-select">
            <option value="all">{{ t("filters.all") }}</option>
            <option value="delivered">{{ t("status.delivered") }}</option>
            <option value="shipped">{{ t("status.shipped") }}</option>
            <option value="processing">{{ t("status.processing") }}</option>
            <option value="backordered">{{ t("status.backordered") }}</option>
          </select>
        </div>
      </div>

      <button
        class="reset-filters-btn"
        @click="resetFilters"
        :disabled="!hasActiveFilters"
        title="Reset all filters"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          fill="currentColor"
        >
          <path
            fill-rule="evenodd"
            d="M4 2a1 1 0 011 1v2.101a7.002 7.002 0 0111.601 2.566 1 1 0 11-1.885.666A5.002 5.002 0 005.999 7H9a1 1 0 010 2H4a1 1 0 01-1-1V3a1 1 0 011-1zm.008 9.057a1 1 0 011.276.61A5.002 5.002 0 0014.001 13H11a1 1 0 110-2h5a1 1 0 011 1v5a1 1 0 11-2 0v-2.101a7.002 7.002 0 01-11.601-2.566 1 1 0 01.61-1.276z"
            clip-rule="evenodd"
          />
        </svg>
      </button>
    </div>
  </div>
</template>

<script>
import { useFilters } from "../composables/useFilters";
import { useI18n } from "../composables/useI18n";

export default {
  name: "FilterBar",
  setup() {
    const {
      selectedPeriod,
      selectedLocation,
      selectedCategory,
      selectedStatus,
      hasActiveFilters,
      resetFilters,
    } = useFilters();

    const { t } = useI18n();

    return {
      t,
      selectedPeriod,
      selectedLocation,
      selectedCategory,
      selectedStatus,
      hasActiveFilters,
      resetFilters,
    };
  },
};
</script>

<style scoped>
.filters-bar {
  background: var(--color-bg);
  border-bottom: 1px solid var(--color-border-light);
  padding: var(--space-3) 0;
  position: sticky;
  top: 0;
  z-index: 90;
}

.filters-container {
  max-width: 1600px;
  margin: 0 auto;
  padding: 0 var(--space-6);
  display: flex;
  align-items: center;
  gap: var(--space-4);
}

.filters-grid {
  display: flex;
  align-items: center;
  gap: var(--space-4);
  flex: 1;
}

.filter-group {
  display: flex;
  align-items: center;
  gap: var(--space-2);
}

.filter-group label {
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--color-muted);
  white-space: nowrap;
}

.filter-select {
  /* 0.4rem/0.75rem doesn't map cleanly to the space scale (space-1=4px, space-2=8px) */
  padding: 0.4rem 0.75rem;
  border: 1px solid var(--color-border);
  border-radius: var(--radius-sm);
  font-size: 0.813rem;
  color: var(--color-ink);
  background: var(--color-surface);
  cursor: pointer;
  transition: all 0.2s;
  font-weight: 500;
  min-width: 140px;
}

.filter-select:hover {
  /* #94a3b8 is an intermediate hover shade between --color-border and --color-text, not in the current token set */
  border-color: #94a3b8;
}

.filter-select:focus {
  outline: none;
  border-color: var(--color-accent);
  /* TODO: requires --color-accent-rgb token for full tokenization */
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.reset-filters-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 0.4rem;
  background: var(--color-surface);
  border: 1px solid var(--color-border-light);
  border-radius: var(--radius-sm);
  color: var(--color-muted);
  cursor: pointer;
  transition: all 0.2s;
  flex-shrink: 0;
}

.reset-filters-btn:hover:not(:disabled) {
  background: var(--color-bg);
  border-color: var(--color-border);
  color: var(--color-ink);
}

.reset-filters-btn:disabled {
  opacity: 0.3;
  cursor: not-allowed;
}

.reset-filters-btn svg {
  width: 18px;
  height: 18px;
}

/* Below 1024px the sidebar is either icon-only (768-1023px) or a full
   drawer (<768px, see Sidebar.vue), narrowing the available width until
   the 4 filters + reset button no longer fit on one row. Wrap the grid
   instead of letting it overflow past the viewport edge. Row-gap keeps
   wrapped rows from looking cramped. */
@media (max-width: 1024px) {
  .filters-container {
    flex-wrap: wrap;
    padding: 0 var(--space-4);
  }

  .filters-grid {
    flex-wrap: wrap;
    row-gap: var(--space-3);
  }
}
</style>
