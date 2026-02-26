# 🧾 AWS Automated Receipt Processing System

A serverless AWS pipeline that automatically extracts, stores, and notifies you about receipt data using AI-powered document analysis.

## 📐 Architecture

```
S3 (Upload Receipt)
      ↓
AWS Lambda (Orchestrator)
      ↓
Amazon Textract (Extract Data)
      ↓
Amazon DynamoDB (Store Results)
      ↓
Amazon SES (Email Notification)
```

## ✨ Features

- 📤 Upload receipts (PDF, JPG, PNG) to S3 and everything happens automatically
- 🤖 AI-powered data extraction using Amazon Textract's Expense Analysis
- 🗄️ Structured storage of receipt data in DynamoDB
- 📧 Instant email notification with extracted receipt details
- 🛡️ Robust error handling and CloudWatch logging

## 🛠️ AWS Services Used

| Service | Purpose |
|---|---|
| Amazon S3 | Receipt file storage and pipeline trigger |
| AWS Lambda | Serverless orchestration (Python 3.x) |
| Amazon Textract | AI receipt data extraction |
| Amazon DynamoDB | NoSQL receipt data storage |
| Amazon SES | Email notifications |
| Amazon CloudWatch | Logging and monitoring |

## 🚀 How It Works

1. A receipt image or PDF is uploaded to the `incoming/` folder in the S3 bucket
2. S3 triggers the Lambda function automatically
3. Lambda calls Textract's `analyze_expense` API to extract structured data
4. Extracted data (vendor, date, total, line items) is stored in DynamoDB
5. A formatted HTML email notification is sent via SES with all receipt details

## 📦 Project Structure

```
aws-receipt-processor/
│
├── lambda_function.py      # Main Lambda function code
├── README.md               # Project documentation
└── screenshots/            # Architecture and demo screenshots
    ├── s3-trigger.png
    ├── dynamodb-results.png
    └── email-notification.png
```

## ⚙️ Setup & Deployment

### Prerequisites
- AWS Account
- Python 3.x
- AWS CLI configured

### 1. S3 Bucket
- Create an S3 bucket (e.g. `your-receipts-bucket`)
- Create an `incoming/` folder inside it

### 2. DynamoDB Table
- Create a table with `receipt_id` as the partition key (String)

### 3. SES Email Verification
- Verify your sender and recipient email addresses in Amazon SES

### 4. Lambda Function
- Create a new Lambda function (Python 3.x runtime)
- Paste the code from `lambda_function.py`
- Set the following environment variables:

| Variable | Description |
|---|---|
| `DYNAMODB_TABLE` | Your DynamoDB table name |
| `SES_SENDER_EMAIL` | Verified sender email address |
| `SES_RECIPIENT_EMAIL` | Verified recipient email address |

### 5. IAM Permissions
Attach the following permissions to your Lambda execution role:
- `s3:GetObject`
- `textract:AnalyzeExpense`
- `dynamodb:PutItem`
- `ses:SendEmail`

### 6. S3 Trigger
- Add an S3 trigger to your Lambda
- Event type: `s3:ObjectCreated:*`
- Prefix: `incoming/`

## 📧 Sample Email Output

The system sends a formatted HTML email containing:
- Receipt ID
- Vendor name
- Date
- Total amount
- Full list of line items with prices and quantities
- Link to original S3 file

## 📊 DynamoDB Schema

```json
{
  "receipt_id": "uuid-string",
  "vendor": "Store Name",
  "date": "2024-01-15",
  "total": "42.99",
  "items": [
    { "name": "Item 1", "price": "10.00", "quantity": "2" }
  ],
  "s3_path": "s3://bucket/incoming/receipt.pdf",
  "processed_timestamp": "2024-01-15T10:30:00"
}
```

## 🔧 Troubleshooting

| Issue | Solution |
|---|---|
| `'s3' KeyError` | Check your Lambda test event uses the correct S3 event format |
| `SubscriptionRequiredException` | Enable Textract Expense Analysis in your AWS account |
| Email not received | Check spam folder; verify SES identities are confirmed |
| `AccessDenied` | Check Lambda IAM role has all required permissions |

## 👤 Author

**Khamid**  
[GitHub](https://github.com/yourusername) · [LinkedIn](https://linkedin.com/in/yourusername)

## 📄 License

MIT License - feel free to use this project as a reference or starting point.
