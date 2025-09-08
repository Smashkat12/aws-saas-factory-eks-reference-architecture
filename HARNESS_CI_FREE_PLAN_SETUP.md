# Enabling Harness CI Free Plan

## Overview
Harness offers a **Free Plan** for CI/CD that includes:
- **2,000 build minutes per month** (much more than Basic tier's 300 minutes!)
- **Unlimited users** (vs 5 users in Basic)
- **Harness Cloud infrastructure** included
- **Community support**
- **All CI features** available

## Step-by-Step Guide to Enable CI Free Plan

### Step 1: Access Harness Account Settings
1. Log in to your Harness account at https://app.harness.io
2. Click on your **Account Settings** (gear icon) in the top-right corner
3. Navigate to **Subscriptions** or **Plans & Billing**

### Step 2: Check Current Plan Status
1. Look for your current plan details
2. You should see options for different modules:
   - **CI (Continuous Integration)**
   - CD (Continuous Delivery)
   - FF (Feature Flags)
   - CCM (Cloud Cost Management)
   - STO (Security Testing Orchestration)

### Step 3: Enable CI Free Plan

#### Option A: If You're on Trial
If you're currently on a trial:
1. Wait for trial to expire OR
2. Click **"Switch to Free Plan"** if available
3. Select **CI Module**
4. Choose **Free Plan**
5. Confirm selection

#### Option B: Direct Free Plan Activation
1. Navigate to **Subscriptions** → **Continuous Integration**
2. Click **"Get Started"** or **"Choose Plan"**
3. Select **"Free"** tier
4. Review included features:
   - 2,000 build minutes/month
   - Harness Cloud runners
   - Unlimited users
   - Community support
5. Click **"Start Free"** or **"Activate"**

#### Option C: Through Module Selection
1. Go to **Account Settings** → **Default Settings** → **Modules**
2. Enable **CI Module**
3. When prompted for plan selection, choose **Free**
4. Accept terms and conditions
5. Click **Confirm**

### Step 4: Verify Activation
1. Go back to **Subscriptions** page
2. Verify CI shows as **"Free Plan - Active"**
3. Check your build minutes quota shows **2,000 minutes**

### Step 5: Configure Your Project for CI
1. Navigate to your **Testing** project
2. Go to **Project Settings** → **CI Settings**
3. Ensure **Harness Cloud** is selected as infrastructure
4. Verify build infrastructure is available

## Important Notes for Free Plan

### What's Included:
✅ **2,000 build minutes per month** on Harness Cloud  
✅ **Unlimited users** can access CI  
✅ **All CI features** (pipelines, triggers, etc.)  
✅ **Harness Cloud infrastructure** (no self-hosted runners needed)  
✅ **Docker layer caching**  
✅ **Parallel step execution**  
✅ **Git integration**  

### Limitations:
❌ No dedicated support (community only)  
❌ No self-hosted runners on Free plan  
❌ Build minutes don't roll over  
❌ No SLA guarantees  

### Build Minutes Calculation:
- **Linux builds**: 1 minute = 1 build minute
- **Windows builds**: 1 minute = 2 build minutes
- **macOS builds**: 1 minute = 4 build minutes

## Optimizing for 2,000 Free Minutes

With 2,000 minutes, you can run your pipeline approximately:
- **26-33 full deployments** per month (60-75 min each)
- That's roughly **1 deployment per day**!

### Tips to Maximize Minutes:
1. **Use caching** for dependencies
2. **Skip unchanged stages** with conditional execution
3. **Use parallel steps** to reduce total time
4. **Set shorter timeouts** to prevent hanging builds
5. **Use manual triggers** for production deployments

## Troubleshooting

### Issue: Can't Find Free Plan Option
**Solution**: 
- Ensure you're logged in as an account admin
- Try accessing: https://app.harness.io/ng/account/[YOUR_ACCOUNT_ID]/subscriptions
- Contact Harness support if option not visible

### Issue: Already on Paid Plan
**Solution**:
- You may need to downgrade first
- Contact billing support to switch plans
- Consider waiting for billing cycle to end

### Issue: Trial Still Active
**Solution**:
- Free plan activates after trial expires
- You can request early conversion through support
- Trial typically lasts 14-30 days

## Quick Links

- **Harness Pricing Page**: https://www.harness.io/pricing
- **CI Documentation**: https://developer.harness.io/docs/continuous-integration
- **Community Forum**: https://community.harness.io
- **Support** (for account issues): https://support.harness.io

## Comparison: Free vs Basic vs Trial

| Feature | Free Plan | Basic Plan | Trial |
|---------|-----------|------------|-------|
| Build Minutes | 2,000/month | 300/month | Unlimited (14-30 days) |
| Users | Unlimited | 5 | Unlimited |
| Support | Community | Basic | Full |
| Infrastructure | Harness Cloud | Harness Cloud | Both |
| Price | $0 | ~$100/month | $0 (limited time) |

## Next Steps After Activation

1. **Import your pipeline** (`harness-pipeline.yaml`)
2. **Run a test deployment** to verify everything works
3. **Set up build notifications**
4. **Configure triggers** for automatic deployments
5. **Monitor usage** in Account Settings → Usage

---

**Note**: The Free Plan is perfect for your AWS SaaS Factory EKS deployment pipeline. With 2,000 minutes, you'll have plenty of capacity for daily deployments and testing!