<template>
  <v-container>
    <v-row>
      <v-col cols="12" class="text-center">
        <h1>Dompetku – Expense & Income Money Management</h1>
      </v-col>

      <!-- Form Input -->
      <v-col cols="12" md="6">
        <v-text-field
          v-model="description"
          label="Keterangan"
          placeholder="Masukkan keterangan"
          outlined
        ></v-text-field>
        <v-text-field
          v-model="amount"
          label="Jumlah Uang"
          placeholder="Masukkan jumlah uang"
          outlined
          type="number"
        ></v-text-field>
        <v-btn color="primary" @click="addTransaction">Submit</v-btn>
      </v-col>

      <!-- List of Transactions -->
      <v-col cols="12" md="6">
        <v-list>
          <v-list-item
            v-for="(transaction, index) in transactions"
            :key="index"
            class="transaction-item"
          >
            <v-list-item-content>
              <v-list-item-title>
                {{ transaction.description }}
              </v-list-item-title>
              <v-list-item-subtitle>
                Rp {{ transaction.amount.toLocaleString() }}
              </v-list-item-subtitle>
            </v-list-item-content>
            <v-list-item-action>
              <v-btn color="error" text @click="deleteTransaction(index)"> Hapus </v-btn>
            </v-list-item-action>
          </v-list-item>
        </v-list>
        <v-btn color="error" @click="clearAll">Hapus Semua Data</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      description: '',
      amount: null,
      transactions: [],
    }
  },
  methods: {
    addTransaction() {
      if (this.description && this.amount) {
        this.transactions.push({
          description: this.description,
          amount: Number(this.amount),
        })
        this.description = ''
        this.amount = null
      } else {
        alert('Masukkan keterangan dan jumlah uang')
      }
    },
    deleteTransaction(index) {
      this.transactions.splice(index, 1)
    },
    clearAll() {
      this.transactions = []
    },
  },
}
</script>

<style scoped>
.transaction-item {
  border-bottom: 1px solid #ccc;
  padding-bottom: 10px;
}
</style>
