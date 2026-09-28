<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
<meta name="theme-color" content="#0f172a" />
<title>Business Dashboard</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
  :root {
    --bg: #f1f5f9;
    --card: #ffffff;
    --text: #0f172a;
    --muted: #64748b;
    --border: #e2e8f0;
    --green: #22c55e;
    --red: #ef4444;
    --blue: #3b82f6;
    --amber: #f59e0b;
    --purple: #8b5cf6;
  }
  body {
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    background: var(--bg);
    color: var(--text);
    padding-bottom: 80px;
    min-height: 100vh;
  }

  /* ---------- Header ---------- */
  header {
    background: linear-gradient(135deg, #0f172a, #1e293b);
    color: #fff;
    padding: 20px 16px 24px;
    border-radius: 0 0 24px 24px;
    position: sticky; top: 0; z-index: 10;
  }
  header h1 { font-size: 18px; font-weight: 700; }
  header p  { font-size: 12px; color: #94a3b8; margin-top: 2px; }
  .header-row { display: flex; justify-content: space-between; align-items: center; }
  .avatar {
    width: 38px; height: 38px; border-radius: 50%;
    background: linear-gradient(135deg, #3b82f6, #8b5cf6);
    display: grid; place-items: center; font-weight: 700; font-size: 14px;
  }

  /* ---------- KPI grid ---------- */
  .kpi-grid {
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 10px; padding: 14px;
    margin-top: -12px;
  }
  .kpi {
    background: var(--card); border-radius: 16px; padding: 14px;
    box-shadow: 0 2px 8px rgba(15,23,42,.06);
    border: 1px solid var(--border);
  }
  .kpi .label { font-size: 11px; color: var(--muted); font-weight: 500; }
  .kpi .value { font-size: 20px; font-weight: 700; margin-top: 4px; }
  .kpi .sub   { font-size: 11px; margin-top: 2px; font-weight: 500; }
  .sub.up   { color: var(--green); }
  .sub.down { color: var(--red); }
  .kpi .icon {
    width: 32px; height: 32px; border-radius: 10px;
    display: grid; place-items: center; font-size: 16px;
    margin-bottom: 8px;
  }
  .icon.blue   { background: #dbeafe; color: #1d4ed8; }
  .icon.green  { background: #dcfce7; color: #15803d; }
  .icon.red    { background: #fee2e2; color: #b91c1c; }
  .icon.purple { background: #ede9fe; color: #6d28d9; }

  /* ---------- Cards ---------- */
  .section { padding: 0 14px 14px; }
  .card {
    background: var(--card); border-radius: 16px; padding: 16px;
    box-shadow: 0 2px 8px rgba(15,23,42,.06);
    border: 1px solid var(--border);
    margin-bottom: 12px;
  }
  .card h3 {
    font-size: 14px; font-weight: 600; margin-bottom: 12px;
    display: flex; justify-content: space-between; align-items: center;
  }
  .card h3 span { font-size: 11px; color: var(--muted); font-weight: 500; }

  /* ---------- Chart wrapper ---------- */
  .chart-wrap { position: relative; height: 220px; }
  .chart-wrap.tall { height: 240px; }

  /* ---------- Toggle buttons ---------- */
  .tabs {
    display: flex; gap: 6px; margin-bottom: 12px;
    background: #f8fafc; padding: 4px; border-radius: 10px;
  }
  .tab {
    flex: 1; padding: 8px; border: none; background: transparent;
    font-size: 12px; font-weight: 600; color: var(--muted);
    border-radius: 8px; cursor: pointer; transition: .2s;
  }
  .tab.active { background: #fff; color: var(--text); box-shadow: 0 1px 3px rgba(0,0,0,.08); }

  /* ---------- Table ---------- */
  table { width: 100%; border-collapse: collapse; font-size: 12px; }
  th {
    text-align: left; color: var(--muted); font-weight: 500;
    padding: 8px 4px; border-bottom: 1px solid var(--border);
    font-size: 11px; text-transform: uppercase; letter-spacing: .5px;
  }
  td { padding: 10px 4px; border-bottom: 1px solid var(--border); }
  tr:last-child td { border-bottom: none; }
  td.profit { color: var(--green); font-weight: 600; }
  td.loss   { color: var(--red);   font-weight: 600; }
  .pill {
    display: inline-block; padding: 2px 8px; border-radius: 20px;
    font-size: 10px; font-weight: 600;
  }
  .pill.green { background: #dcfce7; color: #15803d; }
  .pill.red   { background: #fee2e2; color: #b91c1c; }
  .pill.amber { background: #fef3c7; color: #b45309; }

  /* ---------- Bottom nav ---------- */
  nav {
    position: fixed; bottom: 0; left: 0; right: 0;
    background: #fff; border-top: 1px solid var(--border);
    display: flex; justify-content: space-around;
    padding: 10px 0 calc(10px + env(safe-area-inset-bottom));
    z-index: 20;
  }
  .nav-btn {
    display: flex; flex-direction: column; align-items: center; gap: 2px;
    background: none; border: none; font-size: 10px; color: var(--muted);
    font-weight: 500; cursor: pointer; padding: 4px 12px;
  }
  .nav-btn.active { color: var(--blue); }
  .nav-btn .ico { font-size: 18px; }

  /* ---------- Pull down refresh effect ---------- */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(8px); }
    to   { opacity: 1; transform: translateY(0); }
  }
  .fade { animation: fadeUp .4s ease both; }
</style>
</head>
<body>

<!-- ================= HEADER ================= -->
<header>
  <div class="header-row">
    <div>
      <h1>Business Dashboard</h1>
      <p id="today"></p>
    </div>
    <div class="avatar">AM</div>
  </div>
</header>

<!-- ================= KPI CARDS ================= -->
<section class="kpi-grid fade">
  <div class="kpi">
    <div class="icon blue">👥</div>
    <div class="label">Total Members</div>
    <div class="value" id="kpi-members">—</div>
    <div class="sub up" id="kpi-members-sub">—</div>
  </div>
  <div class="kpi">
    <div class="icon green">💰</div>
    <div class="label">Revenue (YTD)</div>
    <div class="value" id="kpi-revenue">—</div>
    <div class="sub up" id="kpi-revenue-sub">—</div>
  </div>
  <div class="kpi">
    <div class="icon purple">📈</div>
    <div class="label">Profit (YTD)</div>
    <div class="value" id="kpi-profit">—</div>
    <div class="sub up" id="kpi-profit-sub">—</div>
  </div>
  <div class="kpi">
    <div class="icon red">📉</div>
    <div class="label">Loss (YTD)</div>
    <div class="value" id="kpi-loss">—</div>
    <div class="sub down" id="kpi-loss-sub">—</div>
  </div>
</section>

<!-- ================= PROFIT VS LOSS ================= -->
<section class="section fade">
  <div class="card">
    <h3>Profit vs Loss <span>Monthly · $K</span></h3>
    <div class="tabs" id="pl-tabs">
      <button class="tab active" data-view="bar">Bar</button>
      <button class="tab" data-view="line">Line</button>
    </div>
    <div class="chart-wrap"><canvas id="plChart"></canvas></div>
  </div>
</section>

<!-- ================= MEMBER GROWTH ================= -->
<section class="section fade">
  <div class="card">
    <h3>Member Growth <span>Last 12 months</span></h3>
    <div class="chart-wrap"><canvas id="memberChart"></canvas></div>
  </div>
</section>

<!-- ================= REVENUE SPLIT ================= -->
<section class="section fade">
  <div class="card">
    <h3>Member Status <span>This month</span></h3>
    <div class="chart-wrap"><canvas id="statusChart"></canvas></div>
  </div>
</section>

<!-- ================= TABLE ================= -->
<section class="section fade">
  <div class="card">
    <h3>Monthly Breakdown</h3>
    <div style="overflow-x:auto;">
      <table>
        <thead>
          <tr>
            <th>Month</th><th>Mbrs</th><th>Rev</th><th>Cost</th><th>Profit</th>
          </tr>
        </thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
  </div>
</section>

<!-- ================= BOTTOM NAV ================= -->
<nav>
  <button class="nav-btn active"><span class="ico">📊</span>Dashboard</button>
  <button class="nav-btn"><span class="ico">👥</span>Members</button>
  <button class="nav-btn"><span class="ico">💰</span>Finance</button>
  <button class="nav-btn"><span class="ico">⚙️</span>Settings</button>
</nav>

<script>
/* ============================================================
   1) MOCK DATA  — replace with real data later
   ============================================================ */
const DATA = [
  { month: "Jan", revenue: 42, cost: 30, members: 120 },
  { month: "Feb", revenue: 48, cost: 33, members: 135 },
  { month: "Mar", revenue: 55, cost: 38, members: 150 },
  { month: "Apr", revenue: 61, cost: 42, members: 168 },
  { month: "May", revenue: 58, cost: 45, members: 180 },
  { month: "Jun", revenue: 72, cost: 50, members: 205 },
  { month: "Jul", revenue: 80, cost: 54, members: 230 },
  { month: "Aug", revenue: 76, cost: 58, members: 255 },
  { month: "Sep", revenue: 88, cost: 60, members: 280 },
  { month: "Oct", revenue: 95, cost: 64, members: 310 },
  { month: "Nov", revenue: 102, cost: 70, members: 340 },
  { month: "Dec", revenue: 118, cost: 78, members: 375 },
].map(d => ({ ...d, profit: d.revenue - d.cost, loss: Math.max(0, d.cost - d.revenue) }));

const MEMBER_STATUS = [
  { name: "Active",   value: 275, color: "#22c55e" },
  { name: "Inactive", value: 60,  color: "#ef4444" },
  { name: "New",      value: 40,  color: "#3b82f6" },
];

/* ============================================================
   2) COMPUTE TOTALS
   ============================================================ */
const totals = {
  members: DATA.at(-1).members,
  revenue: DATA.reduce((s, d) => s + d.revenue, 0),
  profit:  DATA.reduce((s, d) => s + d.profit, 0),
  loss:    DATA.reduce((s, d) => s + d.loss, 0),
};

/* ============================================================
   3) FILL KPI CARDS
   ============================================================ */
document.getElementById("kpi-members").textContent = totals.members.toLocaleString();
document.getElementById("kpi-members-sub").textContent = "+35 this month";
document.getElementById("kpi-revenue").textContent = "$" + totals.revenue + "K";
document.getElementById("kpi-revenue-sub").textContent = "+18% YoY";
document.getElementById("kpi-profit").textContent  = "$" + totals.profit + "K";
document.getElementById("kpi-profit-sub").textContent  = "+12% YoY";
document.getElementById("kpi-loss").textContent    = "$" + totals.loss + "K";
document.getElementById("kpi-loss-sub").textContent    = "-3% YoY";

document.getElementById("today").textContent = new Date().toLocaleDateString(undefined, {
  weekday: "long", day: "numeric", month: "short"
});

/* ============================================================
   4) CHART DEFAULTS
   ============================================================ */
Chart.defaults.font.family = "-apple-system, Segoe UI, Roboto, sans-serif";
Chart.defaults.font.size = 11;
Chart.defaults.color = "#64748b";
Chart.defaults.plugins.legend.labels.boxWidth = 10;
Chart.defaults.plugins.legend.labels.boxHeight = 10;
Chart.defaults.plugins.legend.labels.usePointStyle = true;

const labels = DATA.map(d => d.month);

/* ---------- Profit vs Loss ---------- */
const plCtx = document.getElementById("plChart").getContext("2d");
let plChart = new Chart(plCtx, {
  type: "bar",
  data: {
    labels,
    datasets: [
      { label: "Profit", data: DATA.map(d => d.profit), backgroundColor: "#22c55e", borderRadius: 6, barPercentage: .7 },
      { label: "Loss",   data: DATA.map(d => d.loss),   backgroundColor: "#ef4444", borderRadius: 6, barPercentage: .7 },
    ],
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { position: "bottom" } },
    scales: {
      x: { grid: { display: false } },
      y: { grid: { color: "#e2e8f0", drawBorder: false }, ticks: { callback: v => "$" + v + "K" } }
    }
  }
});

/* Toggle Bar / Line */
document.querySelectorAll("#pl-tabs .tab").forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelectorAll("#pl-tabs .tab").forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
    plChart.config.type = btn.dataset.view;
    plChart.update();
  });
});

/* ---------- Member Growth ---------- */
const memberCtx = document.getElementById("memberChart").getContext("2d");
const grad = memberCtx.createLinearGradient(0, 0, 0, 220);
grad.addColorStop(0, "rgba(59,130,246,.35)");
grad.addColorStop(1, "rgba(59,130,246,0)");

new Chart(memberCtx, {
  type: "line",
  data: {
    labels,
    datasets: [{
      label: "Members",
      data: DATA.map(d => d.members),
      borderColor: "#3b82f6",
      backgroundColor: grad,
      fill: true, tension: .4, borderWidth: 2.5,
      pointRadius: 3, pointBackgroundColor: "#3b82f6",
    }],
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { display: false } },
    scales: {
      x: { grid: { display: false } },
      y: { grid: { color: "#e2e8f0", drawBorder: false } }
    }
  }
});

/* ---------- Member Status (doughnut) ---------- */
new Chart(document.getElementById("statusChart"), {
  type: "doughnut",
  data: {
    labels: MEMBER_STATUS.map(s => s.name),
    datasets: [{
      data: MEMBER_STATUS.map(s => s.value),
      backgroundColor: MEMBER_STATUS.map(s => s.color),
      borderWidth: 0,
      cutout: "65%",
    }],
  },
  options: {
    responsive: true, maintainAspectRatio: false,
    plugins: { legend: { position: "bottom" } },
  }
});

/* ============================================================
   5) TABLE
   ============================================================ */
const tbody = document.getElementById("tableBody");
tbody.innerHTML = DATA.map(d => `
  <tr>
    <td><b>${d.month}</b></td>
    <td>${d.members}</td>
    <td>$${d.revenue}K</td>
    <td>$${d.cost}K</td>
    <td class="${d.profit >= 0 ? "profit" : "loss"}">
      ${d.profit >= 0 ? "+" : ""}$${d.profit}K
    </td>
  </tr>
`).join("");

/* ============================================================
   6) BOTTOM NAV (demo only)
   ============================================================ */
document.querySelectorAll(".nav-btn").forEach(btn => {
  btn.addEventListener("click", () => {
    document.querySelectorAll(".nav-btn").forEach(b => b.classList.remove("active"));
    btn.classList.add("active");
  });
});
</script>
</body>
</html>
