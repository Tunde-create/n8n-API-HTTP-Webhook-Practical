# n8n API, HTTP Request & Webhook Practical Project

## Project Overview

This project demonstrates how to use **APIs, HTTP Requests, Webhooks, JSON data and conditional logic** to exchange information between systems using n8n.

The project was developed as a hands-on AI automation learning exercise, using two interconnected n8n workflows.

It also demonstrates a simple rule-based customer enquiry classification system using SmartBuy Lagos as a sample business.

## Learning Objectives

- Understand the difference between APIs, HTTP Requests and Webhooks.
- Send GET and POST requests using n8n.
- Retrieve and transmit JSON data.
- Connect two separate n8n workflows through a webhook.
- Extract information from incoming webhook requests.
- Use conditional logic to classify customer enquiries.
- Test, troubleshoot and document automation workflows.

## Technologies Used

| Technology | Purpose |
|---|---|
| n8n Cloud | Workflow automation |
| HTTP Request Node | Send GET and POST requests |
| Webhook Node | Receive HTTP requests |
| JSONPlaceholder | Public API testing service |
| Edit Fields Node | Extract and structure JSON data |
| IF Node | Conditional routing |
| GitHub | Version control and documentation |

## Project Architecture

The project consists of two workflows.

### Workflow A — API and HTTP Request Demonstration

```text
Manual Trigger
      |
      v
HTTP Request (GET)
      |
      v
HTTP Request1 (POST)
      |
      v
HTTP Request2 (POST)
      |
      v
Workflow B Webhook
```

**Node 1: Manual Trigger**

Starts the workflow manually during testing.

**Node 2: HTTP Request — GET**

Endpoint:

`https://jsonplaceholder.typicode.com/users/1`

Retrieves sample user information from the JSONPlaceholder API.

**Node 3: HTTP Request1 — POST**

Endpoint:

`https://jsonplaceholder.typicode.com/posts`

Sends sample JSON data containing:

- `title`: SmartBuy Lagos Customer Enquiry
- `body`: Customer wants to know the price of Samsung Galaxy A55
- `userId`: 1

JSONPlaceholder returns a simulated resource-creation response. This does not permanently create a database record.

**Node 4: HTTP Request2 — POST**

Sends customer enquiry information to the Webhook Test URL in Workflow B.

Example request body:

```json
{
  "customerName": "Chinedu Okafor",
  "enquiry": "What is the price of Samsung Galaxy A55?",
  "source": "SmartBuy Lagos"
}
```

### Workflow B — Webhook and Customer Enquiry Classification

```text
Webhook (POST)
      |
      v
Edit Fields
      |
      v
IF Node
   /      \
 TRUE     FALSE
  |         |
  v         v
Edit       Edit
Fields1    Fields2
  |         |
  v         v
Price      Other
Enquiry    Enquiry
```

**Node 1: Webhook**

Receives incoming HTTP POST requests from Workflow A.

The workflow uses a Webhook Test URL during development.

**Node 2: Edit Fields**

Extracts the following fields from the incoming request body:

- `customerName`
- `enquiry`
- `source`

Example n8n expression:

`{{ $json.body.customerName }}`

**Node 3: IF**

Evaluates the customer enquiry using the condition:

`{{ $json.enquiry }}` contains `price`

Case-insensitive matching is enabled.

**TRUE Branch — Edit Fields1**

If the enquiry contains `price`, the workflow assigns:

`intent = Price Enquiry`

**FALSE Branch — Edit Fields2**

If the enquiry does not contain `price`, the workflow assigns:

`intent = Other Enquiry`

Both branches preserve the original customer information.

## Practical Test Results

### Test 1 — HTTP GET Request

**Objective:** Retrieve sample user data from a public API.

**Result:** Successful.

The HTTP Request node retrieved structured JSON data from JSONPlaceholder.

### Test 2 — HTTP POST Request

**Objective:** Send sample information to a public API.

**Result:** Successful.

The API returned a simulated successful creation response, including an ID.

### Test 3 — Webhook Communication

**Objective:** Send customer enquiry data from Workflow A to Workflow B.

**Result:** Successful.

Workflow B received the HTTP POST request through its Webhook node.

### Test 4 — TRUE Branch Classification

**Sample enquiry:**

`What is the price of Samsung Galaxy A55?`

**Expected intent:** Price Enquiry

**Actual intent:** Price Enquiry

**Result:** PASSED

### Test 5 — FALSE Branch Classification

**Sample enquiry:**

`Do you deliver products to Lekki?`

**Expected intent:** Other Enquiry

**Actual intent:** Other Enquiry

**Result:** PASSED

Both classification branches were successfully executed and their JSON outputs verified.

## Challenges Encountered and Solutions

### Challenge 1 — Webhook Not Registered

**Error encountered:**

`The requested webhook is not registered`

**Cause:**

The n8n Webhook Test URL was not actively listening when Workflow A sent its HTTP request.

**Solution:**

1. Open Workflow B.
2. Open the Webhook node.
3. Click **Listen for test event**.
4. Return to Workflow A.
5. Execute the HTTP Request2 node again.

**Lesson learned:**

n8n Test Webhooks are temporary and must be listening during testing. Production Webhook URLs are intended for published, active workflows.

### Challenge 2 — Understanding HTTP Request and Webhook Roles

Initially, it was important to distinguish between the node that sends information and the node that receives it.

**Solution:**

Two separate workflows were built to demonstrate communication.

- HTTP Request2 sends the information.
- Webhook receives the information.
- Edit Fields extracts the information.
- IF evaluates the enquiry.
- The final Edit Fields node assigns the category.

**Lesson learned:**

HTTP Requests and Webhooks can work together to enable communication between independent systems.

### Challenge 3 — Testing Both Conditional Branches

A single successful workflow execution does not prove that both IF branches work correctly.

**Solution:**

Separate price and delivery enquiries were submitted, and each branch's output was examined.

**Lesson learned:**

Every important conditional path should be tested independently.

## Key Concepts Learned

### API

An Application Programming Interface defines how software systems can communicate and interact.

### HTTP Request

An HTTP Request is a message sent by a client to a server, often using methods such as GET or POST.

### Webhook

A Webhook is an HTTP endpoint commonly used to receive event-driven notifications from another system.

### JSON

JavaScript Object Notation is a structured data format commonly used for exchanging information between software systems.

### Rule-Based Classification vs AI Classification

This project uses rule-based classification.

The IF node checks whether an enquiry contains the word `price`.

An AI-based classifier could potentially recognize broader customer intentions without relying on one exact keyword.

## Repository Structure

```text
n8n-API-HTTP-Webhook-Practical/
|
|-- README.md
|
|-- workflows/
    |-- README.md
    |-- API-HTTP-Webhook-Practical Demo.json
    |-- Webhook-Practical -Demo.json
```

## Installation and Testing

1. Install or access a running n8n instance.
2. Download both workflow JSON files from the `workflows` folder.
3. Import both workflows into n8n.
4. Open Workflow B and copy its Webhook Test URL.
5. Update the URL in Workflow A's HTTP Request2 node.
6. In Workflow B, click **Listen for test event**.
7. Execute Workflow A.
8. Confirm that Workflow B receives and classifies the enquiry.
9. Change the enquiry text and repeat the test to verify both IF branches.

**Important:** Imported workflows may have different webhook URLs. Always update the sender's URL to match the receiving workflow. Never publish API keys, authentication tokens or other sensitive credentials in the repository.

## Project Outcome

The project successfully demonstrates:

- API data retrieval using GET.
- Sample data submission using POST.
- HTTP communication between separate n8n workflows.
- Webhook-based data reception.
- JSON data extraction.
- Rule-based customer enquiry classification.
- Independent TRUE and FALSE branch testing.
- Practical debugging and workflow documentation.

## Future Improvements

Potential extensions include:

- Additional customer enquiry categories.
- AI-powered intent classification.
- Google Sheets logging.
- Automated customer notifications.
- Error-handling and retry logic.
- Production webhook deployment with appropriate security.

## Author's Note

This project forms part of my practical learning journey in AI automation, including concepts explored during the DSA 2.0 training programme powered by Incubator Hub.

The objective is to strengthen my understanding of automation systems through practical implementation, testing, troubleshooting and documentation.

---

**Project Status:** Completed and tested successfully for the documented practical scenarios.
