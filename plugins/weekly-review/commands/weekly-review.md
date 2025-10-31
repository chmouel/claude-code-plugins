---
description: Generate a weekly summary of your merged PRs and commits
argument-hint: [days]
allowed-tools: Bash(gh:*), Bash(git:*)
---

# Weekly Review

Generate a comprehensive summary of your work from the past 7-10 days, prioritizing merged GitHub PRs and falling back to git commits when PRs aren't available. Automatically categorizes work into tech debt, bug fixes, and new features.

## Arguments

**Days**: $ARGUMENTS (defaults to `7` if not provided, maximum `10`)

Parse the number of days from $ARGUMENTS, or use 7 as default.

---

## Phase 1: Setup & Environment Check

**Goal**: Verify git/GitHub configuration and determine time range for analysis

**Actions**:
1. Create a todo list with all phases:
   - Phase 1: Setup & Environment Check
   - Phase 2: Data Collection (PRs or Commits)
   - Phase 3: Work Analysis
   - Phase 4: Report Generation

2. Parse arguments to determine days (default: 7, max: 10)

3. Check if GitHub CLI is available and authenticated:
   ```bash
   gh auth status
   ```

4. Get git user email using: `git config user.email`

5. **Decision Point**:
   - If `gh` is available and authenticated → set mode to "GitHub PRs"
   - If `gh` is not available or not authenticated → set mode to "Git Commits"
   - Inform user which mode will be used

6. **Error Handling**:
   - If no email is configured (and using Git Commits mode), inform the user:
     ```
     I couldn't find your git email configuration.

     To set your email, run:
     git config --global user.email "your-email@example.com"

     Or for this repository only:
     git config user.email "your-email@example.com"
     ```
   - Then exit gracefully

7. Verify we're in a git repository
   - If not, inform user and exit

---

## Phase 2: Data Collection

**Goal**: Gather merged PRs (preferred) or commits from the specified time range

### Mode A: GitHub PRs (Preferred)

**Actions**:
1. Calculate the date range:
   - Start date: N days ago (where N is from arguments or default 7)
   - End date: today

2. List merged PRs authored by the current user:
   ```bash
   gh pr list --state merged --author @me --json number,title,mergedAt,labels,body,url --limit 100
   ```

3. Filter PRs to only those merged in the date range

4. For each merged PR, collect:
   - PR number and title
   - Merge date
   - Labels (for categorization hints)
   - PR body/description
   - URL
   - Files changed: `gh pr diff <number> --name-only`
   - Commit count: `gh pr view <number> --json commits --jq '.commits | length'`

5. **Error Handling**:
   - If no PRs found, inform user:
     ```
     No merged PRs found by @me in the past N days.

     This could mean:
     - You haven't merged any PRs recently
     - PRs were merged by someone else (even if you authored them)
     - You might want to check a specific branch
     ```
   - Ask if they want to fall back to Git Commits mode
   - If yes, switch to Mode B

### Mode B: Git Commits (Fallback)

**Actions**:
1. Calculate the date range:
   - Start date: N days ago (where N is from arguments or default 7)
   - End date: today

2. Collect commits using:
   ```bash
   git log --all --author="<user-email>" --since="N days ago" --pretty=format:"%H|%s|%ar|%ad" --date=short --no-merges
   ```

3. For each commit, get the diff stats:
   ```bash
   git show <commit-hash> --stat --pretty=format:"" --numstat
   ```

4. **Error Handling**:
   - If no commits found, inform user:
     ```
     No commits found by <email> in the past N days.

     This could mean:
     - You haven't committed anything recently
     - Your git email might be different
     - Your commits are on a different branch
     ```
   - Then exit gracefully

5. Store commit information:
   - Commit hash
   - Commit message
   - Date and time ago
   - Files changed
   - Lines added/removed

---

## Phase 3: Work Analysis

**Goal**: Analyze PRs/commits to categorize work and understand patterns

### Analysis for GitHub PRs

**Actions**:
1. For each PR, analyze the title, labels, and body to categorize:

   **Tech Debt indicators**:
   - Labels: refactor, tech-debt, cleanup, chore
   - Keywords in title/body: refactor, cleanup, improve, optimize, simplify, restructure
   - Keywords: dependency update, upgrade, migration
   - Code quality improvements

   **Bug Fix indicators**:
   - Labels: bug, fix, hotfix, bugfix
   - Keywords in title/body: fix, bug, issue, patch, correct, resolve
   - References to issue numbers (Fixes #123, Closes #456)
   - Keywords: crash, error, broken

   **New Functionality indicators**:
   - Labels: feature, enhancement, new
   - Keywords in title/body: add, feature, implement, new, create, introduce
   - Breaking changes mentioned
   - API additions

2. Extract key themes from PR titles and descriptions:
   - Group related PRs together
   - Identify major areas of work
   - Note any significant patterns or initiatives

3. Calculate statistics:
   - Total PRs merged
   - Total files changed (sum across all PRs)
   - Total commits (sum across all PRs)
   - Distribution across categories

### Analysis for Git Commits

**Actions**:
1. For each commit, analyze the commit message and diff to categorize:

   **Tech Debt indicators**:
   - Keywords: refactor, cleanup, improve, optimize, simplify, restructure, chore
   - Large file renames or moves
   - Dependency updates
   - Code formatting changes

   **Bug Fix indicators**:
   - Keywords: fix, bug, issue, patch, correct, resolve
   - References to issue numbers (#123, GH-456)
   - Changes to error handling
   - Test additions/modifications

   **New Functionality indicators**:
   - Keywords: add, feature, implement, new, create, introduce, feat
   - New files added
   - Substantial additions (more lines added than removed)
   - API changes or extensions

2. Extract key themes from commit messages:
   - Group similar commits together
   - Identify major areas of work
   - Note any significant patterns

3. Calculate statistics:
   - Total commits
   - Total files changed
   - Total lines added/removed
   - Distribution across categories

---

## Phase 4: Report Generation

**Goal**: Present a clear, actionable summary of the week's work

### Report Format for GitHub PRs

**Actions**:
1. Generate the report in this format:

```markdown
# Weekly Review (<start-date> to <end-date>)
**Source**: GitHub Pull Requests

## Summary
You merged <N> pull requests over the past <N> days, containing <M> commits and changing <X> files.

## Key Accomplishments
- [2-5 bullet points of major work, derived from PR analysis]
- [Focus on user-facing impact and significant changes]
- [Group related PRs into single bullets]

## Work Breakdown

### New Functionality (<N> PRs, X%)
[Brief description of new features added based on PR titles/descriptions]

### Bug Fixes (<N> PRs, X%)
[Brief description of bugs fixed based on PR titles/descriptions]

### Tech Debt & Maintenance (<N> PRs, X%)
[Brief description of refactoring and cleanup work]

## Detailed Analysis

**Tech Debt**: [1-2 sentence summary of refactoring work, patterns improved, code quality enhancements, mentioning specific PRs]

**Bug Fixes**: [1-2 sentence summary of issues resolved, areas of the codebase stabilized, mentioning specific PRs]

**New Functionality**: [1-2 sentence summary of new capabilities added, features implemented, mentioning specific PRs]

## Merged Pull Requests

### New Features
- #123: Add user authentication system - <url>
- #125: Implement dashboard analytics - <url>

### Bug Fixes
- #124: Fix race condition in data loader - <url>

### Tech Debt
- #126: Refactor database connection pooling - <url>

---

**Next Steps**:
- Review any open PRs that build on this work
- Update project documentation if needed
- Plan next week's priorities based on patterns observed
```

### Report Format for Git Commits

**Actions**:
1. Generate the report in this format:

```markdown
# Weekly Review (<start-date> to <end-date>)
**Source**: Git Commits (no GitHub PR data available)

## Summary
You shipped <N> commits over the past <N> days, changing <N> files with <+X/-Y> lines.

## Key Accomplishments
- [2-5 bullet points of major work, derived from commit analysis]
- [Focus on user-facing impact and significant changes]
- [Group related commits into single bullets]

## Work Breakdown

### New Functionality (<N> commits, X%)
[Brief description of new features added]

### Bug Fixes (<N> commits, X%)
[Brief description of bugs fixed]

### Tech Debt & Maintenance (<N> commits, X%)
[Brief description of refactoring and cleanup work]

## Detailed Analysis

**Tech Debt**: [1-2 sentence summary of refactoring work, patterns improved, code quality enhancements]

**Bug Fixes**: [1-2 sentence summary of issues resolved, areas of the codebase stabilized]

**New Functionality**: [1-2 sentence summary of new capabilities added, features implemented]

## Commit Timeline
[Show commits grouped by day or by category, with commit messages and hashes]

---

**Next Steps**:
- Review any open PRs related to this work
- Update project documentation if needed
- Consider using GitHub PRs for better weekly reviews (install gh CLI)
- Plan next week's priorities based on patterns observed
```

2. Present the report to the user

3. Mark the final phase as completed

---

## Error Handling

**Throughout all phases**:
- If gh commands fail → explain the error and fall back to Git Commits mode
- If git commands fail → provide clear error message and exit
- If parsing fails → show what was attempted and ask for clarification
- If unexpected state → explain what was found and what was expected
- If authentication issues with gh → provide instructions to run `gh auth login`

---

## Important Notes

- This command is **read-only** - it only analyzes PRs/commits, never modifies anything
- **GitHub PRs are preferred** over git commits because they:
  - Represent cohesive units of work
  - Include labels and descriptions for better categorization
  - Show code review and collaboration context
  - Are easier to link back to for reference
- The analysis is based on PR titles/descriptions or commit messages, so quality depends on message clarity
- Categorization uses heuristics and may not be 100% accurate
- The command focuses on PRs merged by @me or commits by the configured git user email
- Merge commits are excluded from git commit analysis
- If using GitHub PRs, you need the GitHub CLI (`gh`) installed and authenticated
