<template>
  <div>
    <h2 class="text-xl font-bold mb-4 text-green-700">💰 Catatan Keuangan</h2>

    <!-- Form Tambah Pengeluaran -->
    <form @submit.prevent="addExpense" class="card bg-base-100 shadow p-4 mb-4">
      <div class="form-control mb-3">
        <label class="label"><span class="label-text">Kategori</span></label>
        <select v-model="newExpense.category" @change="updateItems" class="select select-bordered w-full">
          <option value="" disabled>Pilih kategori</option>
          <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
        </select>
      </div>

      <div class="form-control mb-3">
        <label class="label"><span class="label-text">Jenis Pengeluaran</span></label>
        <select v-model="newExpense.item" class="select select-bordered w-full">
          <option value="" disabled>Pilih pengeluaran</option>
          <option v-for="item in itemsForCategory" :key="item" :value="item">{{ item }}</option>
        </select>
      </div>

      <div class="form-control mb-3">
        <label class="label"><span class="label-text">Nominal</span></label>
        <input v-model.number="newExpense.amount" type="number" placeholder="Rp" class="input input-bordered w-full" />
      </div>

      <button class="btn btn-success w-full">+ Tambah Pengeluaran</button>
    </form>

    <!-- Filter Periode -->
    <div class="flex items-center mb-4">
      <label class="mr-2 font-medium">Tampilkan data:</label>
      <select v-model="summaryPeriod" class="select select-bordered w-48">
        <option value="all">Semua</option>
        <option value="week">Per Minggu</option>
        <option value="month">Per Bulan</option>
        <option value="year">Per Tahun</option>
      </select>
    </div>

    <!-- Table -->
    <div class="overflow-x-auto">
      <table class="min-w-full border border-gray-300 bg-white shadow rounded-lg">
        <thead class="bg-green-200 border-b border-gray-300">
          <tr>
            <th class="py-2 px-4 text-left border-r border-gray-300">Tanggal</th>
            <th class="py-2 px-4 text-left border-r border-gray-300">Kategori</th>
            <th class="py-2 px-4 text-left border-r border-gray-300">Jenis Pengeluaran</th>
            <th class="py-2 px-4 text-left border-r border-gray-300">Nominal</th>
            <th class="py-2 px-4 text-left">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(group, gIndex) in groupedExpenses" :key="gIndex">
            <!-- Header Periode -->
            <tr class="bg-green-100 font-semibold">
              <td class="py-2 px-4 border-r border-gray-300" colspan="5">{{ group.label }}</td>
            </tr>

            <!-- Data Pengeluaran -->
            <tr v-for="(expense, index) in group.items" :key="index" class="border-b border-gray-300 hover:bg-green-50">
              <td class="py-2 px-4 border-r border-gray-300">{{ expense.date }}</td>
              <td class="py-2 px-4 border-r border-gray-300">{{ expense.category }}</td>
              <td class="py-2 px-4 border-r border-gray-300">{{ expense.item }}</td>
              <td class="py-2 px-4 border-r border-gray-300">{{ formatRupiah(expense.amount) }}</td>
              <td class="py-2 px-4">
                <button @click="deleteExpense(expense.id)" class="text-red-500 hover:text-red-700">Hapus</button>
              </td>
            </tr>

            <!-- Total Periode di bawah tiap grup -->
            <tr class="bg-green-200 font-semibold">
              <td class="py-2 px-4 border-r border-gray-300" colspan="3">Total Periode</td>
              <td class="py-2 px-4 border-r border-gray-300">{{ formatRupiah(group.total) }}</td>
              <td class="py-2 px-4"></td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, computed } from "vue"

const categories = ["Bodyweight Exercises", "Gym / Equipment Exercises"]
const itemsByCategory = {
  "Bodyweight Exercises": ["Matras", "Resistance Band", "Suplemen"],
  "Gym / Equipment Exercises": ["Membership Gym", "Treadmill", "Pilates", "Alat Gym"]
}

const expenses = ref([])
const newExpense = ref({ category: "", item: "", amount: null })
const itemsForCategory = ref([])
const summaryPeriod = ref("all")

onMounted(() => {
  const saved = localStorage.getItem("expenses")
  if (saved) {
    // pastikan semua data punya rawDate valid
    expenses.value = JSON.parse(saved).map(e => ({
      ...e,
      rawDate: e.rawDate || new Date().toISOString()
    }))
  }
})

watch(expenses, (val) => {
  localStorage.setItem("expenses", JSON.stringify(val))
}, { deep: true })

const updateItems = () => {
  itemsForCategory.value = itemsByCategory[newExpense.value.category] || []
  newExpense.value.item = ""
}

const addExpense = () => {
  if (!newExpense.value.category || !newExpense.value.item || !newExpense.value.amount) return
  const today = new Date()
  const formattedDate = `${today.getDate().toString().padStart(2,'0')}/${(today.getMonth()+1).toString().padStart(2,'0')}/${today.getFullYear()}`
  const id = Date.now()
  expenses.value.push({ 
    ...newExpense.value, 
    amount: Number(newExpense.value.amount), // pastikan number
    date: formattedDate, 
    rawDate: today.toISOString(), 
    id 
  })
  newExpense.value = { category: "", item: "", amount: null }
  itemsForCategory.value = []
}

const deleteExpense = (id) => {
  const index = expenses.value.findIndex(e => e.id === id)
  if (index !== -1) expenses.value.splice(index, 1)
}

const formatRupiah = (num) =>
  new Intl.NumberFormat("id-ID", { style: "currency", currency: "IDR", minimumFractionDigits: 0 }).format(num || 0)

const sortedExpenses = computed(() => {
  return [...expenses.value].sort((a,b) => new Date(b.rawDate) - new Date(a.rawDate))
})

const groupedExpenses = computed(() => {
  const groups = {}

  sortedExpenses.value.forEach(exp => {
    const d = new Date(exp.rawDate)
    if (isNaN(d)) return // skip kalau tanggal invalid

    let label = "Semua"

    if (summaryPeriod.value === "week") {
      const weekStart = new Date(d)
      const day = d.getDay() || 7 // Minggu = 0 → jadi 7
      weekStart.setDate(d.getDate() - day + 1) // mundur ke Senin
      const weekLabel = `${weekStart.getDate().toString().padStart(2,'0')}/${(weekStart.getMonth()+1).toString().padStart(2,'0')}/${weekStart.getFullYear()}`
      label = `Minggu dimulai ${weekLabel}`
    } else if (summaryPeriod.value === "month") {
      label = `Bulan ${d.getMonth()+1}/${d.getFullYear()}`
    } else if (summaryPeriod.value === "year") {
      label = `Tahun ${d.getFullYear()}`
    }

    if (!groups[label]) groups[label] = { label, items: [], total: 0 }
    groups[label].items.push(exp)
    groups[label].total += Number(exp.amount) || 0
  })

  return Object.values(groups)
})
</script>

