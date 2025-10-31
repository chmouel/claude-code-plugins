# Weekly Review Plugin

A Claude Code plugin that generates comprehensive weekly summaries of your work, prioritizing GitHub PRs and automatically categorizing into tech debt, bug fixes, and new features.

## What It Does

The weekly review plugin analyzes your work from the past week (or custom time range), **prioritizing merged GitHub PRs** when available and falling back to git commits, and provides:

- A concise summary of your accomplishments (PRs or commits)
- 2-5 bullet points highlighting major changes
- Categorization of work into tech debt, bug fixes, and new functionality
- Statistics on PRs merged, commits made, files changed, and lines modified
- A detailed analysis paragraph explaining each category
- Links to merged PRs (when using GitHub mode) for easy reference

## Design Principles

This plugin follows the [Claude Code Plugin Design Guide](../CLAUDE.md):

- **Read-only**: Never modifies your code or git history
- **Transparent**: Clear multi-phase workflow with progress tracking
- **Safe**: Graceful error handling with helpful error messages
- **Actionable**: Provides concrete insights and next steps

## Usage

### Basic Usage

```bash
/weekly-review:weekly-review
```

This will analyze your commits from the past 7 days.

### Custom Time Range

```bash
/weekly-review:weekly-review 10
```

This will analyze your commits from the past 10 days (maximum).

## How It Works

### Phase 1: Setup & Environment Check
- Checks if GitHub CLI (`gh`) is available and authenticated
- Verifies your git email configuration
- Determines which mode to use (GitHub PRs preferred, git commits fallback)
- Validates the repository state

### Phase 2: Data Collection

**GitHub PR Mode (Preferred)**:
- Lists all merged PRs authored by you using `gh pr list`
- Collects PR metadata: title, labels, description, URLs
- Gathers file changes and commit counts for each PR
- Filters to the specified date range

**Git Commits Mode (Fallback)**:
- Gathers all commits by your email address
- Collects diff statistics for each commit
- Filters out merge commits

### Phase 3: Work Analysis

**For GitHub PRs**:
- Categorizes using PR labels and keywords in title/description
- Extracts themes from PR descriptions
- Calculates statistics across all merged PRs

**For Git Commits**:
- Categorizes using commit message keywords
- Identifies patterns and themes
- Calculates statistics and distributions

### Phase 4: Report Generation
- Produces a structured markdown report
- Highlights key accomplishments (2-5 bullet points)
- Provides breakdown by work type with percentages
- Lists merged PRs with links (GitHub mode) or commit timeline (git mode)
- Suggests next steps

## Categorization Logic

The plugin uses intelligent heuristics to categorize your work:

### GitHub PR Mode
Uses PR labels and keywords in title/description:

**Tech Debt**:
- Labels: `refactor`, `tech-debt`, `cleanup`, `chore`
- Keywords: refactor, cleanup, improve, optimize, simplify, dependency update
- Code quality improvements and migrations

**Bug Fixes**:
- Labels: `bug`, `fix`, `hotfix`, `bugfix`
- Keywords: fix, bug, issue, patch, resolve, crash, error
- Issue references (Fixes #123, Closes #456)

**New Functionality**:
- Labels: `feature`, `enhancement`, `new`
- Keywords: add, feature, implement, new, create, introduce
- Breaking changes and API additions

### Git Commits Mode
Uses commit message keywords:

**Tech Debt**:
- Keywords: refactor, cleanup, improve, optimize, simplify, chore
- Code reorganization and dependency updates
- Formatting and linting changes

**Bug Fixes**:
- Keywords: fix, bug, issue, patch, resolve
- Error handling improvements
- Test additions and modifications

**New Functionality**:
- Keywords: add, feature, feat, implement, new, create
- New files and substantial code additions
- API changes and extensions

## Example Output

### GitHub PR Mode

```markdown
# Weekly Review (2025-10-24 to 2025-10-31)
**Source**: GitHub Pull Requests

## Summary
You merged 8 pull requests over the past 7 days, containing 45 commits and changing 67 files.

## Key Accomplishments
- Implemented user authentication system with JWT tokens and refresh logic
- Fixed critical race condition in async data loading that caused data corruption
- Refactored database layer to use connection pooling for better performance
- Added comprehensive test suite for payment processing module

## Work Breakdown

### New Functionality (4 PRs, 50%)
Built authentication system, added payment processing, implemented user dashboard analytics

### Bug Fixes (2 PRs, 25%)
Resolved race conditions in data loader, fixed memory leaks in WebSocket connections

### Tech Debt & Maintenance (2 PRs, 25%)
Refactored database layer, updated dependencies, improved error handling patterns

## Detailed Analysis

**Tech Debt**: Modernized database connection management and updated 12 dependencies to latest stable versions, improving code maintainability.

**Bug Fixes**: Resolved critical race condition in async operations and fixed memory leaks affecting long-running WebSocket connections.

**New Functionality**: Delivered complete authentication system with JWT support, payment processing integration, and real-time analytics dashboard.

## Merged Pull Requests

### New Features
- #342: Add JWT authentication system - https://github.com/user/repo/pull/342
- #345: Implement payment processing module - https://github.com/user/repo/pull/345
- #348: Add real-time analytics dashboard - https://github.com/user/repo/pull/348

### Bug Fixes
- #343: Fix race condition in async data loader - https://github.com/user/repo/pull/343
- #346: Fix memory leak in WebSocket handler - https://github.com/user/repo/pull/346

### Tech Debt
- #344: Refactor database connection pooling - https://github.com/user/repo/pull/344
- #347: Update dependencies to latest versions - https://github.com/user/repo/pull/347

---

**Next Steps**:
- Review open PR #349 that builds on authentication work
- Update API documentation for new payment endpoints
- Plan next sprint focusing on mobile app integration
```

### Git Commits Mode (Fallback)

```markdown
# Weekly Review (2025-10-24 to 2025-10-31)
**Source**: Git Commits (no GitHub PR data available)

## Summary
You shipped 23 commits over the past 7 days, changing 47 files with +1,234/-567 lines.

## Key Accomplishments
- Implemented user authentication system with JWT tokens
- Fixed critical race condition in async data loading
- Refactored database layer to use connection pooling
- Added comprehensive test suite for payment processing

...
```

## Requirements

### Required
- Git repository
- Configured git user email (`git config user.email`)

### Optional (for GitHub PR Mode)
- GitHub CLI (`gh`) installed ([installation guide](https://cli.github.com/))
- Authenticated with `gh auth login`
- Repository with merged pull requests

**Note**: The plugin automatically falls back to git commits mode if GitHub CLI is not available.

## Error Handling

The plugin handles common scenarios gracefully:

- **GitHub CLI not available**: Automatically falls back to git commits mode
- **GitHub CLI not authenticated**: Provides `gh auth login` instructions
- **No merged PRs found**: Offers to fall back to git commits mode
- **No git email configured**: Provides clear instructions to set it
- **Not in a git repository**: Informs user and exits
- **No commits/PRs found**: Explains possible reasons
- **Command failures**: Shows clear error messages with actionable guidance

## Installation

1. Clone this repository or copy the `weekly-review` directory
2. Place it in your `.claude/plugins/` directory:
   ```bash
   cp -r weekly-review ~/.claude/plugins/
   ```
3. Restart Claude Code or run `/reload-plugins`

## Future Enhancements

Potential improvements for future versions:

- Support for filtering by specific branch or label
- Export to different formats (JSON, HTML, PDF)
- Integration with more issue trackers (Jira, Linear, etc.)
- Comparison with previous weeks (trend analysis)
- Team-wide reports (multiple authors)
- Visualization of work patterns and velocity
- Support for other Git hosting platforms (GitLab, Bitbucket)
- Custom categorization rules via configuration file

## Contributing

Found a bug or have an idea for improvement? Please open an issue or submit a PR!

## License

This plugin is part of the Claude Code Plugins collection.
