# Contributing to CBNU Chatbot

Thank you for contributing to the CBNU Foreign Students Chatbot! This guide will help you understand our development workflow.

## Table of Contents
- [Branching Strategy](#branching-strategy)
- [Getting Started](#getting-started)
- [Development Workflow](#development-workflow)
- [Commit Messages](#commit-messages)
- [Pull Request Process](#pull-request-process)
- [Team Structure](#team-structure)

## Branching Strategy

We follow **Git Flow** branching model for organized multi-team development.

### Branch Hierarchy

```
main/                          # Production-ready code (tagged releases)
├── release/v1.0.0            # Release preparation
└── develop/                   # Integration branch (base for all features)
    ├── feature/frontend-*    # Frontend features
    ├── feature/backend-*     # Backend/API features
    ├── feature/rag-*         # RAG pipeline features
    ├── feature/db-*          # Database features
    ├── bugfix/*              # Bug fixes
    └── hotfix/*              # Critical production fixes
```

### Branch Types

| Branch | Purpose | Created From | Merge Back To |
|--------|---------|--------------|---------------|
| `main` | Production releases | N/A | N/A |
| `develop` | Integration point | `main` | N/A |
| `feature/*` | New features | `develop` | `develop` → PR → review |
| `bugfix/*` | Bug fixes | `develop` | `develop` → PR → review |
| `release/*` | Release prep | `develop` | `main` + `develop` |
| `hotfix/*` | Urgent fixes | `main` | `main` + `develop` |

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/your-org/cbnu_chatbot.git
cd cbnu_chatbot
```

### 2. Set Up Local Environment

```bash
# Install Python 3.10+
python --version

# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install dependencies
cd backend
pip install -r requirements.txt
cd ..
```

### 3. Configure Git

```bash
git config user.name "Your Name"
git config user.email "your.email@example.com"

# Set default branch
git config --global init.defaultBranch main
```

### 4. Add Upstream Remote

```bash
git remote add upstream https://github.com/your-org/cbnu_chatbot.git
git fetch upstream
```

## Development Workflow

### Starting a New Feature

```bash
# Step 1: Sync with latest develop
git checkout develop
git pull origin develop

# Step 2: Create feature branch
# Naming: feature/[team-area]-[description]
# Examples:
# - feature/frontend-chat-interface
# - feature/backend-user-auth
# - feature/rag-vector-search
# - feature/db-schema-migration

git checkout -b feature/your-team-feature-description

# Step 3: Make your changes
# ... edit files ...

# Step 4: Commit changes (see Commit Messages section)
git add .
git commit -m "feat: your feature description"

# Step 5: Push to remote
git push origin feature/your-team-feature-description
```

### Keeping Your Branch Updated

```bash
# While working on your feature, keep it synced with develop
git fetch origin
git rebase origin/develop

# Or merge if you prefer (creates merge commit)
git merge origin/develop
```

### Handling Conflicts

```bash
# If conflicts occur during rebase
# 1. Resolve conflicts in your editor
# 2. Stage resolved files
git add .

# 3. Continue rebase
git rebase --continue

# Or abort if needed
git rebase --abort
```

## Commit Messages

Follow the **Conventional Commits** format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- **feat**: New feature
- **fix**: Bug fix
- **docs**: Documentation changes
- **style**: Code style (formatting, semicolons, etc.)
- **refactor**: Code refactoring
- **perf**: Performance improvement
- **test**: Adding/updating tests
- **chore**: Build, dependencies, etc.

### Scope (Optional)

- `frontend`, `backend`, `api`, `rag`, `database`, `auth`, etc.

### Examples

```bash
# Good commits
git commit -m "feat(backend): add user authentication endpoint"
git commit -m "fix(rag): resolve embedding model loading issue"
git commit -m "docs(api): update API specification"
git commit -m "refactor(database): improve query performance"
git commit -m "test(backend): add unit tests for auth service"

# Avoid
git commit -m "fixed stuff"
git commit -m "update"
git commit -m "changes"
```

## Pull Request Process

### 1. Create Pull Request

- Go to GitHub → Your branch → "Create Pull Request"
- Use the PR template (auto-filled)
- Link related issues: `Closes #123`

### 2. PR Naming Convention

```
[TYPE]: Brief description

Examples:
[FEATURE] Add embedding optimization
[BUGFIX] Fix API rate limiting
[DOCS] Update deployment guide
```

### 3. Fill PR Template

```markdown
## Description
Brief description of changes

## Related Issues
Closes #123

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Documentation update
- [ ] Refactoring

## Testing Done
- How did you test this?
- Any edge cases covered?

## Screenshots (if applicable)
Add UI/output screenshots

## Checklist
- [ ] Code follows project style
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No breaking changes
```

### 4. Code Review

**Reviewers will check:**
- ✅ Code quality and style
- ✅ Test coverage
- ✅ Documentation
- ✅ No breaking changes
- ✅ Performance implications

**Approval Required:**
- At least 1 approval from core team
- All CI checks pass
- No merge conflicts

### 5. Merge

```bash
# After approval, merge to develop
# Use "Create a merge commit" option
git checkout develop
git pull origin develop
git merge feature/your-branch
git push origin develop

# Delete remote branch
git push origin --delete feature/your-branch

# Delete local branch
git branch -d feature/your-branch
```

## Team Structure

### Frontend Team
- **Lead**: TBD
- **Branch prefix**: `feature/frontend-*`
- **Focus**: React/Vue UI, styling, user experience

### Backend/API Team
- **Lead**: TBD
- **Branch prefix**: `feature/backend-*`
- **Focus**: FastAPI endpoints, authentication, business logic

### RAG/Data Team
- **Lead**: TBD
- **Branch prefix**: `feature/rag-*`
- **Focus**: Embeddings, ChromaDB, document processing, retrieval

### Database Team
- **Lead**: TBD
- **Branch prefix**: `feature/db-*`
- **Focus**: PostgreSQL schema, migrations, optimization

## Important Rules

### ❌ Never Do This

```bash
# Never push directly to main
git push origin feature:main

# Never force push to shared branches
git push origin feature --force

# Never commit sensitive data
# Use .env.example instead of .env
# Use .gitignore for secrets
```

### ✅ Always Do This

```bash
# Always sync before starting
git pull origin develop

# Always create a feature branch
git checkout -b feature/...

# Always use PR for code review
# Never merge your own PR without review

# Always delete merged branches
git push origin --delete feature/...
```

## Testing Requirements

```bash
# Before pushing, run tests locally
cd backend
pytest tests/

# Check code style
flake8 app/
black --check app/

# Check type hints
mypy app/
```

## Documentation

- Update docstrings for new functions
- Update README if adding major features
- Update API spec if changing endpoints
- Add comments for complex logic

## Questions or Issues?

1. Check existing GitHub issues
2. Ask in team Slack/Discord
3. Create a new issue with details
4. Tag relevant team lead

## Additional Resources

- [Git Flow Cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)

---

**Thank you for contributing to CBNU Chatbot!** 🚀
