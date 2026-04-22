# AI Team Leader Report Generator (v2.0)

## Overview
This skill orchestrates the generation of a professional-grade, interactive Team Leader shift report. It coordinates data retrieval via `mes-pdamda-api` and styling via `bosch-brand-guidelines`.

## Execution Steps

When a user requests a shift report, or when executing a scheduled reporting routine, follow these steps EXACTLY:

1. **Identify Last Active Shift**:
   - Invoke the `mes-pdamda-api` skill to query `GetShiftbookData` for the target line (e.g., l1092).
   - Identify the timestamp of the *last active shift* with data.

2. **Calculate 3-Day Window**:
   - Define a start time 72 hours before the end of that last active shift.

3. **Fetch Multi-Endpoint Data**: 
   - **Shift KPIs**: Call `GetShiftbookData` for the 3-day window.
   - **Interruptions**: Call `GetInterruptions` for the 3-day window.
   - **Hourly Trends**: Call `GetHourlyShiftbookData` (optional, for future trends).

4. **Process Data by Shift & Type**:
   - Group `ShiftbookData` by Shift (Morning/Afternoon/Night) and Date.
   - For each shift, calculate:
     - `output`: Sum of `CountIO`.
     - `scrapRate`: `(Sum of CountNIO / Total) * 100`.
     - `oee`: `(Sum of CountIO / Sum of CountTargetOee100) * 100`.
     - `partTypes`: An array of `{ name: TypeNumber, io: CountIO, nio: CountNIO }`.
     - `interruptions`: Filter `GetInterruptions` results that fall within the shift's `ValidFrom2` and `ValidTo2`. Include `type`, `duration`, and `desc`.
     - `actions`: **AI Reasoner Step**. If `oee < 90%` or `scrapRate > 5%`, generate a `deviation` and a `reaction` (e.g., "Check sensor calibration", "Review startup SOP").

5. **Generate Interactive HTML**:
   - Read the template: `.agents/skills/ai-team-leader-report/template.html`.
   - Replace `/* INJECT_DATA_HERE */ []` with the processed JSON array (sorted by date descending).
   - Save to `product/shift-report-v2.html`.

## Completion
Provide the file path to the user.
