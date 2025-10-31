# Claude Code Plugin Design Guide

A comprehensive guide for designing effective Claude Code plugins, based on real-world experience building the plugins in this repository.

## Table of Contents

- [Plugin Structure](#plugin-structure)
- [Design Principles](#design-principles)
- [Command Design Patterns](#command-design-patterns)
- [Multi-Phase Workflows](#multi-phase-workflows)
- [Agent Design](#agent-design)
- [User Experience](#user-experience)
- [Safety Patterns](#safety-patterns)
- [Reporting & Transparency](#reporting--transparency)
- [Common Patterns](#common-patterns)
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid)

---

## Plugin Structure

### Basic Directory Layout

```
your-plugin/
├── .claude-plugin/
│   └── plugin.json          # Plugin metadata
├── commands/                # Slash commands (user-facing)
│   └── command-name.md
├── agents/                  # Specialized subagents (optional)
│   ├── agent-one.md
│   └── agent-two.md
├── skills/                  # Agent Skills (optional)
└── hooks/                   # Event handlers (optional)
```

### Plugin Metadata (plugin.json)

```json
{
  "name": "your-plugin",
  "version": "1.0.0",
  "description": "Clear, concise description of what it does",
  "author": {
    "name": "Your Name",
    "email": "your@email.com"
  }
}
```

### Repository Structure (For This Bundled Plugins Repository)

**CRITICAL: ALL plugins in this repository MUST follow this structure:**

```
claude-code-plugins/
├── .claude-plugin/
│   └── marketplace.json       # Registry of ALL plugins
├── plugins/                   # ALL plugins go here
│   ├── bug-hunter/
│   ├── deslop/
│   ├── git-commit/
│   └── weekly-review/         # ← CORRECT location
└── weekly-review/             # ← WRONG! Never put plugins at root
```

**When creating a new plugin:**

1. **Create plugin in `plugins/` directory:**
   ```bash
   mkdir -p plugins/your-plugin/.claude-plugin
   mkdir -p plugins/your-plugin/commands
   ```

2. **Create plugin.json in the plugin directory:**
   ```bash
   # plugins/your-plugin/.claude-plugin/plugin.json
   ```

3. **Register in marketplace.json:**
   ```json
   {
     "name": "your-plugin",
     "description": "What it does",
     "version": "1.0.0",
     "source": "./plugins/your-plugin",  // ← MUST be ./plugins/your-plugin
     "category": "development"
   }
   ```

**DO NOT:**
- ❌ Put plugins at repository root level
- ❌ Forget to update marketplace.json
- ❌ Use wrong source path in marketplace.json

**Example of correct registration in marketplace.json:**
```json
{
  "plugins": [
    {
      "name": "bug-hunter",
      "source": "./plugins/bug-hunter"
    },
    {
      "name": "weekly-review",
      "source": "./plugins/weekly-review"
    }
  ]
}
```

---

## Design Principles

### 1. Safety First

**Always prioritize user safety over automation:**
- Get explicit confirmation before making destructive changes
- Show what will change before changing it
- Provide clear undo instructions
- Handle errors gracefully

**Example from deslop:**
```markdown
## Phase 4: User Review & Confirmation
**DO NOT PROCEED WITHOUT USER APPROVAL**
- Present detailed report
- Ask user explicitly
- Wait for confirmation
```

### 2. Transparency Over Magic

**Users should understand what's happening:**
- Explain each phase of the workflow
- Show reasoning behind decisions
- Report confidence levels
- Highlight risks and trade-offs

**Example from bug-hunter:**
```markdown
## Phase 3: Root Cause Analysis
1. Form hypotheses about the bug's cause
2. Investigate with evidence
3. Present findings with file paths and line numbers
```

### 3. Context-Aware Intelligence

**Learn before acting:**
- Read existing code to understand patterns
- Respect project conventions
- Compare against established norms
- Don't apply one-size-fits-all solutions

**Example from deslop:**
```markdown
## Phase 2: Codebase Style Learning
- Read similar files to understand conventions
- Build a "style profile"
- Use this profile to inform decisions
```

### 4. Progressive Disclosure

**Start simple, add complexity as needed:**
- Provide sensible defaults
- Support arguments for customization
- Don't overwhelm with options upfront

**Example:**
```markdown
---
description: Remove AI-generated code slop from your branch
argument-hint: [base-branch]
---
Base branch: $ARGUMENTS (defaults to `main` if not provided)
```

---

## Command Design Patterns

### Pattern 1: Simple Single-Phase Command

**When to use:**
- Task is straightforward and deterministic
- No ambiguity in what should be done
- Side effects are minimal or obvious

**Example structure:**
```markdown
---
description: Format all code files
---
# Format Code

Run the project's formatter on all source files.

**Actions:**
1. Detect formatter (prettier, black, gofmt, etc.)
2. Run formatter on all files
3. Report formatted files
```

### Pattern 2: Multi-Phase Workflow

**When to use:**
- Complex task with multiple steps
- Requires analysis before action
- User input needed at decision points
- Changes are potentially risky

**Example structure (like deslop):**
```markdown
# Phase 1: Analysis
- Gather information
- Understand current state

# Phase 2: Planning
- Form strategy
- Identify what needs to change

# Phase 3: User Confirmation
- Present plan
- Wait for approval

# Phase 4: Execution
- Apply changes
- Track progress

# Phase 5: Verification
- Confirm success
- Provide next steps
```

### Pattern 3: Guided Workflow with Agents

**When to use:**
- Task requires deep exploration
- Multiple specialized perspectives needed
- Highly complex problem domain

**Example structure (like bug-hunter):**
```markdown
# Phase 1: Problem Understanding
- Ask clarifying questions
- Confirm understanding

# Phase 2: Exploration (with agents)
- Launch multiple code-explorer agents
- Read identified files
- Build context

# Phase 3: Analysis (with agents)
- Launch root-cause-analyzer agents
- Form and test hypotheses
- Identify solution

# Phase 4: Solution Design (with agents)
- Launch bug-fixer agents
- Review proposals
- Get user approval

# Phase 5: Implementation
- Apply approved fix
- Verify changes

# Phase 6: Validation (with agents)
- Launch fix-validator agents
- Confirm no regressions
- Final report
```

---

## Multi-Phase Workflows

### Key Characteristics of Good Multi-Phase Design

**1. Clear Phase Boundaries**
```markdown
---

## Phase N: Phase Name

**Goal**: Single, clear objective

**Actions**:
1. Concrete step
2. Another concrete step
3. Final step

---
```

**2. TodoWrite Integration**
```markdown
## Phase 1: Setup
**Actions**:
1. Create a todo list with all phases
   - Phase 1: Setup
   - Phase 2: Analysis
   - Phase 3: Execution
   - Phase 4: Reporting
2. Mark current phase as in_progress
```

**3. Explicit Gates**
```markdown
## Phase 4: User Review

**DO NOT PROCEED WITHOUT USER APPROVAL**

**Actions**:
1. Present detailed report
2. Ask user for explicit confirmation
3. Wait for response before continuing
```

**4. Progress Tracking**
```markdown
## Phase 5: Application
**Actions**:
1. For each file to modify:
   - Read the file
   - Apply changes
   - **Update todos as you progress**
2. Mark phase complete when done
```

### Phase Template

Use this template for each phase:

```markdown
## Phase N: Descriptive Name

**Goal**: What this phase accomplishes (1 sentence)

**Actions**:
1. First concrete action
   - Sub-detail if needed
   - Another sub-detail
2. Second action
3. Third action
4. What to present/report to user

**Error Handling** (optional):
- If X happens → do Y
```

---

## Agent Design

### When to Create Agents

**Create specialized agents when:**
- Task requires deep, focused analysis
- Multiple perspectives improve results
- Parallel execution provides value
- Agent can be reused across commands

**Don't create agents when:**
- Simple sequential steps suffice
- Task is straightforward
- No reuse across commands

### Agent Design Principles

**1. Single Responsibility**

Each agent should have one clear purpose:

```markdown
# code-explorer.md
Deeply analyzes existing codebase features by tracing execution paths,
mapping architecture layers, understanding patterns and abstractions,
and documenting dependencies to inform new development
```

**2. Clear Input/Output Contract**

```markdown
## Input
You will be given: [description of what the agent receives]

## Output
Return exactly: [description of what the agent should produce]
```

**3. Focused Tools**

Limit agent tools to what they actually need:

```markdown
---
tools: Glob, Grep, LS, Read
---
```

### Agent Coordination Pattern

**Sequential agents** (when output of one informs the next):
```markdown
## Phase 2: Exploration
1. Launch code-explorer agent to identify key files
2. **Wait for agent to complete**
3. Read the identified files
4. Use insights for next phase
```

**Parallel agents** (when multiple perspectives help):
```markdown
## Phase 3: Analysis
1. Launch 2-3 root-cause-analyzer agents **in parallel**:
   - "Hypothesis: Race condition in async code"
   - "Hypothesis: Missing null check"
   - "Hypothesis: Configuration error"
2. Review all analyses
3. Identify most likely root cause
```

---

## User Experience

### Principle: No Surprises

**Always:**
- Show what will happen before it happens
- Report what happened after it happens
- Explain why decisions were made

**Example:**
```markdown
## Phase 1: Analysis
[Present findings]

## Phase 2: Planning
Based on the analysis above, I will:
1. Remove 45 redundant comments
2. Simplify 23 defensive checks
3. Fix 12 type casts

Do you want to proceed? [WAIT FOR ANSWER]

## Phase 3: Execution
[Apply changes]

## Phase 4: Report
Here's what I changed:
- File X: Removed 5 comments (lines 12-16)
- File Y: Simplified error handling (line 34)
```

### Principle: Actionable Information

**Bad reporting:**
```markdown
Fixed bugs in 3 files.
```

**Good reporting:**
```markdown
## Files Modified

1. src/auth/login.ts (34 lines changed)
   ✓ Removed redundant JSDoc (lines 23-25)
   ✓ Simplified try-catch (lines 45-52)
   ⚠️  Removed type cast - verify types compile

## Next Steps
1. Review changes: `git diff src/auth/login.ts`
2. Run tests: `npm test`
3. Commit: `git commit -m "fix: remove defensive code"`
```

### Principle: Risk Communication

Always communicate risk levels clearly:

```markdown
### Risk Assessment
- ✅ Safe changes: 120 lines (83%)
- ⚠️  Need review: 20 lines (14%)
- 🔴 Risky: 5 lines (3%)

### High-Risk Changes
🔴 src/payment/process.ts:67 - Removing try-catch in processPayment()
   → Manual review recommended: error handling is safety-critical
```

---

## Safety Patterns

### Pattern 1: Explicit Confirmation Gates

```markdown
## Phase 4: User Review & Confirmation

**DO NOT PROCEED WITHOUT USER APPROVAL**

**Actions**:
1. Present detailed report of what will change
2. **Ask user explicitly**: "Do you want to proceed?"
3. **Wait for response**
4. If approved → continue to Phase 5
5. If rejected → exit gracefully
```

### Pattern 2: Warn About State Changes

```markdown
## Phase 1: Setup & Analysis
**Actions**:
3. Verify git status:
   - Check if there are uncommitted changes
   - If uncommitted changes exist, ask user to confirm if they want to proceed anyway
   - Verify base branch exists
```

### Pattern 3: Provide Undo Instructions

```markdown
### Next Steps
1. Review the changes: `git diff`
2. Run tests: `npm test`
3. Commit changes: `git add . && git commit -m "..."`
4. **To undo**: `git restore .` will revert uncommitted changes if needed
```

### Pattern 4: Graceful Error Handling

```markdown
## Error Handling

If at any point:
- Base branch doesn't exist → ask user for correct branch name
- Working directory has uncommitted changes → ask user to confirm if they want to proceed anyway
- No diff found → inform user and exit gracefully
- User cancels → exit cleanly without making changes
```

---

## Reporting & Transparency

### Multi-Level Reporting

**1. Summary Level** (for quick overview)
```markdown
### Summary
Successfully cleaned 12 files, removing 145 lines of AI-generated slop
```

**2. Category Level** (for understanding)
```markdown
### By Category
- Comments: 45 lines (31%) - mostly redundant explanations
- Defensive code: 67 lines (46%) - unnecessary try-catch and null checks
- Type casts: 23 lines (16%) - any casts and assertions
```

**3. File Level** (for review)
```markdown
### Files Modified
1. src/auth/login.ts - 34 lines removed
   ✓ Removed 1 redundant JSDoc block
   ✓ Removed 1 unnecessary try-catch
   ⚠️  Skipped 1 high-risk change
```

**4. Line Level** (for precision)
```markdown
1. src/auth/login.ts (34 lines to remove)
   HIGH CONFIDENCE:
   - Lines 23-25: Redundant JSDoc comment
   - Lines 45-52: Try-catch around sync operation
   MEDIUM CONFIDENCE:
   - Lines 89-91: Defensive null check (may be intentional)
```

### Confidence Indicators

Always indicate confidence in decisions:

```markdown
HIGH CONFIDENCE: Based on clear pattern match
MEDIUM CONFIDENCE: Likely correct, but verify
LOW CONFIDENCE: Uncertain, manual review required
```

### Progress Indication

For long-running operations:

```markdown
## Phase 3: Analysis
Analyzing file 1 of 12: src/auth/login.ts
[update todo as you go]

Analyzing file 2 of 12: src/utils/helpers.ts
[update todo as you go]
```

---

## Common Patterns

### Pattern: Argument Handling

```markdown
---
description: Brief description of command
argument-hint: [base-branch] [--option]
---

## Arguments

**Base branch**: $ARGUMENTS (defaults to `main` if not provided)

## Phase 1: Setup
1. Parse arguments:
   - Extract base branch or use default
   - Extract options/flags
```

### Pattern: Tool Restrictions

```markdown
---
allowed-tools: Bash(git:*), Bash(npm:*)
---

This restricts Claude to only use git and npm commands via Bash
```

### Pattern: Style Learning

```markdown
## Phase 2: Codebase Style Learning

1. For each modified file, read similar files to understand:
   - [Specific pattern to learn]
   - [Another pattern to learn]

2. Build a "style profile":
   - "[Observation about codebase]"
   - "[Another observation]"

3. Present style profile to user for confirmation
```

### Pattern: Incremental Feedback

```markdown
## Phase 5: Application

1. For each file (12 total):
   - **Before**: "Processing src/auth/login.ts (1/12)"
   - [Apply changes]
   - **After**: "✓ Completed src/auth/login.ts"
   - **Update todos**
```

---

## Anti-Patterns to Avoid

### ❌ Anti-Pattern 1: Magic Without Explanation

**Bad:**
```markdown
Clean up the code and report what changed.
```

**Why it's bad:** User has no idea what "clean up" means or what will happen.

**Good:**
```markdown
## Phase 3: Slop Detection

Look for these specific patterns:
- [Pattern 1 with example]
- [Pattern 2 with example]

## Phase 4: Review
Show exactly what will be removed before removing it.
```

### ❌ Anti-Pattern 2: Assume One-Size-Fits-All

**Bad:**
```markdown
Remove all try-catch blocks and inline comments.
```

**Why it's bad:** Different codebases have different conventions.

**Good:**
```markdown
## Phase 2: Style Learning
Understand this codebase's patterns first:
- Read similar files
- Identify when try-catch IS used appropriately
- Build context-specific rules
```

### ❌ Anti-Pattern 3: No Confirmation for Destructive Actions

**Bad:**
```markdown
1. Analyze code
2. Remove slop
3. Report results
```

**Why it's bad:** User might not want all changes applied.

**Good:**
```markdown
1. Analyze code
2. Present findings
3. **Ask user for approval**
4. **WAIT FOR CONFIRMATION**
5. Apply approved changes only
```

### ❌ Anti-Pattern 4: Vague Error Messages

**Bad:**
```markdown
Error: Cannot proceed
```

**Why it's bad:** User doesn't know what to do.

**Good:**
```markdown
Error: Base branch 'develop' does not exist

Available branches:
- main
- feature/new-auth
- staging

Please specify a valid branch name, or use /deslop:deslop main
```

### ❌ Anti-Pattern 5: Hidden Risk

**Bad:**
```markdown
Removed 145 lines of code.
```

**Why it's bad:** Doesn't communicate risk or allow selective approval.

**Good:**
```markdown
### Risk Assessment
- Safe changes: 120 lines (83%)
- Need review: 20 lines (14%) - defensive code in critical paths
- Risky: 5 lines (3%) - error handling removal

**Recommendation**: Apply safe changes only, manually review risky changes
```

### ❌ Anti-Pattern 6: No Undo Path

**Bad:**
```markdown
Changes applied successfully.
```

**Why it's bad:** User might want to undo.

**Good:**
```markdown
Changes applied successfully.

To undo: `git restore .` will revert uncommitted changes if needed
```

---

## Examples from This Repository

### Example 1: Simple Command (Original deslop)

The original deslop was a simple single-phase command:
- No confirmation
- No detailed reporting
- Just instructions and a summary

**Lesson:** Simple is good for prototyping, but production plugins need safety and transparency.

### Example 2: Multi-Phase with Confirmation (Enhanced deslop)

The enhanced deslop demonstrates:
- ✅ 6 clear phases
- ✅ Style learning before action
- ✅ Explicit user confirmation
- ✅ Detailed multi-level reporting
- ✅ Risk assessment
- ✅ Clear undo instructions

**Lesson:** Multi-phase workflows with gates provide safety and transparency for complex operations.

### Example 3: Agent-Based Workflow (bug-hunter)

The bug-hunter demonstrates:
- ✅ Specialized agents for different tasks
- ✅ Parallel agent execution for efficiency
- ✅ Progressive understanding (explore → analyze → fix → validate)
- ✅ User confirmation before implementation

**Lesson:** Agents are powerful for tasks requiring deep exploration and multiple perspectives.

---

## Quick Start Checklist

When designing a new plugin, ask:

**Core Design:**
- [ ] Is this a simple command, multi-phase workflow, or agent-based?
- [ ] What problem does this solve?
- [ ] Who is the target user?

**Safety:**
- [ ] Does it make destructive changes?
- [ ] Do I ask for confirmation before changing anything?
- [ ] Do I provide undo instructions?
- [ ] Do I handle errors gracefully?

**User Experience:**
- [ ] Do I explain what will happen before it happens?
- [ ] Do I report what happened after it happens?
- [ ] Do I communicate risk levels?
- [ ] Are reports actionable?

**Implementation:**
- [ ] Do I use TodoWrite to track progress?
- [ ] Do phases have clear boundaries?
- [ ] Are agents truly needed, or would simple steps work?
- [ ] Are tool restrictions appropriate?

**Documentation:**
- [ ] Does the README explain what it does?
- [ ] Are usage examples provided?
- [ ] Are argument options documented?
- [ ] Is the workflow clear?

---

## Additional Resources

- [Claude Code Plugins Documentation](https://docs.claude.com/en/docs/claude-code/plugins)
- [Slash Commands Reference](https://docs.claude.com/en/docs/claude-code/slash-commands)
- [Subagents Reference](https://docs.claude.com/en/docs/claude-code/sub-agents)

---

## Contributing to This Guide

This guide is based on real experience designing plugins. If you build plugins and discover new patterns or anti-patterns, please contribute your learnings back to this document.

**Last Updated:** 2025-10-31
