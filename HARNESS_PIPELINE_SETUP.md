# Harness Pipeline Setup Guide

## Overview

This document provides comprehensive instructions for setting up and using the Harness CI/CD pipeline for the AWS SaaS Factory EKS Reference Architecture project.

## Prerequisites

### Harness Account Setup
1. **Harness Account**: Ensure you have access to Harness CI with Cloud build infrastructure (Basic tier supported)
2. **GitHub Integration**: Configure GitHub connector in Harness
3. **AWS Account**: Access to an AWS account with appropriate permissions

### Required Harness Secrets

Before running the pipeline, configure the following secrets in Harness:

| Secret Name | Description | Example |
|-------------|-------------|---------|
| `AWS_ACCESS_KEY_ID` | AWS IAM user access key | `AKIAIOSFODNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM user secret key | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |
| `AWS_DEFAULT_REGION` | AWS deployment region | `us-east-1` |
| `AWS_ACCOUNT_ID` | AWS account ID | `123456789012` |

### Required Pipeline Variables

The pipeline requires these variables to be set:

| Variable Name | Description | Required | Default |
|---------------|-------------|----------|---------|
| `SYSTEM_ADMIN_EMAIL` | System administrator email | Yes | _(empty)_ |
| `AWS_ACCOUNT_ID` | AWS Account ID for CDK bootstrap | Yes | _(empty)_ |

## Pipeline Architecture

### Execution Phases

The pipeline is structured in sequential phases with parallel execution where possible:

```mermaid
graph TD
    A[Environment Setup] --> B[AWS Configuration]
    B --> C[Parallel Build Phase]
    C --> D[CDK Bootstrap]
    D --> E[Main Deployment]
    E --> F[Post-Deployment Validation]
    
    C --> G[Dependencies & Validation]
    C --> H[Code Quality Checks]
    
    G --> I[Install Dependencies]
    G --> J[Run Tests]
    
    H --> K[TypeScript Build]
    H --> L[Security Audit]
```

### Phase Details

#### 1. Environment Setup
- **Node.js 20.x Installation**: Uses NodeSource repository for latest Node.js 20.x
- **AWS CDK Installation**: Installs CDK globally
- **AWS CLI Installation**: Ensures AWS CLI v2 is available
- **Timeout**: 5 minutes per step

#### 2. AWS Configuration
- **Credentials Setup**: Configures AWS credentials from Harness secrets
- **Connectivity Test**: Validates AWS access using STS
- **Region Configuration**: Sets up AWS region and pager settings
- **Timeout**: 3 minutes

#### 3. Parallel Build Phase
Runs two parallel step groups for efficiency:

**Dependencies & Validation:**
- Clean dependency installation (`npm ci`)
- Unit test execution (if tests exist)
- CDK version verification

**Code Quality Checks:**
- TypeScript compilation
- Security audit with npm audit
- Sensitive file detection

#### 4. CDK Bootstrap
- **Bootstrap CDK**: Prepares CDK toolkit in target AWS account/region
- **Account Verification**: Uses AWS_ACCOUNT_ID variable for bootstrap
- **Timeout**: 15 minutes

#### 5. Main Deployment
- **Environment Setup**: Sets required environment variables
- **Email Validation**: Validates SYSTEM_ADMIN_EMAIL parameter
- **Script Execution**: Runs `scripts/install.sh` with email parameter
- **Timeout**: 45 minutes

#### 6. Post-Deployment Validation
- **Stack Validation**: Verifies CloudFormation stacks
- **Output Extraction**: Retrieves Admin and Application Site URLs
- **Report Generation**: Creates detailed deployment report

## Usage Instructions

### 1. Initial Setup

1. **Import Pipeline**: Import `harness-pipeline.yaml` into your Harness project
2. **Configure Secrets**: Set up all required secrets in Harness
3. **Set Variables**: Configure pipeline variables with appropriate values
4. **GitHub Connector**: Ensure GitHub connector is properly configured

### 2. Running the Pipeline

1. **Trigger Pipeline**: Can be triggered manually or via webhook
2. **Provide Variables**: 
   - Enter your system admin email
   - Confirm AWS account ID
3. **Monitor Execution**: Watch the pipeline execution in Harness UI
4. **Check Notifications**: Success/failure notifications will be sent to admin email

### 3. Pipeline Execution Flow

```bash
# The pipeline will execute these key operations:
1. Clone from: https://github.com/Smashkat12/aws-saas-factory-eks-reference-architecture.git (development branch)
2. Setup Node.js 20.x environment
3. Install AWS CDK globally
4. Configure AWS credentials
5. Install project dependencies
6. Run tests and security checks
7. Bootstrap CDK
8. Execute ./scripts/install.sh with email parameter
9. Validate deployment
10. Generate deployment report
```

## Error Handling

### Failure Strategies
- **Step-level**: Individual steps have appropriate failure strategies
- **Stage-level**: Stage rollback on critical failures
- **Pipeline-level**: Comprehensive error handling with notifications

### Common Issues and Solutions

| Issue | Cause | Solution |
|-------|-------|----------|
| Node.js installation fails | Network/permission issues | Check Harness cloud infrastructure connectivity |
| AWS credentials invalid | Incorrect secret values | Verify AWS secret configuration |
| CDK bootstrap fails | Insufficient AWS permissions | Ensure IAM user has CDK bootstrap permissions |
| Deployment timeout | Large infrastructure deployment | Consider increasing timeout values |
| Email validation fails | Missing SYSTEM_ADMIN_EMAIL | Ensure pipeline variable is set |

## Notifications

### Success Notification
- **Recipient**: System admin email
- **Subject**: "✅ SaaS Factory Deployment Successful"
- **Content**: Deployment summary with execution links

### Failure Notification  
- **Recipient**: System admin email
- **Subject**: "❌ SaaS Factory Deployment Failed"
- **Content**: Failure details with log access links

## Security Considerations

### Secret Management
- All AWS credentials stored as Harness secrets
- No hardcoded credentials in pipeline
- Environment variables properly scoped

### Access Control
- Pipeline execution requires appropriate Harness permissions
- AWS IAM user should follow principle of least privilege
- GitHub connector should have minimal required permissions

### Security Auditing
- npm audit runs automatically
- Sensitive file detection included
- Security warnings reported in logs

## Monitoring and Troubleshooting

### Log Access
- **Harness UI**: View detailed execution logs for each step
- **Step-level Logs**: Individual step output and errors
- **Deployment Report**: Generated report with deployment summary

### Performance Monitoring
- **Parallel Execution**: Dependencies and quality checks run in parallel
- **Timeout Management**: Appropriate timeouts set for each phase
- **Resource Optimization**: Cloud infrastructure automatically managed

### Troubleshooting Steps

1. **Check Secrets**: Verify all Harness secrets are correctly configured
2. **Validate Variables**: Ensure pipeline variables have valid values
3. **Review Logs**: Check step-level logs for specific error messages
4. **AWS Permissions**: Verify IAM user has required permissions
5. **Network Connectivity**: Ensure Harness can access AWS and GitHub

## Customization Options

### Modifying Deployment Parameters
To customize the deployment, you can modify the main deployment step:

```yaml
# In the deploy_saas_factory step, you can add additional parameters:
./scripts/install.sh "$CDK_PARAM_SYSTEM_ADMIN_EMAIL" --custom-param "value"
```

### Adding Additional Steps
To add custom validation or configuration steps:

```yaml
- step:
    type: Run
    name: Custom Validation
    identifier: custom_validation
    spec:
      shell: Bash
      command: |
        # Your custom validation logic here
        echo "Running custom validation..."
```

### Notification Customization
Modify notification settings in the `notificationRules` section to:
- Add additional recipients
- Customize message content
- Add Slack or webhook notifications

## Maintenance

### Regular Updates
- **CDK Version**: Keep AWS CDK updated for latest features
- **Node.js Version**: Update Node.js version as needed
- **Dependencies**: Regular npm audit and updates
- **Pipeline Template**: Keep pipeline YAML updated with best practices

### Monitoring
- **Pipeline Execution History**: Regular review of pipeline executions
- **Performance Metrics**: Monitor execution times and optimize
- **Cost Analysis**: Review AWS costs associated with deployments

## Support and Documentation

### Resources
- **Harness Documentation**: [https://docs.harness.io/](https://docs.harness.io/)
- **AWS CDK Documentation**: [https://docs.aws.amazon.com/cdk/](https://docs.aws.amazon.com/cdk/)
- **Project Repository**: [https://github.com/Smashkat12/aws-saas-factory-eks-reference-architecture](https://github.com/Smashkat12/aws-saas-factory-eks-reference-architecture)

### Getting Help
1. **Pipeline Issues**: Check Harness execution logs and error messages
2. **AWS Issues**: Review CloudFormation events and AWS CloudWatch logs
3. **Application Issues**: Check application-specific logs and documentation

---

**Note**: This pipeline is configured for the Basic tier of Harness CI, using Cloud build infrastructure. For enterprise features, consider upgrading your Harness subscription.