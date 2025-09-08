# Harness CI/CD Pipeline Setup Guide
## AWS SaaS Factory EKS Reference Architecture - Basic Tier

This guide provides comprehensive instructions for setting up and running the Harness CI/CD pipeline for the AWS SaaS Factory EKS reference architecture, optimized for Harness Basic tier constraints.

## 📋 Prerequisites

### Harness Account Requirements
- **Harness Basic Tier Account**
  - Maximum 5 users
  - 300 build minutes per month
  - Standard connectors available
  - Basic support only

### AWS Requirements
- **AWS Account** with appropriate permissions
- **Programmatic access credentials** (Access Key ID and Secret Access Key)
- **Sufficient AWS service limits** for EKS, ECR, CloudFormation, etc.
- **Route53 hosted zone** (optional, for custom domain)

### Required AWS Permissions
Your AWS credentials must have the following permissions:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "sts:GetCallerIdentity",
        "cloudformation:*",
        "eks:*",
        "ec2:*",
        "iam:*",
        "ecr:*",
        "cognito-idp:*",
        "apigateway:*",
        "apigatewayv2:*",
        "route53:*",
        "cloudfront:*",
        "s3:*",
        "lambda:*",
        "logs:*",
        "ssm:*"
      ],
      "Resource": "*"
    }
  ]
}
```

## 🔧 Setup Instructions

### Step 1: Import the Pipeline

1. **Download the pipeline configuration**:
   - Use the file: `harness-pipeline-basic-tier.yaml`

2. **Import to Harness**:
   - Navigate to your Harness project
   - Go to **Pipelines** → **Create Pipeline** → **Import From Git** or **YAML**
   - Upload the `harness-pipeline-basic-tier.yaml` file

### Step 2: Configure Secrets

Create the following secrets in Harness:

1. **AWS Access Key ID**:
   - Name: `aws_access_key_id`
   - Type: Text
   - Value: Your AWS access key ID

2. **AWS Secret Access Key**:
   - Name: `aws_secret_access_key`
   - Type: Text
   - Value: Your AWS secret access key

**Security Note**: Never hardcode credentials in the pipeline YAML. Always use Harness secrets manager.

### Step 3: Configure Connectors

1. **GitHub Connector** (if using GitHub):
   - Create a GitHub connector pointing to your repository
   - Ensure it has read access to the repository

2. **Delegate Setup**:
   - Install a Harness delegate in your environment
   - Ensure the delegate has Docker access (for CDK operations)
   - Verify the delegate can access AWS services

### Step 4: Configure Pipeline Variables

When running the pipeline, you'll need to provide these inputs:

#### Required Variables:
- **CDK_PARAM_SYSTEM_ADMIN_EMAIL**: Email address for the SaaS administrator
- **AWS_REGION**: AWS region for deployment (e.g., us-west-2, eu-west-1)

#### Optional Variables:
- **CDK_PARAM_DOMAIN_NAME**: Custom domain name (leave empty for CloudFront URLs)
- **CDK_PARAM_HOSTED_ZONE_ID**: Route53 hosted zone ID (required if domain is provided)
- **KUBECOST_TOKEN**: Kubecost token for cost monitoring

### Step 5: Repository Configuration

1. **Branch Setup**:
   - The pipeline is configured to use the `development` branch
   - Ensure your repository has this branch available

2. **Repository Structure**:
   - The pipeline expects the standard AWS SaaS Factory EKS structure
   - Key files: `package.json`, `cdk.json`, `scripts/install.sh`

## 🚀 Running the Pipeline

### First-Time Deployment

1. **Trigger the Pipeline**:
   - Navigate to your pipeline
   - Click **Run Pipeline**
   - Provide required input values

2. **Monitor Execution**:
   - The pipeline will take approximately 35-45 minutes
   - Monitor each stage for progress and errors

3. **Expected Outputs**:
   - EKS cluster deployed
   - Admin and Application portals available
   - Cognito user pool configured
   - Email with temporary admin credentials

### Subsequent Deployments

- **Build Time**: ~30-40 minutes per run
- **Monthly Limit**: ~7-10 full deployments (300 minutes total)
- **Optimization**: Consider using shorter validation-only runs for testing

## 📊 Basic Tier Optimizations

### Build Time Optimizations
1. **Shallow Git Clone**: Only fetches the latest commit
2. **Cached Dependencies**: Leverages npm cache when possible
3. **Parallel Steps**: Uses step groups for concurrent execution
4. **Optimized Timeouts**: Reduced timeouts to prevent long waits

### Resource Management
1. **Cleanup Stage**: Removes build artifacts to save delegate space
2. **ECR Management**: Automatically creates required repositories
3. **CDK Bootstrap**: Checks and only runs when necessary

### Error Handling
1. **Comprehensive Validation**: Pre-flight checks prevent failed deployments
2. **Rollback Strategy**: Automatic stage rollback on errors
3. **Timeout Management**: Graceful handling of timeout scenarios

## 🔍 Troubleshooting

### Common Issues

#### 1. **AWS Permissions Errors**
```
Error: AccessDenied: User is not authorized to perform...
```
**Solution**: Verify AWS credentials have sufficient permissions (see AWS Requirements section)

#### 2. **CDK Bootstrap Issues**
```
Error: Need to perform AWS CDK bootstrap
```
**Solution**: The pipeline automatically handles bootstrap, but ensure your AWS account has CDK permissions

#### 3. **Domain/Hosted Zone Mismatch**
```
Error: Hosted Zone ID is required when domain is provided
```
**Solution**: Either provide both domain and hosted zone ID, or leave both empty for CloudFront URLs

#### 4. **Build Minutes Exhaustion**
```
Warning: Build minutes quota exceeded
```
**Solution**: Monitor usage in Harness dashboard; consider upgrading to Pro tier for larger projects

### Debugging Steps

1. **Check AWS Credentials**:
   ```bash
   aws sts get-caller-identity
   ```

2. **Validate CDK Bootstrap**:
   ```bash
   aws cloudformation describe-stacks --stack-name CDKToolkit
   ```

3. **Check ECR Repository**:
   ```bash
   aws ecr describe-repositories --repository-names cdk-*
   ```

## 🎯 Best Practices

### For Basic Tier Users

1. **Plan Deployments**: With 300 minutes/month, plan your deployment schedule
2. **Use Development Branch**: Keep main branch for production-ready code
3. **Monitor Usage**: Track build minutes in Harness dashboard
4. **Optimize Locally**: Test changes locally before pipeline runs

### Security Best Practices

1. **Secret Management**: Always use Harness secrets, never hardcode credentials
2. **Least Privilege**: Use AWS IAM roles with minimal required permissions
3. **Audit Logs**: Regularly review pipeline execution logs
4. **Credential Rotation**: Rotate AWS keys regularly

### Cost Optimization

1. **Resource Cleanup**: The pipeline includes automatic cleanup stages
2. **Regional Selection**: Choose AWS regions closest to your users
3. **Instance Sizing**: Consider using smaller instance types for development
4. **Monitoring**: Use Kubecost integration for cost visibility

## 📈 Scaling Beyond Basic Tier

### When to Upgrade to Pro Tier

Consider upgrading when you need:
- **More Build Minutes**: Pro tier offers 2,000 minutes/month
- **More Users**: Pro tier supports up to 20 users
- **Advanced Features**: GitOps, advanced approval workflows
- **Premium Support**: Enhanced support options

### Migration Path

1. **Export Pipeline Configuration**: Save your optimized configuration
2. **Upgrade Harness Account**: Contact Harness sales
3. **Enhanced Configuration**: Add advanced features like:
   - Multi-environment deployments
   - Advanced approval workflows
   - GitOps integration
   - Advanced security scanning

## 🔄 Pipeline Stages Overview

### Stage 1: Validate and Setup (10-15 minutes)
- **Prerequisites Validation**: Email format, domain/hosted zone consistency
- **AWS Environment Setup**: Credential verification, ECR repository creation
- **CDK Bootstrap Check**: Ensures CDK is properly initialized

### Stage 2: Build and Deploy (25-35 minutes)
- **Source Code Management**: Shallow clone from development branch
- **Dependencies and Build**: npm install, TypeScript compilation, CDK synthesis
- **AWS Deployment**: CDK stack deployment, post-deployment configuration

### Stage 3: Validation and Cleanup (5-10 minutes)
- **Deployment Validation**: Stack status verification, EKS cluster check
- **Cleanup and Optimization**: Artifact cleanup, usage reporting

## 📞 Support and Resources

### Harness Resources
- **Documentation**: [Harness Docs](https://docs.harness.io/)
- **Community**: [Harness Community Forum](https://community.harness.io/)
- **Basic Tier Support**: Available through standard channels

### AWS SaaS Factory Resources
- **GitHub Repository**: [AWS SaaS Factory EKS](https://github.com/aws-samples/aws-saas-factory-eks-reference-architecture)
- **Developer Guide**: See GUIDE.md in the repository
- **AWS Support**: Standard AWS support channels

### Getting Help

1. **Pipeline Issues**: Check Harness execution logs and this troubleshooting guide
2. **AWS Issues**: Verify permissions and check AWS service limits
3. **CDK Issues**: Refer to AWS CDK documentation and error messages
4. **General Questions**: Use the project's GitHub issues or discussions

---

## ⚠️ Important Notes for Basic Tier

- **Monthly Limit**: 300 build minutes total across all pipelines
- **Concurrent Builds**: Basic tier may have limitations on concurrent executions
- **Feature Limitations**: Some advanced Harness features require higher tiers
- **Support**: Basic support is available, response times may be longer

This pipeline is specifically optimized for Basic tier constraints while maintaining full functionality for AWS SaaS Factory EKS deployment.