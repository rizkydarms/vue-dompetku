<template>
  <v-container class="pa-4">
    <h1 class="text-center mb-4">Dompetku – Expense & Income Money Management</h1>

    <!-- Row for Summary and Form -->
    <v-row>
      <!-- Summary Column -->
      <v-col cols="12" md="4" class="summary-column">
        <v-card outlined class="pa-4 mb-4">
          <h2>Ringkasan</h2>
          <v-divider class="my-2"></v-divider>
          <p>
            Total Transaksi: <strong>{{ totalTransactions }}</strong>
          </p>
          <p>
            Jumlah Masuk: <strong>+ Rp {{ totalIncome.toLocaleString() }}</strong>
          </p>
          <p>
            Jumlah Keluar: <strong>- Rp {{ totalExpense.toLocaleString() }}</strong>
          </p>
          <p>
            Saldo Akhir: <strong>Rp {{ netTotal.toLocaleString() }}</strong>
          </p>
        </v-card>

        <!-- Form Input -->
        <v-card outlined class="pa-4">
          <h2>{{ isEditing ? 'Edit Transaksi' : 'Tambah Transaksi' }}</h2>
          <v-divider class="my-2"></v-divider>

          <v-text-field
            v-model="description"
            label="Keterangan"
            placeholder="Masukkan keterangan"
            outlined
            dense
          ></v-text-field>

          <v-text-field
            v-model="amount"
            label="Jumlah Uang"
            placeholder="(+) Pemasukan / (-) Pengeluaran"
            outlined
            dense
            type="number"
          ></v-text-field>

          <v-select
            v-model="category"
            :items="categories"
            label="Kategori"
            outlined
            dense
            placeholder="Pilih kategori"
          ></v-select>

          <v-btn
            color="primary"
            class="mt-4"
            block
            @click="isEditing ? updateTransaction() : addTransaction()"
          >
            {{ isEditing ? 'Simpan Perubahan' : 'Tambah' }}
          </v-btn>

          <v-btn v-if="isEditing" color="grey" class="mt-2" block @click="cancelEdit">
            Batal Edit
          </v-btn>
        </v-card>
      </v-col>

      <!-- Transaction List Column -->
      <v-col cols="12" md="8" class="transaction-list-column">
        <v-card outlined class="pa-4">
          <h2>Daftar Transaksi</h2>
          <v-divider class="my-2"></v-divider>
          <v-list dense>
            <v-list-item
              v-for="(transaction, index) in transactions"
              :key="index"
              class="transaction-item"
            >
              <v-list-item-content>
                <v-list-item-title>
                  {{ transaction.description }} ({{ transaction.category }})
                </v-list-item-title>
                <v-list-item-subtitle>
                  Rp {{ transaction.amount.toLocaleString() }}
                </v-list-item-subtitle>
              </v-list-item-content>
              <v-list-item-action>
                <v-btn color="primary" text @click="editTransaction(index)">Edit</v-btn>
                <v-btn color="error" text @click="deleteTransaction(index)">Hapus</v-btn>
              </v-list-item-action>
            </v-list-item>
          </v-list>
          <v-btn color="error" block @click="confirmClearAll">Hapus Semua Data</v-btn>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const description = ref('')
const amount = ref(null)
const category = ref('')
const transactions = ref([])
const isEditing = ref(false)
const editIndex = ref(null)

const categories = ref(['Food', 'Transport', 'Entertainment', 'Bills', 'Others'])

onMounted(() => {
  const savedTransactions = localStorage.getItem('transactions')
  if (savedTransactions) {
    transactions.value = JSON.parse(savedTransactions)
  }
})

const totalTransactions = computed(() => transactions.value.length)
const totalIncome = computed(() =>
  transactions.value.filter((t) => t.amount > 0).reduce((sum, t) => sum + t.amount, 0),
)
const totalExpense = computed(() =>
  transactions.value.filter((t) => t.amount < 0).reduce((sum, t) => sum + Math.abs(t.amount), 0),
)
const netTotal = computed(() => totalIncome.value - totalExpense.value)

function saveToLocalStorage() {
  localStorage.setItem('transactions', JSON.stringify(transactions.value))
}

function addTransaction() {
  if (description.value && amount.value && category.value) {
    transactions.value.push({
      description: description.value,
      amount: Number(amount.value),
      category: category.value,
    })
    saveToLocalStorage()
    resetForm()
  } else {
    alert('Masukkan keterangan, jumlah uang, dan kategori')
  }
}

function editTransaction(index) {
  const transaction = transactions.value[index]
  description.value = transaction.description
  amount.value = transaction.amount
  category.value = transaction.category
  editIndex.value = index
  isEditing.value = true
}

function updateTransaction() {
  if (editIndex.value !== null && description.value && amount.value && category.value) {
    transactions.value[editIndex.value] = {
      description: description.value,
      amount: Number(amount.value),
      category: category.value,
    }
    saveToLocalStorage()
    resetForm()
  }
}

function deleteTransaction(index) {
  transactions.value.splice(index, 1)
  saveToLocalStorage()
}

function confirmClearAll() {
  if (confirm('Apakah Anda yakin ingin menghapus semua data?')) {
    clearAll()
  }
}

function clearAll() {
  transactions.value = []
  saveToLocalStorage()
}

function resetForm() {
  description.value = ''
  amount.value = null
  category.value = ''
  isEditing.value = false
  editIndex.value = null
}

function cancelEdit() {
  resetForm()
}
</script>

<style scoped>
.text-center {
  text-align: center;
}

.summary-column,
.transaction-list-column {
  background-color: #f5f5f5;
  padding: 1.5rem;
  border-radius: 8px;
}

.transaction-item {
  border-bottom: 1px solid #ccc;
  padding-bottom: 8px;
}

.v-card {
  background-color: #ffffff;
  border-radius: 8px;
}

.v-btn {
  font-weight: bold;
}

h1,
h2 {
  font-family: 'Roboto', sans-serif;
  color: #424242;
}

p {
  font-size: 0.9rem;
  color: #757575;
}

.summary-column p strong,
.summary-column h2 {
  color: #009688;
}
</style>
