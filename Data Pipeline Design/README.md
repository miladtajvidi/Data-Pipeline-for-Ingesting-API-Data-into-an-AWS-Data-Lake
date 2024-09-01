# 3. Data Pipeline Design

For this section, we want to focus on planning and structuring how data will flow from the source (Coinbase API) to our data lake (S3), with considerations for security, scalability, and reliability. Below is a detailed breakdown of how this segment can be approached:

## 3.1 Define the Data Flow

* Source: The Coinbase API is the source of our data. We need to define the specific endpoints we’ll be using, how frequently we’ll fetch data, and any parameters required.
  * API Endpoint: e.g., https://api.coinbase.com/v2/prices/spot?currency=USD
  * Data Frequency: e.g., every 5 minutes, hourly, etc.
  * Data Type: e.g., JSON
  
* Destination: Data will be stored in an AWS S3 bucket, structured by date and time to ensure easy retrieval and organization.
  * S3 Bucket Structure: We need to define how we will organize the data in our S3 bucket.
    * Example structure:
      ```
      s3://my-bucket-name/
      ├── raw/
      │   └── coinbase/
      │       ├── year/
      │       │   └── month/
      │       │       └── day/
      │       │           └── hour/
      │       │               └── data.json
      └── processed/
      └── coinbase/
      ```
## 3.2 Design the Data Ingestion Layer

The Lambda function will serve as the primary mechanism for fetching data from the API and storing it in S3.
* Input: API credentials, endpoint, request headers.
* Processing: Data fetched from the API is transformed as needed (e.g., filtering out unnecessary fields) and prepared for storage.
* Output: Raw data is stored in the S3 bucket with the appropriate structure.
* Rate Limiting: We need to implement logic to respect API rate limits and avoid throttling. (Including exponential backoff for retries)
* Error Handling: We define how errors will be handled. For instance, we can have retries on transient errors, log permanent errors, and notify via SNS or CloudWatch

## 3.3 Data Storage strategy

* Raw Data Storage: All data fetched from the API should be stored in its raw form in S3. This ensures that we always have the original data, which can be reprocessed if necessary.
* Processed Data Storage: If transformation or aggregation is needed, we need to define how and where the processed data will be stored in S3.

## 3.4 Security and Compliance (already implemented)

* Encryption: We need to ensure that all data stored in S3 is encrypted using server-side encryption (SSE-S3 or SSE-KMS).
* Access Control: We need to define IAM roles and policies to restrict access to the S3 bucket, Lambda function, and Secrets Manager. Only necessary permissions should be granted to reduce security risks.
* API Key Management: We used AWS Secrets Manager to securely store and retrieve API keys. We need to ensure that the Lambda function has the necessary permissions to access the secrets.


## 3.5 Data Validation and Quality Check

We need to ensure that the data being ingested is complete, accurate, and meets the expected format. This is crucial for maintaining the integrity of our data pipeline.

### 3.5.1 Validation during Ingestion
* Schema Validation: We need to ensure that the data received from the API matches the expected schema. This can include checking for required fields, data types, and value ranges.
  * Within the Lambda function, before storing data in S3, we validate the structure of the JSON response. If the data does not meet the criteria, we log an error and (optionally) notify through CloudWatch or SNS.
* Data Completeness Checks: We'll verify that the data set is complete. For example, if we expect data at specific intervals, we have to ensure that no intervals are missing.
  * We can track timestamps of data ingestion and compare them to expected intervals. If a time period is missing, we can (opteionally) trigger an alert.
* Error Logging and Notification: If data fails validation, it’s important to log the error and notify the relevant personnel.
  * We can use CloudWatch Logs to record validation errors and set up a CloudWatch Alarm to trigger an SNS notification if validation errors exceed a threshold.
 
### 3.5.2 Monitoring Data Quality
* Data Consistency Checks: Over time, we should check for inconsistencies in the data (e.g., sudden spikes or drops that could indicate issues with the API or ingestion process).
  * We can periodically run batch jobs (using AWS Batch or scheduled Lambda functions) that analyze historical data stored in S3 to detect anomalies.
* Historical Data Auditing: We can implement mechanisms to periodically audit the data stored in S3 to ensure that no data has been lost or corrupted over time.
  * We can use AWS Glue to catalog and audit data, creating reports that can be reviewed to ensure data quality.

## 3.6 Scalability and Reliability

### 3.6.1 Scalability
* Lambda Scalability: AWS Lambda automatically scales based on the number of incoming requests, but it’s important to design our function to handle scaling gracefully. We'll Optimize the Lambda function’s code for performance, manage API rate limits, and ensure that the function handles large volumes of data without timing out or exceeding memory limits. We can consider using SQS (Simple Queue Service) as a buffer between the API and Lambda if necessary.
* Data Partitioning in S3: We have used time-based folder structures as described in the earlier steps. This approach also helps when running queries against the data using services like AWS Athena.

### 3.6.2 Reliability

* Error Handling and Retries: We'll implement retry logic in the Lambda function to handle transient errors from the API. AWS Lambda provides a built-in retry mechanism for certain errors.
* Monitoring and Alerts: We can use CloudWatch to monitor key metrics such as Lambda invocation errors, duration, and throttling. Create CloudWatch Alarms that trigger SNS notifications if thresholds are breached.
* High Availability: We won't implement this feature since this isn't a live production code. In real-life scenarios, however, we need to ensure the pipeline is resilient to regional outages or other AWS service disruptions. We can consider setting up cross-region replication for our S3 buckets.












