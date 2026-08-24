<template>
  <div class="backlog">
    <div class="page-header">
      <h2>Backlog Management</h2>
      <p>Track and resolve inventory shortages</p>
    </div>

    <div v-if="loading" class="loading">Loading backlog...</div>
    <div v-else-if="error" class="error">{{ error }}</div>
    <div v-else>
      <div class="stats-grid">
        <div class="stat-card danger">
          <div class="stat-label">High Priority</div>
          <div class="stat-value">
            {{ getBacklogByPriority("high").length }}
          </div>
        </div>
        <div class="stat-card warning">
          <div class="stat-label">Medium Priority</div>
          <div class="stat-value">
            {{ getBacklogByPriority("medium").length }}
          </div>
        </div>
        <div class="stat-card info">
          <div class="stat-label">Low Priority</div>
          <div class="stat-value">{{ getBacklogByPriority("low").length }}</div>
        </div>
        <div class="stat-card">
          <div class="stat-label">Total Backlog Items</div>
          <div class="stat-value">{{ backlogItems.length }}</div>
        </div>
      </div>

      <div class="card">
        <div class="card-header">
          <h3 class="card-title">Backlog Items</h3>
        </div>
        <div v-if="backlogItems.length === 0" class="empty-state">
          <p class="empty-state-message">
            ✓ No backlog items - all orders can be fulfilled!
          </p>
        </div>
        <div v-else class="table-container">
          <table>
            <thead>
              <tr>
                <th>Order ID</th>
                <th>SKU</th>
                <th>Item Name</th>
                <th>Quantity Needed</th>
                <th>Quantity Available</th>
                <th>Shortage</th>
                <th>Days Delayed</th>
                <th>Priority</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in backlogItems" :key="item.id">
                <td>
                  <strong>{{ item.order_id }}</strong>
                </td>
                <td>
                  <strong>{{ item.item_sku }}</strong>
                </td>
                <td>{{ item.item_name }}</td>
                <td>{{ item.quantity_needed }}</td>
                <td>{{ item.quantity_available }}</td>
                <td>
                  <span class="badge danger">
                    {{ item.quantity_needed - item.quantity_available }} units
                    short
                  </span>
                </td>
                <td>
                  <span
                    :class="
                      item.days_delayed > 7 ? 'delay-danger' : 'delay-warning'
                    "
                  >
                    {{ item.days_delayed }} days
                  </span>
                </td>
                <td>
                  <span :class="['badge', item.priority]">
                    {{ item.priority }}
                  </span>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch, computed } from "vue";
import { api } from "../api";
import { useFilters } from "../composables/useFilters";

export default {
  name: "Backlog",
  setup() {
    const loading = ref(true);
    const error = ref(null);
    const allBacklogItems = ref([]);
    const inventoryItems = ref([]);

    // Use shared filters
    const { selectedLocation, selectedCategory, getCurrentFilters } =
      useFilters();

    // Filter backlog based on inventory filters
    const backlogItems = computed(() => {
      if (
        selectedLocation.value === "all" &&
        selectedCategory.value === "all"
      ) {
        return allBacklogItems.value;
      }

      // Get SKUs of items that match the filters
      const validSkus = new Set(inventoryItems.value.map((item) => item.sku));
      return allBacklogItems.value.filter((b) => validSkus.has(b.item_sku));
    });

    const loadBacklog = async () => {
      try {
        loading.value = true;
        const filters = getCurrentFilters();

        const [backlogData, inventoryData] = await Promise.all([
          api.getBacklog(),
          api.getInventory({
            warehouse: filters.warehouse,
            category: filters.category,
          }),
        ]);

        allBacklogItems.value = backlogData;
        inventoryItems.value = inventoryData;
      } catch (err) {
        error.value = "Failed to load backlog: " + err.message;
      } finally {
        loading.value = false;
      }
    };

    const getBacklogByPriority = (priority) => {
      return backlogItems.value.filter((item) => item.priority === priority);
    };

    // Watch for filter changes and reload data
    watch([selectedLocation, selectedCategory], () => {
      loadBacklog();
    });

    onMounted(loadBacklog);

    return {
      loading,
      error,
      backlogItems,
      getBacklogByPriority,
    };
  },
};
</script>

<style scoped>
/* Local empty-state pattern; no shared global .empty-state exists in
   App.vue yet, so this stays scoped here. */
.empty-state {
  padding: var(--space-8);
  text-align: center;
}

.empty-state-message {
  font-size: 1.125rem;
  font-weight: 600;
  /* #10b981 is a distinct success green (emerald-500) that does not match
     any existing --color-* token or .badge.success color pair in App.vue,
     so it is kept as an explicit hex value rather than introducing a new
     token. */
  color: #10b981;
}

/* #ef4444 (red-500) and #f59e0b (amber-500) are distinct shades from the
   .badge.danger/.badge.warning background+text pairs in App.vue and from
   any --color-* token, so they are kept as explicit hex values instead of
   introducing new tokens. */
.delay-danger {
  color: #ef4444;
}

.delay-warning {
  color: #f59e0b;
}
</style>
