# 🔧 Practical GitHub Workflows: Real-World Scenarios

Theory is great, but nothing beats practical examples. This guide walks through common scenarios you'll face as a developer, with step-by-step solutions.

Think of this as your cookbook - when you're stuck, find the recipe that matches your situation and follow along.

---

## 📚 Table of Contents

1. [Daily Development Workflow](#daily-development-workflow)
2. [Starting a New Feature](#starting-a-new-feature)
3. [Fixing a Bug](#fixing-a-bug)
4. [Handling Merge Conflicts](#handling-merge-conflicts)
5. [Updating Your Branch with Main](#updating-your-branch)
6. [Undoing Mistakes](#undoing-mistakes)
7. [Collaborating on Someone Else's Branch](#collaborating-on-a-branch)
8. [Release Management](#release-management)
9. [Emergency Hotfixes](#emergency-hotfixes)
10. [Code Review Workflow](#code-review-workflow)
11. [Working with Forks](#working-with-forks)
12. [Troubleshooting Common Issues](#troubleshooting)

---

## 📅 Daily Development Workflow

**Scenario:** You're starting your workday. Here's your routine to stay in sync with your team.

### Morning Sync Routine

```bash
# 1. Make sure you're on main branch
git checkout main

# 2. Get the latest changes from remote
git pull origin main

# 3. Check what branches you have locally
git branch

# 4. Clean up branches that have been merged
git branch --merged | grep -v "main" | xargs git branch -d

# 5. Check if there are any uncommitted changes
git status
```

### Before Ending Your Day

```bash
# 1. Make sure all your work is committed
git status

# 2. Push your current branch to remote (backup!)
git push origin your-feature-branch

# 3. Optional: Update your branch with latest main
git fetch origin main
git merge origin/main
```

**Why this matters:**
- Starting fresh each day prevents surprises
- Cleaning up old branches keeps your workspace tidy
- Pushing daily means your work is backed up

---

## 🚀 Starting a New Feature

**Scenario:** Your team wants you to add a "dark mode" feature.

### Step-by-Step Process

```bash
# 1. Start from an up-to-date main branch
git checkout main
git pull origin main

# 2. Create a feature branch with a descriptive name
git checkout -b feature/dark-mode-toggle

# 3. Create or modify files
# (Work in your editor here)

# 4. Check what you've changed
git status
git diff

# 5. Stage your changes
git add src/components/ThemeToggle.js
git add src/styles/dark-mode.css

# 6. Commit with a meaningful message
git commit -m "feat: add dark mode toggle component

Created a new ThemeToggle component that allows users to switch
between light and dark themes. The preference is saved to localStorage.

Part of #123"

# 7. Push to remote
git push -u origin feature/dark-mode-toggle
```

### Opening the Pull Request

```bash
# Option 1: Use GitHub CLI
gh pr create --title "feat: add dark mode toggle" \
  --body "Implements dark mode functionality as described in #123"

# Option 2: Go to GitHub.com
# GitHub will show a "Compare & pull request" button
# Click it and fill out the PR form
```

### PR Description Template

```markdown
## Summary
Adds a dark mode toggle to the application settings

## Changes
- Created ThemeToggle component
- Added dark mode CSS variables
- Implemented localStorage persistence
- Updated Settings page to include toggle

## Testing
- [x] Manual testing in dev environment
- [x] Tested in Chrome, Firefox, Safari
- [x] Verified localStorage persistence works
- [ ] Added unit tests (will add in follow-up)

## Screenshots
[Attach before/after screenshots]

## Notes
- Using CSS variables for easy theme switching
- Compatible with all modern browsers
- No breaking changes

Closes #123
```

---

## 🐛 Fixing a Bug

**Scenario:** Users report that the login button doesn't work on mobile devices.

### Investigation Phase

```bash
# 1. Create a bug fix branch
git checkout main
git pull origin main
git checkout -b fix/mobile-login-button

# 2. Reproduce the bug locally
# (Use browser dev tools, test on mobile, etc.)

# 3. Find the problematic code
git log --all --oneline -- src/components/LoginButton.js
# This shows commit history for that file

git blame src/components/LoginButton.js
# This shows who wrote each line
```

### Fix Phase

```bash
# 1. Make your fix
# (Edit files in your editor)

# 2. Test thoroughly
npm test
npm run test:mobile  # If you have mobile-specific tests

# 3. Commit the fix
git add src/components/LoginButton.js
git commit -m "fix: resolve login button not working on mobile devices

The button had a fixed width that was too small for mobile screens.
Changed to use min-width and made it responsive to screen size.

Tested on:
- iPhone 12 (iOS 15)
- Samsung Galaxy S21 (Android 12)
- Chrome mobile emulator

Fixes #456"

# 4. Push and create PR
git push -u origin fix/mobile-login-button
gh pr create --title "fix: mobile login button not working"
```

### Testing Checklist for Bug Fixes

- [ ] Bug is actually fixed (obvious, but verify!)
- [ ] Fix doesn't break anything else
- [ ] Added test to prevent regression
- [ ] Tested on affected platforms/browsers
- [ ] Updated documentation if needed

---

## 🔀 Handling Merge Conflicts

**Scenario:** You try to merge main into your feature branch and Git complains about conflicts.

### When Conflicts Happen

```bash
git checkout feature/your-feature
git merge main

# Output:
# Auto-merging src/app.js
# CONFLICT (content): Merge conflict in src/app.js
# Automatic merge failed; fix conflicts and then commit the result.
```

### Resolving Step-by-Step

```bash
# 1. See which files have conflicts
git status

# Output:
# On branch feature/your-feature
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#     both modified:   src/app.js
```

### Understanding the Conflict Markers

Open the conflicted file:

```javascript
<<<<<<< HEAD (Current Change - Your Branch)
function greetUser() {
  return "Hello, " + username + "!";
}
=======
function greetUser(name) {
  return `Hi ${name}, welcome back!`;
}
>>>>>>> main (Incoming Change)
```

**What this means:**
- `<<<<<<< HEAD` - Your branch's version
- `=======` - Separator
- `>>>>>>> main` - The main branch's version

### Choosing the Right Resolution

**Option 1: Keep your version**
```javascript
function greetUser() {
  return "Hello, " + username + "!";
}
```

**Option 2: Keep their version**
```javascript
function greetUser(name) {
  return `Hi ${name}, welcome back!`;
}
```

**Option 3: Combine both (often the best choice)**
```javascript
function greetUser(name = username) {
  return `Hello ${name}, welcome back!`;
}
```

### Completing the Merge

```bash
# 1. After resolving conflicts, stage the files
git add src/app.js

# 2. Check all conflicts are resolved
git status
# Should show "All conflicts fixed but you are still merging"

# 3. Complete the merge
git commit -m "merge: resolve conflicts with main branch"

# 4. Push the updated branch
git push origin feature/your-feature
```

### Using a Merge Tool

```bash
# Configure your preferred merge tool
git config --global merge.tool vscode  # or meld, kdiff3, etc.

# When conflicts happen, launch the tool
git mergetool

# It provides a visual interface to resolve conflicts
# After resolving, commit as normal
```

---

## 🔄 Updating Your Branch with Main

**Scenario:** You've been working on a feature for a few days. Meanwhile, your team has merged several PRs into main. You need to update your branch.

### Method 1: Merge (Preserves History)

```bash
# 1. Fetch latest from remote
git fetch origin

# 2. While on your feature branch
git checkout feature/your-feature

# 3. Merge main into your branch
git merge origin/main

# 4. Resolve any conflicts (see section above)

# 5. Push the updated branch
git push origin feature/your-feature
```

**Pros:**
- Preserves complete history
- Non-destructive
- Easy to understand

**Cons:**
- Creates merge commits
- History can get messy with many merges

### Method 2: Rebase (Clean History)

```bash
# 1. Fetch latest from remote
git fetch origin

# 2. While on your feature branch
git checkout feature/your-feature

# 3. Rebase onto main
git rebase origin/main

# 4. Resolve conflicts ONE COMMIT AT A TIME
# After resolving each conflict:
git add .
git rebase --continue

# 5. Force push (because history was rewritten)
git push --force-with-lease origin feature/your-feature
```

**Pros:**
- Clean, linear history
- Easier to understand commit sequence
- Looks professional

**Cons:**
- Rewrites history (don't do this on shared branches!)
- More complex conflict resolution
- Requires force push

**When to use each:**
- **Merge**: When others are working on the same branch
- **Rebase**: When you're working alone on a feature branch

### The "Never Force Push to Shared Branches" Rule

```bash
# NEVER do this on main/develop
git push --force origin main  # ❌ DON'T

# OK to do on your personal feature branch
git push --force-with-lease origin feature/your-feature  # ✅ OK

# --force-with-lease is safer than --force
# It prevents overwriting if someone else pushed to your branch
```

---

## ⏪ Undoing Mistakes

**Scenario:** You messed up. Don't panic - Git has your back.

### Mistake: Committed to Wrong Branch

**You did:**
```bash
git checkout main  # Oops, meant to create a branch first
# ...made changes...
git commit -m "feat: new feature"  # OH NO
```

**The fix:**
```bash
# 1. Create the branch you meant to create (it includes your commit)
git branch feature/new-feature

# 2. Reset main to before your commit
git reset --hard origin/main

# 3. Switch to your feature branch
git checkout feature/new-feature

# Now your commit is on the right branch!
```

### Mistake: Wrong Commit Message

**Just committed with a typo:**
```bash
# Amend the last commit (only if not pushed yet!)
git commit --amend -m "feat: correct message here"
```

**Already pushed? Create a new commit:**
```bash
# Don't amend after pushing (unless working alone)
# Just make your next commit message clear
```

### Mistake: Committed Sensitive Data

**You committed an API key:**
```bash
# 1. Remove the sensitive data from the file
# Edit the file to remove secrets

# 2. Commit the removal
git add .
git commit -m "chore: remove sensitive data"

# 3. If already pushed, IMMEDIATELY:
# - Rotate/revoke the secret (generate a new one)
# - Remove from Git history using BFG Repo-Cleaner or git-filter-repo

# Example with git filter-repo:
git filter-repo --invert-paths --path path/to/sensitive-file
git push --force origin main

# 4. Notify your team about the force push
```

### Mistake: Need to Undo Last Commit (Keep Changes)

```bash
# Undo commit but keep changes in working directory
git reset --soft HEAD~1

# Now you can:
# - Make more changes
# - Re-commit with better message
# - Split into multiple commits
```

### Mistake: Need to Undo Last Commit (Discard Changes)

```bash
# ⚠️ WARNING: This deletes your changes permanently!
git reset --hard HEAD~1

# Only do this if you're SURE you want to lose those changes
```

### Mistake: Made Changes to Wrong File

```bash
# Discard changes to a specific file
git checkout -- path/to/wrong-file.js

# Or in newer Git:
git restore path/to/wrong-file.js
```

### Mistake: Need to Recover Deleted Commit

```bash
# Git keeps deleted commits for ~30 days
# View all actions you've taken
git reflog

# Find the commit hash you want to recover
# Output shows something like:
# a1b2c3d HEAD@{1}: commit: feat: that feature I deleted

# Restore it
git checkout a1b2c3d

# Create a branch from it
git checkout -b recovered-feature
```

---

## 👥 Collaborating on Someone Else's Branch

**Scenario:** A teammate started a feature, but they're out sick. You need to finish it.

### Fetching Their Branch

```bash
# 1. Fetch all branches from remote
git fetch origin

# 2. Check out their branch
git checkout feature/teammate-feature
# This creates a local copy tracking the remote branch

# 3. Make sure you're up to date
git pull origin feature/teammate-feature
```

### Making Changes

```bash
# 1. Make your changes
# (Edit files)

# 2. Commit your work
git commit -am "feat: continue work on teammate's feature"

# 3. Push to their branch
git push origin feature/teammate-feature

# 4. Let them know you pushed to their branch
# (Comment on the PR or message them)
```

### Best Practices for Shared Branches

```bash
# Always pull before pushing
git pull origin feature/shared-feature
git push origin feature/shared-feature

# Communicate before force-pushing
# (Spoiler: don't force-push to shared branches!)

# Use meaningful commits
# Your teammate needs to understand what you did

# Consider pair programming
# Screen share and code together
```

---

## 📦 Release Management

**Scenario:** You're ready to release version 2.0 of your app.

### Semantic Versioning Recap

Format: `MAJOR.MINOR.PATCH` (e.g., 2.3.1)

- **MAJOR**: Breaking changes (2.0.0)
- **MINOR**: New features, backwards compatible (2.1.0)
- **PATCH**: Bug fixes (2.0.1)

### Creating a Release

```bash
# 1. Make sure main is ready
git checkout main
git pull origin main

# 2. Run all tests
npm test
npm run build

# 3. Update version in package.json (if applicable)
npm version minor  # or major/patch
# This creates a commit and tag automatically

# 4. Push the tag
git push origin main --tags

# 5. Create GitHub release
gh release create v2.1.0 \
  --title "Version 2.1.0 - Dark Mode Update" \
  --notes "## Features
- Added dark mode toggle
- Improved mobile responsiveness

## Bug Fixes
- Fixed login button on mobile
- Resolved session timeout issue"
```

### Automated Releases with GitHub Actions

Create `.github/workflows/release.yml`:

```yaml
name: Release
on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build
        run: npm ci && npm run build

      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
          draft: false
          prerelease: false
```

Now pushing a tag automatically creates a release!

---

## 🚨 Emergency Hotfixes

**Scenario:** Production is down! There's a critical bug that needs fixing NOW.

### Hotfix Workflow

```bash
# 1. Create hotfix branch from main (production)
git checkout main
git pull origin main
git checkout -b hotfix/critical-login-bug

# 2. Make the minimal fix required
# (Only fix the critical issue, nothing else!)

# 3. Test thoroughly but quickly
npm test
# Manual testing

# 4. Commit
git commit -am "hotfix: resolve critical login authentication bug

Users were unable to log in due to expired SSL certificate.
Updated certificate and added expiration monitoring.

Fixes #789"

# 5. Push and create PR
git push -u origin hotfix/critical-login-bug
gh pr create --title "HOTFIX: Critical login bug" \
  --body "Emergency fix for production login issue. Needs immediate review and deploy."

# 6. Get emergency approval and merge
# (Skip normal review process for true emergencies)

# 7. Merge hotfix to main
git checkout main
git merge hotfix/critical-login-bug
git push origin main

# 8. IMPORTANT: Merge hotfix to develop too (if you use Git Flow)
git checkout develop
git merge hotfix/critical-login-bug
git push origin develop

# 9. Deploy immediately
# (Trigger deployment process)

# 10. Delete hotfix branch
git branch -d hotfix/critical-login-bug
git push origin --delete hotfix/critical-login-bug
```

### Hotfix Best Practices

**DO:**
- Fix only the critical issue
- Test the fix thoroughly
- Document what broke and why
- Merge to all relevant branches
- Schedule a post-mortem to prevent recurrence

**DON'T:**
- Add unrelated features
- Refactor code
- Skip testing
- Forget to merge back to development branches

---

## 🔍 Code Review Workflow

**Scenario:** Your teammate opened a PR and assigned you as a reviewer.

### Reviewing a Pull Request

```bash
# 1. Fetch the PR branch
git fetch origin
git checkout feature/their-feature

# Or use GitHub CLI
gh pr checkout 123  # PR number

# 2. Pull latest changes
git pull origin feature/their-feature

# 3. Run the code locally
npm install  # Install any new dependencies
npm run dev  # Start development server
npm test     # Run tests

# 4. Review the code
# - Read the PR description
# - Check out the changes in GitHub
# - Test functionality manually
# - Look for bugs, edge cases, security issues
```

### Leaving Review Comments

**On GitHub:**
1. Go to "Files changed" tab
2. Hover over a line and click the `+` button
3. Leave your comment
4. Use "Start a review" (not "Add single comment")
5. This batches all your comments into one notification

**Types of comments:**

**Blocking issue (must fix):**
```markdown
This could cause a null pointer exception if `user` is undefined.
Consider adding a null check:

`if (!user) return null;`
```

**Suggestion (nice to have):**
```markdown
**Nitpick:** Consider extracting this into a helper function for reusability.
Not blocking, but might make the code cleaner.
```

**Question (seeking clarification):**
```markdown
Why did you choose approach A over approach B here?
Just trying to understand the reasoning.
```

**Praise (always appreciated):**
```markdown
Nice! This is a much cleaner solution than what we had before.
```

### Submitting Your Review

Three options:

1. **Approve** - "Looks good to me! (LGTM)"
   ```markdown
   Great work! Code looks solid and tests pass.
   Approved for merge.
   ```

2. **Request changes** - "Please address these issues"
   ```markdown
   Good start, but there are a few issues to fix:
   - The null check in line 42
   - The test coverage for the error case

   Once those are addressed, happy to approve!
   ```

3. **Comment** - "Just leaving feedback, no approval needed"
   ```markdown
   Left a few questions inline. Looking good overall!
   ```

### After Your Feedback is Addressed

```bash
# 1. Pull the updated branch
git pull origin feature/their-feature

# 2. Verify fixes
npm test
# Check that your concerns were addressed

# 3. Approve the PR
# On GitHub: "Files changed" → "Review changes" → "Approve"

# Or via CLI
gh pr review 123 --approve -b "LGTM! Thanks for addressing the feedback."
```

---

## 🍴 Working with Forks

**Scenario:** You want to contribute to an open-source project.

### Initial Fork Setup

```bash
# 1. Fork the repo on GitHub (click "Fork" button)

# 2. Clone YOUR fork
git clone https://github.com/YOUR-USERNAME/project-name.git
cd project-name

# 3. Add the original repo as "upstream"
git remote add upstream https://github.com/ORIGINAL-OWNER/project-name.git

# 4. Verify remotes
git remote -v
# origin    https://github.com/YOUR-USERNAME/project-name.git (fetch)
# origin    https://github.com/YOUR-USERNAME/project-name.git (push)
# upstream  https://github.com/ORIGINAL-OWNER/project-name.git (fetch)
# upstream  https://github.com/ORIGINAL-OWNER/project-name.git (push)
```

### Keeping Your Fork Updated

```bash
# 1. Fetch from upstream
git fetch upstream

# 2. Merge upstream changes into your main
git checkout main
git merge upstream/main

# 3. Push to your fork
git push origin main
```

### Contributing to the Project

```bash
# 1. Create a feature branch from updated main
git checkout main
git pull upstream main
git checkout -b feature/your-contribution

# 2. Make your changes
# (Follow the project's contribution guidelines!)

# 3. Commit
git commit -am "feat: add awesome feature"

# 4. Push to YOUR fork
git push origin feature/your-contribution

# 5. Open a PR from your fork to the original repo
# Go to GitHub → your fork → "Compare & pull request"
# The PR will go from: YOUR-FORK:feature-branch → ORIGINAL:main
```

### Syncing Feature Branch with Upstream

```bash
# While on your feature branch
git fetch upstream
git rebase upstream/main

# Resolve conflicts if any
git push --force-with-lease origin feature/your-contribution
```

---

## 🔧 Troubleshooting Common Issues

### Issue: "Permission denied (publickey)"

**Problem:** SSH keys not set up correctly.

**Solution:**
```bash
# 1. Generate SSH key (if you don't have one)
ssh-keygen -t ed25519 -C "your_email@example.com"

# 2. Start SSH agent
eval "$(ssh-agent -s)"

# 3. Add key to agent
ssh-add ~/.ssh/id_ed25519

# 4. Copy public key
cat ~/.ssh/id_ed25519.pub
# Copy the output

# 5. Add to GitHub
# GitHub.com → Settings → SSH and GPG keys → New SSH key
# Paste your public key

# 6. Test connection
ssh -T git@github.com
```

### Issue: "Merge conflict" (Too Scary to Handle)

**Problem:** Conflicts seem overwhelming.

**Solution:**
```bash
# 1. Abort the merge
git merge --abort

# 2. Try rebasing one commit at a time
git rebase -i origin/main

# 3. Or ask for help!
# Create a draft PR and ask a teammate to pair program the merge
```

### Issue: "Detached HEAD state"

**Problem:** You checked out a specific commit.

**Solution:**
```bash
# If you want to keep changes
git checkout -b new-branch-name

# If you want to discard and go back to main
git checkout main
```

### Issue: "Your branch is ahead of 'origin/main' by X commits"

**Problem:** You made commits locally but didn't push.

**Solution:**
```bash
# Push your commits
git push origin your-branch-name

# Or if you want to discard them
git reset --hard origin/your-branch-name  # ⚠️ Loses your commits!
```

### Issue: "Failed to push because remote contains work you don't have"

**Problem:** Someone else pushed to the same branch.

**Solution:**
```bash
# 1. Pull first (incorporates their changes)
git pull origin your-branch-name

# 2. Resolve any conflicts

# 3. Then push
git push origin your-branch-name
```

### Issue: "I deleted a file by accident"

**Problem:** You deleted a file and want it back.

**Solution:**
```bash
# If not committed yet
git checkout -- path/to/deleted-file.js

# If committed
git log --all --full-history -- path/to/deleted-file.js
# Find the commit hash before deletion
git checkout <commit-hash> -- path/to/deleted-file.js
```

### Issue: "Git is slow"

**Problem:** Operations taking forever.

**Solution:**
```bash
# Clean up and optimize
git gc --aggressive --prune=now

# Reduce repository size
git repack -a -d --depth=250 --window=250
```

---

## 🎯 Cheat Sheet: Daily Commands

### Starting Work
```bash
git checkout main
git pull origin main
git checkout -b feature/my-feature
```

### During Work
```bash
git status                    # What changed?
git diff                      # See changes
git add .                     # Stage everything
git commit -m "message"       # Commit
git push origin branch-name   # Backup to remote
```

### Ending Work
```bash
git status                    # Nothing uncommitted?
git push origin branch-name   # Push your work
gh pr create                  # Open PR
```

### Reviewing PRs
```bash
gh pr checkout 123           # Get the PR
npm test                     # Test it
gh pr review 123 --approve   # Approve
```

### Keeping Updated
```bash
git fetch origin
git merge origin/main  # Or: git rebase origin/main
```

---

## 🎓 Workflows Summary

| Scenario | Key Commands |
|----------|-------------|
| **New feature** | `git checkout -b feature/name` |
| **Bug fix** | `git checkout -b fix/issue` |
| **Update branch** | `git merge origin/main` or `git rebase origin/main` |
| **Merge conflict** | `git status` → resolve → `git add` → `git commit` |
| **Undo commit** | `git reset --soft HEAD~1` |
| **Undo changes** | `git checkout -- file` |
| **Review PR** | `gh pr checkout 123` |
| **Fork contribution** | Fork → clone → `git remote add upstream` |

---

## 💡 Remember

1. **Commit often** - Small commits are easier to review and revert
2. **Pull before push** - Stay in sync with your team
3. **Use branches** - Never commit directly to main
4. **Write good messages** - Your future self will thank you
5. **Test before pushing** - Broken code helps nobody
6. **Communicate** - Tell your team what you're working on

---

**You're now equipped to handle real-world GitHub workflows! Practice these scenarios, and they'll become second nature.**

**Remember: Every expert was once a beginner who refused to give up when things got confusing. Keep practicing!**
