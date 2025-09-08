# Harness Connector Troubleshooting Guide

## Fixed: GitHub Connector Scope Issue

### Problem
```
Connector not found for identifier : [Github] with scope: [ACCOUNT]
```

### Solution Applied
The pipeline has been updated to use the correct project-scoped connector reference:
- **Old**: `account.Github` (incorrect - looking at account level)
- **New**: `Github` (correct - looking at project level)

## Understanding Connector Scopes in Harness

### Connector Reference Formats

| Scope | Reference Format | Example | When to Use |
|-------|-----------------|---------|-------------|
| **Project** | `connectorId` | `Github` | Connector created in current project |
| **Organization** | `org.connectorId` | `org.Github` | Connector created at org level |
| **Account** | `account.connectorId` | `account.Github` | Connector created at account level |

### Your Current Setup
- **Connector Name**: Github
- **Scope**: Project (Testing)
- **Correct Reference**: `Github` (no prefix needed)

## How to Verify Your Connector

### Method 1: Via Harness UI
1. Go to your **Testing** project
2. Navigate to **Project Settings** → **Connectors**
3. Look for your GitHub connector
4. Note the **Identifier** (should be `Github`)
5. Check the **Scope** indicator

### Method 2: Test Connection
1. In your project, go to **Connectors**
2. Find your GitHub connector
3. Click the three dots menu → **Test Connection**
4. Verify it shows "Connection Successful"

## Common Connector Issues and Fixes

### Issue 1: Connector Not Found
**Error**: `Connector not found for identifier : [XXX]`

**Fixes**:
1. Check the connector identifier spelling (case-sensitive)
2. Verify the scope prefix:
   - Remove `account.` if connector is project-scoped
   - Remove `org.` if connector is project-scoped
   - Add appropriate prefix if connector is at different scope

### Issue 2: Permission Denied
**Error**: `Permission denied accessing connector`

**Fixes**:
1. Ensure you have access to the project/org/account
2. Check connector sharing settings
3. Verify your role has connector usage permissions

### Issue 3: Repository Not Accessible
**Error**: `Failed to fetch repository`

**Fixes**:
1. Verify the repository name format:
   - Should be: `owner/repository` (e.g., `Smashkat12/aws-saas-factory-eks-reference-architecture`)
   - Not: `https://github.com/owner/repository`
2. Check GitHub permissions:
   - Connector has access to the repository
   - Token/OAuth has sufficient permissions

## Verifying the Fix

After updating the pipeline:

1. **Re-import the Pipeline**:
   ```yaml
   connectorRef: Github  # Now correctly project-scoped
   ```

2. **Run a Test**:
   - Execute the pipeline
   - It should now find the connector correctly
   - Clone step should succeed

## Quick Reference for Your Setup

```yaml
properties:
  ci:
    codebase:
      connectorRef: Github  # ✅ Correct (project-scoped)
      repoName: Smashkat12/aws-saas-factory-eks-reference-architecture
      build: <+input>
```

## Additional Connector Best Practices

1. **Naming Convention**:
   - Use descriptive names: `Github_ReadOnly`, `Github_Admin`
   - Avoid spaces in identifiers
   - Be consistent with casing

2. **Security**:
   - Use Personal Access Tokens with minimal permissions
   - Rotate tokens regularly
   - Never commit tokens to repositories

3. **Organization**:
   - Project-level: For project-specific repositories
   - Org-level: For shared repositories across projects
   - Account-level: For organization-wide resources

## Need More Help?

If the connector still doesn't work after these fixes:

1. **Double-check the identifier**:
   - Go to Connectors page
   - Click on your GitHub connector
   - Copy the exact "Identifier" shown
   - Update pipeline with this exact value

2. **Create a new connector** (if needed):
   - Delete the existing one
   - Create new with identifier: `Github` (no spaces)
   - Test connection before using

3. **Check Harness logs**:
   - Pipeline execution → View logs
   - Look for detailed error messages
   - Share with Harness support if needed

---

**Status**: ✅ Issue Fixed - Pipeline updated to use correct project-scoped connector reference