<template>
  <div>
    <h2 class="text-xl font-bold mb-4 text-green-700">🏋️ Fitness Logs</h2>

    <!-- Form -->
    <form @submit.prevent="addWorkout" class="space-y-3 mb-6 bg-green-50 p-4 rounded-xl shadow">
      <div>
        <label class="block text-sm font-medium text-gray-700">Kategori</label>
        <select v-model="newWorkout.category" @change="updateExercises"
          class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-green-400 focus:outline-none">
          <option value="" disabled>Pilih kategori</option>
          <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
        </select>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700">Nama Latihan</label>
        <select v-model="newWorkout.name"
          class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-green-400 focus:outline-none">
          <option value="" disabled>Pilih latihan</option>
          <option v-for="exercise in exercisesForCategory" :key="exercise" :value="exercise">{{ exercise }}</option>
        </select>
      </div>

      <div>
        <label class="block text-sm font-medium text-gray-700">
          {{ newWorkout.category === 'Bodyweight Exercises' ? 'Jumlah Repetisi' : 'Durasi (menit)' }}
        </label>
        <input
          v-model.number="newWorkout.value"
          type="number"
          min="1"
          :placeholder="newWorkout.category === 'Bodyweight Exercises' ? 'Repetisi' : 'Durasi'"
          class="mt-1 border rounded-lg px-3 py-2 w-full focus:ring-2 focus:ring-green-400 focus:outline-none"
        />
      </div>

      <button
        type="submit"
        class="w-full bg-green-600 text-white py-2 rounded-lg hover:bg-green-700 transition"
      >
        + Tambah Workout
      </button>
    </form>

    <!-- Bodyweight Table -->
    <div v-if="bodyweightWorkouts.length" class="overflow-x-auto mb-6">
      <h3 class="text-lg font-semibold mb-2">Bodyweight Exercises</h3>
      <table class="min-w-full bg-white border border-gray-300 rounded-lg shadow">
        <thead class="bg-green-200 border-b border-gray-300">
          <tr>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Tanggal</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Nama Latihan</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Repetisi</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Status</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(workout, index) in bodyweightWorkouts" :key="index" class="border-b border-gray-200 hover:bg-green-50">
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.date }}</td>
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.name }}</td>
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.value }}</td>
            <td class="py-2 px-4 border-b border-gray-200">
              <span
                class="px-2 py-1 rounded-full text-xs font-medium"
                :class="{
                  'bg-green-200 text-green-800': workout.status === 'Baik',
                  'bg-yellow-200 text-yellow-800': workout.status === 'Kurang Optimal',
                  'bg-red-200 text-red-800': workout.status === 'Buruk'
                }"
              >
                {{ workout.status }}
              </span>
            </td>
            <td class="py-2 px-4 border-b border-gray-200">
              <button @click="deleteWorkout(workout)" class="text-red-500 hover:text-red-700">Hapus</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Gym Table -->
    <div v-if="gymWorkouts.length" class="overflow-x-auto">
      <h3 class="text-lg font-semibold mb-2">Gym / Equipment Exercises</h3>
      <table class="min-w-full bg-white border border-gray-300 rounded-lg shadow">
        <thead class="bg-green-200 border-b border-gray-300">
          <tr>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Tanggal</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Nama Latihan</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Durasi (menit)</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Status</th>
            <th class="py-2 px-4 border-b border-gray-300 text-left">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(workout, index) in gymWorkouts" :key="index" class="border-b border-gray-200 hover:bg-green-50">
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.date }}</td>
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.name }}</td>
            <td class="py-2 px-4 border-b border-gray-200">{{ workout.value }}</td>
            <td class="py-2 px-4 border-b border-gray-200">
              <span
                class="px-2 py-1 rounded-full text-xs font-medium"
                :class="{
                  'bg-green-200 text-green-800': workout.status === 'Baik',
                  'bg-yellow-200 text-yellow-800': workout.status === 'Kurang Optimal',
                  'bg-red-200 text-red-800': workout.status === 'Buruk'
                }"
              >
                {{ workout.status }}
              </span>
            </td>
            <td class="py-2 px-4 border-b border-gray-200">
              <button @click="deleteWorkout(workout)" class="text-red-500 hover:text-red-700">Hapus</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from "vue"

const categories = ["Bodyweight Exercises", "Gym / Equipment Exercises"]

const exercises = {
  "Bodyweight Exercises": ["Push Up", "Sit Up", "Pull Up", "Squat", "Lunge"],
  "Gym / Equipment Exercises": ["Treadmill", "Elliptical", "Pilates", "Bench Press", "Leg Press"]
}

const statusRules = {
  "Push Up": [20, 10],
  "Sit Up": [15, 7],
  "Pull Up": [15, 8],
  "Squat": [25, 10],
  "Lunge": [20, 10],
  "Treadmill": [30, 15],
  "Elliptical": [25, 10],
  "Pilates": [20, 10],
  "Bench Press": [20, 10],
  "Leg Press": [20, 10]
}

const getStatus = (name, value) => {
  const [baikMin, kurangMin] = statusRules[name] || [30, 15]
  if (value >= baikMin) return "Baik"
  else if (value >= kurangMin) return "Kurang Optimal"
  else return "Buruk"
}

const workouts = ref([])
const newWorkout = ref({ name: "", value: null, category: "" })
const exercisesForCategory = ref([])

onMounted(() => {
  const saved = localStorage.getItem("workouts")
  if (saved) workouts.value = JSON.parse(saved)
})

watch(workouts, (val) => {
  localStorage.setItem("workouts", JSON.stringify(val))
}, { deep: true })

const updateExercises = () => {
  exercisesForCategory.value = exercises[newWorkout.value.category] || []
  newWorkout.value.name = ""
  newWorkout.value.value = null
}

const addWorkout = () => {
  if (!newWorkout.value.name || !newWorkout.value.value || !newWorkout.value.category) return

  const status = getStatus(newWorkout.value.name, Number(newWorkout.value.value))
  const today = new Date().toLocaleDateString("id-ID", { day: '2-digit', month: '2-digit', year: 'numeric' })

  workouts.value.push({
    name: newWorkout.value.name,
    value: Number(newWorkout.value.value),
    category: newWorkout.value.category,
    status,
    date: today
  })

  newWorkout.value = { name: "", value: null, category: "" }
}

const deleteWorkout = (workout) => {
  const index = workouts.value.indexOf(workout)
  if (index > -1) workouts.value.splice(index, 1)
}

const bodyweightWorkouts = computed(() =>
  workouts.value.filter(w => w.category === 'Bodyweight Exercises')
)

const gymWorkouts = computed(() =>
  workouts.value.filter(w => w.category === 'Gym / Equipment Exercises')
)
</script>
