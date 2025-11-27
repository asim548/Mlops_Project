# Phase III: Continuous Integration & Deployment (CI/CD)

## Overview

Phase III focuses on implementing a **professional Git workflow** with automated CI/CD pipelines using **GitHub Actions** and **CML (Continuous Machine Learning)**.

## Step 5.1: Strict Branching Model

### 🌳 **Branch Strategy**

```
master (production)
  ↑
  merge with PR approval
  ↑
test (staging)
  ↑
  merge with PR approval
  ↑
dev (development)
  ↑
  merge from feature branches
  ↑
feature/* (individual features)
```

### **Branch Purposes:**

1. **`master`** (or `main`) - Production-ready code
   - Only tested, approved code
   - Protected branch
   - Requires PR approval
   - Triggers production deployment

2. **`test`** - Staging/Testing environment
   - Pre-production testing
   - Model validation
   - Performance testing
   - Requires PR approval from dev

3. **`dev`** - Development integration
   - Integration of features
   - Continuous testing
   - Merges from feature branches

4. **`feature/*`** - Individual features
   - New features
   - Bug fixes
   - Experiments
   - Merges into dev

---

## 🚀 **Implementation Steps**

### **Step 1: Create Branch Structure**

Currently, you only have `main` branch. Let's create the full structure:

```powershell
cd "C:\Users\Aaim Shehzad\OneDrive\Desktop\New folder\lahore_rps_pipeline"

# Create dev branch from main
git checkout -b dev
git push origin dev

# Create test branch from main
git checkout main
git checkout -b test
git push origin test

# Go back to dev for development
git checkout dev
```

---

### **Step 2: Set Up Branch Protection Rules**

Go to GitHub repository settings:

**URL:** https://github.com/asim548/Mlops_Project/settings/branches

#### **For `main` branch:**

1. Click "Add rule"
2. Branch name pattern: `main`
3. Enable:
   - ✅ Require a pull request before merging
   - ✅ Require approvals: 1
   - ✅ Dismiss stale pull request approvals when new commits are pushed
   - ✅ Require status checks to pass before merging
   - ✅ Require branches to be up to date before merging
   - ✅ Include administrators (optional)
4. Click "Create"

#### **For `test` branch:**

1. Click "Add rule"
2. Branch name pattern: `test`
3. Enable:
   - ✅ Require a pull request before merging
   - ✅ Require approvals: 1
   - ✅ Require status checks to pass before merging
4. Click "Create"

---

### **Step 3: Create Example Feature Branch**

```powershell
# Make sure you're on dev
git checkout dev

# Create a feature branch
git checkout -b feature/improve-model-training

# Make some changes (example)
# ... edit files ...

# Commit changes
git add .
git commit -m "Improve model training hyperparameters"

# Push feature branch
git push origin feature/improve-model-training
```

---

### **Step 4: Create Pull Request Workflow**

#### **Feature → Dev:**

1. Push feature branch to GitHub
2. Go to GitHub repository
3. Click "Pull requests" → "New pull request"
4. Base: `dev`, Compare: `feature/improve-model-training`
5. Create PR with description
6. Wait for CI checks (we'll set up next)
7. Merge when checks pass

#### **Dev → Test:**

1. Create PR from `dev` to `test`
2. Require 1 approval
3. CI runs model retraining test
4. CML posts metric comparison
5. Merge if metrics improve

#### **Test → Main:**

1. Create PR from `test` to `main`
2. Require 1 approval
3. CI runs full deployment pipeline
4. Merge to deploy to production

---

## 📋 **Git Workflow Rules**

### **Rule 1: Never Commit Directly to Main/Test**

❌ **Wrong:**
```powershell
git checkout main
git commit -m "Quick fix"
git push origin main
```

✅ **Right:**
```powershell
git checkout dev
git checkout -b feature/quick-fix
# make changes
git commit -m "Fix: issue description"
git push origin feature/quick-fix
# Create PR to dev
```

---

### **Rule 2: Always Use Descriptive Branch Names**

✅ **Good:**
- `feature/add-gradient-boosting-model`
- `feature/improve-feature-engineering`
- `bugfix/fix-data-quality-check`
- `hotfix/fix-critical-airflow-error`

❌ **Bad:**
- `test`
- `my-branch`
- `fix`
- `update`

---

### **Rule 3: Write Clear Commit Messages**

✅ **Good:**
```
feat: Add Gradient Boosting model option

- Implement GradientBoostingRegressor
- Add hyperparameter tuning
- Log experiments to MLflow
- Update documentation

Closes #123
```

❌ **Bad:**
```
update
```

---

### **Rule 4: Keep PRs Small and Focused**

✅ **Good PR:**
- Single feature or bug fix
- < 500 lines changed
- Clear purpose
- Easy to review

❌ **Bad PR:**
- Multiple unrelated changes
- 2000+ lines changed
- Unclear purpose
- Hard to review

---

## 🔄 **Complete Workflow Example**

### **Scenario: Add New Model Type**

#### **Step 1: Create Feature Branch**

```powershell
git checkout dev
git pull origin dev
git checkout -b feature/add-xgboost-model
```

#### **Step 2: Make Changes**

Edit `scripts/train.py`:
```python
# Add XGBoost support
elif model_type == 'xgboost':
    from xgboost import XGBRegressor
    model = XGBRegressor(
        n_estimators=hyperparams.get('n_estimators', 100),
        learning_rate=hyperparams.get('learning_rate', 0.1),
        max_depth=hyperparams.get('max_depth', 5),
    )
```

#### **Step 3: Test Locally**

```powershell
python scripts/test_training_standalone.py
```

#### **Step 4: Commit and Push**

```powershell
git add scripts/train.py
git commit -m "feat: Add XGBoost model support

- Implement XGBRegressor option
- Add hyperparameters
- Test with sample data
- Update documentation"

git push origin feature/add-xgboost-model
```

#### **Step 5: Create PR to Dev**

1. Go to GitHub
2. Create PR: `feature/add-xgboost-model` → `dev`
3. Add description
4. Wait for CI checks
5. Request review (if team)
6. Merge

#### **Step 6: Test in Dev**

```powershell
git checkout dev
git pull origin dev
# Run tests
python scripts/test_training_standalone.py
```

#### **Step 7: Promote to Test**

1. Create PR: `dev` → `test`
2. CI runs model comparison
3. CML posts metrics
4. Get approval
5. Merge

#### **Step 8: Deploy to Production**

1. Create PR: `test` → `main`
2. Final approval
3. CI builds Docker image
4. Merge to deploy

---

## 📊 **Branch Status Tracking**

### **Current State:**

```
main (production)
  └─ Latest stable release
  
test (staging)
  └─ Features being tested
  
dev (development)
  └─ Active development
  
feature/add-xgboost-model
  └─ New feature in progress
```

---

## 🛡️ **Protection Rules Summary**

| Branch | Direct Push | PR Required | Approvals | CI Checks |
|--------|-------------|-------------|-----------|-----------|
| `main` | ❌ | ✅ | 1+ | ✅ |
| `test` | ❌ | ✅ | 1+ | ✅ |
| `dev` | ✅* | ✅ | 0 | ✅ |
| `feature/*` | ✅ | - | - | - |

*Can push directly but PR is recommended

---

## 📝 **PR Template**

Create `.github/pull_request_template.md`:

```markdown
## Description
<!-- Describe your changes -->

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tested locally
- [ ] All tests pass
- [ ] No linting errors

## Checklist
- [ ] Code follows style guidelines
- [ ] Self-review completed
- [ ] Documentation updated
- [ ] No breaking changes (or documented)

## Related Issues
Closes #

## Screenshots (if applicable)
```

---

## 🎯 **Success Criteria**

Phase III Step 5.1 is complete when:

- [ ] `dev`, `test`, `main` branches created
- [ ] Branch protection rules configured
- [ ] Cannot push directly to `main` or `test`
- [ ] PR template created
- [ ] Team understands workflow
- [ ] First feature branch merged successfully

---

## 🔧 **Troubleshooting**

### **Issue: Can't push to main**

**Error:** `remote: error: GH006: Protected branch update failed`

**Solution:** This is expected! Create a PR instead.

---

### **Issue: Need to make hotfix to production**

**Solution:**
```powershell
git checkout main
git checkout -b hotfix/critical-bug
# Fix the bug
git commit -m "hotfix: Fix critical bug"
git push origin hotfix/critical-bug
# Create PR to main with "hotfix" label
```

---

### **Issue: Merge conflicts**

**Solution:**
```powershell
git checkout feature/your-branch
git pull origin dev
# Resolve conflicts
git add .
git commit -m "Resolve merge conflicts"
git push origin feature/your-branch
```

---

## 📚 **Best Practices**

### **1. Keep Branches Up to Date**

```powershell
# Update dev regularly
git checkout dev
git pull origin dev

# Update feature branch from dev
git checkout feature/your-branch
git merge dev
```

### **2. Delete Merged Branches**

```powershell
# After PR is merged
git branch -d feature/your-branch
git push origin --delete feature/your-branch
```

### **3. Use Tags for Releases**

```powershell
git checkout main
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

### **4. Write Good PR Descriptions**

Include:
- What changed
- Why it changed
- How to test
- Screenshots (if UI)
- Related issues

---

## 🎓 **Learning Resources**

- **Git Flow:** https://nvie.com/posts/a-successful-git-branching-model/
- **GitHub Flow:** https://guides.github.com/introduction/flow/
- **PR Best Practices:** https://github.com/blog/1943-how-to-write-the-perfect-pull-request

---

## 📅 **Next Steps**

After completing Step 5.1:

1. **Step 5.2:** GitHub Actions CI/CD
2. **Step 5.3:** CML for model comparison
3. **Step 5.4:** Docker containerization
4. **Step 5.5:** Automated deployment

---

**Let's implement this now!** 🚀

