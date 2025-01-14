<template>
  <div class="container">
    <h1 class="title" id="page-title">
      Monthly Rent Calculator
      <img
        class="title-icon"
        src="/home.png"
        height="100"
        width="100"
        alt="Home icon"
        aria-hidden="true"
      />
    </h1>
    <div class="rent-info" role="main" aria-labelledby="page-title">
      <div class="navigation" role="group" aria-label="Period navigation">
        <div class="period-container">
          <p class="period-label">Current Period:</p>
          <p class="period" aria-live="polite">{{ rentPeriodDisplay }}</p>
        </div>
        <div class="button-group">
          <button
            class="nav-button"
            @click="previousMonth"
            aria-label="View previous month's rent"
          >
            <span aria-hidden="true">&lt;</span> Previous
          </button>
          <button
            class="nav-button"
            @click="nextMonth"
            aria-label="View next month's rent"
          >
            Next <span aria-hidden="true">&gt;</span>
          </button>
        </div>
      </div>
      <p class="rent-amount" aria-live="polite">
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

// Add a helper function for ordinal suffixes
const getOrdinalSuffix = (day) => {
  if (day > 3 && day < 21) return "th";
  switch (day % 10) {
    case 1:
      return "st";
    case 2:
      return "nd";
    case 3:
      return "rd";
    default:
      return "th";
  }
};

const formatDate = (date) => {
  const month = date.toLocaleString("en-US", { month: "long" });
  const day = date.getDate();
  const year = date.getFullYear();
  const suffix = getOrdinalSuffix(day);
  return `${month} ${day}${suffix}, ${year}`;
};

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

  // Update period display with new format
  rentPeriodDisplay.value = `${formatDate(startDate)} - ${formatDate(endDate)}`;
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
  width: 100%;
  max-width: 800px;
  margin: 1rem auto;
  padding: clamp(1rem, 5vw, 2rem);
  text-align: center;
  background-color: var(--surface-1);
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);
  border-radius: 12px;
}

.title {
  color: var(--text-1);
  font-size: clamp(1.5rem, 5vw, 2.5rem);
  margin-bottom: clamp(1rem, 4vw, 2rem);
  font-weight: 600;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
  gap: 1rem;
}

.title-icon {
  height: clamp(50px, 15vw, 100px);
  width: auto;
  object-fit: contain;
}

.rent-info {
  margin-top: clamp(1rem, 4vw, 2rem);
  padding: clamp(1rem, 4vw, 2rem);
  border: 2px solid var(--surface-3);
  border-radius: 12px;
  background-color: var(--surface-2);
}

.navigation {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: clamp(1rem, 4vw, 2rem);
  gap: 1.5rem;
}

.button-group {
  display: flex;
  gap: 1rem;
  width: 100%;
  justify-content: center;
}

.period-container {
  background-color: var(--surface-3);
  padding: 1rem;
  border-radius: 8px;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.period-label {
  color: var(--text-2);
  font-size: clamp(0.875rem, 2.5vw, 1rem);
  margin: 0 0 0.5rem 0;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.period {
  font-size: clamp(1rem, 3vw, 1.2rem);
  color: var(--text-1);
  font-weight: 600;
  width: 100%;
  text-align: center;
  margin: 0;
  line-height: 1.6;
}

.nav-button {
  padding: clamp(0.5rem, 2vw, 0.75rem) clamp(1rem, 3vw, 1.5rem);
  background-color: var(--primary);
  color: var(--text-1);
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: clamp(0.875rem, 2.5vw, 1rem);
  font-weight: 500;
  transition: all 0.2s ease;
  flex: 1;
  max-width: 150px;
  min-width: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
}

.nav-button:hover {
  background-color: var(--primary-light);
  transform: translateY(-1px);
}

.nav-button:focus-visible {
  outline: 2px solid var(--primary-light);
  outline-offset: 2px;
}

.nav-button:active {
  transform: translateY(1px);
}

.rent-amount {
  font-size: clamp(1.25rem, 4vw, 1.5rem);
  color: var(--text-2);
  margin-top: clamp(1rem, 3vw, 1.5rem);
}

.rent-amount span {
  font-weight: 600;
  color: var(--accent);
  font-size: clamp(1.5rem, 5vw, 1.75rem);
  display: block;
  margin-top: 0.5rem;
}

@media (max-width: 480px) {
  .button-group {
    padding: 0 1rem;
  }

  .nav-button {
    padding: 0.75rem 1rem;
  }

  .period-container {
    padding: 0.75rem;
  }
}

/* High contrast mode support */
@media (forced-colors: active) {
  .nav-button {
    border: 2px solid ButtonText;
  }

  .rent-info {
    border: 2px solid ButtonText;
  }
}
</style>
