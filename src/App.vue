<template>
  <div class="container">
    <h1 class="title">
      Monthly Rent Calculator
      <img
        style="margin-left: 2rem"
        src="/home.png"
        height="100"
        width="100"
        alt="Home"
      />
    </h1>
    <div class="rent-info">
      <div class="navigation">
        <button
          class="nav-button"
          @click="previousMonth"
          aria-label="Previous month"
        >
          &lt; Previous
        </button>
        <p class="period">Current Period: {{ rentPeriodDisplay }}</p>
        <button class="nav-button" @click="nextMonth" aria-label="Next month">
          Next &gt;
        </button>
      </div>
      <p class="rent-amount">
        Total Rent Due: <span>${{ rentAmount.toFixed(2) }}</span>
      </p>
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
  max-width: 800px;
  margin: 2rem auto;
  padding: 2rem;
  text-align: center;
  background-color: var(--surface-1);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  border-radius: 12px;
}

.title {
  color: var(--text-1);
  font-size: 2.5rem;
  margin-bottom: 2rem;
  font-weight: 600;
  display: flex;
  align-items: end;
  justify-content: center;
}

.rent-info {
  margin-top: 2rem;
  padding: 2rem;
  border: 2px solid var(--surface-3);
  border-radius: 12px;
  background-color: var(--surface-2);
}

.navigation {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 2rem;
  gap: 1rem;
}

.period {
  font-size: 1.2rem;
  color: var(--text-2);
  font-weight: 500;
  flex-grow: 1;
}

.nav-button {
  padding: 0.75rem 1.5rem;
  background-color: var(--primary);
  color: var(--text-1);
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 1rem;
  font-weight: 500;
  transition: all 0.2s ease;
}

.nav-button:hover {
  background-color: var(--primary-light);
  transform: translateY(-1px);
}

.nav-button:focus {
  outline: 2px solid var(--primary-light);
  outline-offset: 2px;
}

.rent-amount {
  font-size: 1.5rem;
  color: var(--text-2);
  margin-top: 1rem;
}

.rent-amount span {
  font-weight: 600;
  color: var(--accent);
  font-size: 1.75rem;
}

@media (max-width: 640px) {
  .container {
    margin: 1rem;
    padding: 1rem;
  }

  .title {
    font-size: 2rem;
  }

  .navigation {
    flex-direction: column;
    gap: 1rem;
  }

  .period {
    order: -1;
  }
}
</style>
