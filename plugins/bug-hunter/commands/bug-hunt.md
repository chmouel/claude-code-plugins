---
description: Guided bug hunting with codebase exploration, root cause analysis, and fix validation
argument-hint: Optional bug description
---

# Bug Hunting

You are helping a developer find and fix a bug. Follow a systematic approach: understand the bug report, explore the codebase, find the root cause, design a fix, implement it, and validate.

## Core Principles

- **Ask clarifying questions**: Identify ambiguities in the bug report. Ask for steps to reproduce, expected vs. actual behavior, and any relevant logs or error messages.
- **Understand before acting**: Read and comprehend existing code before attempting a fix.
- **Read files identified by agents**: Use agents to find important files, then read them to build context.
- **Simple and safe fixes**: Prioritize fixes that are easy to understand and have minimal side effects.
- **Use TodoWrite**: Track all progress throughout.

---

## Phase 1: Bug Report Analysis

**Goal**: Understand the bug completely

Initial request: $ARGUMENTS

**Actions**:
1. Create a todo list with all phases.
2. If the bug report is unclear, ask the user for:
   - Steps to reproduce the bug.
   - What was the expected behavior?
   - What was the actual behavior?
   - Are there any error messages, logs, or screenshots?
3. Summarize your understanding of the bug and confirm with the user.

---

## Phase 2: Codebase Exploration

**Goal**: Understand the relevant parts of the code

**Actions**:
1. Launch 2-3 `code-explorer` agents in parallel. Each agent should:
   - Trace through the code related to the bug report.
   - Target different aspects (e.g., the specific feature, error handling, data flow).
   - Return a list of 5-10 key files to read.

   **Example agent prompts**:
   - "Explore the code related to [feature with bug] to understand its implementation."
   - "Analyze the error handling and logging in the area of [bug location]."
   - "Trace the data flow for [action that causes bug]."

2. Once the agents return, read all identified files to build a deep understanding.
3. Present a summary of your findings.

---

## Phase 3: Root Cause Analysis

**Goal**: Pinpoint the exact cause of the bug

**Actions**:
1. Launch 2-3 `root-cause-analyzer` agents in parallel. Each agent should:
   - Form a hypothesis about the bug's cause.
   - Dig through the code, logs, and your understanding to prove or disprove the hypothesis.
   - Provide a clear explanation of the root cause with evidence (file paths, line numbers).

   **Example agent prompts**:
   - "Hypothesis: The bug is caused by a race condition in [file.js]. Investigate the async operations."
   - "Hypothesis: A null check is missing in [function]. Analyze the function's inputs and call sites."
   - "Hypothesis: The configuration for [module] is incorrect. Examine the config files and how they are used."

2. Review the analyses and identify the most likely root cause.
3. Present the root cause to the user and get their confirmation.

---

## Phase 4: Solution Design

**Goal**: Propose a clean and effective fix

**Actions**:
1. Launch 1-2 `bug-fixer` agents. Each agent should:
   - Propose a specific, minimal code change to fix the root cause.
   - Explain why the fix works and consider potential side effects.
   - Provide the exact code to be changed.

2. Review the proposed fixes.
3. Present the best fix to the user, explaining the change and its implications.
4. **Ask the user for approval before implementing.**

---

## Phase 5: Implementation

**Goal**: Apply the fix to the code

**DO NOT START WITHOUT USER APPROVAL**

**Actions**:
1. Wait for explicit user approval.
2. Read all relevant files identified in previous phases.
3. Implement the chosen fix.
4. Follow codebase conventions strictly.
5. Update todos as you progress.

---

## Phase 6: Validation

**Goal**: Ensure the fix works and doesn't introduce new bugs

**Actions**:
1. Launch 2-3 `fix-validator` agents in parallel with different focuses:
   - "Does the fix actually resolve the original bug? Try to find edge cases where it might fail."
   - "Does the fix introduce any regressions or new bugs in related areas?"
   - "Does the fix adhere to project conventions and good practices?"
2. Recommend writing a test case that reproduces the original bug and confirms the fix.
3. Consolidate findings and present them to the user.

---

## Phase 7: Summary

**Goal**: Document what was accomplished

**Actions**:
1. Mark all todos complete.
2. Summarize:
   - The bug and its root cause.
   - The fix that was implemented.
   - Files modified.
   - Suggested next steps (e.g., deployment, further testing).

---
