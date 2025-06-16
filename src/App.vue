<template>
  <div id="app" class="container">
    <h1>Budget Planner</h1>

    <!-- Income Entry -->
    <div class="form-section">
      <h2>Add Income</h2>
      <input v-model.number="incomeAmount" type="number" placeholder="Enter income amount" />
      <button @click="addIncome">Add Income</button>
    </div>

    <!-- Expense Entry -->
    <div class="form-section">
      <h2>Add Expense</h2>
      <input v-model.number="expenseAmount" type="number" placeholder="Enter expense amount" />
      <button @click="addExpense">Add Expense</button>
    </div>

    <!-- Budget Summary -->
    <div class="summary">
      <h2>Summary</h2>
      <p>Total Income: {{ totalIncome }}</p>
      <p>Total Expenses: {{ totalExpenses }}</p>
      <p>Balance: {{ balance }}</p>
    </div>

    <!-- Chart Section -->
    <div>
      <h2>Budget Visualization</h2>
      <canvas id="budgetChart"></canvas>
    </div>
  </div>
</template>

<script>
import { Chart, registerables } from 'chart.js';

Chart.register(...registerables);

export default {
  name: 'App',
  data() {
    return {
      incomeAmount: 0,
      expenseAmount: 0,
      totalIncome: 0,
      totalExpenses: 0,
      balance: 0,
      chart: null
    };
  },
  methods: {
    addIncome() {
      if (this.incomeAmount > 0) {
        this.totalIncome += this.incomeAmount;
        this.updateBalance();
        this.renderChart(); // Render the chart fresh
        this.incomeAmount = 0;
      }
    },
    addExpense() {
      if (this.expenseAmount > 0) {
        this.totalExpenses += this.expenseAmount;
        this.updateBalance();
        this.renderChart(); // Render the chart fresh
        this.expenseAmount = 0;
      }
    },
    updateBalance() {
      this.balance = this.totalIncome - this.totalExpenses;
    },
    renderChart() {
      // If the chart already exists, destroy it before re-rendering
      if (this.chart) {
        this.chart.destroy();
      }

      const ctx = document.getElementById('budgetChart').getContext('2d');
      this.chart = new Chart(ctx, {
        type: 'pie',
        data: {
          labels: ['Income', 'Expenses'],
          datasets: [{
            label: 'Budget',
            data: [this.totalIncome, this.totalExpenses],
            backgroundColor: ['#4CAF50', '#F44336']
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: { position: 'bottom' },
            title: { display: true, text: 'Income vs Expenses' }
          }
        }
      });
    }
  },
  mounted() {
    this.renderChart();
  }
};
</script>

<style>
#app {
  text-align: center;
  margin-top: 50px;
}
.container {
  max-width: 800px;
  margin: auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.form-section {
  margin-bottom: 20px;
}

input {
  margin-right: 10px;
}

.summary {
  margin-top: 20px;
  margin-bottom: 20px;
}

canvas {
  max-width: 400px;
  margin: auto;
}
</style>
