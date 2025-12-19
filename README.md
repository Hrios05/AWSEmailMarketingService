# AWS Email Marketing System

A fully automated, event-driven email marketing solution built using a serverless architecture on AWS. This system eliminates the need for managed servers by leveraging Lambda and S3 to handle bulk email dispatches.

## Architecture Overview

The system follows a linear, event-driven flow:
1. **Amazon S3**: Stores the recipient list (`contacts.csv`) and the email template (`email.html`).
2. **Amazon EventBridge**: Acts as a scheduler (Cron) to trigger the workflow.
3. **AWS Lambda**: Processes the data and orchestrates the email sending logic.
4. **Amazon SES**: Delivers the emails to the recipients.
5. **IAM**: Manages security and permissions between services.

<img width="345" height="211" alt="image" src="https://github.com/user-attachments/assets/d039b838-ed11-4f82-b340-0faeafa36cd7" />


---

## Technical Stack

* **Compute**: AWS Lambda (Python 3.x)
* **Storage**: Amazon S3
* **Email Service**: Amazon Simple Email Service (SES)
* **Automation**: Amazon EventBridge (CloudWatch Events)
* **Security**: AWS Identity and Access Management (IAM)

---

## Component Details

### 1. Data & Templates (S3)
The S3 bucket acts as the centralized repository for campaign assets:
* `contacts.csv`: A structured list of target email addresses.
* `email.html`: The HTML/CSS code for the email body, allowing for rich-text marketing content.

### 2. Permissions & Security (IAM)
Security is managed via a dedicated IAM Execution Role assigned to the Lambda function. The role includes a custom policy to ensure the principle of least privilege:
* **S3 Permissions**: `s3:GetObject` to read the contact list and template.
* **SES Permissions**: `ses:SendEmail` and `ses:SendRawEmail` to authorize the Lambda to send mail on behalf of the verified domain.
* **Logs**: `logs:CreateLogGroup` for monitoring execution via CloudWatch.

### 3. Logic Engine (Lambda)
The `lambda.py` script performs the following operations:
1.  Connects to the S3 bucket using the `boto3` library.
2.  Parses the CSV data to extract recipient information.
3.  Reads the HTML content into a string variable.
4.  Iterates through the contact list and invokes the SES API to dispatch emails.

### 4. Scheduling (EventBridge)
To automate the marketing cycle, an EventBridge rule triggers the Lambda function. This can be configured to run daily, weekly, or on a specific date for a one-time campaign blast.

---

## File Structure

| File | Purpose |
| :--- | :--- |
| `lambda.py` | Core Python logic for processing and sending. |
| `email.html` | The responsive marketing email template. |
| `contacts.csv` | The list of recipient email addresses. |

---

## Setup & Deployment

1.  **Verify SES Identity**: Verify your sender email or domain in the AWS SES Console.
2.  **S3 Upload**: Create a bucket and upload `contacts.csv` and `email.html`.
3.  **Create IAM Role**: Create a role for Lambda with `AmazonS3ReadOnlyAccess` and a custom policy for `ses:SendEmail`.
4.  **Deploy Lambda**: Create the function, paste the `lambda.py` code, and attach the IAM role.
5.  **Schedule**: Add an EventBridge trigger to the Lambda function with your desired schedule.
