---
name: ai-team-leader-report
description: Generates a professional, interactive Bosch-branded shift report for Team Leaders using production data from MES. Features 72-hour interactive sliding window, visual KPI analytics (OEE, FPY), and AI-generated corrective actions.
version: 3.0
author: Gemini CLI / AI Associate Team
requirements:
  - Data Provider: Access to MES PdaMda API (e.g., GetShiftbookData, GetInterruptions endpoints)
  - Design Tokens: Bosch brand guidelines (fonts: Bosch Sans, colors: Bosch Blue #005691)
---

# AI Team Leader Report Generator (Universal v3.0)

## Overview
This skill guides any AI agent (Claude, Gemini, Codex) to orchestrate the creation of a production-grade shift analysis dashboard. It abstracts platform-specific tool names into primitive actions: **Read**, **Write**, **Bash**, and **HttpCall**.

## 1. Prerequisites (Capability Check)
Before starting, ensure you have access to:
1.  **MES API Provider**: A way to query production data for the target line (e.g., `l1092`).
2.  **Report Template**: A file named `template.html` located in the same directory as this skill.

## 2. Execution Protocol

### Step A: Data Discovery
1.  **Retrieve Recent Production History**:
    - Use your **HttpCall** or **Bash** capability to fetch the last 72 hours of production records (`GetShiftbookData`) for the target line.
    - Identify the timestamp of the *last active shift* with data.
2.  **Fetch Interruptions**:
    - Fetch downtime/interruption events (`GetInterruptions`) for the same 72-hour window.

### Step B: Data Synthesis & Schema Mapping
Transform the raw API response into a JSON array that matches the following contract:

```json
[
  {
    "shiftName": "YYYY-MM-DD (Morning|Afternoon|Night)",
    "oee": "float (0-100)",
    "output": "integer (Good parts)",
    "scrapRate": "float (0-100)",
    "partTypes": [
      { "name": "Part Number", "io": "integer", "nio": "integer" }
    ],
    "interruptions": [
      { "type": "String", "duration": "integer (min)", "desc": "String" }
    ],
    "actions": [
      { "deviation": "KPI alert", "reaction": "Suggested corrective step", "owner": "Role" }
    ]
  }
]
```
**AI Reasoning Logic**:
- If `oee < 90%` -> Add action item for "Performance Deviation".
- If `scrapRate > 5%` -> Add action item for "Quality Deviation".

### Step C: UI Assembly
1.  **Read** the `template.html` asset from this skill's directory.
2.  **Write** the final report:
    - Replace the marker `/* INJECT_DATA_HERE */ []` in the template with your generated JSON array (sorted descending by date).
    - Save the output to a file named `product/shift-report-v3.html` (or a location requested by the user).

## 3. Completion
- Confirm the file path of the generated report.
- Do not output the raw HTML or JSON in the chat unless specifically asked.
