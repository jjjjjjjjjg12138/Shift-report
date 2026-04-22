---
name: ai-team-leader-report
description: Generates a professional, interactive Bosch-branded shift report. Features a STICKY REAL-TIME output monitor for the current shift and a 72-hour historical interactive slider for depth analysis and MOE3.2 compliance.
version: 6.0
author: Gemini CLI / AI Associate Team
requirements:
  - Data Provider: Access to MES PdaMda API (Shiftbook & Interruption endpoints)
  - Design Tokens: Bosch brand guidelines (fonts: Bosch Sans, colors: Bosch Blue #005691)
---

# AI Team Leader Report Generator (v6.0 Live & Historical)

## Overview
This skill orchestrates a "Single Pane of Glass" dashboard. It places the **Live Production Pulse** of the ongoing shift at the top (sticky), while providing a scrollable **Historical Archive** for the past 3 days below.

## 1. Mathematical Standards
- **Total Produced**: `CountIO + CountNIOSum`
- **FPY %**: `(CountIO / Total Produced) * 100`
- **OEE / Performance %**: `(CountIO / CountTargetOee100) * 100`

## 2. Execution Protocol

### Step A: Live Data Discovery (The "Pulse")
1.  **Determine Current Shift**: Identify if the current time falls into Morning (07-15), Afternoon (15-23), or Night (23-07).
2.  **Fetch Live KPIs**: Call `GetShiftbookData` for the target line. Set `start` to the *start of the current shift* and `end` to `Now`.
3.  **Map Live JSON**:
    ```json
    { "shiftName": "Current Shift", "output": 123, "oee": 94.5, "target": 150 }
    ```

### Step B: Historical Data Discovery (The "Archive")
1.  **Fetch History**: Fetch 72 hours of `GetShiftbookData` and `GetInterruptions`.
2.  **Aggregate**: Group by shift and calculate FPY, OEE, and Loss Pareto (Top 3 reasons).
3.  **Draft Handover**: Generate a 3-bullet point summary for each historical shift.

### Step C: UI Assembly
1.  **Read** the `template.html` asset.
2.  **Inject Live Data**: Replace `/* INJECT_LIVE_HERE */ null` with your live JSON object.
3.  **Inject Historical Data**: Replace `/* INJECT_DATA_HERE */ []` with your historical JSON array.
4.  **Write**: Save to `product/shift-report-live-v6.html`.

## 3. Completion
- Confirm the live output and OEE values captured.
- Provide the file path to the user.
