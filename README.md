# Amazon Lex - Start Stop EC2 Instances Example

## Architecture

![Alt text](images/LexEC2StartStop.png?raw=true "Amazon Lex - Start Stop EC2 Instances Example")

## Background

This project implements a simple Amazon Lex Bot which provides a conversational interface to start and stop EC2 instances.

## Getting Started

### Prerequisites

Before you begin, ensure you have the following:

1. **AWS Account** with appropriate permissions
2. **IAM Permissions** to create and manage:
   - Amazon Lex bots
   - AWS Lambda functions
   - EC2 instances
   - IAM roles and policies
3. **EC2 Instances** tagged with `Type` key (e.g., `Type: license`, `Type: render`, `Type: workstation`)
4. **Python 3.x** (for local development/testing)
5. **AWS CLI** configured with your credentials (optional but recommended)

### Setup Instructions

#### Step 1: Prepare Your EC2 Instances

Tag your EC2 instances with a `Type` tag:
- Navigate to EC2 Console
- Select your instances
- Add a tag with Key: `Type` and Value: `license`, `render`, or `workstation` (or any custom value)

#### Step 2: Create IAM Role for Lambda

Create an IAM role with the following permissions:
- `AWSLambdaBasicExecutionRole` (for CloudWatch Logs)
- EC2 permissions:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "ec2:DescribeInstances",
          "ec2:StartInstances",
          "ec2:StopInstances"
        ],
        "Resource": "*"
      }
    ]
  }
  ```

#### Step 3: Deploy Lambda Function

1. **Package the Lambda function:**
   ```bash
   zip lambda.zip lambda.py
   ```

2. **Create Lambda function via AWS Console:**
   - Navigate to AWS Lambda Console
   - Click "Create function"
   - Choose "Author from scratch"
   - Function name: `EC2StartStopLexFunction`
   - Runtime: `Python 3.x`
   - Role: Select the IAM role created in Step 2
   - Upload the `lambda.zip` file

3. **Set Environment Variables:**
   - Key: `INSTANCE_REGION`
   - Value: Your AWS region (e.g., `us-east-1`)

4. **Note the Lambda function ARN** - you'll need this for Lex configuration

#### Step 4: Create Amazon Lex Bot

1. **Create a new Lex bot:**
   - Navigate to Amazon Lex Console
   - Click "Create bot"
   - Bot name: `EC2StartStopBot`
   - Choose language and voice settings

2. **Create Intents:**

   **StartInstances Intent:**
   - Intent name: `StartInstances`
   - Sample utterances:
     - "Start {serverType} instances"
     - "Start {serverType}"
     - "Start the {serverType} servers"
   - Slot: `serverType` (type: AMAZON.AlphaNumeric or custom slot type)
   - Fulfillment: Lambda function created in Step 3

   **StopInstances Intent:**
   - Intent name: `StopInstances`
   - Sample utterances:
     - "Stop {serverType} instances"
     - "Stop {serverType}"
     - "Stop the {serverType} servers"
   - Slot: `serverType` (type: AMAZON.AlphaNumeric or custom slot type)
   - Fulfillment: Lambda function created in Step 3

3. **Build and test the bot** in the Lex Console

#### Step 5: Grant Lex Permissions to Lambda

Add a resource-based policy to your Lambda function to allow Lex to invoke it:
```bash
aws lambda add-permission \
  --function-name EC2StartStopLexFunction \
  --statement-id LexInvokePermission \
  --action lambda:InvokeFunction \
  --principal lex.amazonaws.com \
  --source-arn "arn:aws:lex:REGION:ACCOUNT_ID:intent:StartInstances:*"
```

Repeat for the StopInstances intent.

### Usage

Once deployed, you can interact with the bot using natural language:

- "Start license instances"
- "Stop render servers"
- "Start workstation"

The bot will:
1. Identify the intent (start or stop)
2. Extract the server type from your request
3. Find all EC2 instances with matching `Type` tag
4. Execute the start/stop operation
5. Return a confirmation message

### Troubleshooting

- **Lambda timeout**: Increase timeout if managing many instances
- **No instances found**: Verify EC2 instance tags match the serverType value
- **Permission errors**: Check IAM role has necessary EC2 permissions
- **Region mismatch**: Ensure `INSTANCE_REGION` environment variable matches your EC2 region

### Configuration Files

The `lambda/` directory contains sample Lex intent configurations for different server types:
- `lambda.startinstances.*.json` - Start intent configurations
- `lambda.stopinstances.*.json` - Stop intent configurations

These can be imported into Lex Console for quick setup.