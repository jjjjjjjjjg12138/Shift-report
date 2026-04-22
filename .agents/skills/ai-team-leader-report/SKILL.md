---
name: ai-team-leader-report
description: Generates a professional, interactive Bosch-branded shift report for Team Leaders, specifically aligned with MOE3.2 operational requirements. Features 72-hour interactive window, Loss Pareto analysis, and automated Shift Handover drafts.
version: 5.0
author: Gemini CLI / AI Associate Team
requirements:
  - Data Provider: Access to MES PdaMda API (e.g., GetShiftbookData, GetInterruptions endpoints)
  - Design Tokens: Bosch brand guidelines (fonts: Bosch Sans, colors: Bosch Blue #005691)
---

# AI Team Leader Report Generator (v5.0 MOE3.2 Aligned)

## Overview
This skill guides any AI agent to orchestrate the creation of a shift analysis dashboard that directly fulfills MOE3.2 tasks: OEE/Output loss identification (#1, #2), FPY registration (#9), and automated Handover Log generation (#8).

## 1. Mathematical Standards (MOE3.2 & Bosch)
- **Total Parts Produced**: `CountIO + CountNIOSum`
- **FPY (First Pass Yield) %**: `(CountIO / Total Parts Produced) * 100` (MOE3.2 #9)
- **Scrap Rate %**: `(CountScrap / Total Parts Produced) * 100`
- **Performance %**: `(CountIO / CountTargetOee100) * 100` (MOE3.2 #1)

## 2. Execution Protocol

### Step A: Data Discovery
1.  **Retrieve History**: Fetch 72 hours of `GetShiftbookData` and `GetInterruptions` for the target line.
2.  **Identify Last Active Shift**: Locate the most recent data timestamp.

### Step B: MOE3.2 Deep Analysis & Verification
1.  **Loss Pareto Analysis (MOE3.2 #1 & #2)**:
    - Aggregate all interruptions by `TypeText`.
    - Rank top 3 causes by total duration (min).
    - Identify if the losses are Technical, Organizational, or Changeover.
2.  **Shift Handover Generation (MOE3.2 #8)**:
    - Summarize the shift status into a concise "TL-to-TL" message.
    - Include: Total Output vs. Target, major quality issues (FPY), and significant equipment events.
3.  **Precision Check**: Ensure the sum of part types equals the shift total output.

### Step C: JSON Schema Mapping
```json
[
  {
    "shiftName": "YYYY-MM-DD (Morning|Afternoon|Night)",
    "verificationStatus": "Verified by AI | Data Anomaly",
    "oee": "float",
    "oeeTarget": "float",
    "fpy": "float",
    "output": "integer",
    "scrapRate": "float",
    "lossPareto": [
      { "reason": "TypeText", "duration": "integer", "class": "Tech|Org|CO" }
    ],
    "handoverDraft": "A 3-bullet point summary for the TL handover log.",
    "partTypes": [
      { "name": "Part Number", "io": "integer", "nio": "integer", "fpy": "float" }
    ],
    "interruptions": [
      { "type": "String", "duration": "integer", "desc": "String" }
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
    - Replace `/* INJECT_DATA_HERE */ []` with the MOE3.2 enriched JSON.
    - Save to `product/shift-report-moe32-v5.html`.

## 3. Completion
- State the "Top 1 Loss Reason" identified for the latest shift.
- Confirm the handover draft is ready for the Team Leader.
