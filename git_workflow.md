---
dewey: 000.4
id: git-workflow-standards
title: Git Workflow Standards
project: global
description: Version: 1.2.0 Last Updated: 2025-09-13 Owner: DevOps Team Review Date: 2025-10-13
status: active
tags: [git, workflow, standards]
category: 800 — Global Standards
created: 2025-09-13
updated: 2026-04-27
version: 1.0.0
author: CieloVista Software
relativepath: git_workflow.md
---
# Git Workflow Standards

**Version**: 1.2.0  
**Last Updated**: 2025-09-13  
**Owner**: DevOps Team  
**Review Date**: 2025-10-13

## Purpose

This document defines the Git workflow, branching strategy, commit standards, and pull request process for all Cielo Vista Software repositories.

## Quick Reference

### Branch Naming
```bash
feature/TICKET-123-add-trace-search
bugfix/TICKET-456-fix-memory-leak
hotfix/TICKET-789-security-patch
```

### Commit Format
```bash
feat(trace): add search functionality to trace element

- Implement real-time search with debouncing
- Add keyboard shortcuts for search
- Include search in export functionality

Fixes: TICKET-123
```

### Common Commands
```bash
# Start new feature
git checkout -b feature/TICKET-123-description

# Keep branch updated
git fetch origin
git rebase origin/main

# Create PR
git push origin feature/TICKET-123-description
```

## Branching Strategy

### Branch Types

#### Main Branches
- **`main`** - Production-ready code, always deployable
- **`develop`** - Integration branch for features

#### Supporting Branches
- **`feature/*`** - New features and enhancements
- **`bugfix/*`** - Bug fixes for current release
- **`hotfix/*`** - Critical fixes for production
- **`release/*`** - Release preparation
- **`docs/*`** - Documentation updates

### Branch Naming Convention

```bash
# Feature branches
feature/TICKET-123-add-user-authentication
feature/TICKET-456-implement-dark-mode
feature/no-ticket-update-dependencies

# Bug fix branches  
bugfix/TICKET-789-fix-login-redirect
bugfix/TICKET-101-resolve-memory-leak
bugfix/critical-fix-trace-overflow

# Hotfix branches
hotfix/TICKET-999-security-vulnerability
hotfix/TICKET-888-production-crash

# Release branches
release/v2.1.0
release/v2.1.0-rc.1

# Documentation branches
docs/update-api-documentation
docs/add-deployment-guide
```

### Branch Lifecycle

#### Feature Branch Workflow
```bash
# 1. Create feature branch from main
git checkout main
git pull origin main
git checkout -b feature/TICKET-123-add-search

# 2. Work on feature with regular commits
git add .
git commit -m "feat(search): implement basic search functionality"

# 3. Keep branch updated with main
git fetch origin
git rebase origin/main

# 4. Push and create PR
git push origin feature/TICKET-123-add-search
# Create PR via GitHub/GitLab interface

# 5. After PR approval and merge, cleanup
git checkout main
git pull origin main
git branch -d feature/TICKET-123-add-search
```

#### Hotfix Workflow
```bash
# 1. Create hotfix from main
git checkout main
git pull origin main
git checkout -b hotfix/TICKET-999-security-fix

# 2. Make fix and commit
git commit -m "fix(security): patch XSS vulnerability in trace display"

# 3. Create PR for immediate merge
git push origin hotfix/TICKET-999-security-fix

# 4. After merge, tag release if needed
git checkout main
git pull origin main
git tag -a v2.0.1 -m "Security hotfix v2.0.1"
git push origin v2.0.1
```

## Commit Standards

### Commit Message Format

```xml
type(scope): description

[optional body]

[optional footer]
```

#### Types
- **feat**: New feature
- **fix**: Bug fix  
- **docs**: Documentation changes
- **style**: Code style changes (formatting, semicolons, etc.)
- **refactor**: Code refactoring without functionality changes
- **perf**: Performance improvements
- **test**: Adding or updating tests
- **chore**: Maintenance tasks, dependency updates
- **ci**: CI/CD configuration changes
- **build**: Build system changes

#### Scopes
- **trace**: Trace element component
- **auth**: Authentication system
- **api**: API related changes
- **ui**: User interface components
- **config**: Configuration changes
- **deps**: Dependency updates

### Commit Message Examples

```bash
# Good examples
feat(trace): add real-time search functionality
fix(auth): resolve token refresh infinite loop  
docs(api): update trace element documentation
perf(trace): optimize virtual scrolling performance
test(trace): add integration tests for search feature
chore(deps): update development dependencies

# Examples with body and footer
feat(trace): implement export functionality

Add JSON, CSV, and text export options for trace data.
Include filtering options in export dialog.
Add keyboard shortcut Ctrl+E for quick export.

Fixes: TICKET-456
Co-authored-by: Jane Developer <jane@example.com>

# Breaking change
feat(api)!: redesign trace entry data structure

BREAKING CHANGE: TraceEntry interface has changed.
The 'type' field has been renamed to 'level' and 
'direction' is now required.

Migration guide available in docs/migration/v2.md
```

### Commit Best Practices

#### Make Atomic Commits
```bash
# ✅ Good - Single logical change
git commit -m "feat(trace): add search input field"
git commit -m "feat(trace): implement search logic"  
git commit -m "feat(trace): add search keyboard shortcuts"

# ❌ Bad - Multiple unrelated changes
git commit -m "feat(trace): add search, fix bug, update docs"
```

#### Write Descriptive Messages
```bash
# ✅ Good - Clear and specific
git commit -m "fix(trace): prevent memory leak in virtual scrolling"
git commit -m "feat(search): add debounced search with 300ms delay"

# ❌ Bad - Vague or unclear
git commit -m "fix bug"
git commit -m "update stuff"
git commit -m "wip"
```

## Pull Request Process

### PR Requirements

#### Before Creating PR
- [ ] Branch is up-to-date with target branch
- [ ] All tests pass locally
- [ ] Code follows style guidelines
- [ ] Documentation updated if needed
- [ ] Self-review completed

#### PR Template
```markdown
## Summary
Brief description of changes and why they're needed.

## Type of Change
- [ ] Bug fix (non-breaking change that fixes an issue)
- [ ] New feature (non-breaking change that adds functionality)  
- [ ] Breaking change (fix or feature that causes existing functionality to not work as expected)
- [ ] Documentation update
- [ ] Performance improvement
- [ ] Refactoring (no functional changes)

## Testing
- [ ] Unit tests pass
- [ ] Integration tests pass
- [ ] E2E tests pass (if applicable)
- [ ] Manual testing completed
- [ ] Performance impact assessed

## Screenshots/Demo
(If applicable, add screenshots or demo links)

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No console errors or warnings
- [ ] Accessibility considerations addressed
- [ ] Security implications considered

## Related Issues
Fixes: #123
Related: #456, #789
```

### Code Review Process

#### Review Criteria
1. **Functionality**: Does the code work as intended?
2. **Code Quality**: Is it readable, maintainable, and follows standards?
3. **Performance**: Are there any performance implications?
4. **Security**: Are there security vulnerabilities?
5. **Testing**: Are there adequate tests?
6. **Documentation**: Is documentation updated?

#### Review Guidelines

##### For Reviewers
```markdown
# Good Review Comments

## Constructive feedback
"Consider using const instead of let here since the value isn't reassigned"

## Suggest alternatives  
"This works, but we could simplify it using destructuring: 
const { level, message } = entry;"

## Ask questions for clarity
"Could you explain the reasoning behind this approach? 
I'm wondering if there's a simpler way."

## Acknowledge good practices
"Great error handling here! This will make debugging much easier."

# Avoid
❌ "This is wrong" (not constructive)
❌ "I don't like this" (not specific)  
❌ "Why did you do this?" (sounds accusatory)
```

##### For Authors
- Respond to all comments
- Ask for clarification if needed
- Thank reviewers for their time
- Address feedback promptly
- Update tests and docs as needed

### PR Merge Strategy

#### Merge Types
- **Squash and Merge**: Default for feature branches
  - Creates clean commit history
  - Use for multiple small commits
  
- **Merge Commit**: For release branches
  - Preserves branch history
  - Use for significant features
  
- **Rebase and Merge**: For small, clean PRs
  - Linear history
  - Use when branch has few, well-crafted commits

#### Protection Rules
```yaml
# main branch protection
required_status_checks:
  - tests-unit
  - tests-integration  
  - lint-javascript
  - lint-css
  - security-scan

required_reviews: 2
dismiss_stale_reviews: true
require_code_owner_reviews: true
restrict_pushes: true
```

## Git Configuration

### Global Configuration
```bash
# User information
git config --global user.name "Your Name"
git config --global user.email "your.email@cielovista.software"

# Editor and diff tool
git config --global core.editor "code --wait"
git config --global merge.tool "vscode"

# Line ending handling
git config --global core.autocrlf input  # Mac/Linux
git config --global core.autocrlf true   # Windows

# Default branch
git config --global init.defaultBranch main

# Push behavior
git config --global push.default simple

# Rebase by default for pulls
git config --global pull.rebase true
```

### Repository Configuration
```bash
# .gitconfig in repository root
[core]
    ignorecase = false
    filemode = true

[branch "main"]
    remote = origin
    merge = refs/heads/main

[branch "develop"]
    remote = origin  
    merge = refs/heads/develop
```

### Git Hooks Setup

#### Pre-commit Hook
```bash
#!/bin/sh
# .git/hooks/pre-commit

# Run linting
npm run lint
if [ $? -ne 0 ]; then
    echo "❌ Linting failed. Please fix errors before committing."
    exit 1
fi

# Run unit tests
npm run test:unit
if [ $? -ne 0 ]; then
    echo "❌ Unit tests failed. Please fix tests before committing."
    exit 1
fi

echo "✅ Pre-commit checks passed"
```

#### Commit Message Hook
```bash
#!/bin/sh
# .git/hooks/commit-msg

# Check commit message format
commit_regex='^(feat|fix|docs|style|refactor|perf|test|chore|ci|build)(\(.+\))?: .{1,50}'

if ! grep -qE "$commit_regex" "$1"; then
    echo "❌ Invalid commit message format"
    echo "Format: type(scope): description"
    echo "Example: feat(trace): add search functionality"
    exit 1
fi

echo "✅ Commit message format valid"
```

## Troubleshooting

### Common Issues

#### Merge Conflicts
```bash
# When rebasing encounters conflicts
git status  # See conflicted files
# Edit files to resolve conflicts
git add <resolved-files>
git rebase --continue

# If too complex, abort and merge instead
git rebase --abort
git merge origin/main
```

#### Accidentally Committed to Wrong Branch
```bash
# Move commit to correct branch
git log --oneline -5  # Find commit hash
git checkout correct-branch
git cherry-pick <commit-hash>
git checkout wrong-branch
git reset --hard HEAD~1  # Remove commit from wrong branch
```

#### Reset Local Branch to Match Remote
```bash
git fetch origin
git reset --hard origin/main
```

#### Undo Last Commit (not pushed)
```bash
# Keep changes in working directory
git reset --soft HEAD~1

# Discard changes completely  
git reset --hard HEAD~1
```

## Tools and Integrations

### Recommended Tools
- **GitHub CLI**: `gh pr create`, `gh pr review`
- **Git GUI**: GitKraken, SourceTree, or VS Code Git extension
- **Commit Message Helper**: Commitizen for standardized commits

### IDE Integration
```json
// VS Code settings.json
{
    "git.autofetch": true,
    "git.confirmSync": false,
    "git.enableSmartCommit": true,
    "git.postCommitCommand": "sync",
    "gitlens.hovers.currentLine.over": "line",
    "gitlens.currentLine.enabled": false
}
```

## Related Documents

- [Code Review Process](../project/code-review-process.md)
- [Build & Deploy](./build-deploy.md)
- [Release Process](../project/release-process.md)

## Changelog

- **v1.2.0** (2025-09-13): Added hotfix workflow, updated commit message standards
- **v1.1.0** (2025-08-15): Enhanced PR template and review guidelines  
- **v1.0.0** (2025-07-01): Initial Git workflow standards