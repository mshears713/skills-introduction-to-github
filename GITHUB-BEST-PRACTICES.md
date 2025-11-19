# 🌟 GitHub Best Practices for Beginner-Intermediate Developers

So you've learned the basics of Git and GitHub. Awesome! But there's a gap between knowing the commands and using them like a professional developer. This guide bridges that gap.

Think of this as your field manual - the real-world wisdom that takes years to accumulate. We'll cover the patterns that make you look like you know what you're doing (because you will), and help you avoid the mistakes that make experienced developers cringe.

---

## 📚 Table of Contents

1. [The Git Mindset](#the-git-mindset)
2. [Commit Best Practices](#commit-best-practices)
3. [Branch Management Strategies](#branch-management-strategies)
4. [Pull Request Excellence](#pull-request-excellence)
5. [Code Review Skills](#code-review-skills)
6. [Common Pitfalls and How to Avoid Them](#common-pitfalls)
7. [GitHub-Specific Power Features](#github-power-features)
8. [Team Collaboration Patterns](#team-collaboration-patterns)
9. [Security and Sensitive Data](#security-and-sensitive-data)
10. [Productivity Tips and Tools](#productivity-tips-and-tools)

---

## 🧠 The Git Mindset

Before we dive into tactics, let's talk strategy. Great developers think differently about version control.

### **Think in Snapshots, Not Changes**

Git doesn't store diffs (differences) - it stores complete snapshots of your project. Each commit is like taking a photo of your entire project at that moment.

**Why this matters:**
- You can jump to any point in your project's history
- You can compare any two points in time
- You're building a time machine for your code

### **Your Commit History is a Story**

Imagine your commit history as a book about your project. Would you rather read:

**Bad story:**
```
- updated stuff
- more changes
- fixed things
- asdfasdf
- final version
- final version for real this time
```

**Good story:**
```
- Add user authentication with JWT tokens
- Create login form component with validation
- Add password reset functionality
- Fix session timeout bug in auth middleware
- Update user model to include last login timestamp
```

The second version tells you what happened, why it happened, and how the project evolved. That's the goal.

### **Branches Are Cheap, Use Them Liberally**

Creating a branch takes milliseconds and zero disk space (initially). There's no excuse not to use them.

**The rule:** Never commit directly to `main` (or `master`). Always use a feature branch.

**Why:**
- Your main branch stays stable
- You can experiment without fear
- You can work on multiple features simultaneously
- You can abandon bad ideas without cluttering history

---

## ✍️ Commit Best Practices

Commits are the atomic units of your project's history. Master these, and you master Git.

### **The Anatomy of a Great Commit**

A great commit has three qualities:

1. **Atomic**: Does one thing and does it completely
2. **Meaningful**: Represents a logical unit of work
3. **Descriptive**: Has a clear message explaining what and why

### **Writing Commit Messages That Don't Suck**

Follow this format (known as the "Conventional Commits" style):

```
<type>: <short summary> (50 characters or less)

<optional longer description explaining why this change was needed>
<and how it addresses the issue, with any nuances worth noting>

<optional footer with references to issues, breaking changes, etc.>
```

**Types you'll commonly use:**
- `feat:` - A new feature
- `fix:` - A bug fix
- `docs:` - Documentation changes
- `style:` - Code style changes (formatting, semicolons, etc.)
- `refactor:` - Code changes that neither fix bugs nor add features
- `test:` - Adding or updating tests
- `chore:` - Maintenance tasks (updating dependencies, etc.)

**Examples:**

```
feat: add dark mode toggle to settings page

Users have requested this feature in issue #42. This adds a toggle
in the settings page that switches between light and dark themes.
The preference is saved in localStorage and persists across sessions.

Closes #42
```

```
fix: prevent race condition in user authentication

The auth middleware was checking session validity before the session
was fully initialized, causing intermittent login failures. Now we
wait for session initialization to complete before validating.

Fixes #128
```

```
refactor: extract email validation into utility function

The email validation logic was duplicated in three components.
This extracts it into a reusable utility function in src/utils/validation.js
```

### **When to Commit**

**Commit when you've completed a logical unit of work:**
- Fixed a bug ✅
- Added a feature (or a working part of one) ✅
- Refactored a function ✅
- Updated documentation ✅

**Don't commit:**
- Code that doesn't compile/run ❌
- Half-finished features (unless using feature flags) ❌
- Commented-out code ❌
- Temporary debugging code ❌

### **The Perfect Commit Size**

Too small:
```
- add variable
- use variable
- rename variable
- fix typo in variable name
```

Too large:
```
- redesign entire authentication system, add user profiles,
  update database schema, refactor API, add tests, update docs
```

Just right:
```
- add user profile page with avatar upload
```

**Rule of thumb:** If you can't summarize the commit in one sentence, it's probably too big.

### **Amending Commits (Use with Caution)**

Made a typo in your last commit message? Forgot to add a file?

```bash
# Amend the last commit (before pushing)
git add forgotten-file.js
git commit --amend
```

**WARNING:** Only amend commits that haven't been pushed. Amending rewrites history, which causes chaos if others have already pulled your commits.

---

## 🌿 Branch Management Strategies

Branches are where the magic happens. Here's how to use them like a pro.

### **Naming Conventions That Make Sense**

Bad branch names:
- `branch1`
- `test`
- `mike-branch`
- `new-stuff`

Good branch names:
- `feature/user-authentication`
- `fix/login-redirect-bug`
- `refactor/database-queries`
- `docs/api-documentation`
- `hotfix/critical-security-patch`

**Pattern:** `<type>/<brief-description>`

**Common types:**
- `feature/` - New features
- `fix/` or `bugfix/` - Bug fixes
- `hotfix/` - Critical fixes that need immediate deployment
- `refactor/` - Code refactoring
- `docs/` - Documentation updates
- `test/` - Test additions or updates
- `chore/` - Maintenance tasks

### **The Feature Branch Workflow**

This is the most common workflow for teams:

1. **Start from an up-to-date main branch**
   ```bash
   git checkout main
   git pull origin main
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/awesome-new-thing
   ```

3. **Do your work, committing along the way**
   ```bash
   git add src/components/AwesomeThing.js
   git commit -m "feat: add AwesomeThing component"
   ```

4. **Keep your branch updated with main**
   ```bash
   # Periodically fetch the latest main
   git checkout main
   git pull origin main
   git checkout feature/awesome-new-thing
   git merge main
   # Or use rebase if you prefer linear history
   git rebase main
   ```

5. **Push your branch**
   ```bash
   git push -u origin feature/awesome-new-thing
   ```

6. **Open a pull request**
   (We'll cover this in detail below)

7. **After merging, clean up**
   ```bash
   git checkout main
   git pull origin main
   git branch -d feature/awesome-new-thing
   ```

### **Long-Lived vs. Short-Lived Branches**

**Short-lived branches** (preferred):
- Created for a specific task
- Live for hours or days
- Merged and deleted quickly
- Easier to review
- Fewer merge conflicts

**Long-lived branches** (use sparingly):
- `main` or `master` - The production-ready code
- `develop` - Integration branch (in some workflows)
- `staging` - Pre-production testing (if needed)

**The rule:** Feature branches should be short-lived. If your branch is alive for more than a week, you're probably trying to do too much at once.

### **Dealing with Merge Conflicts**

Merge conflicts happen when two branches modify the same lines of code. Don't panic!

**When you see a conflict:**
```bash
git merge main
# Auto-merging src/app.js
# CONFLICT (content): Merge conflict in src/app.js
# Automatic merge failed; fix conflicts and then commit the result.
```

**How to resolve:**

1. Open the conflicted file. You'll see:
   ```javascript
   <<<<<<< HEAD
   const greeting = "Hello, World!";
   =======
   const greeting = "Hi there!";
   >>>>>>> main
   ```

2. Decide what to keep:
   ```javascript
   const greeting = "Hello, World!";
   ```

3. Remove the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)

4. Stage and commit:
   ```bash
   git add src/app.js
   git commit -m "merge: resolve conflict in greeting message"
   ```

**Pro tips:**
- Use a merge tool: `git mergetool`
- Keep feature branches small to minimize conflicts
- Merge main into your branch regularly
- Communicate with your team about which files you're working on

---

## 🎯 Pull Request Excellence

Pull requests (PRs) are where code review happens. A great PR is a joy to review; a bad one is torture.

### **Before You Open a PR**

**Checklist:**
- [ ] All tests pass
- [ ] Code follows the project's style guide
- [ ] No commented-out code
- [ ] No console.logs or debug statements
- [ ] Documentation is updated (if needed)
- [ ] Commit messages are clear
- [ ] Branch is up-to-date with main
- [ ] You've tested your changes locally

### **Writing a PR Description That Gets Approved**

**Bad PR description:**
```
Updated some stuff
```

**Good PR description:**
```markdown
## Summary
Adds user profile editing functionality

## Changes
- Created ProfileEdit component
- Added API endpoint for updating user data
- Implemented form validation
- Added unit tests for validation logic

## Why
Users have been requesting the ability to edit their profiles (issue #42)

## Testing
- [x] Manual testing in dev environment
- [x] All existing tests pass
- [x] Added new tests for validation

## Screenshots
[Include before/after screenshots if UI changed]

## Notes for Reviewers
- I used Formik for form handling - open to other suggestions
- The avatar upload feature will come in a separate PR

Closes #42
```

### **PR Title Conventions**

Use the same format as commit messages:

```
feat: add user profile editing
fix: prevent crash when username is empty
docs: update API documentation for auth endpoints
```

### **The Perfect PR Size**

**Too small:**
- Changing one typo (just push to main or bundle with other docs changes)

**Too large:**
- 50 files changed, 2000+ lines added/removed
- Reviewers will either skim it or put it off forever

**Just right:**
- 3-10 files changed
- 100-300 lines added/removed
- Focused on one feature or fix
- Reviewable in 15-30 minutes

**Rule:** If your PR is huge, break it into smaller PRs. Use feature flags to merge incomplete features safely.

### **Responding to Review Comments**

You'll get feedback. That's the point! Here's how to handle it professionally:

**When the suggestion is good:**
```markdown
Good catch! I've updated the code and pushed a new commit.
```

**When you disagree:**
```markdown
I considered that approach, but chose this one because [reason].
I'm open to changing it if you feel strongly. What do you think?
```

**When you need clarification:**
```markdown
Can you elaborate on what you mean here? I want to make sure I understand correctly.
```

**When you'll fix it later:**
```markdown
Great point. I'll create a follow-up issue (#123) to address this,
as it's outside the scope of this PR.
```

**Always:**
- Be professional and respectful
- Assume good intentions
- Explain your reasoning
- Don't take it personally
- Thank reviewers for their time

### **Using Draft PRs**

Opening a PR but not ready for review? Mark it as a draft:

- Click "Create draft pull request" instead of "Create pull request"
- Use when you want early feedback or need to trigger CI
- Convert to "Ready for review" when it's done

---

## 👀 Code Review Skills

Being a good reviewer makes you a better developer. Here's how to review code like a pro.

### **What to Look For**

**Functionality:**
- Does the code do what it claims to do?
- Are there edge cases not handled?
- Could this break existing features?

**Code Quality:**
- Is it readable and maintainable?
- Are variables and functions well-named?
- Is there duplicate code that could be extracted?
- Is it more complex than necessary?

**Best Practices:**
- Does it follow the project's conventions?
- Are there security issues?
- Are errors handled properly?
- Is it testable?

**Tests:**
- Are there tests for new functionality?
- Do the tests actually test the right things?
- Are edge cases covered?

### **How to Give Feedback**

**Be specific:**
❌ "This could be better"
✅ "Consider extracting this logic into a separate function for reusability"

**Explain why:**
❌ "Don't use var"
✅ "Use `const` or `let` instead of `var` to prevent hoisting issues and make scope clearer"

**Suggest, don't demand:**
❌ "Change this immediately"
✅ "Have you considered using a Map instead of an object here? It might be more performant for large datasets"

**Praise good work:**
✅ "Nice job handling this edge case!"
✅ "This is a clever solution"
✅ "Great test coverage"

**Use GitHub's review features:**
- **Comment** - Ask questions or point out issues
- **Approve** - The code looks good, ready to merge
- **Request changes** - Issues that must be fixed before merging

### **The Review Checklist**

- [ ] I understand what this code is trying to do
- [ ] The code does what the PR description claims
- [ ] There are no obvious bugs or edge cases missed
- [ ] The code is readable and maintainable
- [ ] Tests are present and meaningful
- [ ] No security issues or secrets exposed
- [ ] Performance seems reasonable
- [ ] Documentation is updated if needed

---

## ⚠️ Common Pitfalls

Learn from others' mistakes. Here are the traps beginners fall into:

### **Pitfall #1: Committing Secrets**

**The mistake:**
```javascript
// config.js
const API_KEY = "sk_live_abc123secretkey";
```

**Why it's bad:**
- Once committed, the secret is in your Git history forever
- Even if you delete it later, it's still there
- Bots scan GitHub for API keys and exploit them

**The fix:**
```javascript
// config.js
const API_KEY = process.env.API_KEY;
```

```
# .env (NOT committed to Git)
API_KEY=sk_live_abc123secretkey
```

```
# .gitignore
.env
.env.local
.env.production
*.key
*.pem
```

**Already committed a secret?**
1. Immediately rotate/revoke the secret (generate a new one)
2. Use `git filter-branch` or BFG Repo-Cleaner to remove it from history
3. Force push (and notify your team)

### **Pitfall #2: Committing Directly to Main**

**The mistake:**
```bash
git checkout main
# make changes
git commit -m "quick fix"
git push origin main
```

**Why it's bad:**
- No code review
- No CI/CD checks
- Can break production
- No traceability

**The fix:**
Always use feature branches, even for "quick fixes":
```bash
git checkout -b fix/quick-bug
# make changes
git commit -m "fix: resolve quick bug"
git push origin fix/quick-bug
# Open PR, get review, then merge
```

### **Pitfall #3: Giant Commits**

**The mistake:**
```
git commit -m "updated everything"
# 50 files changed, 3000 lines added/removed
```

**Why it's bad:**
- Impossible to review properly
- Hard to find bugs later
- Can't revert specific changes
- Loses the story of what changed

**The fix:**
Commit logically related changes together:
```bash
git add src/components/LoginForm.js
git commit -m "feat: add login form component"

git add src/api/auth.js
git commit -m "feat: add authentication API endpoints"

git add tests/auth.test.js
git commit -m "test: add auth API tests"
```

### **Pitfall #4: Meaningless Commit Messages**

**The mistake:**
```
git commit -m "fix"
git commit -m "update"
git commit -m "asdf"
git commit -m "final version"
git commit -m "final version 2"
```

**Why it's bad:**
- Can't understand what changed without reading code
- Makes debugging harder
- Looks unprofessional

**The fix:**
Describe what and why:
```bash
git commit -m "fix: prevent null pointer exception in user profile"
git commit -m "update: migrate from axios to fetch API for better bundle size"
git commit -m "feat: add password strength indicator"
```

### **Pitfall #5: Not Reading Error Messages**

**The mistake:**
```bash
git push origin main
# ERROR: permission denied
# You: "Git is broken!"
```

**The fix:**
Read the error message. It usually tells you exactly what's wrong:
```
ERROR: Permission to user/repo.git denied to username.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

Translation: Your SSH keys aren't set up correctly, or you don't have write access to this repo.

### **Pitfall #6: Forgetting to Pull Before Pushing**

**The mistake:**
```bash
git commit -m "my changes"
git push origin main
# ERROR: Updates were rejected because the remote contains work that you do not have locally
```

**Why it happens:**
Someone else pushed changes while you were working.

**The fix:**
```bash
git pull origin main
# Resolve any conflicts
git push origin main
```

**Better:** Use feature branches so this happens less often.

---

## 🚀 GitHub-Specific Power Features

GitHub is more than just Git hosting. Here are features that boost your productivity:

### **GitHub Issues: Project Management Built-In**

**Use issues to:**
- Track bugs
- Plan features
- Discuss ideas
- Assign tasks

**Anatomy of a great issue:**
```markdown
## Description
The login form crashes when username contains special characters

## Steps to Reproduce
1. Go to /login
2. Enter username: "test@user"
3. Click submit
4. App crashes with TypeError

## Expected Behavior
Should accept usernames with @ symbol

## Actual Behavior
Crashes with "Cannot read property 'match' of undefined"

## Environment
- Browser: Chrome 120
- OS: macOS 14
- App version: 1.2.3

## Possible Solution
The username validation regex doesn't account for @ symbols
```

**Pro tips:**
- Use labels (bug, enhancement, documentation, etc.)
- Assign issues to people
- Link issues to PRs using keywords: "Closes #42"
- Use issue templates for consistency

### **GitHub Projects: Kanban Boards**

Organize your issues into boards:
- **Backlog**: Things to do eventually
- **To Do**: Things to do soon
- **In Progress**: Currently being worked on
- **In Review**: PRs open and awaiting review
- **Done**: Completed tasks

### **GitHub Actions: Automation Superpowers**

You've seen workflows in this repo. Here's what else you can automate:

**Run tests on every PR:**
```yaml
name: Tests
on: [pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: npm test
```

**Auto-deploy when merging to main:**
```yaml
name: Deploy
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to production
        run: ./deploy.sh
```

**Common automation ideas:**
- Run linters and formatters
- Build and test on multiple platforms
- Auto-assign reviewers to PRs
- Post updates to Slack
- Generate release notes
- Check for security vulnerabilities

### **GitHub CLI: Command Line Power**

Install `gh` CLI for terminal-based GitHub operations:

```bash
# Create a PR from command line
gh pr create --title "feat: add dark mode" --body "Implements dark mode toggle"

# Check PR status
gh pr status

# Review a PR
gh pr checkout 42
gh pr review --approve

# Create an issue
gh issue create --title "Bug in login" --body "Users can't log in with @ in username"

# View issues
gh issue list
```

### **Code Owners**

Automatically request reviews from specific people for specific files:

```
# .github/CODEOWNERS
*.js    @frontend-team
*.py    @backend-team
/docs/  @documentation-team
```

Now when someone opens a PR touching JavaScript files, the frontend team is automatically requested for review.

### **Protected Branches**

Prevent accidental pushes to important branches:

**Settings → Branches → Add rule:**
- Require pull request reviews before merging
- Require status checks to pass (tests, linting, etc.)
- Require conversation resolution before merging
- Require signed commits
- Include administrators (rules apply to everyone)

This forces good practices and prevents "oops" moments.

---

## 👥 Team Collaboration Patterns

Working with others? These patterns keep teams productive:

### **Communication is Key**

**Before starting work:**
- Check if someone else is working on it
- Create or claim an issue
- Discuss approach if it's complex

**While working:**
- Push your branch regularly (even WIP commits)
- Update the issue with progress
- Ask for help when stuck

**When opening a PR:**
- Tag relevant team members
- Explain non-obvious decisions
- Highlight areas where you want feedback

### **Branching Strategies for Teams**

**Git Flow** (traditional):
- `main` - Production
- `develop` - Integration branch
- `feature/*` - New features (branch from develop)
- `release/*` - Release preparation
- `hotfix/*` - Emergency fixes (branch from main)

**GitHub Flow** (simpler, recommended):
- `main` - Always deployable
- `feature/*` - All work happens in feature branches
- PR → merge to main → deploy

**Trunk-Based Development** (advanced):
- Everyone commits to `main` frequently
- Use feature flags to hide incomplete features
- Deploy multiple times per day

Choose based on your team's size and deployment frequency.

### **Handling Disagreements**

Code reviews can get heated. Stay professional:

**When you disagree about an approach:**
1. Explain your reasoning clearly
2. Ask questions to understand their perspective
3. Be willing to compromise
4. If it's blocking progress, get a third opinion
5. Remember: working code shipped > perfect code in review forever

**When to stand firm:**
- Security issues
- Privacy violations
- Breaking changes without discussion
- Code that will cause production outages

**When to let it go:**
- Stylistic preferences (use a linter instead)
- Micro-optimizations
- "Not how I would do it" (if their way works fine)

---

## 🔐 Security and Sensitive Data

Protect yourself and your users:

### **Never Commit These:**

- API keys and tokens
- Passwords and credentials
- Private keys (SSH, SSL, etc.)
- Database credentials
- OAuth secrets
- Encryption keys
- AWS access keys
- Personal data (emails, addresses, etc.)

### **How to Stay Safe:**

1. **Use environment variables**
   ```javascript
   const dbPassword = process.env.DB_PASSWORD;
   ```

2. **Use .gitignore properly**
   ```
   .env
   .env.local
   .env.*.local
   config/secrets.yml
   *.key
   *.pem
   ```

3. **Use secrets management**
   - GitHub Secrets for Actions
   - HashiCorp Vault for production
   - AWS Secrets Manager
   - Environment-specific config files

4. **Scan for secrets regularly**
   - Use tools like git-secrets, truffleHog, or Gitleaks
   - Enable GitHub's secret scanning
   - Set up pre-commit hooks to catch secrets before they're committed

5. **If you leak a secret:**
   - Rotate it immediately (assume it's compromised)
   - Remove from Git history (BFG Repo-Cleaner or git filter-repo)
   - Audit logs to see if it was accessed
   - Document the incident

---

## 🛠️ Productivity Tips and Tools

Level up your GitHub game with these tips:

### **Command Line Shortcuts**

```bash
# Status (short format)
git status -s

# Diff staged changes
git diff --staged

# Commit all modified files
git commit -am "your message"

# Undo last commit (keep changes)
git reset --soft HEAD~1

# Discard all local changes
git reset --hard HEAD

# View commit history (pretty)
git log --oneline --graph --all

# Find who changed a line
git blame filename.js

# Search commit messages
git log --grep="bug fix"

# Create alias for common commands
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
```

### **Git GUI Tools**

Command line not your thing? Try these:

- **GitHub Desktop** - Official, simple, great for beginners
- **GitKraken** - Beautiful interface, visual merge conflict resolution
- **SourceTree** - Free, feature-rich
- **VS Code** - Built-in Git support

### **VS Code Extensions**

- **GitLens** - Supercharges Git in VS Code
- **GitHub Pull Requests** - Review PRs in your editor
- **Git Graph** - Visualize your repository
- **Git History** - Browse Git history easily

### **Useful GitHub Keyboard Shortcuts**

Press `?` on any GitHub page to see all shortcuts. Here are the best:

- `t` - File finder
- `l` - Jump to line number
- `w` - Switch branch or tag
- `s` - Focus search bar
- `g c` - Go to Code tab
- `g i` - Go to Issues
- `g p` - Go to Pull requests

### **Conventional Commits + Semantic Versioning**

Automate your release process:

1. Use conventional commits (feat, fix, etc.)
2. Use a tool like `semantic-release`
3. It automatically:
   - Determines next version number
   - Generates changelog
   - Creates GitHub release
   - Publishes to npm (if applicable)

### **Pre-commit Hooks**

Run checks before committing:

```bash
# Install husky
npm install husky --save-dev

# Add pre-commit hook
npx husky add .husky/pre-commit "npm test"
npx husky add .husky/pre-commit "npm run lint"
```

Now your tests and linter run automatically before each commit. Bad code never gets committed!

---

## 🎓 Continuous Learning

You've learned a ton, but there's always more:

### **Practice Projects**

- Contribute to open source (start with issues labeled "good first issue")
- Create a personal project and use proper Git workflow
- Fork a project and add a feature
- Review other people's PRs to learn different approaches

### **Resources to Explore**

- **GitHub Docs** - Comprehensive and well-written
- **Pro Git Book** - Free online, covers everything about Git
- **GitHub Skills** - Interactive tutorials (like this one!)
- **Oh My Git!** - A game to learn Git
- **Git Katas** - Practice exercises

### **Topics to Explore Next**

- Git rebase vs merge
- Cherry-picking commits
- Git bisect for bug hunting
- Submodules and subtrees
- Git hooks
- GitHub GraphQL API
- GitHub Apps and Marketplace

---

## 🎯 Final Thoughts

You now have the knowledge to:
- Write meaningful commits
- Create clean PRs
- Review code professionally
- Collaborate effectively
- Avoid common mistakes
- Use GitHub's power features

**Remember:**
- Everyone was a beginner once
- Making mistakes is how you learn
- The best code is code that ships
- Communication matters as much as code
- Be kind to your future self (and your teammates)

**The mark of a great developer isn't never making mistakes - it's making each mistake only once.**

Now get out there and build something amazing. Your commit history is waiting to tell a great story.

---

**Happy coding! May your builds be green and your merge conflicts be few.**
