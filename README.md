# aws-lambda-ebs-snapshots

## Prepare IAM

**Create the ebs-backup-worker role and attach the trust policy to it:**

`aws iam create-role --role-name ebs-backup-worker --assume-role-policy-document file://snapshot-trust.json`

**Attach the IAM policy to the role:**

`aws iam put-role-policy --role-name ebs-backup-worker --policy-name TakeSnapshots --policy-document file://snapshot-policy.json`

## Create the Lambda Function

In the AWS Console go to Lambda and click "Create a Lambda function"

### Blueprint
- Click "skip" (we're creating our own code)

### Configure function

##### Lambda function code

- **Name:** yourlambdafunction
- **Description:** your lambda function description
- **Runtime:** Python
- **Code entry type:** paste contents of schedule-ebs-snapshot-backups.py

##### Lambda function handler and role
- **Handler:** lambda_function.lambda_handler
- **Role:** select "ebs-backup-worker" from the list

##### Advanced settings
- Customize if necessary
- Click "Next"
- Review
- Click "Create function"

#### Test
- Click the "Event sources" tab
- Click "Add event source"
- **Event source type:** CloudWatch Events - Schedule
- **Rule name:** arbitrary
- **Rule description:** arbitrary
- **Schedule expression:** choose your frequency here - note that cron expressions are in UTC
    - cron example: `cron(0 * * * ? *)`
- Click "Submit"

#### Test the function

- Click "Event sources" and copy the **arn** string for the scheduled event we just created
- Click "Test" 
    - Input test event
        - Sample event template: Scheduled Event
        - Paste the **arn** value you just copied into the resources section
        - Click "Save"
        - Click "Test" to test the function
