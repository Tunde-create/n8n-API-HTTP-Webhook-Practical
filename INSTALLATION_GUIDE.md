# Installation Guide
## n8n API, HTTP Request & Webhook Practical Project

### 1. Introduction

This guide explains how to install, configure and test the two n8n workflows included in this project.

The workflows demonstrate:

- Sending HTTP GET and POST requests.
- Retrieving information from a public API.
- Sending JSON data between separate workflows.
- Receiving HTTP requests using a Webhook node.
- Extracting customer information.
- Classifying customer enquiries using conditional logic.

**No OpenAI API key or paid AI service is required.**

### 2. Prerequisites

Before beginning, ensure you have:

- Access to n8n Cloud or a running self-hosted n8n instance.
- A web browser.
- Internet access to reach JSONPlaceholder.
- The two workflow JSON files from this repository.

A GitHub account is optional for downloading the project.

### 3. Download the Workflow Files

Open the `workflows` folder in this repository.

Download the following files:

1. `API-HTTP-Webhook-Practical Demo.json`
2. `Webhook-Practical -Demo.json`

Save both files to your computer.

Alternatively, download the entire repository as a ZIP file using **Code → Download ZIP** and extract it.

### 4. Import Workflow B First

We recommend importing Workflow B first because it receives the HTTP request sent by Workflow A.

1. Log in to n8n.
2. Create a new workflow.
3. Open the workflow menu.
4. Select **Import from File**.
5. Select `Webhook-Practical -Demo.json`.
6. Confirm that the workflow contains these nodes:

   - Webhook
   - Edit Fields
   - IF
   - Edit Fields1
   - Edit Fields2

7. Save the workflow.

### 5. Configure Workflow B's Webhook

1. Open the Webhook node.
2. Confirm that the HTTP Method is `POST`.
3. Select the **Test URL**.
4. Copy the displayed webhook URL.

The URL will resemble:

`https://your-instance.app.n8n.cloud/webhook-test/your-webhook-path`

The actual URL depends on your n8n instance and the imported workflow's webhook configuration.

**Important:** Do not assume that the webhook URL from the original exported workflow will work in your own n8n account.

### 6. Import Workflow A

1. Create another new workflow in n8n.
2. Select **Import from File**.
3. Import `API-HTTP-Webhook-Practical Demo.json`.
4. Confirm that the workflow contains:

   - Manual Trigger
   - HTTP Request
   - HTTP Request1
   - HTTP Request2

5. Save the workflow.

### 7. Configure Workflow A

#### HTTP Request — GET

Confirm the following:

**Method:** GET

**URL:**

`https://jsonplaceholder.typicode.com/users/1`

This retrieves sample user information from JSONPlaceholder.

#### HTTP Request1 — POST

Confirm the following:

**Method:** POST

**URL:**

`https://jsonplaceholder.typicode.com/posts`

The node sends sample information using these body fields:

| Field | Value |
|---|---|
| title | SmartBuy Lagos Customer Enquiry |
| body | Customer wants to know the price of Samsung Galaxy A55 |
| userId | 1 |

JSONPlaceholder simulates successful resource creation but does not permanently store the submitted data.

#### HTTP Request2 — POST

Open HTTP Request2.

**Method:** POST

Replace the existing URL with the **Webhook Test URL copied from Workflow B**.

Confirm that the request body contains these fields:

| Field | Example value |
|---|---|
| customerName | Chinedu Okafor |
| enquiry | What is the price of Samsung Galaxy A55? |
| source | SmartBuy Lagos |

Save your changes.

### 8. Test Communication Between Both Workflows

Follow this sequence carefully:

**Step 1 — Start the Webhook Listener**

1. Open Workflow B.
2. Open its Webhook node.
3. Click **Listen for test event**.

Workflow B is now waiting for an incoming test request.

**Step 2 — Send the HTTP Request**

1. Return to Workflow A.
2. Execute the workflow.
3. Check the HTTP Request2 node for a successful execution.

**Step 3 — Verify Workflow B**

Return to Workflow B and confirm that the Webhook node received the customer information.

Check that the Edit Fields node extracted:

- customerName
- enquiry
- source

### 9. Test the TRUE Branch

Use this sample enquiry in Workflow A:

`What is the price of Samsung Galaxy A55?`

Start a new Webhook test listener in Workflow B, then execute Workflow A.

**Expected result:**

The IF node routes the enquiry through TRUE.

Edit Fields1 assigns:

`intent = Price Enquiry`

The output should retain the original customer information.

### 10. Test the FALSE Branch

Change the enquiry to:

`Do you deliver products to Lekki?`

Start a new Webhook test listener and execute Workflow A again.

**Expected result:**

The IF node routes the enquiry through FALSE.

Edit Fields2 assigns:

`intent = Other Enquiry`

### 11. Understanding the Classification Logic

The IF node evaluates the following condition:

**Input:**

`{{ $json.enquiry }}`

**Operator:** contains

**Value:** `price`

**Case sensitivity:** Ignored

If the enquiry contains `price`, it is classified as Price Enquiry.

Otherwise, it is classified as Other Enquiry.

This is a simple rule-based classifier, not an AI classifier.

### 12. Troubleshooting

#### Error: The Requested Webhook Is Not Registered

**Possible cause:**

Workflow B is not listening for a test event.

**Solution:**

1. Return to Workflow B.
2. Click **Listen for test event**.
3. Execute Workflow A again.

Test webhooks are temporary and must be registered during testing.

#### HTTP Request2 Fails

Check that:

- The Webhook Test URL is correct.
- Workflow B is listening.
- The HTTP method is POST.
- The n8n instance is accessible.

#### Customer Fields Are Missing

Check the expressions in Workflow B's Edit Fields node:

`{{ $json.body.customerName }}`

`{{ $json.body.enquiry }}`

`{{ $json.body.source }}`

Also confirm that Workflow A sends the corresponding field names.

#### Incorrect Classification

Verify that the IF node:

- Checks the `enquiry` field.
- Uses the `contains` operator.
- Compares against `price`.
- Has case-insensitive matching enabled.

#### HTTP Request1 Does Not Create a Permanent Post

This is expected. JSONPlaceholder is a testing API that simulates POST operations without permanently saving the submitted records.

### 13. Using a Production Webhook

The project uses a Test URL for learning and development.

For continuous operation:

1. Open Workflow B.
2. Review its Webhook node and select the Production URL.
3. Publish or activate Workflow B, as required by your n8n version.
4. Update HTTP Request2 in Workflow A to use that Production URL.
5. Save the updated workflow.
6. Test the connection.

For real-world deployment, implement suitable authentication, request validation, access controls and error handling before accepting external requests.

### 14. Expected Results

After completing the installation and testing, you should have:

- A functioning HTTP GET request.
- A functioning HTTP POST request.
- Successful communication between two n8n workflows.
- Successful JSON data extraction.
- Working TRUE and FALSE conditional branches.
- Correct customer enquiry classification.

### 15. Conclusion

This practical project demonstrates the fundamental principles of API communication, HTTP Requests, Webhooks and conditional workflow automation.

The same concepts can be extended to customer support systems, e-commerce platforms, notifications, business applications and other automation solutions.

---

**Project:** n8n API, HTTP Request & Webhook Practical

**Platform:** n8n

**Documentation:** GitHub

**Project status:** Successfully tested in the original learning environment.
