# Optimization Plan: AI Team Leader Report (v2.0)

## 1. Objective
Transform the current "Simplified" shift report into a professional-grade, interactive dashboard that matches the depth and visual quality of the `shift-report-l1092-night-apr16.html` reference.

## 2. Gap Analysis (v1.0 vs. Reference)
| Feature | v1.0 (Current) | Reference Sample | Gap |
| :--- | :--- | :--- | :--- |
| **Visuals** | Static cards, text-only | Chart.js (Stacked bars, Donut charts) | High - Missing visual analytics |
| **Granularity** | Shift-level summary | Output by Part Type + Interruption Logs | High - Missing diagnostic detail |
| **Business Value** | KPI observation only | Quick Reaction / Action Items | Critical - Missing "Closing the Loop" |
| **Branding** | Basic blue header | Bosch Gradient Banner + Bosch Sans | Medium - Visual polish |

## 3. Implementation Steps

### Phase 1: Data Enrichment (Backend Logic)
*   **Split by Type**: Modify the data processing script to group output by `TypeNumber` (Part Number).
*   **Fetch Interruptions**: Call `GET /Shiftbook/GetInterruptions` for the 3-day window to populate the "Interruption Log".
*   **KPI Thresholds**: Define OK/Warn/Bad thresholds:
    *   OEE: < 85% (Bad), 85-90% (Warn), > 90% (OK).
    *   FPY: < 95% (Bad).

### Phase 2: Template Upgrade (`template.html`)
*   **Layout**: Switch to a 3-column summary row (Output, FPY, Performance) with dynamic top-border colors.
*   **Charts**: 
    *   Inject `Chart.js` via CDN.
    *   Add a `stacked bar chart` for Good vs. Reject output by part type.
    *   Add a `donut chart` for Loss Breakdown (Technical vs. Organizational vs. Changeover).
*   **Interruption Table**: Add a scrolling table for the last 10 downtime events.
*   **Action Section**: Add a "Quick Reaction" table with AI-generated placeholders based on identified deviations.

### Phase 3: Brand & UX Refinement
*   **Header**: Implement the `linear-gradient(90deg, #007bc0 0%, #005f96 40%, #003c6e 100%)` top bar.
*   **Typography**: Ensure `BoschSans-Regular` and `BoschSans-Bold` are correctly mapped from the brand CDN.
*   **Responsive**: Ensure the dashboard remains readable on mobile devices (Team Leaders on the shop floor).

## 4. Execution Plan
1.  **Update `template.html`**: Rewrite the HTML/CSS/JS structure based on the reference.
2.  **Update `SKILL.md`**: Enhance the instructions to handle multi-endpoint data fetching and JSON merging.
3.  **Test Run**: Generate `playground/optimized-report-v2.html` using the new logic.
4.  **Review**: Compare output with the reference and finalize.

---
**Status**: Drafted | **Owner**: Gemini CLI | **Date**: 2026-04-22
