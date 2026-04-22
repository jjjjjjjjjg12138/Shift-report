# Optimization Plan: MOE3.2 Operational Alignment (v5.0)

## 1. Objective
Refine the `ai-team-leader-report` skill to directly support the manual/semi-automated tasks identified in the `MOE3.2.xlsx` operational matrix. The goal is to maximize the ROI for the Team Leader by automating the "Shift Handover" and "Loss Analysis" routines.

## 2. Key Alignments with MOE3.2

### A. Loss Source Analysis (MOE3.2 #1 & #2)
*   **Target**: Answer "Where did the OEE/Output losses come from?"
*   **Implementation**: 
    *   Deep-parse `GetInterruptions` data.
    *   Aggregate downtime by `TypeText` and `Class`.
    *   **New Feature**: Display the "Top 3 Downtime Reasons" with their cumulative impact in minutes.

### B. Shift Handover Automation (MOE3.2 #8)
*   **Target**: Auto-generate the "交接本" (Handover Log) draft.
*   **Implementation**: 
    *   Create a dedicated **"Shift Handover Draft"** text block in the UI.
    *   Pre-fill: Output stats, significant equipment anomalies (from Interruption log), and quality trends.
    *   AI Prompting: Summarize the shift status into a 3-bullet point "TL-to-TL" message.

### C. FPY & Quality Detail (MOE3.2 #9)
*   **Target**: Automate FPY registration.
*   **Implementation**: 
    *   Explicitly display FPY % as the primary quality metric (replacing or augmenting Scrap Rate).
    *   Include a "Quality Breakdown" table showing `IO`, `NIO`, and `Scrap` for every active part type.

## 3. Implementation Steps

### Phase 1: Data Logic (SKILL.md)
*   Update the "AI Reasoning Logic" to include a **Handover Summarization Step**.
*   Enhance the "Data Synthesis" section to require ranking of interruptions.

### Phase 2: Template Upgrades (template.html)
*   **New Section**: `Shift Handover Draft` (A "Copy-to-Clipboard" ready text area).
*   **New Chart**: `Loss Pareto` (Top 5 loss categories).
*   **Refined KPIs**: Update labels to match MOE3.2 terminology (e.g., FPY, Output Target).

### Phase 3: Verification
*   Test with `l1092` data to ensure the "Top 3 Reasons" and "Handover Draft" are contextually accurate.

## 4. Execution Plan
1.  **Update `SKILL.md`**: Add MOE3.2 specific reasoning tasks.
2.  **Update `template.html`**: Redesign the layout to include the Handover Preview.
3.  **Validation**: Generate `product/shift-report-moe32-v5.html`.

---
**Status**: Drafted | **Owner**: Gemini CLI | **Date**: 2026-04-22
