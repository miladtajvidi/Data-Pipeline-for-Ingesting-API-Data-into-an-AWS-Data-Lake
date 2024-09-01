# 2. AWS Setup

## 2.1 Setting up S3 for Data Storage
We need to create an S3 bucket as our Data Lake. We can either do this programatically or manually using the AWS console. Since this is the only bucket that we need for now, we can take the manual approach. If you are interested in using CLI. the following template steps can be used: <br>

### 2.1.1 Create the Bucket

```bash
aws s3api create-bucket \
    --bucket my-bucket-name \
    --region us-west-2 \
    --acl private
```
### 2.1.2 Enable Server-Side Encryption

```bash
aws s3api put-bucket-encryption \
    --bucket my-bucket-name \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                }
            }
        ]
    }'

```
### 2.1.3 Enable Versioning(Optional)
```bash
aws s3api put-bucket-versioning \
    --bucket my-bucket-name \
    --versioning-configuration Status=Enabled
```

### 2.1.4 Set Bucket Policies (Optional)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": <Principal's ARN>
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-bucket-name/*"
        }
    ]
}
```

Apply the policy then using:
```bash
aws s3api put-bucket-policy \
    --bucket my-bucket-name \
    --policy file://bucket-policy.json


```

### 2.1.5 Enable Logging (Optional)
First, you need to create a 'log-config.json' file such as the following:
```json
{
    "LoggingEnabled": {
        "TargetBucket": "log-bucket-name",
        "TargetPrefix": "my-bucket-logs/"
    }
}


```
Then, apply the logging config using:
```bash
aws s3api put-bucket-logging \
    --bucket my-bucket-name \
    --bucket-logging-status file://logging-config.json


```

### 2.1.6 Enable Object Lock (Optional)

Please note that if you need to enable Object Lock, this must be done during the creation:
(Also, please remember that Object Lock works only in versioned buckets, thus enabling Object Lock automatically enables Versioning.)

```bash
aws s3api create-bucket \
    --bucket my-bucket-name \
    --region us-west-2 \
    --acl private \
    --object-lock-enabled-for-bucket


```
Then, set the Object Lock configuration using:
```bash
aws s3api put-object-lock-configuration \
    --bucket my-bucket-name \
    --object-lock-configuration '{
        "ObjectLockEnabled": "Enabled",
        "Rule": {
            "DefaultRetention": {
                "Mode": "GOVERNANCE",
                "Days": 30
            }
        }
    }'


```

## 2.2 Configuring IAM Roles and Policies

Since we will be working with a Lambda function to have a serverless application that can scale in real-time, we need to create a role that can call different components on our behalf. We choose AWS service as our Trusted Entity and select Lambda as our use case.
* For S3 access, we can use 'AmazonS3FullAccess' or a more restrictive policy.
* For Secrets Manager, we can use 'SecretsManagerReadWrite'.
We can give our role a descriptive name such as 'CryptoDataPipelineRole'.
We need to later attach this role to our Lambda instance.


## 2.3 Integrating AWS Secrets Manager

In the AWS management console head to the Secrets Manager service and click on 'Store a new secret'. Select 'Other type of secrets'. Choose the key as 'COINBASE_API_KEY' and paste its value there.<br>
By default, Secrets Manager encrypts our secrets using AWS KMS. Name your secret(e.g. 'prod/coinbase/api_key'). Later, we can retrieve this secret programatically using:
```python
import boto3
import json

def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=secret_name)
    secret = json.loads(response['SecretString'])
    return secret['COINBASE_API_KEY']


```
