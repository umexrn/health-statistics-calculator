// Health Statistics Tracker

const STORAGE_KEY = "healthHistory";
const ACTIVITY_MULTIPLIERS = {
  "Sedentary Lifestyle": 1.20,
  "Light Active Lifestyle": 1.375,
  "Moderate Active Life Style": 1.55,
  "Very Active Lifestyle": 1.725,
  "Athlete": 1.90
};

const GOAL_ADJUSTMENTS = {
  "Weight Loss": -500,
  "Fat Loss": -500,
  "Muscle Gain": 300,
  "Muscle Mass Gain": 300,
  "Weight Gain": 300,
  "Body Recomposition": -250,
  "Maintenance": 0,
  "General Fitness": 0
};

let history = loadHistory();
let chartMode = "weight";
let chartInstance = null;

// ---------- Storage ----------
function loadHistory() {
  const raw = localStorage.getItem(STORAGE_KEY);
  return raw ? JSON.parse(raw) : [];
}

function saveHistory() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(history));
}

// ---------- Calculations ----------
function calcBmi(weightKg, heightCm) {
  const heightM = heightCm / 100;
  return weightKg / (heightM * heightM);
}

function bmiCategory(bmi) {
  if (bmi < 18.5) return "Underweight";
  if (bmi < 25) return "Healthy Range";
  if (bmi < 30) return "Overweight";
  return "Obese";
}

function calcBmr(weightKg, heightCm, age) {
  // Sex-neutral estimate: midpoint of Mifflin-St Jeor male/female constants
  return 10 * weightKg + 6.25 * heightCm - 5 * age - 78;
}

function calcTdee(bmr, lifestyle) {
  return bmr * ACTIVITY_MULTIPLIERS[lifestyle];
}

function calcTarget(tdee, goal) {
  return tdee + GOAL_ADJUSTMENTS[goal];
}

// ---------- Form submit ----------
const statsForm = document.getElementById("statsForm");

statsForm.addEventListener("submit", (e) => {
  e.preventDefault();

  const entry = {
    id: crypto.randomUUID(),
    date: new Date().toISOString().split("T")[0],
    name: document.getElementById("fName").value.trim(),
    age: Number(document.getElementById("fAge").value),
    height: Number(document.getElementById("fHeight").value),
    weight: Number(document.getElementById("fWeight").value),
    lifestyle: document.getElementById("fLifestyle").value,
    dailyCalories: document.getElementById("fCalories").value
      ? Number(document.getElementById("fCalories").value)
      : null,
    meals: Number(document.getElementById("fMeals").value),
    goal: document.getElementById("fGoal").value
  };

  history.push(entry);
  saveHistory();

  renderResults(entry);
  renderHistoryTable();
  renderChart();

  statsForm.reset();
});

// ---------- Results dashboard ----------
function renderResults(entry) {
  const bmi = calcBmi(entry.weight, entry.height);
  const bmr = calcBmr(entry.weight, entry.height, entry.age);
  const tdee = calcTdee(bmr, entry.lifestyle);
  const target = calcTarget(tdee, entry.goal);
  const perMeal = target / entry.meals;

  document.getElementById("resultsCard").hidden = false;

  document.getElementById("resBmi").textContent = bmi.toFixed(1);
  document.getElementById("resBmiCategory").textContent = bmiCategory(bmi);
  document.getElementById("resTdee").textContent = `${Math.round(tdee)} kcal`;
  document.getElementById("resTarget").textContent = `${Math.round(target)} kcal/day`;
  document.getElementById("resGoal").textContent = entry.goal;
  document.getElementById("resPerMeal").textContent = `${Math.round(perMeal)} kcal/meal`;

  const diffBlock = document.getElementById("resDiffBlock");
  if (entry.dailyCalories) {
    const diff = target - entry.dailyCalories;
    const direction = diff > 0 ? "increase" : "decrease";
    document.getElementById("resDiff").textContent =
      `${direction === "increase" ? "Increase" : "Decrease"} your daily intake by approximately ${Math.abs(Math.round(diff))} kcal`;
    diffBlock.hidden = false;
  } else {
    diffBlock.hidden = true;
  }
}

// ---------- History table ----------
function renderHistoryTable() {
  const tbody = document.getElementById("historyTableBody");
  tbody.innerHTML = "";

  if (history.length === 0) {
    document.getElementById("historyCard").hidden = true;
    return;
  }
  document.getElementById("historyCard").hidden = false;

  [...history].reverse().forEach(entry => {
    const bmi = calcBmi(entry.weight, entry.height);
    const tr = document.createElement("tr");
    tr.innerHTML = `
      <td>${entry.date}</td>
      <td>${entry.weight} kg</td>
      <td>${bmi.toFixed(1)}</td>
      <td>${entry.goal}</td>
      <td class="row-actions"><button data-id="${entry.id}">Delete</button></td>
    `;
    tbody.appendChild(tr);
  });
}

document.getElementById("historyTableBody").addEventListener("click", (e) => {
  if (e.target.tagName === "BUTTON") {
    history = history.filter(h => h.id !== e.target.dataset.id);
    saveHistory();
    renderHistoryTable();
    renderChart();
    if (history.length > 0) {
      renderResults(history[history.length - 1]);
    } else {
      document.getElementById("resultsCard").hidden = true;
      document.getElementById("progressCard").hidden = true;
    }
  }
});

// ---------- Chart ----------
document.querySelectorAll(".toggle-btn").forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelectorAll(".toggle-btn").forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
    chartMode = btn.dataset.chart;
    renderChart();
  });
});

function renderChart() {
  if (history.length === 0) {
    document.getElementById("progressCard").hidden = true;
    return;
  }
  document.getElementById("progressCard").hidden = false;

  const labels = history.map(h => h.date);
  const ctx = document.getElementById("progressChart").getContext("2d");

  if (chartInstance) chartInstance.destroy();

  if (chartMode === "weight") {
    const data = history.map(h => h.weight);
    chartInstance = new Chart(ctx, {
      type: "line",
      data: {
        labels,
        datasets: [{
          label: "Weight (kg)",
          data,
          borderColor: "#2563eb",
          backgroundColor: "rgba(37,99,235,0.1)",
          tension: 0.3,
          fill: true
        }]
      },
      options: baseChartOptions()
    });
  } else {
    const data = history.map(h => calcBmi(h.weight, h.height));
    chartInstance = new Chart(ctx, {
      type: "line",
      data: {
        labels,
        datasets: [{
          label: "BMI",
          data,
          borderColor: "#2563eb",
          backgroundColor: "rgba(37,99,235,0.1)",
          tension: 0.3,
          fill: true
        }]
      },
      options: {
        ...baseChartOptions(),
        plugins: {
          ...baseChartOptions().plugins,
          annotation: undefined
        }
      },
      plugins: [bmiRangeBandPlugin]
    });
  }
}

const bmiRangeBandPlugin = {
  id: "bmiRangeBand",
  beforeDraw(chart) {
    const { ctx, chartArea, scales } = chart;
    if (!chartArea) return;
    const yTop = scales.y.getPixelForValue(24.9);
    const yBottom = scales.y.getPixelForValue(18.5);
    ctx.save();
    ctx.fillStyle = "rgba(34,197,94,0.12)";
    ctx.fillRect(chartArea.left, yTop, chartArea.right - chartArea.left, yBottom - yTop);
    ctx.restore();
  }
};

function baseChartOptions() {
  return {
    responsive: true,
    plugins: {
      legend: { labels: { color: "#e5e5e5" } }
    },
    scales: {
      x: { ticks: { color: "#999" }, grid: { color: "#2a2a2c" } },
      y: { ticks: { color: "#999" }, grid: { color: "#2a2a2c" } }
    }
  };
}

// ---------- Init ----------
if (history.length > 0) {
  renderResults(history[history.length - 1]);
  renderHistoryTable();
  renderChart();
}
