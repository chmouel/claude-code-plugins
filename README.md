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
