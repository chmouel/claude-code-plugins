---
description: Create a well-formatted commit message for your current work
---

# Commit Current Work

Review changed files individually and create a concise, conventional commit message.

## Important

- **DO NOT use the Bash tool with echo or any other command-line tools to communicate with the user** - output all communication directly in your response text
- **Use parallel tool calls** when gathering git information (status, diff, log)
- **Only commit when user explicitly confirms** - this command is for structured review and commit creation
- **Do NOT keep committing subsequent work** unless explicitly instructed

---

## Workflow

### Phase 1: Gather Repository Information

**Goal**: Understand current state in parallel

**Actions**:

1. Create a todo list for this workflow:
   - Phase 1: Gather repository information
   - Phase 2: Review files individually
   - Phase 3: Create commit message
   - Phase 4: Execute commit

2. Run these commands **in parallel** using multiple Bash tool calls:
   - `git status` - See all untracked files and changes
   - `git diff --stat` - See summary of changes
   - `git diff` - See both staged and unstaged changes
   - `git log -5 --oneline` - See recent commits for style reference

3. Analyze the results:
   - Current branch name
   - Files changed (staged, unstaged, untracked)
   - Nature of changes (new files, modifications, deletions)

4. Present summary:
   ```
   Current branch: feature-auth

   Changes:
   - 3 modified files
   - 2 new files
   - ~150 lines changed
   ```

---

### Phase 2: Review Each File Individually

**Goal**: Ensure all changes are related and understand what changed

**Actions**:

1. For **each changed file**, review the diff and describe:
   - What changed in this file
   - Why it's part of this work
   - Any concerns or notable aspects

2. Present a file-by-file summary:
   ```
   Files to commit:

   1. src/auth/login.ts (45 lines)
      ✓ Added JWT token validation
      ✓ Implemented login/logout handlers
      Related: Core auth feature

   2. src/middleware/auth.ts (new file, 32 lines)
      ✓ Created auth middleware for route protection
      Related: Core auth feature

   3. tests/auth.test.ts (new file, 73 lines)
      ✓ Added tests for auth flow
      Related: Core auth feature
   ```

3. Ask: "Do these changes look correct for a single commit?"

---

### Phase 3: Create Commit Message

**Goal**: Draft a concise, meaningful commit message

**Actions**:

1. Based on the file review, create a commit message that focuses on **WHY** not just what:

   Format:
   ```
   <type>: <short description (max 80 chars)>

   - <bullet point describing impact/reason>
   - <bullet point describing impact/reason>
   - <bullet point describing impact/reason>
   ```

   **Types**: feat, fix, chore, docs, refactor, test, perf, style, ci

2. **Guidelines**:
   - First line: Clear, concise summary under 80 characters
   - Bullet points: Focus on "why" and impact, not just "what"
   - Each bullet: Under 80 characters
   - 2-3 bullets (not more)

3. **Example** (good):
   ```
   feat: Add user authentication system

   - Enable secure user sessions for multi-user support
   - Prevent unauthorized API access with JWT validation
   - Establish foundation for role-based permissions
   ```

4. **Anti-example** (bad - too "what" focused):
   ```
   feat: Add auth

   - Added login.ts file
   - Added middleware
   - Added tests
   ```

5. Present the commit message to the user

6. Ask: "Would you like to commit with this message?"

---

### Phase 4: Execute Commit

**Goal**: Create the commit safely

**DO NOT PROCEED WITHOUT USER APPROVAL**

**Actions**:

1. If user approves the commit message:

2. Stage files as needed:
   - If there are unstaged changes, ask which files to include
   - Stage approved files: `git add <files>`

3. Create commit with HEREDOC format:
   ```bash
   git commit -m "$(cat <<'EOF'
   <type>: <title>

   - <bullet 1>
   - <bullet 2>
   - <bullet 3>

   🤖 Generated with [Claude Code](https://claude.com/claude-code)

   Co-Authored-By: Claude <noreply@anthropic.com>
   EOF
   )"
   ```

4. Verify commit succeeded: `git log -1 --oneline`

5. Mark todo complete

---

### Phase 5: Optional Push

**Goal**: Offer to push if appropriate

**Actions**:

1. Check current branch name from earlier git status

2. **If NOT on main/master**:
   - Ask: "Would you like to push this commit?"
   - If yes:
     - Check if upstream exists: `git rev-parse --abbrev-ref @{u}` (may error if no upstream)
     - If no upstream: `git push -u origin <branch>`
     - If upstream exists: `git push`

3. **If on main/master**:
   - Do NOT offer to push automatically
   - Inform: "You're on main/master. Push manually if needed: `git push`"

---

## Commit Message Examples

### Feature
```
feat: Implement user authentication

- Enable secure multi-user sessions with JWT
- Prevent unauthorized access to protected routes
- Lay groundwork for role-based access control
```

### Bug Fix
```
fix: Prevent null pointer in user validation

- Resolve crashes when user object is undefined
- Ensure defensive checks at API boundaries
```

### Refactoring
```
refactor: Simplify error handling in API layer

- Reduce code duplication across endpoints
- Improve error message clarity for debugging
- Make error handling more maintainable
```

### Chore
```
chore: Update dependencies and resolve warnings

- Address security vulnerabilities in npm packages
- Fix ESLint warnings for cleaner codebase
```

---

## Error Handling

- **No changes to commit**: Inform user and exit gracefully
- **User rejects commit message**: Ask if they want to provide their own or cancel
- **Git command fails**: Show error and ask user to resolve
- **User cancels**: Exit cleanly without committing

---

## Best Practices

1. **One concern per commit**: If files address different concerns, suggest splitting into multiple commits
2. **Focus on "why"**: Commit messages should explain impact and reasoning, not just what changed
3. **Use conventional commits**: Helps with changelog generation and understanding history
4. **Keep it concise**: Respect 80-character limits for readability in git log
5. **Review individually**: Understand each file's changes before committing

---

## Notes

- This command uses TodoWrite to track progress - you'll see phases marked as you go
- Parallel git commands run faster and use fewer tokens
- The commit format includes Claude Code attribution automatically
- Only commit when the user explicitly confirms
