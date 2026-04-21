---
name: ai-team-leader-report
description: Generates a comprehensive, interactive HTML and Markdown shift report for Team Leaders using MES data and Bosch branding. Triggered by user requests for shift reports or scheduled routines.
---

# AI Team Leader Report Generator

## Overview
This skill orchestrates the generation of an interactive Team Leader shift report. It coordinates data retrieval via `mes-pdamda-api` and styling via `bosch-brand-guidelines`.

## Execution Steps

When a user requests a shift report, or when executing a scheduled reporting routine, follow these steps EXACTLY:

1. **Identify Last Active Shift**:
   - Invoke the `mes-pdamda-api` skill to query the most recent production data or shift record for the target line (e.g., l1092).
   - Identify the timestamp of the *last active shift* with data. Do not assume "today" has data yet.

2. **Calculate 3-Day Window**:
   - Define a start time 72 hours before the end of that last active shift.
   - Example: If the last data is from April 16th, the window is April 13th to April 16th.

3. **Fetch Historical Data**: 
   - Invoke the `mes-pdamda-api` skill to fetch detailed OEE, Output (pcs), Scrap Rate, and Shift Logbook events for that 3-day window.
   - Group data chronologically by Shift (Morning, Afternoon, Night).

4. **Format Markdown Report**:
   - Synthesize the *latest* shift's data into a high-level summary.
   - Save to `product/shift-report-latest.md`.

5. **Generate Interactive HTML**:
   - Read the template: `.agents/skills/ai-team-leader-report/template.html`.
   - Prepare a JSON array where each object is: `{ "shiftName": "Date - Shift", "oee": XX, "output": XX, "scrapRate": XX, "events": ["..."] }`.
   - Replace the marker `/* INJECT_DATA_HERE */ []` with the actual JSON string.
   - **Bosch Branding**: Invoke `bosch-brand-guidelines` to ensure the colors, fonts, and header styling match the corporate identity (e.g., using Bosch Sans, the Supergraphic banner, and specific Blue/Gray tones).
   - Save to `product/shift-report-interactive.html`.

## Completion
Provide the file paths to the user. Do not output the raw HTML/JSON in the chat.
