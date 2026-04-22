---
name: ai-team-leader-report
description: Generates a professional, interactive Bosch-branded shift report for Team Leaders using production data from MES. Features 72-hour interactive sliding window, visual KPI analytics (OEE, FPY), and AI-generated corrective actions. Includes v4.0 Precision Verification Logic.
version: 4.0
author: Gemini CLI / AI Associate Team
requirements:
  - Data Provider: Access to MES PdaMda API (e.g., GetShiftbookData, GetInterruptions endpoints)
  - Design Tokens: Bosch brand guidelines (fonts: Bosch Sans, colors: Bosch Blue #005691)
---

# AI Team Leader Report Generator (v4.0 Precision)

## Overview
This skill guides any AI agent (Claude, Gemini, Codex) to orchestrate the creation of a production-grade shift analysis dashboard with built-in mathematical verification and data anomaly detection.

## 1. Mathematical Standards (Truth Source)
Ensure all KPIs are calculated using these exact Bosch-aligned formulas:
- **Total Parts Produced**: `CountIO + CountNIOSum`
- **FPY (First Pass Yield) %**: `(CountIO / Total Parts Produced) * 100`
- **Scrap Rate %**: `(CountScrap / Total Parts Produced) * 100`
- **Performance %**: `(CountIO / CountTargetOee100) * 100`
- **OEE (Simplified %)**: Same as Performance % (unless multi-factor data is available).

## 2. Execution Protocol

### Step A: Data Discovery
1.  **Retrieve Recent Production History**:
    - Fetch the last 72 hours of production records (`GetShiftbookData`) for the target line.
    - Identify the timestamp of the *last active shift* with data.
2.  **Fetch Interruptions**:
    - Fetch downtime/interruption events (`GetInterruptions`) for the same 72-hour window.

### Step B: Precision Processing & Verification
Before generating the final JSON, perform a **Verification Check**:
1.  **Cross-Sum Check**: Does the sum of `IO` and `NIO` across all `PartTypes` equal the total `Shift IO` and `Shift NIO`?
2.  **Anomaly Detection**:
    - If `OEE > 110%` -> Mark as "Data Anomaly: Potential Target Misconfiguration".
    - If `Target == 0` -> Mark KPI as "No Target Data".
    - If `Total Parts == 0` -> Mark KPI as "Downtime / No Production".

### Step C: JSON Schema Mapping
```json
[
  {
    "shiftName": "YYYY-MM-DD (Morning|Afternoon|Night)",
    "verificationStatus": "Verified by AI | Data Anomaly | Incomplete Data",
    "oee": "float",
    "oeeTarget": "float",
    "output": "integer",
    "scrapRate": "float",
    "fpy": "float",
    "partTypes": [
      { "name": "Part Number", "io": "integer", "nio": "integer", "fpy": "float" }
    ],
    "interruptions": [
      { "type": "String", "duration": "integer (min)", "desc": "String" }
    ],
    "actions": [
      { "deviation": "String", "reaction": "String", "owner": "String" }
    ]
  }
]
```

### Step D: UI Assembly
1.  **Read** the `template.html` asset from this skill's directory.
2.  **Write** the final report:
    - Replace the marker `/* INJECT_DATA_HERE */ []` in the template with your verified JSON array.
    - Save the output to `product/shift-report-v4.html`.

## 3. Completion
- Confirm the file path.
- State whether any data anomalies were detected during the verification step.
