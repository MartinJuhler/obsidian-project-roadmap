---
type: dashboard
---

# Project Dashboard

> Metadata-driven dashboard. Change `status:` in any feature spec and this page updates automatically.

---

## 📈 Overall Project Status

> **Project timeline** — target auto-calculates daily based on working hours per month.

```dataviewjs
const pages = dv.pages('"Project Roadmap/03 - Feature Specifications" OR "Project Roadmap/04 - Operations"').where(p => p.type === "feature");
const total = pages.length;
const done = pages.where(p => p.status === "Done").length;
const totalCost = pages.values.reduce((s, p) => s + (p.cost_nok || 0), 0);
const doneCost = pages.where(p => p.status === "Done").values.reduce((s, p) => s + (p.cost_nok || 0), 0);
const budgetPct = totalCost > 0 ? Math.round(doneCost / totalCost * 100) : 0;

// Monthly cumulative targets — customize for your project
// [month_index (0=Jan), cumulative_%_expected_by_end_of_month]
const milestones = [
  [0, 0], [1, 10], [2, 20], [3, 30], [4, 40],
  [5, 50], [6, 60], [7, 65], [8, 75], [9, 85],
  [10, 95], [11, 100]
];

const now = new Date();
const month = now.getMonth();
const dayInMonth = now.getDate();
const daysInMonth = new Date(now.getFullYear(), month + 1, 0).getDate();
const monthProgress = dayInMonth / daysInMonth;

let target = 0;
if (month >= 12) { target = 100; }
else {
  const prev = milestones.find(m => m[0] === month) || [0, 0];
  const next = milestones.find(m => m[0] === month + 1) || [11, 100];
  target = Math.round(prev[1] + (next[1] - prev[1]) * monthProgress);
}

const status = budgetPct >= target ? "✅ On track" : "⚠️ Behind target";

dv.table(
  ["Features Done", "Feature %", "Budget %", "Target", "Spent / Total", "Status"],
  [[`${done} / ${total}`, `${Math.round(done/total*100)}%`, `${budgetPct}%`, `${target}% expected`, `${doneCost.toLocaleString()} / ${totalCost.toLocaleString()}`, status]]
);
```

### 🧑‍💻 My Adjusted Progress

> Adjusted for days off. Change `sickDays` below to match your actual days off.

```dataviewjs
const pages = dv.pages('"Project Roadmap/03 - Feature Specifications" OR "Project Roadmap/04 - Operations"').where(p => p.type === "feature");
const totalCost = pages.values.reduce((s, p) => s + (p.cost_nok || 0), 0);
const doneCost = pages.where(p => p.status === "Done").values.reduce((s, p) => s + (p.cost_nok || 0), 0);
const budgetPct = totalCost > 0 ? Math.round(doneCost / totalCost * 100) : 0;

// ✏️ EDIT THIS: your total sick/personal days off
const sickDays = 0;
const totalWorkDays = 220;
const netRatio = (totalWorkDays - sickDays) / totalWorkDays;

const milestones = [
  [0, 0], [1, 10], [2, 20], [3, 30], [4, 40],
  [5, 50], [6, 60], [7, 65], [8, 75], [9, 85],
  [10, 95], [11, 100]
];

const now = new Date();
const month = now.getMonth();
const dayInMonth = now.getDate();
const daysInMonth = new Date(now.getFullYear(), month + 1, 0).getDate();
const monthProgress = dayInMonth / daysInMonth;

let rawTarget = 0;
if (month >= 12) { rawTarget = 100; }
else {
  const prev = milestones.find(m => m[0] === month) || [0, 0];
  const next = milestones.find(m => m[0] === month + 1) || [11, 100];
  rawTarget = prev[1] + (next[1] - prev[1]) * monthProgress;
}
const myTarget = Math.round(rawTarget * netRatio);

const done = pages.where(p => p.status === "Done").length;
const total = pages.length;
const status = budgetPct >= myTarget ? "✅ On track" : "⚠️ Behind target";

dv.table(
  ["Features Done", "Budget %", "My Target", "Spent / Total", "My Status"],
  [[`${done} / ${total}`, `${budgetPct}%`, `${myTarget}% expected`, `${doneCost.toLocaleString()} / ${totalCost.toLocaleString()}`, status]]
);
```

---

## 📊 Phase Completion

```dataview
TABLE WITHOUT ID
  phase AS "Phase",
  length(filter(rows, (r) => r.status = "Done")) + " / " + length(rows) AS "Done / Total",
  round(length(filter(rows, (r) => r.status = "Done")) / length(rows) * 100) + "%" AS "Feature %",
  sum(map(rows, (r) => choice(r.status = "Done", r.cost_nok, 0))) AS "Spent",
  sum(map(rows, (r) => choice(r.status != "Done", r.cost_nok, 0))) AS "Remaining"
FROM "Project Roadmap/03 - Feature Specifications" OR "Project Roadmap/04 - Operations"
WHERE type = "feature"
GROUP BY phase
SORT phase ASC
```

---

## 📋 Features

### Frontend

```dataview
TABLE WITHOUT ID
  file.link AS "Feature",
  hours AS "Hours",
  cost_nok AS "Cost",
  choice(codebase_status = "Complete", "Done", choice(codebase_status = "Partial", "In Progress", codebase_status)) AS "Status"
FROM "Project Roadmap/03 - Feature Specifications/Frontend"
WHERE type = "feature"
SORT choice(codebase_status = "Complete", 1, choice(codebase_status = "Partial", 2, 3)) ASC, file.name ASC
```

### Backend

```dataview
TABLE WITHOUT ID
  file.link AS "Feature",
  hours AS "Hours",
  cost_nok AS "Cost",
  choice(codebase_status = "Complete", "Done", choice(codebase_status = "Partial", "In Progress", codebase_status)) AS "Status"
FROM "Project Roadmap/03 - Feature Specifications/Backend"
WHERE type = "feature"
SORT choice(codebase_status = "Complete", 1, choice(codebase_status = "Partial", 2, 3)) ASC, file.name ASC
```

### Integrations

```dataview
TABLE WITHOUT ID
  file.link AS "Feature",
  hours AS "Hours",
  cost_nok AS "Cost",
  owner AS "Owner",
  choice(codebase_status = "Complete", "Done", choice(codebase_status = "Partial", "In Progress", codebase_status)) AS "Status"
FROM "Project Roadmap/03 - Feature Specifications/Integrations"
WHERE type = "feature"
SORT choice(codebase_status = "Complete", 1, choice(codebase_status = "Partial", 2, 3)) ASC, file.name ASC
```

### Operations

```dataview
TABLE WITHOUT ID
  file.link AS "Item",
  hours AS "Hours",
  cost_nok AS "Cost",
  owner AS "Owner",
  status AS "Status"
FROM "Project Roadmap/04 - Operations"
WHERE type = "feature"
SORT choice(status = "Done", 1, choice(status = "In Progress", 2, 3)) ASC, cost_nok DESC
```

---

## 🏃 Work Pace Tracker

```dataviewjs
const pages = dv.pages('"Project Roadmap/03 - Feature Specifications" OR "Project Roadmap/04 - Operations"').where(p => p.type === "feature");

const doneHours = pages.where(p => p.status === "Done").values.reduce((s, p) => s + (p.hours || 0), 0);

// ✏️ EDIT: team members whose hours you don't do yourself
const otherOwners = ["Alice", "Bob"];
const myRemaining = pages.where(p => p.status !== "Done" && !otherOwners.some(o => p.owner === o)).values.reduce((s, p) => s + (p.hours || 0), 0);
const othersRemaining = pages.where(p => p.status !== "Done" && otherOwners.some(o => p.owner === o)).values.reduce((s, p) => s + (p.hours || 0), 0);

// ✏️ EDIT: net working days per month [month_index, days]
const monthlyDays = [
  [0, 20], [1, 20], [2, 22], [3, 18], [4, 20],
  [5, 22], [6, 10], [7, 10], [8, 22], [9, 20],
  [10, 20], [11, 15]
];
const totalNetDays = monthlyDays.reduce((s, m) => s + m[1], 0);

const now = new Date();
const month = now.getMonth();
const dayInMonth = now.getDate();
const daysInCurrentMonth = new Date(now.getFullYear(), month + 1, 0).getDate();

let daysUsed = 0;
for (let i = 0; i < month && i < 12; i++) {
  daysUsed += monthlyDays[i][1];
}
if (month < 12) {
  daysUsed += Math.round(monthlyDays[month][1] * (dayInMonth / daysInCurrentMonth));
}

const daysRemaining = totalNetDays - daysUsed;
const avgPace = daysUsed > 0 ? (doneHours / daysUsed).toFixed(1) : "–";
const reqPace = daysRemaining > 0 ? (myRemaining / daysRemaining).toFixed(1) : "–";
const paceStatus = parseFloat(avgPace) >= parseFloat(reqPace) ? "✅ Ahead of pace" : "⚠️ Needs attention";

dv.table(
  ["Metric", "Value"],
  [
    ["**Hours completed**", `${doneHours}h`],
    ["**Days used**", `~${daysUsed} days`],
    ["**Average pace**", `${avgPace}h/day`],
    ["", ""],
    ["**Your remaining hours**", `${myRemaining}h`],
    ["**Days remaining**", `~${daysRemaining} days`],
    ["**Required pace**", `**${reqPace}h/day**`],
    ["", ""],
    ["**Other owners' remaining**", `${othersRemaining}h`],
    ["", ""],
    ["**Status**", paceStatus],
  ]
);
```

---

### What's Left

```dataview
TABLE WITHOUT ID
  file.link AS "Feature",
  phase AS "Phase",
  status AS "Status",
  cost_nok AS "Cost",
  hours AS "Hours",
  owner AS "Owner"
FROM "Project Roadmap/03 - Feature Specifications" OR "Project Roadmap/04 - Operations"
WHERE type = "feature" AND status != "Done"
SORT cost_nok DESC
```
