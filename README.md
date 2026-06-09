# ✈ Airline Crew Scheduling Efficiency Analyzer
### Advanced Qlik Sense Dashboard | DGCA Compliance | What-If Analysis

![Qlik Sense](https://img.shields.io/badge/Qlik%20Sense-Desktop-009845?style=flat&logo=qlik)
![Domain](https://img.shields.io/badge/Domain-Aviation%20%7C%20Compliance-003580?style=flat)
![Data](https://img.shields.io/badge/Data-Synthetic%20%7C%2060%20Days-EF9F27?style=flat)
![Sheets](https://img.shields.io/badge/Sheets-5%20Dashboards-1D9E75?style=flat)

---

## Project Overview

A production-grade Qlik Sense dashboard that models airline crew scheduling operations against **DGCA (Directorate General of Civil Aviation)** regulatory compliance for Indian airlines. Built to simulate real-world crew operations across 60 days, 946 flights, 40 crew members, and 4,046 crew assignments.

This project demonstrates advanced Qlik Sense development skills including:
- Complex multi-table data modeling
- Advanced Set Analysis expressions
- Alternate States for what-if scenario analysis
- DGCA regulatory violation detection
- Real-time cost impact calculations

---

## Business Problem

Indian airlines including IndiGo and Air India face recurring DGCA penalties for crew scheduling violations — primarily duty hour breaches, insufficient rest periods, and qualification mismatches. Manual scheduling systems fail to flag violations proactively, resulting in:

- Regulatory fines from DGCA
- Last-minute crew reassignments costing ₹15,000–₹25,000 per incident
- Cascading rest violations when flights are cancelled
- Lack of visibility into which crew members are most at risk

This dashboard addresses all four problems in a single analytical platform.

---

## Dashboard Sheets

### Sheet 1 — Overview
Executive summary of all operations across 60 days.

**Key objects:**
- 10 KPIs with conditional coloring (green/amber/red thresholds)
- Flights by Status bar chart
- Daily avg duty hours line chart with DGCA 14h reference line
- Violation Rate % gauge
- Reassignment cost by reason
- Flights by Aircraft Type donut chart
- Flight Network airport traffic map
- Navigation buttons to all sheets

**Key measures:**
```qlik
// Duty violation rate
Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID) / Count(AssignmentID) * 100

// Cancellation rate
Count({<Status={'Cancelled'}>} FlightID) / Count(FlightID) * 100

// Total reassignment cost
Sum(ReassignmentCostINR)
```

---

### Sheet 2 — DGCA Violations
Compliance audit sheet — core analytical sheet of the project.

**Key objects:**
- Violation heatmap (Pivot Table: CrewName × Date × HasViolation)
- Duty severity treemap
- Rest deficit hours bar chart
- All violations detail table with colored status badges

**Showpiece expression — Heatmap cell coloring:**
```qlik
If(Sum(HasViolation) > 0, RGB(226,75,74), RGB(241,248,244))
```

---

### Sheet 3 — Crew Detail
Individual crew drill-down. Select any crew member to filter all charts.

**Key objects:**
- Crew name filter pane (associative engine demo)
- Rolling 7-day duty hours combo chart
- Shift distribution donut
- Duty severity breakdown
- Assignment detail table with per-column conditional coloring

**Rolling average expression:**
```qlik
RangeAvg(Above(Avg(DutyHours), 0, 7))
```

---

### Sheet 4 — What-If Analysis
Simulates flight cancellation scenarios using Qlik Alternate States.

**Key objects:**
- FlightID filter pane (Cancelled alternate state)
- Crew at risk KPIs
- Cancellation impact table
- Baseline vs emergency cost butterfly chart
- High risk crew by shift Mekko chart

**Alternate States expressions:**
```qlik
// Crew at risk in cancellation scenario
Count({[Cancelled] <CancellationRiskLevel={'High Risk'}>} AssignmentID)

// Emergency re-crewing cost
Sum({[Cancelled]} EmergencyRewCrewCostINR)

// Additional cost vs baseline
Sum({[Cancelled]} EmergencyRewCrewCostINR) - Sum({$} ReassignmentCostINR)
```

---

### Sheet 5 — Cost Analysis
Financial impact analysis with advanced chart types.

**Key objects:**
- Cost vs violations scatter plot (bubble size = total assignments)
- Daily cost trend line chart (reassignment vs emergency)
- Waterfall chart — cumulative cost breakdown
- Duty hours distribution by role (min/avg/max)
- Pivot table — cost by airline × reason
- DGCA regulations reference table with live violation counts

**Advanced scatter plot expression:**
```qlik
// Bubble color by cost tier
If(Sum(ReassignmentCostINR) > 300000, RGB(226,75,74),
   If(Sum(ReassignmentCostINR) > 150000, RGB(239,159,39),
      RGB(29,158,117)))
```

---

## Data Model

Calendar ──── Flights ──── CrewAssignments ──── CrewMaster
│
┌─────┴──────┐
ViolationSummary  WhatIfCancellationImpact
│
RestPeriods (via CrewID)
MonthlyHours (via CrewID)
ConsecutiveDutyCheck (via CrewID)

**Tables:**

| Table | Records | Key Field | Purpose |
|---|---|---|---|
| Flights | 946 | FlightID | Master flight schedule |
| CrewAssignments | 4,046 | AssignmentID | Crew-flight mapping |
| CrewMaster | 40 | CrewID | Crew profiles |
| RestPeriods | 4,006 | CrewID | Rest gap calculation |
| MonthlyHours | ~80 | CrewID | Monthly hour aggregation |
| ViolationSummary | 4,046 | AssignmentID | Violation flags |
| WhatIfCancellationImpact | 4,046 | AssignmentID | Cancellation scenario |
| Calendar | 60 | FlightDate | Time intelligence |
| Regulations | 6 | RegCode | DGCA rules reference |

---

## DGCA Regulations Modeled

| Code | Rule | Limit | Unit |
|---|---|---|---|
| DGCA-FTL-01 | Max Flight Duty Period | 14 | Hours |
| DGCA-FTL-02 | Min Rest Between Duties | 12 | Hours |
| DGCA-FTL-03 | Max Monthly Flight Hours | 100 | Hours |
| DGCA-FTL-04 | Max Weekly Duty Hours | 60 | Hours |
| DGCA-FTL-05 | Max Consecutive Duty Days | 7 | Days |
| DGCA-QUA-01 | Aircraft Type Rating Required | Yes | Boolean |

---

## Advanced Qlik Techniques Used

| Technique | Where Used | Why It Matters |
|---|---|---|
| Set Analysis | All sheets | Isolating violation subsets across linked tables |
| Alternate States | Sheet 4 | Side-by-side what-if scenario comparison |
| RangeAvg / Above() | Sheet 3 | Rolling window calculations |
| Conditional coloring RGB() | All sheets | Visual compliance indicators |
| RESIDENT LOAD | Load script | Multi-pass derived table creation |
| Pivot table heatmap | Sheet 2 | Crew × date violation matrix |
| Scatter plot 3-variable | Sheet 5 | Cost vs violations vs workload |
| Butterfly chart | Sheet 4 | Baseline vs emergency cost comparison |
| Mekko chart | Sheet 4 | Shift × risk level distribution |
| Master Calendar | Load script | Time intelligence across all tables |
| Variable-driven thresholds | Load script | Maintainable DGCA limit references |

---

## Setup Instructions

### Prerequisites
- Qlik Sense Desktop (free) — [Download here](https://www.qlik.com/us/try-or-buy/download-qlik-sense)
- Windows OS

### Steps

**1. Clone or download this repository**
```bash
git clone https://github.com/yourusername/airline-crew-scheduling-analyzer.git
```

**2. Create a Data Connection in Qlik**
- Open Qlik Sense Desktop
- Create new app → Data Load Editor
- Create new connection → Folder → browse to the `data/` folder
- Name the connection: `CrewProject`

**3. Load the script**
- Copy contents of `script/LoadScript.qvs`
- Paste into Data Load Editor
- Click Load Data

**4. Verify data model**
- Open Data Model Viewer
- Confirm all 9 tables are linked with no synthetic keys
- No circular references should appear

**5. Build the dashboard**
- Follow `docs/dashboard_guide.md` for sheet-by-sheet build instructions
- All color expressions and chart configurations are documented

---

## Key Metrics from the Data

| Metric | Value |
|---|---|
| Total Flights | 946 |
| Total Crew Assignments | 4,046 |
| Duty Hour Violations | 316 (7.81%) |
| Rest Period Violations | 40 |
| Total Reassignment Cost | ₹7.71M |
| Total Emergency Cost | ₹41.1M |
| Cancellation Rate | 1.08% |
| Airlines Covered | IndiGo, Air India |
| Aircraft Types | A320, A321, B737, ATR72 |
| Airports Covered | 10 Indian airports |
| Data Period | 60 days (Jan–Feb 2024) |

---

## Color Scheme

| Color | Hex | Usage |
|---|---|---|
| Deep Blue | `#003580` | Headers, titles, navigation |
| Airline Blue | `#0055A4` | Primary metrics, compliant status |
| Green | `#1D9E75` | Compliant, safe, good values |
| Amber | `#EF9F27` | Warning, near-limit values |
| Red | `#E24B4A` | Violations, critical values |
| Grey | `#546E8A` | Neutral buttons, night shift |

---

## Project Context

**Built for:** Portfolio demonstration of advanced Qlik Sense development skills

**Target roles:** Qlik Sense Developer | BI Platform Engineer | BI Administrator

**Domain expertise demonstrated:** Aviation operations, DGCA regulatory compliance, crew scheduling, cost impact analysis

**Data:** Synthetic data generated to simulate real Indian airline operations — no real personal or operational data used
