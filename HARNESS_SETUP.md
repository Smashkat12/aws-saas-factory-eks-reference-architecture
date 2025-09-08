# Harness Pipeline Setup Guide for AWS SaaS Factory EKS

## Overview

This guide provides step-by-step instructions for setting up and configuring the Harness CI/CD pipeline to deploy the AWS SaaS Factory EKS Reference Architecture from your GitHub repository.

## Prerequisites

### Harness Account Setup
- **Harness Basic Tier Account** (supports 5 users, 300 build minutes/month)
- **Organization** and **Project** created in Harness

### AWS Requirements
- **AWS Account** with appropriate permissions
- **IAM User** with programmatic access for deployment
- **Route53 Hosted Zone** (optional, for custom domain)

### Required AWS IAM Permissions
The IAM user needs the following permissions:
- CloudFormation: Full access
- EKS: Full access
- EC2: Full access (for EKS nodes)
- IAM: Create/manage roles and policies
- VPC: Full access
- S3: Full access (for CDK assets)
- ECR: Full access (for container images)
- Cognito: Full access
- API Gateway: Full access
- Lambda: Full access
- DynamoDB: Full access
- Route53: Full access (if using custom domain)

## Step 1: Configure Harness Secrets

Navigate to **Project Settings** → **Secrets** and verify you have the following secrets:

### Required Secrets (Already Configured)
1. **aws_access_key_testing**
   - Type: Text
   - Value: Your AWS Access Key ID
   - Status: ✅ Already exists in your testing project

2. **aws_secret_key_testing**
   - Type: Text
   - Value: Your AWS Secret Access Key
   - Status: ✅ Already exists in your testing project

## Step 2: GitHub Connector

### Already Configured
✅ You already have a GitHub connector configured at the account level that has access to all repositories in your GitHub account.

**Connector Reference**: `account.Github`

No additional configuration needed for this step.

## Step 3: Import Pipeline

### Option A: Via Harness UI
1. Navigate to **Pipelines**
2. Click **+ Create a Pipeline**
3. Select **Import From Git**
4. Choose the `harness-pipeline.yaml` file from the repository

### Option B: Via YAML Editor
1. Navigate to **Pipelines**
2. Click **+ Create a Pipeline**
3. Switch to **YAML** view
4. Copy and paste the contents of `harness-pipeline.yaml`
5. Click **Save**

## Step 4: Configure Pipeline Variables

When running the pipeline, you'll need to provide:

### Required Variables
- **SYSTEM_ADMIN_EMAIL**: Email address for the system administrator (e.g., `smashkat12@gmail.com`)

### Pre-configured Variables (with defaults)
- **AWS_REGION**: AWS region for deployment (default: `eu-west-1`)
- **DOMAIN**: Your domain (default: `thinkm8.co.uk`)
- **HOSTED_ZONE_ID**: Route53 hosted zone ID (default: `Z0494651P0BXWLJV1MEX`)

Note: The domain and hosted zone are pre-configured with your values. You can override them if needed.

## Step 5: Run the Pipeline

### First Deployment
1. Navigate to **Pipelines**
2. Select **AWS SaaS Factory EKS Deployment**
3. Click **Run Pipeline**
4. Configure runtime inputs:
   - **Branch**: `development`
   - **SYSTEM_ADMIN_EMAIL**: Your email address (e.g., `smashkat12@gmail.com`)
   - **AWS_REGION**: Accept default `eu-west-1` or change if needed
   - **DOMAIN**: Accept default `thinkm8.co.uk` or change if needed
   - **HOSTED_ZONE_ID**: Accept default `Z0494651P0BXWLJV1MEX` or change if needed
5. Click **Run Pipeline**

### Monitoring Execution
- The pipeline consists of 3 stages:
  1. **Setup and Validate** (~10 minutes)
  2. **Build and Deploy** (~45-60 minutes)
  3. **Validation** (~5 minutes)
- Total execution time: ~60-75 minutes
- Monitor progress in the execution view
- Check logs for each step if issues occur

## Step 6: Post-Deployment

After successful deployment, you'll receive:
- **Admin Site URL**: Access the admin console
- **Application Site URL**: Main application interface
- **Landing Site URL**: Public landing page

These URLs will be displayed in the pipeline logs and sent via email notification.

## Troubleshooting

### Common Issues and Solutions

#### 1. CDK Bootstrap Error
**Issue**: "CDKToolkit stack not found"
**Solution**: The pipeline automatically handles CDK bootstrap. If it fails:
```bash
# Manually bootstrap CDK (run locally)
npx cdk bootstrap aws://ACCOUNT_ID/eu-west-1
```

#### 2. ECR Repository Error
**Issue**: "No repository policy to allow principal"
**Solution**: The pipeline creates ECR repositories automatically. If issues persist, verify IAM permissions.

#### 3. EKS Cluster Creation Timeout
**Issue**: Pipeline times out during EKS cluster creation
**Solution**: 
- EKS cluster creation can take 15-20 minutes
- Increase the timeout in the Deploy CDK Application step if needed
- Check AWS Console for cluster status

#### 4. Domain Configuration Error
**Issue**: "Invalid domain or hosted zone"
**Solution**:
- Ensure both DOMAIN and HOSTED_ZONE_ID are provided together
- Verify the hosted zone exists in Route53
- Check domain ownership

### Build Minutes Optimization (Basic Tier)

With 300 build minutes per month, you can run approximately:
- **4-5 full deployments** (60-75 minutes each)
- **7-10 partial deployments** (if CDK bootstrap already done)

**Tips to save build minutes:**
1. Use manual approval gates for production deployments
2. Skip validation stage for development deployments
3. Cache dependencies where possible
4. Run CDK bootstrap manually once per region

## Security Best Practices

1. **Rotate AWS credentials regularly**
2. **Use least-privilege IAM policies**
3. **Enable MFA on AWS accounts**
4. **Store secrets only in Harness Secrets Manager**
5. **Review pipeline logs for sensitive information**
6. **Use separate AWS accounts for dev/staging/prod**

## Customization

### Modifying Default Domain
The pipeline is pre-configured with your domain (`thinkm8.co.uk`) and hosted zone (`Z0494651P0BXWLJV1MEX`). These values are set as defaults in the pipeline variables, so you can:
1. Accept the defaults when running the pipeline
2. Override them at runtime if deploying to a different domain
3. Modify the default values in the pipeline YAML if permanently changing domains

### Adding Additional Stages
You can add custom stages for:
- Security scanning
- Performance testing
- Backup creation
- Cost analysis

### Parallel Execution
The pipeline uses step groups for parallel execution. You can add more parallel steps to reduce execution time.

## Support and Resources

### Harness Documentation
- [Harness CI Documentation](https://developer.harness.io/docs/continuous-integration)
- [Pipeline YAML Reference](https://developer.harness.io/docs/platform/pipelines/harness-yaml-quickstart)

### AWS SaaS Factory Resources
- [AWS SaaS Factory](https://aws.amazon.com/partners/programs/saas-factory/)
- [EKS Best Practices](https://aws.github.io/aws-eks-best-practices/)

### Getting Help
- Harness Community: https://community.harness.io
- AWS Support: Via AWS Console
- Repository Issues: https://github.com/Smashkat12/aws-saas-factory-eks-reference-architecture/issues

## Pipeline Features Summary

✅ **Automated Setup**
- Node.js 20.x installation
- AWS CDK installation
- AWS CLI v2 installation
- Dependency management

✅ **Security**
- Secrets management via Harness
- No hardcoded credentials
- AWS credential validation
- Parameter validation

✅ **Reliability**
- Pre-deployment validation
- Error handling at each step
- Post-deployment verification
- Email notifications

✅ **Optimization**
- Parallel execution where possible
- Conditional CDK bootstrap
- ECR lifecycle policies
- Build time tracking

## Next Steps

1. **Test the deployment** with a development environment
2. **Configure monitoring** using CloudWatch or Datadog
3. **Set up backup strategies** for stateful components
4. **Implement cost monitoring** with AWS Cost Explorer
5. **Create separate pipelines** for staging and production

---

**Last Updated**: 2025
**Pipeline Version**: 1.0.0
**Compatible with**: AWS SaaS Factory EKS Reference Architecture