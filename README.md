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

## Installation

Install this plugin collection in Claude Code:

```
/extension add chmouel/claude-code-plugins
```

## Plugin Structure

Each plugin is located in the `plugins/` directory and includes:

- `.claude-plugin/plugin.json` - Plugin metadata
- `commands/` - Slash commands for user interaction
- `agents/` - Specialized agents for specific tasks

## Contributing

Plugins are defined in `.claude-plugin/marketplace.json`. To add a new plugin, create a directory in `plugins/` following the existing structure.

## Author

Chmouel - <chmouel@chmouel.com>

## License

[Apache-2.0 License](LICENSE)
