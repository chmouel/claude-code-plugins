# Claude Code Plugins

A collection of plugins for [Claude Code](https://claude.com/claude-code) to enhance your development workflow.

## Plugins

### Bug Hunter

A comprehensive bug hunting workflow that guides you through systematic debugging with specialized agents for:

- **Codebase Exploration**: Trace execution paths and understand architecture
- **Root Cause Analysis**: Form and investigate hypotheses about bug origins
- **Bug Fixing**: Propose clean, effective code fixes
- **Fix Validation**: Ensure fixes work without introducing regressions

**Usage**: `/bug-hunter:bug-hunt [optional bug description]`

The plugin follows a structured 7-phase approach:

1. Bug report analysis
2. Codebase exploration
3. Root cause analysis
4. Solution design
5. Implementation
6. Validation
7. Summary

### Deslop

Remove AI-generated code "slop" from your branches using a systematic, safe, multi-phase approach.

**What it detects and removes**:

- Extra comments that are inconsistent with the codebase
- Unnecessary defensive checks or try/catch blocks
- Type casts to `any` used to bypass type issues
- Over-verbose naming and unnecessary intermediate variables
- Other AI-generated patterns that don't match your code style

**Usage**:

```
/deslop:deslop              # Compare against main (default)
/deslop:deslop develop      # Compare against develop branch
```

The command provides detailed file-by-file reports including risk assessment
and asks for user confirmation before making any changes.

### Git Commit

Create thoughtful commit messages that focus on **why** changes were made, not just what changed.

**What makes it different**:

- Systematic file-by-file review to ensure commit coherence
- Commit messages that explain **impact and reasoning** (the "why")
- Conventional commits with proper Claude Code attribution
- Parallel git operations for speed and efficiency
- Smart push offers (only when NOT on main/master)

**Usage**:

```
/git-commit:commit
```

The plugin enhances Claude Code's native git workflow with structured review, better messaging, and safety gates. Perfect for maintaining clean, meaningful commit history.

### Weekly Review

Generate comprehensive weekly summaries of your work, automatically categorized by work type. **Prioritizes GitHub PRs** when available and falls back to git commits.

**What it analyzes**:

- Merged GitHub pull requests (preferred) or git commits from past 7-10 days
- PR labels, titles, and descriptions for intelligent categorization
- Automatic categorization into tech debt, bug fixes, and new features
- Statistics on PRs merged, commits made, files changed, and lines modified

**Usage**:

```
/weekly-review:weekly-review        # Review past 7 days
/weekly-review:weekly-review 10     # Review past 10 days
```

The plugin generates a structured report with:

- Summary of accomplishments (2-5 bullet points)
- Work breakdown by category with percentages
- Detailed analysis of tech debt, bug fixes, and new functionality
- Links to merged PRs (GitHub mode) or commit timeline (git mode)

**Features**:

- Automatically detects GitHub CLI availability
- Uses PR labels and descriptions for better categorization
- Read-only operation - never modifies your code or git history

### Jira Ticket

Generate high-level Jira tickets from discussions, PRs, issues, files, or conversations with smart context gathering and validation.

**What it supports**:

- GitHub PR URLs and PR numbers
- GitHub issue URLs and issue numbers
- File paths (discussion notes, specs, etc.)
- Git commit hashes and commit ranges
- Current conversation context

**Usage**:

```
/jira-ticket:jira-ticket https://github.com/owner/repo/pull/123
/jira-ticket:jira-ticket 456                    # PR/issue number in current repo
/jira-ticket:jira-ticket ./docs/discussion.md   # From file
/jira-ticket:jira-ticket main..feature-branch   # From commits
/jira-ticket:jira-ticket conversation            # From current chat
```

The plugin follows a 4-phase workflow:

1. Context discovery and gathering from your reference
2. Generate Jira issue with high-level context (no implementation details)
3. Validation and preview with user confirmation
4. Save to file with smart filename generation

**Features**:

- Anonymizes customer names automatically
- Adds relevant links to PRs, issues, commits, and files
- Validates required fields before saving
- Jira-ready markdown formatting for copy-paste
- Choose save location (/tmp or current directory)

**Requirements**: GitHub CLI (`gh`) for PR/issue context, Git for commit context

## Installation

Install this plugin collection from the shell:

```
claude plugin marketplace add chmouel/claude-code-plugins 
```

To install specific plugins:

```
claude plugin install bug-hunter
claude plugin install deslop
claude plugin install git-commit
claude plugin install jira-ticket
claude plugin install weekly-review
```

## Plugin Structure

Each plugin is located in the `plugins/` directory and includes:

- `.claude-plugin/plugin.json` - Plugin metadata
- `commands/` - Slash commands for user interaction
- `agents/` - Specialized agents for specific tasks

## Plugin Development

Want to create your own Claude Code plugins? Check out [CLAUDE.md](CLAUDE.md) for a comprehensive guide covering:

- Plugin design principles (safety, transparency, context-awareness)
- Command design patterns (simple, multi-phase, agent-based)
- Multi-phase workflow design with user confirmation
- Agent design and coordination
- User experience best practices
- Safety patterns and error handling
- Reporting and transparency strategies
- Common patterns and anti-patterns to avoid

The guide is based on real-world experience designing the plugins in this repository.

## Contributing

Plugins are defined in `.claude-plugin/marketplace.json`. To add a new plugin, create a directory in `plugins/` following the existing structure.

## Author

Chmouel - <chmouel@chmouel.com>

## License

[Apache-2.0 License](LICENSE)
