# GitHub Actions PR Permission Test Guide

## Purpose

This guide helps administrators verify that GitHub Actions has the necessary permissions to create Pull Requests automatically.

## Two Solutions

### ✅ Solution 1: Enable Repository Setting (Recommended)

**Advantages:**
- ✅ Simple and straightforward
- ✅ Uses GitHub's built-in security model
- ✅ PR created by `github-actions[bot]`
- ✅ No token management required
- ✅ No expiration concerns

**How to Enable:**
1. Go to: **Repository Settings** → **Actions** → **General**
2. Scroll to: **Workflow permissions** section
3. Check: ✓ **Allow GitHub Actions to create and approve pull requests**
4. Click: **Save**

**When to Use:**
- You have repository admin access
- You want the simplest solution
- You prefer built-in GitHub features

### ✅ Solution 2: Use Personal Access Token (Alternative)

**Advantages:**
- ✅ Works when repository setting can't be enabled
- ✅ Works across organizational boundaries
- ✅ Can be used with fine-grained permissions

**Disadvantages:**
- ⚠️ Requires token management
- ⚠️ Token can expire (needs renewal)
- ⚠️ PR shows as created by user, not bot

**How to Enable:**
1. Create a Fine-grained Personal Access Token:
   - Visit: **GitHub Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens**
   - Click: **Generate new token**
   - Name: `Auto PR Workflow - Python_Git_exam`
   - Repository access: **Only select repositories** → Choose this repository
   - Permissions:
     - **Contents**: Read and write
     - **Pull requests**: Read and write
   - Generate and copy the token (shown only once!)

2. Add as Repository Secret:
   - Go to: **Repository Settings** → **Secrets and variables** → **Actions**
   - Click: **New repository secret**
   - Name: `PAT_TOKEN`
   - Value: Paste the token
   - Click: **Add secret**

3. **That's it!** The workflow automatically detects and uses `PAT_TOKEN` if available.

**When to Use:**
- Organization doesn't allow the repository setting
- You don't have permission to change repository settings
- You need more control over token permissions

## Testing Your Configuration

After enabling either solution, test with these steps:

### Test Steps

```bash
# 1. Create a test branch
git checkout -b test/permission-check

# 2. Make a small change to trigger the workflow
echo "# Permission test" >> src/bad_style.py

# 3. Commit and push
git add src/bad_style.py
git commit -m "test: verify PR creation permissions"
git push origin test/permission-check
```

### Expected Results

✅ **Success Indicators:**
1. Go to **Actions** tab → See "Auto Create PR" workflow running
2. Workflow status shows ✅ green checkmark (Success)
3. Go to **Pull requests** tab → See automatically created PR
4. PR title: "Fix: PEP 8 compliance for bad_style.py - test/permission-check"
5. "PR Validation" workflow automatically runs on the PR

❌ **Failure Indicators:**
1. Workflow shows ❌ red X (Failed)
2. Error message: "GitHub Actions is not permitted to create or approve pull requests"
3. No PR is created

### Clean Up Test

After successful test:
```bash
# Close and delete the test PR via GitHub UI, or:
gh pr close test/permission-check --delete-branch
```

## Verification Checklist

Use this checklist to confirm your setup:

### For Solution 1 (Repository Setting):
- [ ] Repository Settings → Actions → General is accessible
- [ ] "Workflow permissions" section is visible
- [ ] "Allow GitHub Actions to create and approve pull requests" is checked
- [ ] Settings are saved (click Save button)
- [ ] Test branch push triggers workflow successfully
- [ ] PR is created automatically
- [ ] PR is created by `github-actions[bot]`

### For Solution 2 (PAT Token):
- [ ] Personal Access Token is created with correct permissions
- [ ] Token has "Contents: Write" permission
- [ ] Token has "Pull Requests: Write" permission
- [ ] Token is added as repository secret named `PAT_TOKEN`
- [ ] Secret is accessible in Settings → Secrets and variables → Actions
- [ ] Test branch push triggers workflow successfully
- [ ] PR is created automatically
- [ ] PR is created by your user account

## Troubleshooting

### Problem: "Allow GitHub Actions to create and approve pull requests" is grayed out

**Cause:** Organization-level setting is disabled

**Solution:**
1. Ask organization admin to enable at: `https://github.com/organizations/[ORG]/settings/actions`
2. Enable same setting at organization level
3. Return to repository settings - option should now be available

**Alternative:** Use Solution 2 (PAT Token) instead

### Problem: Workflow still fails after enabling setting

**Checks:**
1. Wait 2-5 minutes for settings to propagate
2. Verify the setting is still enabled (sometimes needs re-saving)
3. Check workflow permissions in the YAML file:
   ```yaml
   permissions:
     contents: write
     pull-requests: write
   ```
4. Try re-running the workflow

### Problem: PAT Token not working

**Checks:**
1. Verify token hasn't expired
2. Verify token has correct permissions (Contents + Pull Requests, both "Write")
3. Verify secret name is exactly `PAT_TOKEN` (case-sensitive)
4. Verify token is for the correct repository
5. Try creating a new token

### Problem: PR created but validation doesn't run

**Cause:** Modified files don't match PR validation trigger

**Check:**
- Ensure you modified files in `src/` or `tests/` directories
- Verify files have `.py` extension
- Check `.github/workflows/pr-validation.yml` for exact trigger conditions

## How the Workflow Decides Which Token to Use

The workflow uses this logic:

```yaml
GH_TOKEN: ${{ secrets.PAT_TOKEN || github.token }}
```

This means:
1. **If `PAT_TOKEN` secret exists** → Use it (Solution 2)
2. **If `PAT_TOKEN` secret doesn't exist** → Use default `github.token` (Solution 1)

You can use **either** solution - the workflow adapts automatically!

## Workflow Behavior by Token Type

| Aspect | Default GITHUB_TOKEN | PAT_TOKEN |
|--------|----------------------|-----------|
| Setup Required | Enable repository setting | Create and add token secret |
| PR Created By | `github-actions[bot]` | Your user account |
| Expiration | Never | Yes (30-90 days typical) |
| Maintenance | None | Renew token periodically |
| Best For | Most users | Special cases / restrictions |

## Getting Help

If you've tried both solutions and still have issues:

1. Check the workflow logs:
   - Go to **Actions** tab
   - Click the failed workflow run
   - Click the "create-pr" job
   - Review the "Create Pull Request" step
   - Look for specific error messages

2. Common error messages and solutions:
   - "GitHub Actions is not permitted" → Enable repository setting or add PAT_TOKEN
   - "Bad credentials" → PAT_TOKEN is invalid or expired
   - "Resource not accessible" → PAT_TOKEN lacks required permissions

3. Review documentation:
   - [QUICK_SETUP.md](QUICK_SETUP.md) - Step-by-step setup
   - [SETUP_GUIDE.md](SETUP_GUIDE.md) - Detailed explanations
   - [WORKFLOW.md](WORKFLOW.md) - Complete workflow documentation

4. Create an issue with:
   - Screenshot of error
   - Which solution you tried
   - Workflow logs
   - Steps you've already taken

## Summary

**Quick Reference:**

- ✅ **Recommended**: Enable "Allow GitHub Actions to create and approve pull requests" in repository settings
- ✅ **Alternative**: Add `PAT_TOKEN` secret with a Personal Access Token
- ✅ **Either works**: Workflow automatically uses whichever is available
- ✅ **Can combine**: If both exist, PAT_TOKEN takes priority

**Time to Setup:**
- Solution 1: ~2 minutes (repository setting)
- Solution 2: ~5 minutes (create token + add secret)

**Choose Solution 1 if:**
- You have admin access to the repository
- You want the simplest solution
- You don't want to manage tokens

**Choose Solution 2 if:**
- You can't enable the repository setting
- Organization policy prevents Solution 1
- You need more fine-grained control

Both solutions are secure and work equally well! ✨
