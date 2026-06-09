# Dashboard Build Guide
## Airline Crew Scheduling Efficiency Analyzer

This document covers every chart, expression, and color setting for all 5 sheets.

---

## Global Color Reference

| Color | Hex | RGB | Use |
|---|---|---|---|
| Deep Blue | #003580 | RGB(0,53,128) | Headers, titles |
| Airline Blue | #0055A4 | RGB(0,85,164) | Primary charts |
| Green | #1D9E75 | RGB(29,158,117) | Compliant |
| Amber | #EF9F27 | RGB(239,159,39) | Warning |
| Red | #E24B4A | RGB(226,75,74) | Violation |
| Grey | #546E8A | RGB(84,110,138) | Neutral |

---

## Global KPI Color Logic

All KPIs use this pattern:
- Background color expression → `RGB()` function
- Value color → `'#FFFFFF'` (white string in quotes)
- Title color → `'#FFFFFF'`

---

## Sheet 1: Overview

### Filter Pane
Dimensions: Airline, FlightDate, AircraftType, Status
Place as horizontal strip below title bar.

### KPI 1 — Total Flights
```
Measure: Count(FlightID)
Background: RGB(0, 85, 164)
Value color: '#FFFFFF'
```

### KPI 2 — Total Assignments
```
Measure: Count(AssignmentID)
Background: RGB(0, 85, 164)
Value color: '#FFFFFF'
```

### KPI 3 — Total Active Crew
```
Measure: Count(DISTINCT CrewID)
Background: RGB(29, 158, 117)
Value color: '#FFFFFF'
```

### KPI 4 — Total Delayed Flights
```
Measure: Count({<IsDelayed={1}>} FlightID)
Background: If(Count({<IsDelayed={1}>} FlightID) > 0, RGB(239,159,39), RGB(29,158,117))
Value color: '#FFFFFF'
```

### KPI 5 — Total Delay Hours
```
Measure: Sum(DelayMinutes)/60
Background: If(Sum(DelayMinutes)/60 > 200, RGB(226,75,74), If(Sum(DelayMinutes)/60 > 50, RGB(239,159,39), RGB(29,158,117)))
Value color: '#FFFFFF'
```

### KPI 6 — Duty Violations
```
Measure: Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID)
Background: If(Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID) > 0, RGB(226,75,74), RGB(29,158,117))
Value color: '#FFFFFF'
```

### KPI 7 — Rest Violations
```
Measure: Count({<RestViolation={'Yes'}>} CrewID)
Background: If(Count({<RestViolation={'Yes'}>} CrewID) > 0, RGB(226,75,74), RGB(29,158,117))
Value color: '#FFFFFF'
```

### KPI 8 — Qualification Mismatches
```
Measure: Count({<QualificationMatch={'No'}>} AssignmentID)
Background: If(Count({<QualificationMatch={'No'}>} AssignmentID) > 0, RGB(226,75,74), RGB(29,158,117))
Value color: '#FFFFFF'
```

### KPI 9 — Reassignment Cost
```
Measure: Sum(ReassignmentCostINR)
Background: If(Sum(ReassignmentCostINR) > 5000000, RGB(226,75,74), If(Sum(ReassignmentCostINR) > 2000000, RGB(239,159,39), RGB(29,158,117)))
Value color: '#FFFFFF'
```

### KPI 10 — Cancellation Rate %
```
Measure: Count({<Status={'Cancelled'}>} FlightID) / Count(FlightID) * 100
Background: If(Count({<Status={'Cancelled'}>} FlightID)/Count(FlightID)*100 > 10, RGB(226,75,74), If(Count({<Status={'Cancelled'}>} FlightID)/Count(FlightID)*100 > 5, RGB(239,159,39), RGB(29,158,117)))
Value color: '#FFFFFF'
```

### Bar Chart — Flights by Status
```
Dimension: Status
Measure: Count(FlightID)
Color expression: If(Status='Completed', RGB(29,158,117), If(Status='Delayed', RGB(239,159,39), If(Status='Cancelled', RGB(226,75,74), RGB(55,138,221))))
```

### Line Chart — Daily Avg Duty Hours
```
Dimension: AssignmentDate
Measure: Avg(DutyHours)
Line color: If(Avg(DutyHours)>14, RGB(226,75,74), If(Avg(DutyHours)>12, RGB(239,159,39), RGB(0,85,164)))
Reference line: 14 | Label: DGCA Limit | Color: #E24B4A
```

### Gauge — Violation Rate %
```
Measure: Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID) / Count(AssignmentID) * 100
Zone 0-5: #1D9E75
Zone 5-10: #EF9F27
Zone 10-100: #E24B4A
```

### Bar Chart — Reassignment Cost by Reason
```
Dimension: ReassignmentReason
Measure: Sum({<IsReassignment={'Yes'}>} ReassignmentCostINR)
Color: If(ReassignmentReason='Sick Leave', RGB(226,75,74), If(ReassignmentReason='Operational', RGB(239,159,39), RGB(0,85,164)))
```

### Donut Chart — Flights by Aircraft Type
```
Dimension: AircraftType
Measure: Count(FlightID)
Color: If(AircraftType='A320', RGB(0,85,164), If(AircraftType='A321', RGB(29,158,117), If(AircraftType='B737', RGB(239,159,39), RGB(226,75,74))))
```

### Map — Airport Traffic
```
Dimension: AirportCode
Latitude: Latitude
Longitude: Longitude
Size measure: Count(FlightID)
Color: If(Count(FlightID)>20, RGB(226,75,74), If(Count(FlightID)>10, RGB(239,159,39), RGB(0,85,164)))
Base map: Dark
```

---

## Sheet 2: DGCA Violations

### KPI — Duty Violation Rate %
```
Measure: Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID) / Count(AssignmentID) * 100
Background: If(...>10, RGB(226,75,74), If(...>5, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Rest Violations
```
Measure: Count({<RestViolation={'Yes'}>} CrewID)
Background: If(Count({<RestViolation={'Yes'}>} CrewID)>0, RGB(226,75,74), RGB(29,158,117))
```

### KPI — Qualification Mismatches
```
Measure: Count({<QualificationMatch={'No'}>} AssignmentID)
Background: If(Count({<QualificationMatch={'No'}>} AssignmentID)>0, RGB(226,75,74), RGB(29,158,117))
```

### KPI — Avg Excess Duty Hours
```
Measure: Avg({<DutyHourStatus={'VIOLATION'}>} DutyExcessHours)
Background: If(Avg({<DutyHourStatus={'VIOLATION'}>} DutyExcessHours)>2, RGB(226,75,74), If(...>1, RGB(239,159,39), RGB(29,158,117)))
```

### Pivot Table — Violation Heatmap
```
Row dimension: AssignedCrewName
Column dimension: Date(AssignmentDate,'MMM DD')
Measure: Sum(HasViolation)
Background color: If(Sum(HasViolation)>0, RGB(226,75,74), RGB(241,248,244))
Text color: If(Sum(HasViolation)>0, RGB(255,255,255), RGB(100,120,100))
```

### Treemap — Duty Severity
```
Dimension: DutySeverity
Measure: Count(AssignmentID)
Color: If(DutySeverity='CRITICAL VIOLATION (>16h)', RGB(156,0,0), If(DutySeverity='VIOLATION (<16h)', RGB(226,75,74), If(DutySeverity='Near Limit (12-14h)', RGB(239,159,39), If(DutySeverity='Elevated (10-12h)', RGB(255,200,50), RGB(29,158,117)))))
```

### Bar Chart — Rest Deficit by Crew
```
Dimension: AssignedCrewName
Measure: Sum({<RestViolation={'Yes'}>} RestDeficitHours)
Color: If(Sum({<RestViolation={'Yes'}>} RestDeficitHours)>5, RGB(226,75,74), If(...>2, RGB(239,159,39), RGB(29,158,117)))
```

### Violations Detail Table
```
Columns: AssignedCrewName | FlightID | AssignmentDate | DutyHours | DutyHourStatus | RestViolation | IsReassignment
DutyHours BG: If(DutyHours>14, RGB(226,75,74), If(DutyHours>12, RGB(239,159,39), RGB(241,248,244)))
DutyHourStatus BG: If(DutyHourStatus='VIOLATION', RGB(226,75,74), RGB(29,158,117))
RestViolation BG: If(RestViolation='Yes', RGB(226,75,74), RGB(29,158,117))
All text color on colored cells: RGB(255,255,255)
```

---

## Sheet 3: Crew Detail

### Filter Pane
```
Dimension: AssignedCrewName
Header background: #003580
Selected value: #0055A4
```

### Combo Chart — Rolling 7-Day Duty Hours
```
Dimension: Date(AssignmentDate,'DD MMM')
Bar measure: Avg(DutyHours) | Color: RGB(0,85,164)
Line measure: RangeAvg(Above(Avg(DutyHours),0,7)) | Color: RGB(239,159,39)
Reference line: 14 | Color: #E24B4A | Label: DGCA Limit
```

### Donut — Shift Distribution
```
Dimension: ShiftType
Measure: Count(AssignmentID)
Color: If(ShiftType='Morning', RGB(0,85,164), If(ShiftType='Evening', RGB(239,159,39), RGB(83,110,138)))
```

### Bar — Duty Severity by Role
```
Dimension: AssignedRole
Measure 1: Avg(DutyHours) | Color: #0055A4
Measure 2: Max(DutyHours) | Color: #E24B4A
Measure 3: Min(DutyHours) | Color: #1D9E75
Reference line: 14 | Color: #E24B4A
```

### Assignment Detail Table
```
Columns: FlightID | Route (Origin&'→'&Destination) | ReportTime | DutyHours | DutyHourStatus | IsReassignment | ShiftType
DutyHours BG: If(DutyHours>14, RGB(226,75,74), If(DutyHours>12, RGB(239,159,39), RGB(241,248,244)))
DutyHourStatus BG: If(DutyHourStatus='VIOLATION', RGB(226,75,74), RGB(29,158,117))
IsReassignment BG: If(IsReassignment='Yes', RGB(239,159,39), RGB(241,248,244))
ShiftType BG: If(ShiftType='Morning', RGB(0,85,164), If(ShiftType='Evening', RGB(239,159,39), RGB(83,110,138)))
All colored cells text: RGB(255,255,255)
```

---

## Sheet 4: What-If Analysis

### Setup Alternate States
```
App settings → Alternate States → Add: 'Cancelled' and 'Baseline'
FlightID filter pane → State: Cancelled
All other objects → Default state
```

### KPI — Crew at Risk
```
Measure: Count({[Cancelled] <CancellationRiskLevel={'High Risk'}>} AssignmentID)
Background: If(...>5, RGB(226,75,74), If(...>0, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Emergency Cost
```
Measure: Sum({[Cancelled]} EmergencyRewCrewCostINR)
Background: If(Sum({[Cancelled]} EmergencyRewCrewCostINR)>100000, RGB(226,75,74), If(...>50000, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Rest Violations Triggered
```
Measure: Count({[Cancelled] <RestViolation={'Yes'}>} CrewID)
Background: If(Count({[Cancelled] <RestViolation={'Yes'}>} CrewID)>0, RGB(226,75,74), RGB(29,158,117))
```

### KPI — Additional Cost vs Baseline
```
Measure: Sum({[Cancelled]} EmergencyRewCrewCostINR) - Sum({$} ReassignmentCostINR)
Background: If(Sum({[Cancelled]} EmergencyRewCrewCostINR)-Sum({$} ReassignmentCostINR)>0, RGB(226,75,74), RGB(29,158,117))
```

### Impact Table
```
Columns: AssignmentID | AssignedCrewName | CancellationRiskLevel | EmergencyRewCrewCostINR | DutyHours | RestViolation
CancellationRiskLevel BG: If(CancellationRiskLevel='High Risk', RGB(226,75,74), RGB(29,158,117))
EmergencyRewCrewCostINR BG: If(Sum({[Cancelled]} EmergencyRewCrewCostINR)>20000, RGB(226,75,74), RGB(239,159,39))
DutyHours BG: If(DutyHours>14, RGB(226,75,74), If(DutyHours>12, RGB(239,159,39), RGB(241,248,244)))
RestViolation BG: If(RestViolation='Yes', RGB(226,75,74), RGB(29,158,117))
```

### Butterfly Chart — Baseline vs Emergency
```
Dimension: Date(AssignmentDate,'DD MMM')
Measure 1: Sum({$} ReassignmentCostINR) | Label: Baseline Cost
Measure 2: Sum({[Cancelled]} EmergencyRewCrewCostINR) | Label: Emergency Cost
Colors: By measure → Diverging gradient (red-blue scheme)
```

### Mekko Chart — High Risk by Shift
```
Dimension 1: ShiftType
Dimension 2: CancellationRiskLevel
Measure: Count({[Cancelled]} AssignmentID)
Color: If(CancellationRiskLevel='High Risk', RGB(226,75,74), RGB(29,158,117))
```

---

## Sheet 5: Cost Analysis

### KPI — Total Reassignment Cost
```
Measure: Sum(ReassignmentCostINR)
Background: If(Sum(ReassignmentCostINR)>5000000, RGB(226,75,74), If(...>2000000, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Total Emergency Cost
```
Measure: Sum(EmergencyRewCrewCostINR)
Background: If(Sum(EmergencyRewCrewCostINR)>10000000, RGB(226,75,74), If(...>5000000, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Total Reassignments
```
Measure: Count({<IsReassignment={'Yes'}>} AssignmentID)
Background: If(Count({<IsReassignment={'Yes'}>} AssignmentID)>500, RGB(226,75,74), If(...>200, RGB(239,159,39), RGB(29,158,117)))
```

### KPI — Avg Cost Per Reassignment
```
Measure: Sum(ReassignmentCostINR) / Count({<IsReassignment={'Yes'}>} AssignmentID)
Background: RGB(0,85,164)
```

### Scatter Plot — Cost vs Violations
```
Dimension: AssignedCrewName
X-axis: Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID)
Y-axis: Sum(ReassignmentCostINR)
Bubble size: Count(AssignmentID)
Color: If(Sum(ReassignmentCostINR)>300000, RGB(226,75,74), If(...>150000, RGB(239,159,39), RGB(29,158,117)))
```

### Line Chart — Daily Cost Trend
```
Dimension: AssignmentDate
Measure 1: Sum(ReassignmentCostINR) | Color: #0055A4
Measure 2: Sum(EmergencyRewCrewCostINR) | Color: #E24B4A
```

### Waterfall Chart
```
Measure 1: Sum({<ReassignmentReason={'Operational'}>} ReassignmentCostINR) | Label: Operational
Measure 2: Sum({<ReassignmentReason={'Sick Leave'}>} ReassignmentCostINR) | Label: Sick Leave
Measure 3: Sum({<ReassignmentReason={'Crew Request'}>} ReassignmentCostINR) | Label: Crew Request
Measure 4: Sum(ReassignmentCostINR) | Label: Total | Mark as Subtotal
Positive color: #0055A4
Subtotal color: #003580
```

### Bar — Duty Hours Distribution by Role
```
Dimension: AssignedRole
Measure 1: Avg(DutyHours) | Label: Avg | Color: #0055A4
Measure 2: Max(DutyHours) | Label: Max | Color: #E24B4A
Measure 3: Min(DutyHours) | Label: Min | Color: #1D9E75
Reference line: 14 | Label: DGCA Limit | Color: #E24B4A
```

### Pivot Table — Cost by Airline × Reason
```
Row: Airline
Column: ReassignmentReason
Measure: Sum(ReassignmentCostINR)
BG color: If(Sum(ReassignmentCostINR)>1400000, RGB(226,75,74), If(...>1000000, RGB(239,159,39), RGB(0,85,164)))
Text color: RGB(255,255,255)
Enable row and column totals
```

### DGCA Regulations Table
```
Columns: RegCode | Description | LimitValue | Unit | AppliesTo | Live Violation Count

RegCode BG: RGB(0,53,128) | Text: RGB(255,255,255)
LimitValue BG: If(LimitValue<=12, RGB(226,75,74), If(LimitValue<=60, RGB(239,159,39), RGB(29,158,117)))
AppliesTo BG: If(AppliesTo='All Crew', RGB(0,85,164), RGB(29,158,117))
Live Violation Count expression:
  If(RegCode='DGCA-FTL-01', Count({<DutyHourStatus={'VIOLATION'}>} AssignmentID),
     If(RegCode='DGCA-FTL-02', Count({<RestViolation={'Yes'}>} CrewID), 0))
Live Count BG: If(value>0, RGB(226,75,74), RGB(29,158,117))
All text on colored: RGB(255,255,255)
```
