# SaaS Factory EKS Deployment Flow

## Overview
This document outlines the comprehensive Harness CI/CD pipeline for deploying the SaaS Factory EKS infrastructure using AWS CDK.

## Pipeline Configuration

### Pipeline Parameters
- **Email**: smashkat12@gmail.com
- **Domain**: thinkm8.co.uk (prod) / dev.thinkm8.co.uk (dev)  
- **Hosted Zone ID**: Z0494651P0BXWLJV1MEX
- **AWS Region**: us-east-1
- **AWS Connector**: testing_aws_connector_direct

## Deployment Stages

### 1. Build and Test Stage
**Duration**: ~5-10 minutes
**Purpose**: Validate code quality and CDK synthesis

#### Steps:
1. **Setup Environment**
   - Configure Node.js environment
   - Install yq for YAML parsing
   - Configure AWS CLI with connector credentials
   - Validate AWS access

2. **Install Dependencies**
   - Run `npm ci` to install exact dependencies
   - Build project with `npm run build`
   - Set environment variables for email, domain, and hosted zone

3. **Run Tests**
   - Execute test suite with `npm test`
   - Validate code quality and functionality

4. **CDK Synth**
   - Synthesize CDK templates with parameters:
     - email=smashkat12@gmail.com
     - domain=thinkm8.co.uk
     - hostedZoneId=Z0494651P0BXWLJV1MEX
     - environment={ENV}
   - Validate CloudFormation templates

5. **Security Scan**
   - Run npm audit for vulnerability detection
   - Scan for hardcoded secrets
   - Security validation

### 2. Deploy Infrastructure Stage
**Duration**: ~15-30 minutes
**Purpose**: Deploy AWS infrastructure using CDK

#### Steps:
1. **Pre-deployment Validation**
   - Validate AWS credentials and permissions
   - Check AWS CLI configuration
   - Validate Route53 hosted zone access
   - Verify account and region settings

2. **CDK Bootstrap**
   - Bootstrap CDK in target AWS account/region
   - Create CDK toolkit resources
   - Configure execution policies

3. **CDK Deploy**
   - Deploy all CDK stacks with parameters:
     - Context: email, domain, hostedZoneId, environment
     - Parameters: email, domain, hostedZoneId
   - Use --require-approval never for automation
   - Deploy EKS cluster and associated resources

4. **Post-Deploy Configuration**
   - Update kubeconfig for EKS cluster
   - Verify cluster connectivity
   - Apply additional Kubernetes configurations

### 3. Post-Deployment Validation Stage
**Duration**: ~5-10 minutes
**Purpose**: Validate deployed infrastructure

#### Steps:
1. **Validate EKS Cluster**
   - Describe EKS cluster details
   - Update kubeconfig
   - Validate nodes are ready and healthy
   - Check node status and configurations

2. **Run Health Checks**
   - Check all pods across namespaces
   - Validate services and ingress controllers
   - Test DNS resolution within cluster
   - Validate service connectivity

3. **Infrastructure Testing**
   - Test Route53 DNS records for domain
   - Test load balancer endpoints
   - Validate security groups configuration
   - Check networking and connectivity

4. **Generate Deployment Report**
   - Create comprehensive deployment report
   - Include cluster information and deployed services
   - Document deployment timestamp and configuration
   - Export report as artifact

## Secrets Configuration

### Required Secrets (must be created in Harness):
- `aws_access_key_id`: AWS Access Key ID
- `aws_secret_access_key`: AWS Secret Access Key  
- `aws_account_id`: AWS Account ID
- `github_pat`: GitHub Personal Access Token
- `github_username`: GitHub Username
- `datadog_api_key`: Datadog API Key (for monitoring)

### Creating Secrets via Harness UI:
1. Navigate to Project Settings > Secrets
2. Click "New Secret" > "Text"
3. Enter identifier and value
4. Set appropriate scope (Project/Org)

## Connectors

### AWS Connector (testing_aws_connector_direct)
- **Type**: AWS
- **Authentication**: Access Key/Secret Key
- **Region**: us-east-1
- **Delegate**: harness-delegate

### GitHub Connector (github_connector)
- **Type**: GitHub
- **URL**: https://github.com/your-org/saas-factory-eks
- **Authentication**: Personal Access Token
- **Delegate**: harness-delegate

## Environment-Specific Configurations

### Development Environment
- **Domain**: dev.thinkm8.co.uk
- **Cluster Name**: saas-factory-dev-cluster
- **Environment**: development

### Production Environment  
- **Domain**: thinkm8.co.uk
- **Cluster Name**: saas-factory-prod-cluster
- **Environment**: production

## Deployment Triggers

### Main Branch Trigger
- Automatically triggers on push to main branch
- Uses production input set
- Requires manual approval for production deployment

### Feature Branch Trigger
- Triggers on pull request creation/update
- Uses development input set
- Automatic deployment to dev environment

## Rollback Strategy

### Automatic Rollback
- Pipeline configured with StageRollback on failure
- CDK supports stack rollback on deployment failure
- EKS cluster maintains previous configuration

### Manual Rollback
1. Identify successful deployment version
2. Use CDK to redeploy previous stack version
3. Update Kubernetes configurations if needed
4. Validate rollback completion

## Monitoring and Alerting

### Pipeline Monitoring
- Harness dashboard provides real-time pipeline status
- Email notifications sent to: smashkat12@gmail.com
- Slack integration for team notifications

### Infrastructure Monitoring
- CloudWatch monitoring for EKS cluster
- Datadog integration for application monitoring
- Custom metrics and dashboards

## Security Considerations

### Pipeline Security
- All secrets stored in Harness Secret Manager
- AWS credentials use least privilege principle
- GitHub access limited to repository scope

### Infrastructure Security
- EKS cluster uses private subnets
- Security groups configured with minimal access
- Pod Security Policies enabled
- Network policies enforced

## Troubleshooting

### Common Issues
1. **CDK Bootstrap Fails**: Check AWS permissions and region
2. **EKS Cluster Creation**: Verify VPC and subnet configurations  
3. **DNS Resolution**: Check Route53 hosted zone configuration
4. **Kubectl Access**: Verify EKS cluster endpoint and authentication

### Debug Commands
```bash
# Check CDK status
cdk list
cdk diff

# Check EKS cluster
aws eks describe-cluster --name {cluster-name} --region us-east-1
kubectl get nodes

# Check DNS
nslookup thinkm8.co.uk
```

## Cost Optimization

### Resource Management
- EKS cluster autoscaling enabled
- Spot instances for non-production workloads
- Resource quotas and limits configured

### Monitoring
- AWS Cost Explorer integration
- Budget alerts configured
- Resource utilization monitoring

## Compliance and Governance

### Audit Trail
- All pipeline executions logged
- CloudTrail enabled for AWS resources
- Change management through Git workflow

### Compliance
- SOC2 compliance considerations
- Data residency in specified regions
- Security scanning and vulnerability management

## Next Steps

1. **Environment Setup**: Create required secrets in Harness
2. **Connector Configuration**: Validate AWS and GitHub connectors
3. **Pipeline Testing**: Run pipeline in development environment
4. **Production Deployment**: Execute production deployment with approval
5. **Monitoring Setup**: Configure monitoring and alerting
6. **Documentation**: Update team documentation and runbooks

---

**Contact**: smashkat12@gmail.com  
**Last Updated**: $(date)  
**Version**: 1.0