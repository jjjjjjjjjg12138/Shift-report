# Optimization Plan: Cross-Agent Compatibility (v3.0)

## 1. Objective
Refactor the `ai-team-leader-report` skill to be platform-agnostic, allowing it to be executed by different AI agents (e.g., Gemini CLI, Claude Code, GitHub Copilot/Codex) without being tied to specific Gemini CLI tool naming or internal skill-to-skill invocation mechanisms.

## 2. Key Strategies for Universal Portability

### A. Abstracting Skill-to-Skill Dependencies
*   **Current Issue**: The skill explicitly tells the agent to "Invoke the `mes-pdamda-api` skill."
*   **Optimization**: Define the **Functional Interface** rather than the skill name. Specify that the agent requires a "Data Provider Capability" (HTTP/API access) to specific endpoints (`/Shiftbook/GetShiftbookData`, `/Shiftbook/GetInterruptions`).
*   **Fallback**: Include a `dependencies.md` or a `prerequisites` section in the skill metadata.

### B. Standardizing Tool Primitives
*   **Current Issue**: Relies on the agent's implicit understanding of "Save to..." or "Read...".
*   **Optimization**: Use **Primitive Action Mappings** (Read, Write, Bash, HttpCall). This ensures that if the agent is Claude, it maps "Read" to `read_file`, and if it's Codex, it maps to its respective tool.

### C. Relative Path Management
*   **Current Issue**: Hardcoded paths like `.agents/skills/...` might vary.
*   **Optimization**: Use the `$SKILL_DIR` placeholder or explicitly state that assets are located "within the same directory as this skill file."

### D. Enhanced Instruction Structure (Claude Code/Superpowers Style)
*   Adopt the `--- metadata ---` header used by industry-standard skill systems (like Superpowers).
*   Add **Input/Output Schemas** so different LLMs understand the exact JSON structure required for the template injection.

## 3. Implementation Steps

### Step 1: Metadata & Requirements (v3.0 Template)
*   Add metadata block: `name`, `description`, `version`, `author`, `requirements`.
*   Explicitly list required API permissions (CORS/Auth context for MES).

### Step 2: Protocol-based Instructions
*   Instead of "Invoke [Skill]", use: *"Retrieve production data using the established Data Provider Protocol (e.g., mes-pdamda-api or direct HTTP call)..."*

### Step 3: Explicit Data Contract
*   Define the exact JSON object structure for `/* INJECT_DATA_HERE */` in the `SKILL.md` file to prevent different agents from guessing the schema.

### Step 4: Verification & Portability Test
*   Create a `test-agent-agnostic.md` to simulate how different agents should interpret the instructions.

## 4. Execution Plan
1.  Draft the new `SKILL.md` (v3.0) with the standardized metadata and interface definitions.
2.  Keep `template.html` as the shared asset.
3.  Provide a `package.json`-like manifest (e.g., `skill.json`) if required by certain environments (like GitHub Copilot Extensions).

---
**Status**: Drafted | **Owner**: Gemini CLI | **Date**: 2026-04-22
