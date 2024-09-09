# 6. Testing and Validation

In this final section, we’ll focus on testing and validating the entire pipeline to ensure that each component works as expected. This includes verifying the Lambda function, S3 data storage, error handling, automation, and monitoring. We’ll also test various failure scenarios and ensure the data pipeline is robust and resilient to errors.

## 6.1 End-to-End Testing

The goal of end-to-end testing is to ensure the entire pipeline works seamlessly, from data ingestion to data storage in S3.

### 6.1.1 Manually Triggering the Lambda Function

We start by manually triggering the Lambda function to verify that it fetches data from the Coinbase API and stores it in S3.

* Triggering Lambda Manually using:
    * 1. AWS Console:
        * Go to the Lambda section in the AWS console.
        * Select the CoinbaseDataIngestion function.
        * Click on Test to manually invoke the function. You can create a test event using the default input provided by AWS or leave it empty.
        * Verify the output and check if the data is being stored in the correct S3 path.
    * 2. AWS CLI:
        * You can also manually invoke the Lambda function using the AWS CLI:
            ```bash
                    aws lambda invoke --function-name CoinbaseDataIngestion \
                    --payload '{}' output.txt
            ```
        * Check the output.txt file for the response, and then verify the S3 bucket for the presence of the fetched data.
