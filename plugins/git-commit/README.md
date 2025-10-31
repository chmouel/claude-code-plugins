# Git Commit Plugin

Create thoughtful, well-formatted commit messages with systematic file review and optional push functionality.

## Philosophy

This plugin enhances Claude Code's native git workflow by adding:
- **Systematic file review** to ensure commit coherence
- **"Why"-focused messaging** that explains impact and reasoning
- **Conventional commits** for better project history
- **Guided workflow** with clear phases and confirmations

## Features

- ✅ **Parallel git operations** - Fast information gathering using multiple tool calls
- ✅ **File-by-file review** - Understand each file's changes and ensure they're related
- ✅ **"Why" over "what"** - Commit messages focus on impact and reasoning, not just changes
- ✅ **Conventional commits** - Uses feat, fix, chore, docs, refactor, test, perf, style, ci
- ✅ **Claude Code attribution** - Automatically includes proper co-authorship
- ✅ **Smart push** - Offers to push only when NOT on main/master
- ✅ **TodoWrite integration** - Track progress through phases

## Usage

```bash
/git-commit:commit
```

## Workflow Phases

### 1. Gather Repository Information
Runs git status, diff, and log **in parallel** for fast analysis.

### 2. Review Files Individually
Reviews each changed file's diff and ensures all changes belong together.

### 3. Create Commit Message
Drafts a concise message focusing on **why** changes were made and their impact.

Format:
```
<type>: <title under 80 chars>

- <impact/reason bullet under 80 chars>
- <impact/reason bullet under 80 chars>
- <impact/reason bullet under 80 chars>

🤖 Generated with [Claude Code](https://claude.com/claude-code)

Co-Authored-By: Claude <noreply@anthropic.com>
```

### 4. Execute Commit
Creates the commit after user approval, staging files as needed.

### 5. Optional Push
Offers to push (only when not on main/master branch).

## Example Messages

### Good (focuses on "why")
```
feat: Implement user authentication

- Enable secure multi-user sessions with JWT
- Prevent unauthorized access to protected routes
- Lay groundwork for role-based access control
```

### Bad (focuses on "what")
```
feat: Add auth

- Added login.ts file
- Added middleware
- Added tests
```

## Conventional Commit Types

- **feat**: New feature or capability
- **fix**: Bug fix
- **chore**: Maintenance tasks, dependency updates
- **docs**: Documentation changes
- **refactor**: Code restructuring without behavior change
- **test**: Adding or updating tests
- **perf**: Performance improvements
- **style**: Code formatting, whitespace
- **ci**: CI/CD pipeline changes

## Why This Plugin?

Claude Code already has excellent git support, but this plugin adds value by:

1. **Enforcing systematic review** - Reviews each file individually to ensure coherent commits
2. **Teaching better commit messages** - Guides towards "why" over "what"
3. **Conventional commits** - Maintains consistent commit history
4. **Safety gates** - Multiple confirmations before committing
5. **Smart defaults** - Knows when to offer push and when not to

## Best Practices

- **One concern per commit** - If files address different concerns, split into multiple commits
- **Focus on impact** - Explain why changes matter, not just what changed
- **Keep it concise** - Respect 80-character limits
- **Use conventional types** - Helps with changelog generation

## Configuration

No configuration needed. Works with your existing git setup.

## Safety Features

- ✅ Only commits when user explicitly confirms
- ✅ Won't auto-push to main/master branches
- ✅ Reviews all files before committing
- ✅ Graceful error handling
- ✅ Follows Claude Code's git safety protocols

## Performance

Uses parallel tool calls to gather git information efficiently, reducing token usage and latency.

## Author

Chmouel - <chmouel@chmouel.com>

## License

Apache-2.0
