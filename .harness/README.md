# Harness CI/CD Configuration

This directory contains Harness CI/CD pipeline configurations for the SaaS Factory EKS deployment.

## Structure

- `pipeline.yaml` - Main deployment pipeline definition
- `input-sets/` - Environment-specific input sets
  - `dev.yaml` - Development environment configuration
  - `prod.yaml` - Production environment configuration
- `triggers/` - Pipeline triggers
  - `main-branch-trigger.yaml` - Trigger on main branch push
- `connectors/` - External system connectors
  - `aws-connector.yaml` - AWS connector configuration
- `templates/` - Reusable templates
  - `cdk-deployment-template.yaml` - CDK deployment template

## Configuration

The main configuration is stored in `harness-config.yaml` at the root level with the following parameters:

- Email: smashkat12@gmail.com
- Domain: thinkm8.co.uk
- Hosted Zone: Z0494651P0BXWLJV1MEX

## Pipeline Stages

1. **Build and Test** - Installs dependencies, runs tests, and performs CDK synth
2. **Deploy Infrastructure** - Bootstraps and deploys CDK infrastructure
3. **Post-Deployment Validation** - Validates the deployed EKS cluster

## Environment Variables

The pipeline uses the following environment variables:
- `ENV` - Environment name (development/production)
- `AWS_REGION` - AWS region for deployment
- `CLUSTER_NAME` - EKS cluster name
- `ACCOUNT_ID` - AWS account ID

## Secrets Required

Ensure the following secrets are configured in Harness:
- `aws_access_key` - AWS access key
- `aws_secret_key` - AWS secret key
- `datadog_api_key` - DataDog API key (optional)

## Usage

1. Import the pipeline configuration into Harness
2. Configure the required connectors and secrets
3. Set up the triggers as needed
4. Execute the pipeline with appropriate input sets