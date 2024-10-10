
## Serverless Email Marketing Application
This project demonstrates how to build a serverless email marketing application using AWS. It enables users to send scheduled emails, such as weekly newsletters, by leveraging several AWS services like Lambda, S3, SES, and EventBridge.

### Key features 
• **Serverless Architecture** : The system operates without the need for managing servers, thanks to AWS Lambda.

• **Email Automation**: Email campaigns are scheduled and triggered using Amazon EventBridge.

• **Personalized Emails**: Each email is customized using a template and recipient information from a CSV file.

• **Scalability and Cost-Efficiency**: Built using serverless components, ensuring minimal costs and scalability, especially with EventBridge's scheduler.

## Project Components

1. **Amazon S3 (Simple Storage Service)**:
    - Stores the **email template** in HTML format, which includes placeholders for personalized information.
    - Contains a **CSV file** (`contacts.csv`) with recipient details like first name and email addresses.
  
2. **Amazon SES (Simple Email Service)**:
    - Sends the emails. Requires initial setup for domain and email verification.
    - Supports both **sandbox** (for testing) and **production environments**.
    - In the sandbox, both sender and recipient email addresses need to be verified.

3. **AWS Lambda**:
    - Contains the core Python code that:
        - Reads the email template and contact list from S3.
        - Merges recipient information into the template to personalize each email.
        - Sends emails through SES using the **boto3** library.
    - Requires the necessary IAM permissions to interact with S3 and SES.

4. **Amazon EventBridge**:
    - Schedules and triggers the Lambda function at defined intervals (e.g., weekly).
    - Supports flexible scheduling options, including one-time or recurring events.

---

## AWS Services Used

- **Amazon S3** – Stores the email template and contact list.
- **Amazon SES** – Manages the actual sending of emails.
- **AWS Lambda** – Executes the serverless function to handle email personalization and dispatch.
- **Amazon EventBridge** – Triggers Lambda functions based on custom schedules.
- **AWS IAM** – Ensures security and permissions for AWS resources.

---

## Prerequisites

- **AWS Account**: Ensure you have the correct permissions for SES, Lambda, S3, and EventBridge.
- **SES Setup**:
  - A verified "From" email address (e.g., from your own domain).
  - Verified recipient email addresses (for sandbox mode).
- **Basic Knowledge** of AWS services and Python to understand the application and its components.

---

## Setup Instructions

### 1. Create an S3 Bucket

- Create an S3 bucket to store the following:
  - **Email template**: `email_template.html` (can include placeholders for recipient data).
  - **Contact list**: `contacts.csv` (contains the list of recipient email addresses and other personalized data).
- Ensure proper access controls (e.g., block public access).

### 2. Configure Amazon SES

- In **SES**, verify both the **sender email address** and recipient addresses.
- You can start in the **SES Sandbox** for testing or request production access to send emails to unverified addresses.
- Optionally, set up **DKIM** and **SPF** records for better deliverability.

### 3. Create a Lambda Function

- **Runtime**: Python 3.x.
- **Role**: Assign the function an IAM role with permissions to access S3 and SES.
- **Code**: Paste the Python code to:
  - Fetch the **CSV file** and **email template** from S3.
  - Merge the contact data with the template.
  - Use **SES** to send emails to the contacts.
  
Example Lambda Handler (Python):
```python
import boto3
import csv

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    ses = boto3.client('ses')

    # Load CSV and template from S3
    bucket_name = 'your-bucket-name'
    contacts_file = 'contacts.csv'
    template_file = 'email_template.html'

    # Fetch contacts and email template
    contacts_data = s3.get_object(Bucket=bucket_name, Key=contacts_file)['Body'].read().decode('utf-8').splitlines()
    email_template = s3.get_object(Bucket=bucket_name, Key=template_file)['Body'].read().decode('utf-8')

    contacts = csv.DictReader(contacts_data)

    # Loop through each contact and send email
    for contact in contacts:
        email_content = email_template.replace('{{name}}', contact['name'])  # Personalize email
        send_email(contact['email'], email_content)

def send_email(recipient_email, content):
    ses.send_email(
        Source='your-verified-email@example.com',
        Destination={'ToAddresses': [recipient_email]},
        Message={
            'Subject': {'Data': 'Your Email Subject'},
            'Body': {'Html': {'Data': content}}
        }
    )
```

### 4. Set up EventBridge Schedule

- In **EventBridge**, create a rule to schedule the Lambda function.
- Configure the event schedule based on your campaign requirements (e.g., send emails every Monday at 9 AM).
- Set the Lambda function as the target for the scheduled event.

---

## Enhancement Ideas

1. **Integrate DynamoDB**:
   - Replace the CSV file with **DynamoDB** to store contact details for better scalability and data management.

2. **User Interface (UI)**:
   - Build a web interface using **API Gateway** and **Lambda** to manage email campaigns and contacts.
   
3. **Analytics**:
   - Add email tracking and analytics for open rates and click-through rates.

4. **Advanced Features**:
   - Implement features like **unsubscribe management** and **bounce handling** to enhance the email marketing system.

---

## Clean-Up Instructions

To avoid unnecessary charges, ensure to delete the resources you no longer need:
- **EventBridge Schedule**: Delete the EventBridge rule triggering the Lambda function.
- **S3 Bucket**: Empty and delete the bucket.
- **Lambda Function**: Delete the Lambda function.
- **SES Identities**: Delete any verified email addresses or domains in SES.
- **IAM Role**: Optionally, delete the IAM policy and role associated with the Lambda function.

---

## Conclusion

This email marketing system uses a serverless architecture with AWS services to deliver a scalable, cost-efficient solution for email automation. It provides flexibility for future improvements, including integrating more AWS services, UI development, and tracking capabilities.


