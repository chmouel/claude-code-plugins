---
description: Generate Jira tickets from discussions, PRs, issues, or files
argument-hint: [reference]
---

# Generate Jira Ticket

Create a high-level Jira issue from a discussion, PR, issue, file, or conversation.

**Arguments**: `$ARGUMENTS` (required)
- Can be: GitHub URL, PR number, issue number, file path, commit hash/range, or "conversation"

---

## Phase 1: Context Discovery & Gathering

**Goal**: Identify what the argument references and gather relevant context

**Actions**:
1. Create a todo list with all phases:
   - Phase 1: Context Discovery & Gathering
   - Phase 2: Generate Jira Issue
   - Phase 3: Validation & Preview
   - Phase 4: Save to File

2. Mark Phase 1 as in_progress

3. Analyze `$ARGUMENTS` to determine the type:
   - **If empty or not provided**: Error - ask user to provide a reference
   - **If GitHub URL (starts with https://github.com/ or http://github.com/)**: Parse and use gh
     - For PR URLs (e.g., `https://github.com/owner/repo/pull/123`):
       - Extract owner, repo, and PR number
       - Use `gh pr view <number> -R owner/repo`
     - For issue URLs (e.g., `https://github.com/owner/repo/issues/456`):
       - Extract owner, repo, and issue number
       - Use `gh issue view <number> -R owner/repo`
   - **If numeric (e.g., "123")**: Check if it's a PR or issue number in current repo
     - Try `gh pr view $ARGUMENTS` first
     - If that fails, try `gh issue view $ARGUMENTS`
   - **If starts with # (e.g., "#123")**: Treat as issue number in current repo
     - Use `gh issue view ${ARGUMENTS#\#}`
   - **If starts with PR# or pr# (e.g., "PR#123")**: Treat as PR number in current repo
     - Extract number and use `gh pr view <number>`
   - **If file path exists**: Read the file contents
     - Use Read tool to read the file
   - **If git commit hash or range (e.g., "abc123" or "abc123..def456")**: Get commit messages
     - Use `git log --format="%h %s%n%b" $ARGUMENTS`
   - **If "conversation" or "chat"**: Use current conversation context
     - Inform user you'll use the current conversation as context
   - **Otherwise**: Treat as a search term or ask user to clarify

4. Gather the context based on the identified type:
   - For PRs: Extract title, description, and relevant comments
   - For issues: Extract title, description, and discussion
   - For files: Read and summarize the content
   - For commits: Collect commit messages and changes summary
   - For conversation: Use recent messages in this chat

5. Present a brief summary of gathered context to confirm with user:
   ```
   **Context Gathered**:
   Type: [PR/Issue/File/Commits/Conversation]
   Source: [specific reference]
   Summary: [1-2 sentence summary]

   Proceeding to generate Jira issue...
   ```

6. Mark Phase 1 as completed, Phase 2 as in_progress

**Error Handling**:
- If argument is missing → ask user to provide a reference
- If PR/issue not found → ask user to verify the number
- If file doesn't exist → ask user to check the path
- If git command fails → ask if we're in a git repository

---

## Phase 2: Generate Jira Issue

**Goal**: Create a high-level Jira issue following the template

**Actions**:
1. Using the gathered context, generate a Jira issue that:
   - Follows the Jira template structure (provided below)
   - Extracts **high-level context only** - NO implementation details or design steps
   - Anonymizes any customer names → replace with "customer"
   - Adds links where possible (PR links, issue links, commit links, file URLs)
   - Uses Jira markdown formatting
   - Is copy-paste ready for Jira input box

2. Generate a recommended title (one line, descriptive, under 100 characters)

3. Use this Jira template structure:

```jira-markup
h1. Story (Required)

As a <PERSONA> trying to <ACTION> I want <THIS OUTCOME>

_<Describes high level purpose and goal for this story. Answers the questions: Who is impacted, what is it and why do we need it? How does it improve the customer's experience?>_

h2. *Background (Required)*

_<Describes the context or background related to this story>_

h2. *Out of scope*

_<Defines what is not included in this story>_

h2. *Approach (Required)*

_<Description of the general technical path on how to achieve the goal of the story. Include details like json schema, class definitions>_

h2. *Dependencies*

_<Describes what this story depends on. Dependent Stories and EPICs should be linked to the story.>_

h2. *Acceptance Criteria (Mandatory)*

_<Describe edge cases to consider when implementing the story and defining tests>_
_<Provides a required and minimum list of acceptance tests for this story. More is expected as the engineer implements this story>_
```

4. **IMPORTANT Guidelines**:
   - Do NOT add implementation details - keep it high-level for engineers to figure out
   - Do NOT add low-level design steps
   - DO add links to relevant resources (PRs, issues, docs, commits)
   - DO use clear, testable Acceptance Criteria (Given/When/Then or numbered checklist)
   - DO keep customer references anonymous as "customer"
   - DO format everything in Jira markdown (h1., h2., *, _, etc.)

5. Mark Phase 2 as completed, Phase 3 as in_progress

---

## Phase 3: Validation & Preview

**Goal**: Validate the generated issue and get user approval

**Actions**:
1. Validate that all required fields are filled:
   - Story section has content
   - Background section has content
   - Approach section has content
   - Acceptance Criteria has content

2. Present the generated Jira issue to the user:
   ```
   **Recommended Title**:
   [Generated title]

   **Jira Issue Body**:
   [Show the full generated Jira issue in a code block]
   ```

3. **Ask user**: "Does this Jira issue look good? Should I save it to a file?"

4. **WAIT FOR USER RESPONSE**

5. If user wants changes:
   - Ask what needs to be modified
   - Regenerate the issue
   - Return to step 2

6. If user approves:
   - Mark Phase 3 as completed, Phase 4 as in_progress
   - Proceed to Phase 4

7. If user rejects:
   - Exit gracefully
   - Mark all remaining phases as completed

**Error Handling**:
- If any required field is missing → warn user and ask if they want to proceed anyway

---

## Phase 4: Save to File

**Goal**: Save the Jira issue to a file with a smart filename

**Actions**:
1. Generate a filename slug from the title:
   - Convert title to lowercase
   - Replace spaces with hyphens
   - Remove special characters
   - Limit to 50 characters
   - Add `.md` extension
   - Example: "Add user authentication flow" → "add-user-authentication-flow.md"

2. Ask user for save location using AskUserQuestion tool:
   - Option 1: Save to `/tmp/jira-[slug].md`
   - Option 2: Save to current directory `./jira-[slug].md`
   - Show both full paths in the options

3. Save the file to the chosen location:
   - Write the Jira issue content to the file
   - Include a comment at the top with metadata:
     ```
     <!-- Generated by Claude Code jira-ticket plugin -->
     <!-- Source: [context source] -->
     <!-- Date: [current date] -->
     ```

4. Report success to user:
   ```
   ✓ Jira issue saved successfully!

   **File**: [full path to saved file]
   **Title**: [recommended title]

   **Next Steps**:
   1. Review the file: `cat [path]`
   2. Copy to clipboard: `cat [path] | pbcopy` (macOS) or `cat [path] | xclip -selection clipboard` (Linux)
   3. Paste into Jira and use the recommended title above
   ```

5. Mark Phase 4 as completed

**Error Handling**:
- If file write fails → report error and suggest alternative location
- If directory doesn't exist → create it or ask user for alternative

---

## Final Notes

- The plugin uses TodoWrite to track progress through all phases
- Each phase has clear boundaries and completion markers
- User approval is required before saving
- Smart context gathering handles multiple input types
- Output is Jira-ready markdown with proper formatting
