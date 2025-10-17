# Serverless-ETL-Pipeline-for-Real-time-data-processing

This project demonstrates a fully automated, event-driven, serverless ETL (Extract, Transform, Load) pipeline on AWS. The pipeline automatically processes raw, nested JSON order data the moment it's uploaded to an S3 bucket, transforms it into a structured Parquet format, and stores it in a separate S3 bucket, ready for analytics.

Problem Statement
Raw data from source systems is often generated in complex, nested JSON formats. This structure is difficult to query efficiently and is not optimized for analytical workloads. Manually processing these files is time-consuming, prone to error, and does not scale. This project aims to solve this by creating an automated pipeline that restructures this data into a query-optimized format in real-time.

Solution Architecture
The architecture is entirely serverless and event-driven. It follows a simple, robust workflow that ensures data is processed immediately upon arrival.

<!-- Add your architecture diagram screenshot here. A simple diagram showing S3 -> Lambda -> S3 would be perfect. -->




Workflow Steps:

Ingestion: A raw JSON file containing order data is uploaded to the 'raw-data' S3 bucket.

Trigger: An S3 event notification is configured on the bucket, which triggers an AWS Lambda function upon a CreateObject event.

Transformation (Lambda): The Lambda function, written in Python, is invoked with the event data.

It reads the uploaded JSON file from S3.

It uses the Pandas library to flatten the nested JSON structure into a tabular DataFrame.

It converts the DataFrame into the highly efficient Apache Parquet format.

Load: The newly created Parquet file is uploaded to the 'processed-data' S3 bucket, organized by the date and time of processing.

Key Features
Fully Automated: The entire pipeline is hands-off, requiring no manual intervention after setup.

Real-Time Processing: Data is transformed and made available for analysis in seconds, not hours.

Serverless & Cost-Effective: With AWS Lambda, compute resources are only used during execution. This eliminates idle server costs, making the solution incredibly cheap to run (typically < $1/month for a personal project).

Scalable: The architecture scales seamlessly. AWS Lambda automatically handles concurrent executions, whether one file or a thousand files are uploaded.

Optimized for Analytics: The final output is in Parquet format, which reduces storage footprint by up to 80% and significantly accelerates query performance in services like Amazon Athena.

Technologies Used
AWS S3: For scalable, durable object storage of raw and processed data.

AWS Lambda: For serverless, on-demand compute to run the transformation code.

AWS IAM: For securely managing permissions between AWS services.

Amazon CloudWatch: For logging and monitoring the Lambda function's execution.

Python 3.9: The programming language for the Lambda function.

Pandas & PyArrow: Python libraries used for data manipulation and Parquet file creation.

Boto3: The AWS SDK for Python, used to interact with S3.

Project Setup and Configuration
1. S3 Bucket Creation
Two S3 buckets are required: one for raw data and one for the processed output.






<!-- Add a screenshot of your S3 buckets in the AWS console. -->

2. IAM Role for Lambda
An IAM role with the necessary permissions must be created for the Lambda function.

AWSLambdaBasicExecutionRole: This policy allows the function to write logs to CloudWatch.

Custom S3 Policy: An inline policy was added to grant s3:GetObject permissions on the raw bucket and s3:PutObject permissions on the processed bucket.


<!-- Add a screenshot of the IAM role's permission policies. -->

3. Lambda Function Setup
The Lambda function is configured with the Python 3.9 runtime and linked to the IAM role created above. The code, along with its dependencies (Pandas, PyArrow), is packaged into a ZIP file and uploaded as a Lambda layer or directly.


<!-- Add a screenshot of your Lambda function's main configuration page. -->

4. S3 Trigger Configuration
An S3 trigger is added to the Lambda function, pointing to the raw-data bucket. It is configured to invoke the function on any s3:ObjectCreated:* event.

<!-- Add a screenshot of the S3 trigger configuration on the Lambda page. -->

Code Overview
The core logic is contained within the lambda_function.py script.



lambda_handler(event, context): This is the main entry point. It parses the S3 event to get the bucket name and object key, reads the file using boto3, and calls the transformation function.

flatten(data): This helper function takes the raw JSON data, iterates through the nested 'products' array, and uses Pandas to create a flat, structured DataFrame.

How to Test the Pipeline
Create a sample JSON file with the expected nested structure.

Upload the file to the 'raw-data' S3 bucket.

Navigate to the 'processed-data' S3 bucket. Within seconds, a new Parquet file should appear in the destination folder.
