<template>
  <div>
    <h2 style="font-size: 1.5rem; font-weight: bold; margin-bottom: 1.5rem; color: #1f2937; display: flex; align-items: center; gap: 0.75rem;">
      <span style="font-size: 2rem;">💰</span>
      Catatan Keuangan
    </h2>

    <!-- Modern Form -->
    <form @submit.prevent="addExpense" style="margin-bottom: 2rem; background: linear-gradient(135deg, #eff6ff, #e0e7ff); padding: 1.5rem; border-radius: 1rem; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); border: 1px solid #dbeafe;">
        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1.5rem;">
  <div>
    <label style="display: block; font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem;">Tanggal</label>
    <input v-model="newExpense.date" type="date"
      style="width: 90%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem; background: white; box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); transition: all 0.2s;"
      onfocus="this.style.outline='none'; this.style.borderColor='#60a5fa'; this.style.boxShadow='0 0 0 3px rgba(96, 165, 250, 0.1)';"
      onblur="this.style.borderColor='#e5e7eb'; this.style.boxShadow='0 1px 2px rgba(0, 0, 0, 0.05)';" />
  </div>

  <div>
    <label style="display: block; font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem;">Kategori</label>
    <select v-model="newExpense.category" @change="updateItems"
      style="width: 100%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem; background: white; box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); transition: all 0.2s;"
      onfocus="this.style.outline='none'; this.style.borderColor='#60a5fa'; this.style.boxShadow='0 0 0 3px rgba(96, 165, 250, 0.1)';"
      onblur="this.style.borderColor='#e5e7eb'; this.style.boxShadow='0 1px 2px rgba(0, 0, 0, 0.05)';">
      <option value="" disabled>Pilih kategori</option>
      <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
    </select>
  </div>

  <div>
    <label style="display: block; font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem;">Jenis Pengeluaran</label>
    <select v-model="newExpense.item"
      style="width: 100%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem; background: white; box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); transition: all 0.2s;"
      onfocus="this.style.outline='none'; this.style.borderColor='#60a5fa'; this.style.boxShadow='0 0 0 3px rgba(96, 165, 250, 0.1)';"
      onblur="this.style.borderColor='#e5e7eb'; this.style.boxShadow='0 1px 2px rgba(0, 0, 0, 0.05)';">
      <option value="" disabled>Pilih pengeluaran</option>
      <option v-for="item in itemsForCategory" :key="item" :value="item">{{ item }}</option>
    </select>
  </div>

  <div>
    <label style="display: block; font-size: 0.875rem; font-weight: 600; color: #374151; margin-bottom: 0.5rem;">Nominal(Rp)</label>
    <input v-model.number="newExpense.amount" type="number" placeholder="Masukkan nominal..."
      style="width: 90%; border: 2px solid #e5e7eb; border-radius: 0.75rem; padding: 0.75rem 1rem; background: white; box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05); transition: all 0.2s;"
      onfocus="this.style.outline='none'; this.style.borderColor='#60a5fa'; this.style.boxShadow='0 0 0 3px rgba(96, 165, 250, 0.1)';"
      onblur="this.style.borderColor='#e5e7eb'; this.style.boxShadow='0 1px 2px rgba(0, 0, 0, 0.05)';" />
  </div>
</div>


      <button
        type="submit"
        style="width: 100%; background: linear-gradient(to right, #3b82f6, #2563eb); color: white; padding: 0.75rem 1.5rem; border-radius: 0.75rem; font-weight: 600; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); transition: all 0.2s; border: none; cursor: pointer;"
        onmouseover="this.style.background='linear-gradient(to right, #2563eb, #1d4ed8)'; this.style.transform='scale(1.02)';"
        onmouseout="this.style.background='linear-gradient(to right, #3b82f6, #2563eb)'; this.style.transform='scale(1)';"
        onmousedown="this.style.transform='scale(0.98)';"
        onmouseup="this.style.transform='scale(1.02)';"
      >
        <span style="font-size: 1.25rem; margin-right: 0.5rem;">+</span>
        Tambah Pengeluaran
      </button>
    </form>

  <!-- Modern Filter -->
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



    <!-- Modern Table -->
    <div style="overflow: hidden; border-radius: 1rem; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); border: 1px solid #e5e7eb;">
      <table style="width: 100%; background: white; border-collapse: collapse;">
        <thead style="background: linear-gradient(to right, #3b82f6, #2563eb);">
          <tr>
            <th style="padding: 1rem 1.5rem; text-align: left; color: white; font-weight: 600;">Tanggal</th>
            <th style="padding: 1rem 1.5rem; text-align: left; color: white; font-weight: 600;">Kategori</th>
            <th style="padding: 1rem 1.5rem; text-align: left; color: white; font-weight: 600;">Jenis Pengeluaran</th>
            <th style="padding: 1rem 1.5rem; text-align: left; color: white; font-weight: 600;">Nominal(Rp)</th>
            <th style="padding: 1rem 1.5rem; text-align: left; color: white; font-weight: 600;">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(group, gIndex) in groupedExpenses" :key="gIndex">
            <!-- Header Periode -->
            <tr style="background: linear-gradient(to right, #dbeafe, #e0e7ff);">
              <td style="padding: 0.75rem 1.5rem; font-weight: bold; color: #1e40af;" colspan="5">
                <span style="font-size: 1.125rem;">📅 {{ group.label }}</span>
              </td>
            </tr>

            <!-- Data Pengeluaran -->
            <tr v-for="(expense, index) in group.items" :key="index" 
                style="border-bottom: 1px solid #f3f4f6; transition: all 0.2s;"
                onmouseover="this.style.background='linear-gradient(to right, #eff6ff, #e0e7ff)';"
                onmouseout="this.style.background='white';">
              <td style="padding: 1rem 1.5rem; color: #374151; font-weight: 500;">{{ expense.date }}</td>
              <td style="padding: 1rem 1.5rem; color: #1f2937; font-weight: 600;">{{ expense.category }}</td>
              <td style="padding: 1rem 1.5rem; color: #374151;">{{ expense.item }}</td>
              <td style="padding: 1rem 1.5rem; color: #1f2937; font-weight: bold;">{{ formatRupiah(expense.amount) }}</td>
              <td style="padding: 1rem 1.5rem;">
                <button 
                  @click="deleteExpense(expense.id)" 
                  style="background: #ef4444; color: white; padding: 0.5rem 1rem; border-radius: 0.5rem; font-weight: 600; box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1); transition: all 0.2s; border: none; cursor: pointer;"
                  onmouseover="this.style.background='#dc2626'; this.style.transform='scale(1.05)';"
                  onmouseout="this.style.background='#ef4444'; this.style.transform='scale(1)';"
                  onmousedown="this.style.transform='scale(0.95)';"
                  onmouseup="this.style.transform='scale(1.05)';"
                >
                  Hapus
                </button>
              </td>
            </tr>

            <!-- Total Periode -->
            <tr style="background: linear-gradient(to right, #bbf7d0, #a7f3d0);">
              <td style="padding: 0.75rem 1.5rem; font-weight: bold; color: #065f46;" colspan="3">
                <span style="font-size: 1.125rem;">💵 Total Periode</span>
              </td>
              <td style="padding: 0.75rem 1.5rem; font-weight: bold; color: #065f46; font-size: 1.125rem;">{{ formatRupiah(group.total) }}</td>
              <td style="padding: 0.75rem 1.5rem;"></td>
            </tr>
          </template>
        </tbody>
      </table>
    </div>

    <!-- Empty State -->
    <div v-if="!groupedExpenses.length" style="text-align: center; padding: 3rem; background: linear-gradient(135deg, #f9fafb, #f3f4f6); border-radius: 1rem; border: 2px dashed #d1d5db;">
      <div style="font-size: 4rem; margin-bottom: 1rem;">📊</div>
      <h3 style="font-size: 1.25rem; font-weight: 600; color: #4b5563; margin-bottom: 0.5rem;">Belum ada data pengeluaran</h3>
      <p style="color: #6b7280;">Mulai tambahkan pengeluaran untuk melihat laporan keuangan Anda</p>
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
const newExpense = ref({ category: "", item: "", amount: null, date: "" })
const itemsForCategory = ref([])
const summaryPeriod = ref("all")

onMounted(() => {
  const saved = localStorage.getItem("expenses")
  if (saved) {
    expenses.value = JSON.parse(saved).map(e => ({
      ...e,
      rawDate: e.rawDate || new Date().toISOString()
    }))
  }
  
  // Set default date to today
  const today = new Date().toISOString().split('T')[0]
  newExpense.value.date = today
})

watch(expenses, (val) => {
  localStorage.setItem("expenses", JSON.stringify(val))
}, { deep: true })

const updateItems = () => {
  itemsForCategory.value = itemsByCategory[newExpense.value.category] || []
  newExpense.value.item = ""
}

const addExpense = () => {
  if (!newExpense.value.category || !newExpense.value.item || !newExpense.value.amount || !newExpense.value.date) return
  
  const selectedDate = new Date(newExpense.value.date)
  const formattedDate = `${selectedDate.getDate().toString().padStart(2,'0')}/${(selectedDate.getMonth()+1).toString().padStart(2,'0')}/${selectedDate.getFullYear()}`
  const id = Date.now()
  
  expenses.value.push({ 
    ...newExpense.value, 
    amount: Number(newExpense.value.amount),
    date: formattedDate, 
    rawDate: selectedDate.toISOString(), 
    id 
  })
  newExpense.value = { category: "", item: "", amount: null, date: "" }
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