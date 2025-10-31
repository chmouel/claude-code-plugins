# Jira Ticket Plugin

Generate high-level Jira tickets from discussions, PRs, issues, files, or conversations with smart context gathering and validation.

## Features

- **Smart Context Gathering**: Automatically detects and gathers context from multiple sources
- **Multiple Input Types**: Supports PRs, issues, files, commits, or conversation context
- **High-Level Focus**: Generates only high-level context without implementation details
- **Customer Anonymization**: Automatically replaces customer names with "customer"
- **Link Detection**: Adds relevant links to PRs, issues, commits, and files
- **Validation & Preview**: Shows generated issue before saving with user confirmation
- **Smart File Naming**: Creates descriptive filenames based on the issue title
- **Flexible Save Location**: Choose between `/tmp` or current directory

## Installation

This plugin is part of the `chmouel-cc-plugins` marketplace.

```bash
# Install from the bundled marketplace
claude-code install chmouel-cc-plugins/jira-ticket
```

## Usage

```bash
/jira-ticket <reference>
```

### Supported Reference Types

| Reference Type | Example | Description |
|---------------|---------|-------------|
| **GitHub PR URL** | `/jira-ticket https://github.com/owner/repo/pull/123` | Fetches PR from any GitHub repo using `gh` CLI |
| **GitHub Issue URL** | `/jira-ticket https://github.com/owner/repo/issues/456` | Fetches issue from any GitHub repo using `gh` CLI |
| **PR Number** | `/jira-ticket 123` or `/jira-ticket PR#123` | Fetches PR details from current repo using `gh` CLI |
| **Issue Number** | `/jira-ticket #456` | Fetches issue details from current repo using `gh` CLI |
| **File Path** | `/jira-ticket ./notes/discussion.md` | Reads file contents |
| **Commit Hash** | `/jira-ticket abc1234` | Gets commit message and changes |
| **Commit Range** | `/jira-ticket abc123..def456` | Gets all commits in range |
| **Conversation** | `/jira-ticket conversation` | Uses current chat context |

### Examples

**From a GitHub PR URL:**
```bash
/jira-ticket https://github.com/anthropics/claude-code/pull/123
```

**From a GitHub Issue URL:**
```bash
/jira-ticket https://github.com/anthropics/claude-code/issues/456
```

**From a Pull Request (current repo):**
```bash
/jira-ticket 123
```

**From an Issue (current repo):**
```bash
/jira-ticket #456
```

**From a Discussion File:**
```bash
/jira-ticket ./docs/feature-discussion.md
```

**From Recent Commits:**
```bash
/jira-ticket main..feature-branch
```

**From Current Conversation:**
```bash
/jira-ticket conversation
```

## Workflow

The plugin follows a 4-phase workflow:

### Phase 1: Context Discovery & Gathering
- Automatically detects what your reference points to
- Gathers relevant context (PR description, issue details, file contents, etc.)
- Presents a summary of gathered context

### Phase 2: Generate Jira Issue
- Creates a Jira issue following the standard template
- Extracts high-level context only (no implementation details)
- Anonymizes customer names
- Adds relevant links
- Generates a recommended title

### Phase 3: Validation & Preview
- Validates all required fields are filled
- Shows preview of the generated issue
- Asks for user confirmation before saving

### Phase 4: Save to File
- Creates a smart filename based on the issue title
- Lets you choose save location (`/tmp` or current directory)
- Saves the file with metadata comments
- Provides copy-to-clipboard instructions

## Jira Template Structure

The plugin generates issues following this structure:

```jira-markup
h1. Story (Required)
As a <PERSONA> trying to <ACTION> I want <THIS OUTCOME>

h2. *Background (Required)*
<Context and background>

h2. *Out of scope*
<What is not included>

h2. *Approach (Required)*
<General technical path>

h2. *Dependencies*
<Dependencies and linked stories>

h2. *Acceptance Criteria (Mandatory)*
<Edge cases and acceptance tests>
```

## Guidelines

The plugin follows these important guidelines:

- ✅ **High-level only**: No implementation details or design steps
- ✅ **Engineer-friendly**: Leaves technical decisions to implementers
- ✅ **Link-rich**: Includes references to PRs, issues, docs, and commits
- ✅ **Testable**: Clear acceptance criteria (Given/When/Then or checklist)
- ✅ **Anonymous**: Customer names replaced with "customer"
- ✅ **Jira-ready**: Proper Jira markdown formatting for copy-paste

## Requirements

- **GitHub CLI (`gh`)**: Required for PR and issue context gathering
  ```bash
  # Install gh CLI
  brew install gh  # macOS
  # or
  sudo apt install gh  # Linux

  # Authenticate
  gh auth login
  ```

- **Git**: Required for commit context gathering

## Output Example

After running the command, you'll get:

```
✓ Jira issue saved successfully!

**File**: /tmp/jira-add-user-authentication-flow.md
**Title**: Add user authentication flow

**Next Steps**:
1. Review the file: `cat /tmp/jira-add-user-authentication-flow.md`
2. Copy to clipboard: `cat /tmp/jira-add-user-authentication-flow.md | pbcopy` (macOS)
3. Paste into Jira and use the recommended title above
```

## Error Handling

The plugin handles errors gracefully:

- **Missing argument**: Asks user to provide a reference
- **PR/issue not found**: Asks user to verify the number
- **File doesn't exist**: Asks user to check the path
- **Git command fails**: Checks if you're in a git repository
- **Missing required fields**: Warns and asks if you want to proceed

## Design Philosophy

This plugin follows the [Claude Code Plugin Design Guide](../../CLAUDE.md) with:

- **Safety First**: User confirmation before saving
- **Transparency**: Clear multi-phase workflow with progress tracking
- **Context-Aware**: Smart detection and gathering from multiple sources
- **Progressive Disclosure**: Simple defaults with customization options

## Version

Current version: **1.0.0**

## License

Part of the `chmouel-cc-plugins` collection.
