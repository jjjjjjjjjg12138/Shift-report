# AI Team Leader Report Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Create a reusable `ai-team-leader-report` skill that enables any AI agent to fetch production data via the MES API, apply Bosch branding, and generate an interactive HTML and Markdown shift report.

**Architecture:**
The skill will consist of a `SKILL.md` instruction file and an HTML template asset. 
1. The `SKILL.md` will instruct the agent to use the `mes-pdamda-api` skill to fetch the last 3 days of OEE, Output, and Shiftbook data.
2. The agent will format this data into a structured Markdown report (`.md`).
3. The agent will inject this data (as a JSON object) into a pre-built HTML template.
4. The HTML template will contain Alpine.js or vanilla JS to drive a time-slider (allowing users to scrub through the last 3 days of shifts) and will include Bosch brand design tokens via the `bosch-brand-guidelines` skill.

**Tech Stack:** Markdown, HTML/CSS/JS (Vanilla), TailwindCSS (Bosch Brand Guidelines), MES PdaMda REST API.

---

### Task 1: Create Skill Directory and HTML Template

**Files:**
- Create: `.agents/skills/ai-team-leader-report/template.html`

- [ ] **Step 1: Create the skill directory**
```bash
mkdir -p .agents/skills/ai-team-leader-report
```

- [ ] **Step 2: Create the interactive HTML template**
Create an HTML file that includes a range slider for time selection and placeholders for Bosch branding and JSON data injection.

```html
<!-- .agents/skills/ai-team-leader-report/template.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Team Leader - Shift Report</title>
    <!-- Bosch Brand Guidelines injected here -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Placeholder for Bosch specific typography and colors */
        :root { --bosch-blue: #005691; --bosch-light-blue: #008ECF; }
        body { font-family: 'Bosch Sans', sans-serif; }
    </style>
</head>
<body class="bg-gray-50 text-gray-900 p-8">
    <div class="max-w-5xl mx-auto bg-white shadow-lg rounded-lg overflow-hidden">
        <header class="bg-[#005691] text-white p-6">
            <h1 class="text-2xl font-bold">Team Leader Shift Report</h1>
            <p class="text-sm opacity-80" id="report-date-range"></p>
        </header>
        
        <div class="p-6 border-b border-gray-200 bg-gray-50 flex items-center gap-4">
            <label for="shift-slider" class="font-semibold whitespace-nowrap">Select Time/Shift:</label>
            <input type="range" id="shift-slider" min="0" max="0" value="0" class="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer">
            <span id="slider-label" class="font-bold text-[#008ECF] min-w-[120px]"></span>
        </div>

        <main class="p-6 grid grid-cols-1 md:grid-cols-2 gap-6" id="dashboard-content">
            <!-- Data will be rendered here by JS -->
        </main>
    </div>

    <script>
        // Data injected by the AI Agent during generation
        const reportData = /* INJECT_DATA_HERE */ []; 
        
        const slider = document.getElementById('shift-slider');
        const sliderLabel = document.getElementById('slider-label');
        const content = document.getElementById('dashboard-content');

        if (reportData && reportData.length > 0) {
            slider.max = reportData.length - 1;
            slider.value = reportData.length - 1; // Default to latest
            
            function renderShift(index) {
                const data = reportData[index];
                sliderLabel.textContent = data.shiftName;
                
                content.innerHTML = `
                    <div class="bg-blue-50 p-4 rounded border border-blue-100 shadow-sm">
                        <h2 class="text-xl font-bold text-[#005691] mb-2">KPIs</h2>
                        <p class="text-lg"><strong>OEE:</strong> ${data.oee}%</p>
                        <p class="text-lg"><strong>Output:</strong> ${data.output} pcs</p>
                        <p class="text-lg"><strong>Scrap Rate:</strong> ${data.scrapRate}%</p>
                    </div>
                    <div class="bg-gray-50 p-4 rounded border border-gray-200 shadow-sm">
                        <h2 class="text-xl font-bold text-gray-700 mb-2">Logbook Events</h2>
                        <ul class="list-disc pl-5 space-y-1">
                            ${data.events.map(e => `<li>${e}</li>`).join('')}
                        </ul>
                    </div>
                `;
            }

            slider.addEventListener('input', (e) => renderShift(e.target.value));
            renderShift(slider.value);
        } else {
            content.innerHTML = '<p class="text-red-500">No data available.</p>';
        }
    </script>
</body>
</html>
```

### Task 2: Create the SKILL.md Instructions

**Files:**
- Create: `.agents/skills/ai-team-leader-report/SKILL.md`

- [ ] **Step 1: Write the SKILL.md file**
Define the triggers, required context, and exact execution steps for the agent.

```markdown
<!-- .agents/skills/ai-team-leader-report/SKILL.md -->
---
name: ai-team-leader-report
description: Generates a comprehensive, interactive HTML and Markdown shift report for Team Leaders using MES data and Bosch branding. Triggered by user requests for shift reports or scheduled routines.
---

# AI Team Leader Report Generator

## Overview
This skill orchestrates the generation of an interactive Team Leader shift report. It coordinates data retrieval via `mes-pdamda-api` and styling via `bosch-brand-guidelines`.

## Execution Steps

When a user requests a shift report, or when executing a scheduled reporting routine, follow these steps EXACTLY:

1. **Determine Time Range**: 
   - First, invoke the `mes-pdamda-api` skill to query the most recent production data or shift record for the target line (e.g., l1092) to identify the timestamp of the *last active shift*. (Do not assume the current time is the end of the range, as the line might have been inactive).
   - Then, calculate the date range starting from 3 days prior to that last active shift timestamp.
2. **Fetch Data**: 
   - Invoke the `mes-pdamda-api` skill to fetch data for the calculated 3-day range.
   - Extract OEE, Output (pcs), Scrap Rate, and Shift Logbook events, grouped chronologically by Shift/Hour.
3. **Format Markdown Report**:
   - Synthesize the latest shift's data into a concise Markdown summary.
   - Save as `product/shift-report-YYYYMMDD.md`.
4. **Generate Interactive HTML Report**:
   - Read the template file from `.agents/skills/ai-team-leader-report/template.html`.
   - Convert the 3-day data into a JSON array, where each item represents a shift time-slice. Ensure the data structure matches:
     `{ "shiftName": "Apr 16 - Night", "oee": 85.2, "output": 1200, "scrapRate": 1.5, "events": ["Machine stop 10m", "Material refill"] }`
   - Replace the literal string `/* INJECT_DATA_HERE */ []` in the HTML template with your generated JSON array.
   - Invoke the `bosch-brand-guidelines` skill to apply correct Bosch branding (colors, fonts, header styles) to the HTML if the template requires further refinement.
   - Save the final interactive HTML file to `product/shift-report-YYYYMMDD.html`.

## Completion
Once saved, provide the user with the paths to the generated `.md` and `.html` files. Do NOT print the entire HTML content in the chat.
```

### Task 3: Commit the New Skill

**Files:**
- Modify: (git index)

- [ ] **Step 1: Commit the files to version control**
```bash
git add .agents/skills/ai-team-leader-report/
git commit -m "feat: add ai-team-leader-report skill with interactive HTML template"
```