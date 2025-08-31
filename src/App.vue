<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <div class="max-w-4xl mx-auto bg-white rounded-2xl shadow-lg p-6">
      <h1 class="text-3xl font-extrabold text-center text-green-700 mb-6">
        Workout & Expense Tracker
      </h1>

      <!-- Tabs -->
      <div class="flex justify-center gap-4 mb-6">
        <button
          @click="tab = 'fitness'"
          :class="tab === 'fitness' ? activeTab : inactiveTab"
        >
          🏋️ Fitness
        </button>
        <button
          @click="tab = 'finance'"
          :class="tab === 'finance' ? activeTab : inactiveTab"
        >
          💰 Finance
        </button>
      </div>

      <!-- Content -->
      <div>
        <FitnessLogs v-if="tab === 'fitness'" />
        <FinanceLogs v-if="tab === 'finance'" />
      </div>
      
      <!-- Summary dihapus -->
      <!-- <Summary /> -->
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue"
import FitnessLogs from "./pages/FitnessLogs.vue"
import FinanceLogs from "./pages/FinanceLogs.vue"
// import Summary from "./components/Summary.vue" // dihapus

const tab = ref("fitness")

const activeTab =
  "bg-green-600 text-white px-5 py-2 rounded-lg shadow-md font-semibold"
const inactiveTab =
  "bg-gray-200 text-gray-600 px-5 py-2 rounded-lg hover:bg-gray-300"
</script>
