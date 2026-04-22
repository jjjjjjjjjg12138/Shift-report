# Optimization Plan: Precision & Verification (v4.0)

## 1. Objective
Enhance the `ai-team-leader-report` skill with a formal **Verification & Review Mechanism** to ensure that derived KPIs (Scrap Rate, OEE, FPY) are mathematically accurate and aligned with Bosch manufacturing standards before being presented to the Team Leader.

## 2. The "Double-Check" Architecture

### A. Formal KPI Definitions (Truth Source)
*   **Current Issue**: Calculations are implicit in the prompt, leading to potential variance between LLM models.
*   **Optimization**: Explicitly define the mathematical formulas in the skill's `Constants` or `Logic` section:
    *   `Total Parts = CountIO + CountNIOSum`
    *   `FPY (First Pass Yield) % = (CountIO / Total Parts) * 100`
    *   `Scrap Rate % = (CountScrap / Total Parts) * 100`
    *   `Performance % = (CountIO / CountTargetOee100) * 100`

### B. Two-Step Verification Routine
The skill will now follow a **Calculate -> Verify -> Report** flow:
1.  **Phase 1 (Calculate)**: The agent performs the initial aggregation and math.
2.  **Phase 2 (Cross-Verify)**: The agent must perform a "Reverse Check": 
    *   *Check*: Does `CountIO + CountNIO` equal the total parts used in the denominator?
    *   *Check*: Is `Performance %` exceeding 110%? (If yes, flag as potential data anomaly).
    *   *Check*: Compare the calculated `FPY` against the reference range (0-100%).

### C. Data Anomaly Handling
*   If `CountTargetOee100` is 0 or null, the skill must not output "NaN" or "0%"; it must label the KPI as "No Target Data".
*   If `CountIO` is 0 but `ValidTo2` indicates a completed shift, flag as "Potential Downtime/Sensor Error".

## 3. Implementation Steps

### Step 1: Logic Hardening (SKILL.md)
*   Add a mandatory `# Validation Rules` section.
*   Instruct the agent to perform a "Self-Correction" step: *"Before generating the JSON, verify that the sum of part types equals the total shift output."*

### Step 2: UI Transparency
*   In `template.html`, add a small "info icon" or tooltip next to KPIs explaining the formula used (e.g., "Calculated as IO / Total Parts").
*   Add a "Data Status" badge: `Verified by AI` vs `Data Anomaly Detected`.

### Step 3: Audit Log
*   Generate a small `product/report-audit.log` for every report run, showing the raw inputs and the intermediate calculation steps for transparency.

## 4. Execution Plan
1.  **Update `SKILL.md` (v4.0)**: Integrate the validation rules and formula definitions.
2.  **Enhance `template.html`**: Add the "Verification Status" visual indicator.
3.  **Update Processing Script**: Include the cross-verification logic.

---
**Status**: Drafted | **Owner**: Gemini CLI | **Date**: 2026-04-22
