# DevCollab Inc — Git Workflow Guide

> **Workflow:** Git Flow  
> **Version:** 1.0.0  
> **Last Updated:** April 2026

---

## Table of Contents

1. [Branching Strategy](#branching-strategy)
2. [Branch Naming Conventions](#branch-naming-conventions)
3. [Commit Message Conventions](#commit-message-conventions)
4. [Pull Request Process](#pull-request-process)
5. [Release Procedure](#release-procedure)
6. [Team Onboarding](#team-onboarding)

---

## 1. Branching Strategy

We use **Git Flow** — a structured branching model suited for a SaaS team managing scheduled releases.

### Core Branches

| Branch | Purpose | Direct Push Allowed? |
|--------|---------|----------------------|
| `main` | Production-ready code only | ❌ No — PR required |
| `develop` | Integration branch for features | ❌ No — PR required |

### Supporting Branches

| Branch | Branches From | Merges Into | Lifespan |
|--------|--------------|-------------|----------|
| `feature/*` | `develop` | `develop` | Short-lived |
| `release/*` | `develop` | `main` + `develop` | Until release |
| `hotfix/*` | `main` | `main` + `develop` | Short-lived |
| `bugfix/*` | `develop` | `develop` | Short-lived |

### Flow Diagram

```
main       ──────────────────────────────────────► (production)
              ↑ merge PR             ↑ hotfix
develop    ──────────────────────────────────────► (integration)
              ↑ feature merged   ↑ feature merged
feature/x  ──────────►
feature/y        ──────────────►
```

---

## 2. Branch Naming Conventions

All branch names must follow this format:

```
<type>/<short-description>
```

Use lowercase letters and hyphens only. No spaces or underscores.

### Examples

```
feature/user-authentication
feature/dashboard-ui
feature/api-rate-limiting
bugfix/login-redirect-error
hotfix/payment-null-pointer
release/v1.1.0
```

### Rules

- Maximum 50 characters after the prefix
- Must start with one of: `feature/`, `bugfix/`, `hotfix/`, `release/`, `chore/`
- Reference a ticket number if applicable: `feature/PROJ-42-user-auth`

---

## 3. Commit Message Conventions

We follow the **Conventional Commits** specification.

### Format

```
<type>(<scope>): <short description>

[optional body]

[optional footer]
```

### Types

| Type | When to Use |
|------|------------|
| `feat` | A new feature |
| `fix` | A bug fix |
| `docs` | Documentation changes only |
| `style` | Formatting, missing semicolons, etc. |
| `refactor` | Code restructuring (no feature or fix) |
| `test` | Adding or updating tests |
| `chore` | Build process, dependency updates |
| `ci` | CI/CD configuration changes |

### Examples

```
feat(auth): add OAuth2 login with GitHub provider

fix(dashboard): resolve null reference on empty data set

docs(readme): update local setup instructions

chore(deps): upgrade eslint to v8.0.0
```

### Rules

- Subject line: max 72 characters
- Use imperative mood: "add feature" not "added feature"
- No period at the end of the subject line
- Breaking changes: add `BREAKING CHANGE:` in the footer

---

## 4. Pull Request Process

### Before Opening a PR

- [ ] Branch is up to date with `develop` (rebase if needed)
- [ ] All CI checks pass locally
- [ ] Pre-commit hooks pass
- [ ] Self-reviewed the diff

### Opening a PR

1. Push your feature branch to GitHub
2. Open a PR from `feature/your-branch` → `develop`
3. Use the PR template (auto-loaded from `.github/pull_request_template.md`)
4. Assign at least **1 reviewer**
5. Link any related issues

### PR Title Format

Follow the same Conventional Commits format:

```
feat(auth): add GitHub OAuth login
```

### Review Requirements

- Minimum **1 approval** required before merge
- All CI checks must pass (green) before merge
- At least **3 inline review comments** expected for substantive PRs
- Reviewer must not be the PR author

### Merging

- Use **Squash and Merge** for feature branches (keeps `develop` history clean)
- Use **Merge Commit** for `release/*` and `hotfix/*` branches

---

## 5. Release Procedure

### Step 1 — Create a Release Branch

```bash
git checkout develop
git pull origin develop
git checkout -b release/v1.1.0
```

### Step 2 — Finalise and Test

- Update version number in `package.json` (or equivalent)
- Update `CHANGELOG.md`
- Fix any last-minute bugs on the release branch only

### Step 3 — Merge to Main

```bash
# Open PR: release/v1.1.0 → main
# After approval and green CI — merge
git checkout main
git pull origin main
```

### Step 4 — Tag the Release

```bash
git tag -a v1.1.0 -m "Release v1.1.0 — dashboard and auth features"
git push origin v1.1.0
```

### Step 5 — Merge Back to Develop

```bash
git checkout develop
git merge --no-ff main
git push origin develop
```

### Step 6 — Publish GitHub Release

- Go to GitHub → Releases → Draft a new release
- Select the tag `v1.1.0`
- Paste the relevant section from `CHANGELOG.md`
- Publish

### Semantic Versioning Rules

| Change Type | Version Bump | Example |
|-------------|-------------|---------|
| Breaking change | Major | `1.0.0 → 2.0.0` |
| New feature (backward compatible) | Minor | `1.0.0 → 1.1.0` |
| Bug fix | Patch | `1.0.0 → 1.0.1` |

---

## 6. Team Onboarding

### Prerequisites

- Git 2.30+
- Node.js 18+ (for hooks and linting)
- A GitHub account with 2FA enabled
- GPG key configured (see below)

### First-Time Setup

```bash
# 1. Clone the repo
git clone https://github.com/your-org/devcollab-workflow.git
cd devcollab-workflow

# 2. Install dependencies
npm install

# 3. Install Git hooks
cp .githooks/pre-commit .git/hooks/pre-commit
cp .githooks/commit-msg .git/hooks/commit-msg
chmod +x .git/hooks/pre-commit .git/hooks/commit-msg

# 4. Set up GPG signing
gpg --gen-key
gpg --list-secret-keys --keyid-format LONG
# Copy the key ID (long format)
git config --global user.signingkey YOUR_KEY_ID
git config --global commit.gpgsign true
# Add GPG public key to GitHub: Settings → SSH and GPG Keys

# 5. Configure your identity
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

### Starting a New Feature

```bash
git checkout develop
git pull origin develop
git checkout -b feature/your-feature-name
# ... make changes ...
git add .
git commit -m "feat(scope): describe what you did"
git push -u origin feature/your-feature-name
# Open PR on GitHub
```

### Getting Help

- Ask in the `#git-help` Slack channel
- Refer to this document first
- Contact the tech lead for branch protection or release queries


