<template>
  <div class="restocking">
    <div class="page-header">
      <h2>{{ t('restocking.title') }}</h2>
      <p>{{ t('restocking.description') }}</p>
    </div>

    <div class="card budget-card">
      <div class="budget-header">
        <label class="budget-label" for="budget-slider">{{ t('restocking.availableBudget') }}</label>
        <div class="budget-value">{{ currencySymbol }}{{ budget.toLocaleString() }}</div>
      </div>
      <input
        id="budget-slider"
        type="range"
        class="budget-slider"
        min="0"
        max="10000"
        step="250"
        v-model.number="budget"
      />
      <div class="budget-range-labels">
        <span>{{ currencySymbol }}0</span>
        <span>{{ currencySymbol }}10,000</span>
      </div>
    </div>

    <div v-if="submitSuccess" class="success-banner">
      {{ t('restocking.orderSuccess', { orderNumber: submitSuccess.order_number, date: formatDate(submitSuccess.expected_delivery) }) }}
    </div>

    <div v-if="submitError" class="error">{{ submitError }}</div>

    <div v-if="loading" class="loading">{{ t('common.loading') }}</div>
    <div v-else-if="error" class="error">{{ error }}</div>
    <div v-else>
      <div class="card">
        <div class="card-header">
          <h3 class="card-title">{{ t('restocking.recommendations') }} ({{ recommendations.length }})</h3>
        </div>

        <div v-if="recommendations.length === 0" class="empty-state">
          {{ t('restocking.noRecommendations') }}
        </div>
        <div v-else>
          <div class="table-container">
            <table>
              <thead>
                <tr>
                  <th>{{ t('restocking.table.itemName') }}</th>
                  <th>{{ t('restocking.table.sku') }}</th>
                  <th>{{ t('restocking.table.trend') }}</th>
                  <th>{{ t('restocking.table.warehouse') }}</th>
                  <th>{{ t('restocking.table.unitCost') }}</th>
                  <th>{{ t('restocking.table.quantity') }}</th>
                  <th>{{ t('restocking.table.subtotal') }}</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in recommendations" :key="item.item_sku">
                  <td>{{ translateProductName(item.item_name) }}</td>
                  <td><strong>{{ item.item_sku }}</strong></td>
                  <td>
                    <span :class="['badge', item.trend]">{{ t(`trends.${item.trend}`) }}</span>
                  </td>
                  <td>{{ translateWarehouse(item.warehouse) }}</td>
                  <td>{{ currencySymbol }}{{ item.unit_cost.toLocaleString() }}</td>
                  <td>{{ item.recommended_quantity }}</td>
                  <td><strong>{{ currencySymbol }}{{ item.subtotal.toLocaleString() }}</strong></td>
                </tr>
              </tbody>
            </table>
          </div>

          <div class="allocation-summary">
            {{ t('restocking.budgetAllocated', {
              allocated: currencySymbol + totalCost.toLocaleString(),
              budget: currencySymbol + budget.toLocaleString()
            }) }}
          </div>

          <button
            class="place-order-btn"
            :disabled="recommendations.length === 0 || submitting"
            @click="placeOrder"
          >
            {{ submitting ? t('restocking.placingOrder') : t('restocking.placeOrder') }}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch, onMounted } from 'vue'
import { api } from '../api'
import { useI18n } from '../composables/useI18n'

export default {
  name: 'Restocking',
  setup() {
    const { t, currentCurrency, currentLocale, translateProductName, translateWarehouse } = useI18n()

    const currencySymbol = computed(() => {
      return currentCurrency.value === 'JPY' ? '¥' : '$'
    })

    const budget = ref(5000)
    const recommendations = ref([])
    const loading = ref(true)
    const error = ref(null)
    const submitting = ref(false)
    const submitError = ref(null)
    const submitSuccess = ref(null)

    let debounceTimer = null

    const totalCost = computed(() => {
      return recommendations.value.reduce((sum, item) => sum + item.subtotal, 0)
    })

    const loadRecommendations = async () => {
      try {
        loading.value = true
        error.value = null
        recommendations.value = await api.getRestockRecommendations(budget.value)
      } catch (err) {
        error.value = t('restocking.loadError') + ': ' + err.message
      } finally {
        loading.value = false
      }
    }

    // Debounce budget slider changes to avoid excessive API calls while dragging
    watch(budget, () => {
      submitSuccess.value = null
      submitError.value = null
      if (debounceTimer) clearTimeout(debounceTimer)
      debounceTimer = setTimeout(() => {
        loadRecommendations()
      }, 400)
    })

    const placeOrder = async () => {
      submitting.value = true
      submitError.value = null
      submitSuccess.value = null
      try {
        const orderData = {
          budget: budget.value,
          items: recommendations.value.map(item => ({
            item_sku: item.item_sku,
            item_name: item.item_name,
            quantity: item.recommended_quantity,
            unit_cost: item.unit_cost,
            subtotal: item.subtotal
          }))
        }
        const order = await api.createRestockOrder(orderData)
        submitSuccess.value = order
        recommendations.value = []
      } catch (err) {
        submitError.value = t('restocking.orderError') + ': ' + err.message
      } finally {
        submitting.value = false
      }
    }

    const formatDate = (dateString) => {
      // Date-only strings ("YYYY-MM-DD") are parsed by `new Date()` as UTC
      // midnight; appending a local time component avoids the resulting
      // off-by-one-day shift when rendered via toLocaleDateString() in
      // timezones behind UTC. Skip strings that already include a time part.
      const hasTimeComponent = /T/.test(dateString)
      const date = new Date(hasTimeComponent ? dateString : `${dateString}T00:00:00`)
      if (isNaN(date.getTime())) return dateString
      const locale = currentLocale.value === 'ja' ? 'ja-JP' : 'en-US'
      return date.toLocaleDateString(locale, {
        year: 'numeric',
        month: 'short',
        day: 'numeric'
      })
    }

    onMounted(loadRecommendations)

    return {
      t,
      budget,
      recommendations,
      loading,
      error,
      submitting,
      submitError,
      submitSuccess,
      totalCost,
      currencySymbol,
      placeOrder,
      formatDate,
      translateProductName,
      translateWarehouse
    }
  }
}
</script>

<style scoped>
.budget-card {
  padding: 1.25rem;
}

.budget-header {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  margin-bottom: 0.75rem;
}

.budget-label {
  font-size: 0.875rem;
  font-weight: 600;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.budget-value {
  font-size: 2rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.budget-slider {
  width: 100%;
  accent-color: #2563eb;
  cursor: pointer;
}

.budget-range-labels {
  display: flex;
  justify-content: space-between;
  font-size: 0.813rem;
  color: #94a3b8;
  margin-top: 0.375rem;
}

.empty-state {
  text-align: center;
  padding: 2.5rem 1rem;
  color: #64748b;
  font-size: 0.938rem;
}

.allocation-summary {
  margin-top: 1rem;
  padding: 0.875rem 1rem;
  background: #f8fafc;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.938rem;
  font-weight: 600;
  color: #0f172a;
}

.place-order-btn {
  margin-top: 1rem;
  padding: 0.75rem 1.5rem;
  background: #2563eb;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 0.938rem;
  font-weight: 600;
  cursor: pointer;
  transition: background 0.2s ease;
}

.place-order-btn:hover:not(:disabled) {
  background: #1d4ed8;
}

.place-order-btn:disabled {
  background: #cbd5e1;
  cursor: not-allowed;
}

.success-banner {
  background: #d1fae5;
  border: 1px solid #6ee7b7;
  color: #065f46;
  padding: 1rem;
  border-radius: 8px;
  margin-bottom: 1.25rem;
  font-size: 0.938rem;
  font-weight: 500;
}
</style>
