# GitHub Actions Workflow Templates

This directory contains modern GitHub Actions workflows for the EC2StartStop Lambda project.

## Why are these here?

Due to GitHub App security permissions, workflow files in `.github/workflows/` cannot be pushed automatically. These template files are provided here for you to manually add to your repository.

## Installation

### Option 1: Copy via Command Line

If you have the repository cloned locally:

```bash
# From the repository root
cp -r workflow-templates/.github .
git add .github/
git commit -m "Add GitHub Actions workflows"
git push
```

### Option 2: Add via GitHub Web Interface

1. Go to your repository on GitHub
2. Click "Add file" → "Create new file"
3. For each file below, create the path and copy the content:
   - `.github/workflows/ci.yml`
   - `.github/workflows/release.yml`
   - `.github/dependabot.yml`

## Workflow Descriptions

### CI Workflow (`ci.yml`)

Runs on every push and pull request:
- **Linting**: Ruff for fast Python linting, Black for code formatting
- **Type Checking**: mypy for static type analysis
- **Security Scanning**: Bandit for security issues, Safety for dependency vulnerabilities
- **Lambda Validation**: Builds and validates Lambda deployment package
- **Multi-Python Testing**: Tests on Python 3.11 and 3.12
- **Artifacts**: Uploads Lambda package for testing

### Release Workflow (`release.yml`)

Runs on tags and releases:
- **Automated Packaging**: Creates production-ready lambda.zip
- **Checksums**: Generates SHA256 checksums for verification
- **Release Assets**: Attaches packages to GitHub releases
- **AWS Deployment**: Optional Lambda deployment (requires configuration)

To enable AWS deployment:
1. Configure repository secrets:
   - `AWS_ROLE_ARN` - IAM role ARN for OIDC
   - `AWS_REGION` - Your AWS region
2. Uncomment the deployment section in `release.yml`
3. Update the Lambda function name

### Dependabot Configuration (`dependabot.yml`)

Automated dependency updates:
- **GitHub Actions**: Weekly updates for action versions
- **Python Dependencies**: Weekly updates for pip packages
- **Auto-labeling**: Organized with dependency labels

## Usage

Once installed, workflows will run automatically:
- CI runs on every push/PR
- Release workflow runs when you create a tag or release
- Dependabot creates PRs weekly for dependency updates

## Requirements

These workflows use:
- GitHub Actions: v4/v5 (latest stable)
- Python: 3.11, 3.12
- Dependencies: boto3, ruff, black, mypy, bandit, safety, pytest

All dependencies are installed automatically by the workflows.
