# 5. Data Lake Management

We have already covered data partionining in S3, versioning, and lifecycle policies. Now, we can move on to cataloging our data using AWS Glue to make it more easier to be discovered and queried.

## 5.1 Data Cataloging with Glue:

### 5.1.1 Creating a Glue Catalog

* Glue Crawler Setup:
  *  Setting up AWS Glue Crawlers to automatically discover and catalog the data in our S3 bucket:
      * steps:
        * We create a new crawler at AWS Glue console
        * We define the S3 path where our data is stored (e.g., s3://my-bucket-name/raw/coinbase/).
        * We run the crawler to populate the Glue Data Catalog with tables representing our data.
  * Metadata Management:
    * Glue Crawlers will generate metadata about our data, such as schemas and data types, which are stored in the Glue Data Catalog.

### 5.1.2 Querying with Amazon Athena:

* Integrating with Athena:
  * Once our data is cataloged in AWS Glue, we can use Amazon Athena to query our data directly in S3 using standard SQL.
    * Example Query:
      ```SQL
        SELECT * FROM "my_database"."coinbase_data"
        WHERE year = '2024' AND month = '08' AND day = '30'

      ```
* Optimizing Queries:
  * We optimize our queries by using partition pruning (querying specific partitions) and selecting only the necessary columns.
 

## 5.2 Data Security and Access Control
We have already implemented bucket policy that grants access based on the spacific IAM role. We also have ensured that all data stored in S3 is encrypted using SSE-S3.

## 5.3 Data Governance and Compliance

### 5.3.1 Auditing and Logging

* S3 Access logs:
  * We have enabled S3 access logging to record requests made to our S3 bucket, which can be used for auditing access patterns.
* CloudTrail:
  * We can use AWS CloudTrail to log API calls made on our AWS account, including actions taken on our S3 bucket and other AWS services.

### 5.3.2 Data Retention Policies

* Defining Retention Periods:
  * Setting retention periods for different types of data based on regulatory requirements or business needs.
* Automating Data Deletion:
  * We can use S3 Lifecycle policies to automatically delete data that exceeds the retention period

## 5.4 Data Optimization and Performance

### 5.4.1 Data Compression

* Compression Formats:
  * We can use data compression formats like Parquet or ORC, which are both storage-efficient and optimized for querying with Athena.
 
### 5.4.2 Data Aggregation

* Pre-aggregating Data:
  * For commonly used metrics, we can  pre-aggregate the data during the ETL process to reduce query time and costs.



