<template>
  <div>
    <h2 style="font-size: 1.5rem; font-weight: bold; margin-bottom: 1.5rem; color: #1f2937; display: flex; align-items: center; gap: 0.75rem;">
      <span style="font-size: 2rem;">🏋️</span>
      Fitness Logs
    </h2>

    <!-- Form Tambah Workout -->
    <form @submit.prevent="addWorkout"
  style="margin-bottom: 2rem; background: linear-gradient(135deg, #eff6ff, #e0e7ff); padding: 1.5rem; border-radius: 1rem; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); border: 1px solid #dbeafe;">

  <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1rem; margin-bottom: 1rem;">
    
    <!-- Input Tanggal -->
    <div>
      <label style="font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem; display: block;">Tanggal</label>
      <input v-model="newWorkout.date" type="date"
        style="width: 90%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem;" />
    </div>

    <!-- Input kategori -->
    <div>
      <label style="font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem; display: block;">Kategori</label>
      <select v-model="newWorkout.category" @change="updateExercises"
        style="width: 100%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem;">
        <option value="" disabled>Pilih kategori</option>
        <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
      </select>
    </div>

    <!-- Input nama latihan -->
    <div>
      <label style="font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem; display: block;">Nama Latihan</label>
      <select v-model="newWorkout.name"
        style="width: 100%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem;">
        <option value="" disabled>Pilih latihan</option>
        <option v-for="ex in exercisesForCategory" :key="ex" :value="ex">{{ ex }}</option>
      </select>
    </div>

    <!-- Input repetisi / durasi -->
    <div>
      <label style="font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem; display: block;">
        {{ newWorkout.category === 'Bodyweight Exercises' ? 'Jumlah Repetisi' : 'Durasi (menit)' }}
      </label>
      <input v-model.number="newWorkout.value" type="number" min="1"
        :placeholder="newWorkout.category === 'Bodyweight Exercises' ? 'Repetisi' : 'Durasi'"
        style="width: 90%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem;" />
    </div>
  </div>

  <button type="submit"
    style="width: 100%; background: linear-gradient(to right, #3b82f6, #2563eb); color: white; padding: 0.75rem 1.5rem; border-radius: 0.75rem; font-weight: 600; border: none; cursor: pointer;">
    + Tambah Workout
  </button>
</form>

   <div style="display: flex; align-items: center; margin-bottom: 1.5rem; background: white; padding: 1rem; border-radius: 0.75rem; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1); border: 1px solid #f3f4f6;">
  <label style="font-size: 0.875rem; font-weight: 600; color: #374151; margin-right: 1rem;">Tampilkan data:</label>
  <select v-model="summaryPeriod" 
    style="border: 2px solid #d1d5db; border-radius: 0.5rem; padding: 0.5rem 1rem; background: #e3e3e3; color: #374151; font-weight: 500; box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); transition: all 0.2s;"
    onfocus="this.style.outline='none'; this.style.borderColor='#60a5fa'; this.style.boxShadow='0 0 0 3px rgba(96, 165, 250, 0.2)';"
    onblur="this.style.borderColor='#d1d5db'; this.style.boxShadow='0 1px 2px rgba(0, 0, 0, 0.05)';">
    <option value="all">Semua</option>
    <option value="week">Per Minggu</option>
    <option value="month">Per Bulan</option>
    <option value="year">Per Tahun</option>
  </select>
</div>


    <!-- Table -->
    <div v-if="groupedWorkouts.length" style="overflow: hidden; border-radius: 1rem; box-shadow: 0 10px 15px -3px rgba(0,0,0,0.1); border: 1px solid #e5e7eb;">
      <table style="width: 100%; background: white; border-collapse: collapse;">
        <thead style="background: linear-gradient(to right, #3b82f6, #2563eb);">
          <tr>
            <th style="padding: 1rem; color: white; text-align: left;">Tanggal</th>
            <th style="padding: 1rem; color: white; text-align: left;">Kategori</th>
            <th style="padding: 1rem; color: white; text-align: left;">Nama Latihan</th>
            <th style="padding: 1rem; color: white; text-align: left;">Repetisi/Durasi</th>
            <th style="padding: 1rem; color: white; text-align: left;">Status</th>
            <th style="padding: 1rem; color: white; text-align: left;">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(group, gIndex) in groupedWorkouts" :key="gIndex">
            <!-- Header Grup -->
            <tr style="background: linear-gradient(to right, #dbeafe, #e0e7ff);">
              <td colspan="6" style="padding: 0.75rem 1.5rem; font-weight: bold; color: #1e40af;">
                📅 {{ group.label }}
              </td>
            </tr>

            <!-- Data -->
            <tr v-for="(w, i) in group.items" :key="i" style="border-bottom: 1px solid #f3f4f6;">
              <td style="padding: 0.75rem 1.5rem;">{{ w.date }}</td>
              <td style="padding: 0.75rem 1.5rem;">{{ w.category }}</td>
              <td style="padding: 0.75rem 1.5rem;">{{ w.name }}</td>
              <td style="padding: 0.75rem 1.5rem;">{{ w.value }}</td>
              <td style="padding: 0.75rem 1.5rem;">
                <span :style="statusStyle(w.status)">{{ w.status }}</span>
              </td>
              <td style="padding: 0.75rem 1.5rem;">
                <button @click="deleteWorkout(w)" style="background:#ef4444; color:white; padding:0.5rem 1rem; border-radius:0.5rem; font-weight:600;">Hapus</button>
              </td>
            </tr>

            <!-- Total -->
            <tr style="background: linear-gradient(to right, #bbf7d0, #a7f3d0);">
              <td colspan="3" style="padding: 0.75rem 1.5rem; font-weight:bold; color:#065f46;">💪 Total Periode</td>
              <td style="padding: 0.75rem 1.5rem; font-weight:bold; color:#065f46;">{{ group.total }}</td>
              <td colspan="2"></td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>

    <!-- Empty -->
    <div v-else style="text-align: center; padding: 3rem; background: linear-gradient(135deg,#f9fafb,#f3f4f6); border-radius: 1rem; border: 2px dashed #d1d5db;">
      <div style="font-size: 4rem; margin-bottom: 1rem;">📊</div>
      <h3 style="font-size: 1.25rem; font-weight: 600; color: #4b5563; margin-bottom: 0.5rem;">Belum ada workout</h3>
      <p style="color: #6b7280;">Tambahkan workout untuk melihat catatan latihan Anda</p>
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
  const [baik, kurang] = statusRules[name] || [30, 15]
  if (value >= baik) return "Baik"
  if (value >= kurang) return "Kurang Optimal"
  return "Buruk"
}

const workouts = ref([])
const newWorkout = ref({ name: "", value: null, category: "",date:""})
const exercisesForCategory = ref([])
const summaryPeriod = ref("all")

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
  
  const chosenDate = newWorkout.value.date || new Date().toISOString().split("T")[0]
  const rawDate = newWorkout.value.date 
    ? new Date(newWorkout.value.date).toISOString() 
    : new Date().toISOString()

  workouts.value.push({
    ...newWorkout.value,
    value: Number(newWorkout.value.value),
    status,
    date: chosenDate,
    rawDate,
    id: Date.now()
  })
  newWorkout.value = { name: "", value: null, category: "", date: "" }
}

const deleteWorkout = (w) => {
  workouts.value = workouts.value.filter(x => x.id !== w.id)
}

const sortedWorkouts = computed(() =>
  [...workouts.value].sort((a,b) => new Date(b.rawDate) - new Date(a.rawDate))
)

const groupedWorkouts = computed(() => {
  const groups = {}
  sortedWorkouts.value.forEach(w => {
    const d = new Date(w.rawDate)
    let label = "Semua"
    if (summaryPeriod.value === "week") {
      const weekStart = new Date(d)
      const day = d.getDay() || 7
      weekStart.setDate(d.getDate() - day + 1)
      label = `Minggu mulai ${weekStart.toLocaleDateString("id-ID")}`
    } else if (summaryPeriod.value === "month") {
      label = `Bulan ${d.getMonth()+1}/${d.getFullYear()}`
    } else if (summaryPeriod.value === "year") {
      label = `Tahun ${d.getFullYear()}`
    }
    if (!groups[label]) groups[label] = { label, items: [], total: 0 }
    groups[label].items.push(w)
    groups[label].total += w.value
  })
  return Object.values(groups)
})

const statusStyle = (status) => {
  const base = "padding:0.25rem 0.75rem; border-radius:1rem; font-size:0.75rem; font-weight:600;"
  if (status === "Baik") return base + "background:#bbf7d0; color:#065f46;"
  if (status === "Kurang Optimal") return base + "background:#fde68a; color:#92400e;"
  return base + "background:#fecaca; color:#991b1b;"
}
</script>
