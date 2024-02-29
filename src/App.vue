<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpenses :income="income" :expenses="expenses" />
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmited"/>
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';
import { useToast } from 'vue-toastification';

import { ref, computed, onMounted } from 'vue';

const transactions = ref([
  // { id: 1, text: 'Flower', amount: -20 },
  // { id: 2, text: 'Salary', amount: 300 },
  // { id: 3, text: 'Book', amount: -10 },
  // { id: 4, text: 'Camera', amount: 150 }
]);

const toast = useToast();

/* on mounted check local storage */
onMounted(() => { 
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

/* get total */
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0);
});

/* get income */
const income = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount > 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0);
});

/* get expenses */
const expenses = computed(() => {
  return transactions.value.filter((transaction) => transaction.amount < 0)
  .reduce((acc, transaction) => {
    return acc + transaction.amount
  }, 0);
});

/* add transaction */
const handleTransactionSubmited = (transactionData) => {
  transactions.value.push({
    id: generateUniqueID(),
    text: transactionData.text,
    amount: transactionData.amount
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction added!');
}

/* generate id */
const generateUniqueID = () => {
  return Math.floor(Math.random() * 100000);
}

/* delete transaction */
const handleTransactionDeleted = (id) => { 

  transactions.value = transactions.value.filter((transaction) => {
    return transaction.id !== id;
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction deleted!');
}

/* Save to localstorage */
const saveTransactionsToLocalStorage = () => { 
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>
<style lang="">
  
</style>