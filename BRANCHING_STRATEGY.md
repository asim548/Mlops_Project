# Git Branching Strategy

## 🌳 Branch Structure

```
main (production)
  ↑
  PR with approval
  ↑
test (staging)
  ↑
  PR with approval
  ↑
dev (development)
  ↑
  PR from features
  ↑
feature/* (features)
hotfix/* (urgent fixes)
```

## 📋 Branch Descriptions

### **`main`** - Production Branch
- **Purpose:** Production-ready code only
- **Protection:** ✅ Protected
- **Direct Push:** ❌ Not allowed
- **PR Required:** ✅ Yes (from `test` only)
- **Approvals:** 1+ required
- **CI Checks:** Must pass
- **Deployment:** Automatic to production

**Rules:**
- Only merge from `test` branch
- Must have passing CI/CD checks
- Requires peer review and approval
- Tagged with version numbers (v1.0.0, v1.1.0, etc.)

---

### **`test`** - Staging Branch
- **Purpose:** Pre-production testing and validation
- **Protection:** ✅ Protected
- **Direct Push:** ❌ Not allowed
- **PR Required:** ✅ Yes (from `dev` only)
- **Approvals:** 1+ required
- **CI Checks:** Must pass (including model validation)
- **Deployment:** Automatic to staging environment

**Rules:**
- Only merge from `dev` branch
- Run full test suite
- Model performance validation
- CML reports metrics comparison
- Requires approval before merging to `main`

---

### **`dev`** - Development Branch
- **Purpose:** Integration branch for features
- **Protection:** ⚠️ Partially protected
- **Direct Push:** ✅ Allowed (but not recommended)
- **PR Required:** ✅ Recommended (from feature branches)
- **Approvals:** 0 (but review recommended)
- **CI Checks:** Must pass
- **Deployment:** Automatic to dev environment

**Rules:**
- Merge feature branches here
- Keep up to date with `main`
- Should always be in working state
- Run tests before merging to `test`

---

### **`feature/*`** - Feature Branches
- **Purpose:** Individual feature development
- **Protection:** ❌ Not protected
- **Direct Push:** ✅ Allowed
- **PR Required:** ✅ Yes (to `dev`)
- **Naming:** `feature/description-of-feature`
- **Lifespan:** Short-lived (delete after merge)

**Examples:**
- `feature/add-xgboost-model`
- `feature/improve-feature-engineering`
- `feature/add-data-validation`

**Rules:**
- Branch from `dev`
- Merge back to `dev`
- Delete after successful merge
- Keep focused on single feature

---

### **`hotfix/*`** - Hotfix Branches
- **Purpose:** Urgent production fixes
- **Protection:** ❌ Not protected
- **Direct Push:** ✅ Allowed
- **PR Required:** ✅ Yes (to `main` and `dev`)
- **Naming:** `hotfix/description-of-fix`
- **Lifespan:** Very short-lived

**Examples:**
- `hotfix/fix-critical-data-bug`
- `hotfix/fix-airflow-crash`

**Rules:**
- Branch from `main`
- Merge to both `main` AND `dev`
- Requires immediate review
- Tag with patch version (v1.0.1)

---

## 🔄 Workflow Examples

### **Example 1: Adding New Feature**

```powershell
# 1. Start from dev
git checkout dev
git pull origin dev

# 2. Create feature branch
git checkout -b feature/add-gradient-boosting

# 3. Make changes
# ... edit files ...

# 4. Commit changes
git add .
git commit -m "feat: Add Gradient Boosting model

- Implement GradientBoostingRegressor
- Add hyperparameter tuning
- Update documentation"

# 5. Push feature branch
git push origin feature/add-gradient-boosting

# 6. Create PR on GitHub: feature/add-gradient-boosting → dev
# 7. Wait for CI checks
# 8. Get review (if team)
# 9. Merge PR
# 10. Delete feature branch
git branch -d feature/add-gradient-boosting
git push origin --delete feature/add-gradient-boosting
```

---

### **Example 2: Promoting to Test**

```powershell
# 1. Ensure dev is ready
git checkout dev
git pull origin dev

# Run tests locally
python scripts/test_training_standalone.py

# 2. Create PR on GitHub: dev → test
# 3. CI runs model validation
# 4. CML posts metrics comparison
# 5. Review metrics
# 6. Get approval
# 7. Merge PR
```

---

### **Example 3: Deploying to Production**

```powershell
# 1. Ensure test is validated
git checkout test
git pull origin test

# 2. Create PR on GitHub: test → main
# 3. Final review and approval
# 4. CI builds Docker image
# 5. Merge PR
# 6. Automatic deployment to production

# 7. Tag release
git checkout main
git pull origin main
git tag -a v1.0.0 -m "Release v1.0.0: Initial production release"
git push origin v1.0.0
```

---

### **Example 4: Hotfix for Production**

```powershell
# 1. Branch from main
git checkout main
git pull origin main
git checkout -b hotfix/fix-critical-bug

# 2. Fix the bug
# ... edit files ...

# 3. Commit fix
git commit -m "hotfix: Fix critical data validation bug"

# 4. Push hotfix branch
git push origin hotfix/fix-critical-bug

# 5. Create PR to main (urgent review)
# 6. Merge to main
# 7. Tag with patch version
git tag -a v1.0.1 -m "Hotfix v1.0.1"
git push origin v1.0.1

# 8. Merge hotfix to dev as well
git checkout dev
git merge hotfix/fix-critical-bug
git push origin dev

# 9. Delete hotfix branch
git branch -d hotfix/fix-critical-bug
git push origin --delete hotfix/fix-critical-bug
```

---

## 📊 Branch Protection Rules

### **GitHub Settings**

Go to: `https://github.com/asim548/Mlops_Project/settings/branches`

#### **For `main` branch:**

```yaml
Branch name pattern: main

Settings:
  ✅ Require a pull request before merging
    ✅ Require approvals: 1
    ✅ Dismiss stale pull request approvals when new commits are pushed
    ✅ Require review from Code Owners
  
  ✅ Require status checks to pass before merging
    ✅ Require branches to be up to date before merging
    Required checks:
      - CI/CD Pipeline
      - Linting
      - Tests
  
  ✅ Require conversation resolution before merging
  ✅ Require signed commits (optional)
  ✅ Include administrators (optional)
  ✅ Restrict who can push to matching branches
```

#### **For `test` branch:**

```yaml
Branch name pattern: test

Settings:
  ✅ Require a pull request before merging
    ✅ Require approvals: 1
  
  ✅ Require status checks to pass before merging
    Required checks:
      - Model Validation
      - Integration Tests
```

---

## 🎯 Commit Message Convention

Use **Conventional Commits** format:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### **Types:**

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Maintenance tasks
- `perf`: Performance improvements
- `ci`: CI/CD changes

### **Examples:**

```
feat(model): Add XGBoost model support

- Implement XGBRegressor option
- Add hyperparameter configuration
- Update training script
- Add tests for XGBoost

Closes #123
```

```
fix(data): Fix null value handling in quality check

The data quality check was incorrectly counting null values.
This fix ensures proper validation.

Fixes #456
```

```
docs(readme): Update installation instructions

- Add Python 3.12 requirement
- Update dependency versions
- Add troubleshooting section
```

---

## 📝 PR Guidelines

### **Creating a PR:**

1. **Use the PR template** (automatically loaded)
2. **Write clear title** following commit convention
3. **Fill out all sections** of the template
4. **Link related issues** using keywords (Closes #, Fixes #)
5. **Add screenshots** if UI changes
6. **Request reviewers** (automatic via CODEOWNERS)
7. **Add labels** (feature, bugfix, documentation, etc.)

### **Reviewing a PR:**

1. **Check code quality** - Style, readability, best practices
2. **Test locally** - Pull branch and test
3. **Review tests** - Adequate coverage?
4. **Check documentation** - Updated?
5. **Security review** - Any vulnerabilities?
6. **Performance impact** - Any concerns?
7. **Leave constructive feedback**
8. **Approve or request changes**

---

## 🚫 What NOT to Do

### ❌ **Don't:**

1. Push directly to `main` or `test`
2. Merge without PR approval
3. Skip CI checks
4. Create long-lived feature branches
5. Commit secrets or credentials
6. Force push to shared branches
7. Merge broken code
8. Ignore code review feedback

### ✅ **Do:**

1. Always create PRs for changes
2. Keep branches up to date
3. Write descriptive commit messages
4. Test before pushing
5. Review others' code
6. Delete merged branches
7. Use meaningful branch names
8. Follow the workflow

---

## 🔧 Useful Git Commands

### **Branch Management:**

```powershell
# List all branches
git branch -a

# Create and switch to new branch
git checkout -b feature/new-feature

# Switch branches
git checkout dev

# Delete local branch
git branch -d feature/old-feature

# Delete remote branch
git push origin --delete feature/old-feature

# Rename branch
git branch -m old-name new-name
```

### **Keeping Up to Date:**

```powershell
# Update dev from main
git checkout dev
git merge main
git push origin dev

# Update feature branch from dev
git checkout feature/my-feature
git merge dev
# Or use rebase for cleaner history
git rebase dev
```

### **Viewing History:**

```powershell
# View commit history
git log --oneline --graph --all

# View changes
git diff dev..main

# View file history
git log --follow -- path/to/file
```

---

## 📅 Release Process

### **Version Numbering:**

Use **Semantic Versioning** (SemVer): `MAJOR.MINOR.PATCH`

- **MAJOR:** Breaking changes (v2.0.0)
- **MINOR:** New features, backwards compatible (v1.1.0)
- **PATCH:** Bug fixes (v1.0.1)

### **Creating a Release:**

```powershell
# 1. Merge test to main
# 2. Tag the release
git checkout main
git pull origin main
git tag -a v1.0.0 -m "Release v1.0.0: Initial production release

Features:
- Real-time weather prediction
- MLflow tracking
- Dagshub integration
- Automated ETL pipeline"

git push origin v1.0.0

# 3. Create GitHub Release
# Go to: https://github.com/asim548/Mlops_Project/releases/new
# - Select tag: v1.0.0
# - Write release notes
# - Attach artifacts (if any)
# - Publish release
```

---

## 🎓 Best Practices

1. **Commit Often:** Small, focused commits
2. **Pull Frequently:** Stay up to date
3. **Test Locally:** Before pushing
4. **Write Good Messages:** Clear and descriptive
5. **Review Thoroughly:** Help your team
6. **Keep Branches Short-Lived:** Merge quickly
7. **Delete Merged Branches:** Keep repo clean
8. **Use Tags:** Mark releases
9. **Follow Conventions:** Consistency matters
10. **Communicate:** Use PR descriptions and comments

---

## 📊 Current Branch Status

```
✅ main   - Production (protected)
✅ test   - Staging (protected)
✅ dev    - Development (active)
```

---

## 🆘 Need Help?

- **Git Documentation:** https://git-scm.com/doc
- **GitHub Flow:** https://guides.github.com/introduction/flow/
- **Conventional Commits:** https://www.conventionalcommits.org/
- **Semantic Versioning:** https://semver.org/

---

**Last Updated:** November 27, 2025
**Status:** ✅ Branching Strategy Implemented

