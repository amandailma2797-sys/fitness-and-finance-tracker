<template>
  <div style="min-height: 100vh; background-color: #f0f1edff; padding: 1.5rem;">
    <div class="max-w-4xl mx-auto bg-white rounded-2xl shadow-lg p-6">
      <h1 class="text-3xl font-extrabold text-center text-green-700 mb-6">
        Workout & Expense Tracker
      </h1>

      <!-- Tabs -->
      <div class="flex justify-center gap-6 mb-8">
        <!-- Fitness Button -->
        <button
          @click="tab = 'fitness'"
          :style="{
            transition: 'all 0.3s',
            transform: 'scale(1)',
            padding: '1rem 1.5rem',
            borderRadius: '0.5rem',
            boxShadow: '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
            border: '2px solid #d1d5db',
            display: 'flex',
            alignItems: 'center',
            gap: '0.5rem',
            background: tab === 'fitness' ? 'linear-gradient(to right, #22c55e, #16a34a)' : '#f9fafb',
            color: tab === 'fitness' ? 'white' : '#374151',
            borderColor: tab === 'fitness' ? '#4ade80' : '#d1d5db'
          }"
          onmouseover="this.style.transform = 'scale(1.05)'"
          onmouseout="this.style.transform = 'scale(1)'"
        >
          <span style="font-size: 1.25rem;">🏋️</span>
          <span style="font-weight: bold; font-size: 1rem;">Fitness</span>
        </button>

        <!-- Finance Button -->
        <button
          @click="tab = 'finance'"
          :style="{
            transition: 'all 0.3s',
            transform: 'scale(1)',
            padding: '1rem 1.5rem',
            borderRadius: '0.5rem',
            boxShadow: '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
            border: '2px solid #d1d5db',
            display: 'flex',
            alignItems: 'center',
            gap: '0.5rem',
            background: tab === 'finance' ? 'linear-gradient(to right, #22c55e, #16a34a)' : '#f9fafb',
            color: tab === 'finance' ? 'white' : '#374151',
            borderColor: tab === 'finance' ? '#4ade80' : '#d1d5db'
          }"
          onmouseover="this.style.transform = 'scale(1.05)'"
          onmouseout="this.style.transform = 'scale(1)'"
        >
          <span style="font-size: 1.25rem;">💰</span>
          <span style="font-weight: bold; font-size: 1rem;">Finance</span>
        </button>
      </div>

      <!-- Content -->
      <div>
        <FitnessLogs v-if="tab === 'fitness'" />
        <FinanceLogs v-if="tab === 'finance'" />
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref } from "vue"
import FitnessLogs from "./pages/FitnessLogs.vue"
import FinanceLogs from "./pages/FinanceLogs.vue"

const tab = ref("fitness")

const baseBtn =
  "transition-all duration-300 transform hover:scale-105 px-8 py-4 rounded-xl shadow-lg border-2 flex items-center gap-3 focus:outline-none"
</script>