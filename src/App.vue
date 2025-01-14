<template>
  <div class="container">
    <h1>Monthly Rent Calculator</h1>
    <div class="rent-info">
      <div class="navigation">
        <button @click="previousMonth">&lt; Previous</button>
        <p>Current Period: {{ rentPeriodDisplay }}</p>
        <button @click="nextMonth">Next &gt;</button>
      </div>
      <p>Total Rent Due: ${{ rentAmount.toFixed(2) }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const DAILY_RATE = 280 / 7; // Convert weekly rate to daily rate
const rentAmount = ref(0);
const rentPeriodDisplay = ref("");
const monthOffset = ref(0);

const calculateRent = () => {
  const today = new Date();
  let startDate = new Date();
  let endDate = new Date();

  // Adjust dates based on monthOffset
  startDate.setMonth(startDate.getMonth() + monthOffset.value);
  endDate.setMonth(endDate.getMonth() + monthOffset.value);

  // Set the dates for the current rent period
  if (today.getDate() >= 15) {
    startDate.setDate(15);
    endDate.setMonth(endDate.getMonth() + 1);
    endDate.setDate(15);
  } else {
    startDate.setMonth(startDate.getMonth() - 1);
    startDate.setDate(15);
    endDate.setDate(15);
  }

  // Calculate number of days between dates
  const timeDiff = endDate - startDate;
  const daysDiff = Math.ceil(timeDiff / (1000 * 60 * 60 * 24));

  // Calculate rent
  rentAmount.value = daysDiff * DAILY_RATE;

  // Update period display
  rentPeriodDisplay.value = `${startDate.toLocaleDateString()} - ${endDate.toLocaleDateString()}`;
};

// Add navigation functions
const nextMonth = () => {
  monthOffset.value++;
  calculateRent();
};

const previousMonth = () => {
  monthOffset.value--;
  calculateRent();
};

// Initial calculation
onMounted(() => {
  calculateRent();

  // Set up auto-update on the 15th of each month
  setInterval(() => {
    const today = new Date();
    if (today.getDate() === 15) {
      calculateRent();
    }
  }, 24 * 60 * 60 * 1000); // Check once per day
});
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 2rem auto;
  padding: 1rem;
  text-align: center;
}

.rent-info {
  margin-top: 2rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.navigation {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #2c3e50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #34495e;
}
</style>
