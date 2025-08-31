<template>
  <div class="mt-8">
    <h2 class="text-xl font-bold mb-4 text-purple-700">📋 Fitness Log Table</h2>

    <table class="min-w-full bg-white rounded-lg shadow overflow-hidden">
      <thead class="bg-gray-200">
        <tr>
          <th class="py-2 px-4 text-left text-gray-700">Tanggal</th>
          <th class="py-2 px-4 text-left text-gray-700">Latihan</th>
          <th class="py-2 px-4 text-left text-gray-700">Durasi (menit)</th>
          <th class="py-2 px-4 text-left text-gray-700">Steps</th>
          <th class="py-2 px-4 text-left text-gray-700">Status</th>
          <th class="py-2 px-4 text-gray-700">Aksi</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(w, index) in workouts" :key="index" class="border-b">
          <td class="py-2 px-4">{{ w.date }}</td>
          <td class="py-2 px-4">{{ w.name }}</td>
          <td class="py-2 px-4">{{ w.duration }}</td>
          <td class="py-2 px-4">{{ w.steps || '-' }}</td>
          <td class="py-2 px-4">
            <span
              class="px-2 py-1 rounded-full text-xs font-semibold"
              :class="{
                'bg-green-200 text-green-800': w.status === 'Baik',
                'bg-yellow-200 text-yellow-800': w.status === 'Kurang Optimal',
                'bg-red-200 text-red-800': w.status === 'Buruk'
              }"
            >
              {{ w.status }}
            </span>
          </td>
          <td class="py-2 px-4">
            <button @click="deleteWorkout(index)" class="text-red-500 hover:text-red-700">
              ✖
            </button>
          </td>
        </tr>
        <tr v-if="workouts.length === 0">
          <td colspan="6" class="text-center py-4 text-gray-500">Belum ada workout</td>
        </tr>
      </tbody>
    </table>

    <!-- Tambah Workout -->
    <form @submit.prevent="addWorkout" class="mt-6 space-y-3 bg-blue-50 p-4 rounded-xl shadow">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-3">
        <div>
          <label class="block text-sm font-medium text-gray-700">Tanggal</label>
          <input
            v-model="newWorkout.date"
            type="date"
            class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-blue-400 focus:outline-none"
          />
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700">Nama Latihan</label>
          <input
            v-model="newWorkout.name"
            placeholder="Push Up"
            class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-blue-400 focus:outline-none"
          />
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700">Durasi (menit)</label>
          <input
            v-model.number="newWorkout.duration"
            type="number"
            min="1"
            placeholder="Durasi"
            class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-blue-400 focus:outline-none"
          />
        </div>
        <div>
          <label class="block text-sm font-medium text-gray-700">Steps (opsional)</label>
          <input
            v-model.number="newWorkout.steps"
            type="number"
            placeholder="Steps"
            class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-blue-400 focus:outline-none"
          />
        </div>
      </div>
      <button
        type="submit"
        class="w-full bg-blue-600 text-white py-2 rounded-lg hover:bg-blue-700 transition"
      >
        + Tambah Workout
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from "vue"

const workouts = ref([])

// default object
const newWorkout = ref({ date: "", name: "", duration: null, steps: null })

onMounted(() => {
  const saved = localStorage.getItem("workouts")
  if (saved) workouts.value = JSON.parse(saved)
})

watch(workouts, (val) => {
  localStorage.setItem("workouts", JSON.stringify(val))
}, { deep: true })

const addWorkout = () => {
  if (!newWorkout.value.date || !newWorkout.value.name || !newWorkout.value.duration) return

  const duration = Number(newWorkout.value.duration)
  let status = ""
  if (duration >= 30) status = "Baik"
  else if (duration >= 15) status = "Kurang Optimal"
  else status = "Buruk"

  workouts.value.push({ ...newWorkout.value, status })
  newWorkout.value = { date: "", name: "", duration: null, steps: null }
}

const deleteWorkout = (index) => {
  workouts.value.splice(index, 1)
}
</script>
