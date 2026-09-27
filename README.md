<p align="center">
  <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExMnJsbWtrbGY0NzExeTc0MmoxandodnBtY3h1OGJwOWwzcXR0ZDJkcCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/R59Hhh3cnfuffSSAxP/giphy.gif" width="100%" alt="AI Customer Feedback Pipeline Header">
</p>

# 🤖 AI Customer Feedback Pipeline — n8n

**An AI-powered customer feedback automation pipeline built with n8n for retrieving customer feedback, generating professional responses, classifying feedback using structured AI output, handling AI failures with retries, and submitting the processed result to the Academy API.**

---

# 📌 Project Overview

## Project Name

**AI Customer Feedback Pipeline**

### Workflow Name

```text
Section 4 - Feedback Pipeline
```

### Platform

**n8n**

### Course

**n8n Foundations — Section 4**

### AI Provider

**Groq**

### AI Models

```text
Classification → openai/gpt-oss-20b
Generation     → openai/gpt-oss-120b
```

### Estimated Time

```text
60 minutes
```

### Tag

```text
n8n103
```

---

# 🎯 Project Objective

The objective of this project is to build an AI-powered customer feedback automation pipeline using n8n.

The workflow retrieves customer feedback from the n8n Academy API, uses an LLM to classify the feedback into structured fields, generates a professional customer response, enhances the response using the classification result, and submits the final result back to the Academy API.

The pipeline demonstrates three important AI automation patterns:

1. **Text generation**
2. **Structured AI classification**
3. **Resilient AI workflow execution**

The complete process is:

```text
Fetch Feedback
      ↓
Select Feedback Item
      ↓
Classify Feedback
      ↓
Validate Structured Output
      ↓
Prepare Classification
      ↓
Generate Response
      ↓
Submit Result
```

The project demonstrates how n8n can combine traditional workflow automation with LLMs, structured output validation, expressions, API integration, and error handling.

---

# 🏗️ Architecture

The workflow starts by retrieving customer feedback from the Academy API.

The feedback is then passed through an AI classification chain. A Structured Output Parser ensures that the classification follows the required JSON schema.

The validated classification is then used to improve the generated customer response.

```text
                         ┌─────────────────────┐
                         │    TriggerManual    │
                         │    Manual Trigger   │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │     GetFeedback     │
                         │    HTTP Request     │
                         │    Feedback API     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │  SetFeedbackItem    │
                         │       Limit        │
                         │     Max Items = 1  │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │  ClassifyFeedback   │
                         │   Basic LLM Chain   │
                         │     gpt-oss-20b     │
                         └──────────┬──────────┘
                                    │
                                    │ Output Parser
                                    ▼
                         ┌─────────────────────┐
                         │    OutputParser     │
                         │ Structured Output   │
                         │       Parser        │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │SetClassificationResult│
                         │    Edit Fields      │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    GenerateReply    │
                         │   Basic LLM Chain   │
                         │    gpt-oss-120b     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ SendGeneratedReply  │
                         │     HTTP POST       │
                         │    Academy API      │
                         └─────────────────────┘
```

---

# 🔄 Business Flow

The customer feedback passes through two AI stages:

```text
                     Customer Feedback
                            │
                            ▼
                    ClassifyFeedback
                            │
                            ▼
                    Structured Output
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
         Sentiment        Topic         Urgency
             │              │              │
             └──────────────┼──────────────┘
                            │
                            ▼
                       Key Issue
                            │
                            ▼
                  SetClassificationResult
                            │
                            ▼
                      GenerateReply
                            │
                            ▼
                 Classification-Aware
                       Response
                            │
                            ▼
                 SendGeneratedReply
```

The classification determines how the generated response should be written.

```text
Positive
   ↓
Appreciative + warm

Negative / High Urgency
   ↓
Empathetic + apologetic

Billing / Support
   ↓
Offer specific next steps
```

---

# 🏷️ Node Naming Convention

Every node follows a clear and descriptive naming convention.

| Node Type                | Node Name                 |
| ------------------------ | ------------------------- |
| Manual Trigger           | `TriggerManual`           |
| HTTP Request             | `GetFeedback`             |
| Limit                    | `SetFeedbackItem`         |
| Basic LLM Chain          | `ClassifyFeedback`        |
| Groq Chat Model          | `gpt-oss-20b`             |
| Structured Output Parser | `OutputParser`            |
| Edit Fields              | `SetClassificationResult` |
| Basic LLM Chain          | `GenerateReply`           |
| Groq Chat Model          | `gpt-oss-120b`            |
| HTTP Request             | `SendGeneratedReply`      |

Clear node names make the AI workflow easier to understand, debug, maintain, and hand over to another developer.

---

# 🔐 Authentication

The Academy API requires authentication.

The workflow uses an n8n **Header Auth credential** for the Academy API key.

## Credential

```text
Credential Type:
Header Auth
```

```text
Header Name:
X-API-Key
```

The workflow also sends the assessment identifier using a separate HTTP header.

## Assessment Header

```text
Name:
X-Assessment-ID
```

```text
Value:
[Your Assessment ID]
```

The authentication configuration is applied to the Academy HTTP Request nodes:

```text
GetFeedback
SendGeneratedReply
```

> Do not commit the Academy API key, assessment ID, or Groq API key to source control.

---

# 🤖 AI Provider Configuration

## Groq Credential

Create a Groq credential in n8n.

```text
Credential Type:
Groq
```

```text
Credential Name:
Groq API
```

The Groq API key should be stored securely in n8n Credentials.

The workflow uses two models for different tasks.

```text
                    AI Processing
                         │
             ┌───────────┴───────────┐
             │                       │
             ▼                       ▼
       Classification           Generation
             │                       │
             ▼                       ▼
     gpt-oss-20b              gpt-oss-120b
```

Using separate models allows the workflow to use a smaller model for classification and a larger model for more nuanced response generation.

---

# 📥 Step 1 — Fetch Customer Feedback

## Learning Objectives

This step demonstrates:

* HTTP Request configuration
* Header authentication
* API integration
* n8n expressions
* Retrieving AI input data

---

## 1.1 Create the Workflow

Create a new n8n workflow named:

```text
Section 4 - Feedback Pipeline
```

Add the tag:

```text
n8n103
```

Add a **Manual Trigger** node.

Rename it:

```text
TriggerManual
```

---

# 1.2 Configure GetFeedback

Add an HTTP Request node.

Rename it:

```text
GetFeedback
```

### Configuration

| Property        | Value                                                        |
| --------------- | ------------------------------------------------------------ |
| Method          | `GET`                                                        |
| URL             | `https://learn.app.n8n.cloud/webhook/course/n8n103/feedback` |
| Authentication  | Generic Credential Type                                      |
| Credential Type | Header Auth                                                  |
| Credential      | n8n Academy API Key                                          |

Enable:

```text
Send Headers
```

Add:

```text
X-Assessment-ID: [Your Assessment ID]
```

Execute the node and inspect the returned customer feedback.

A feedback item contains fields such as:

```text
feedback_id
customer_name
customer_id
channel
message
received_at
```

Example:

```json
{
  "feedback_id": "FB-001",
  "customer_name": "Acme Corp",
  "customer_id": "CUST-042",
  "channel": "email",
  "message": "We've been using your Enterprise License for three months now and the team loves it.",
  "received_at": "2026-01-18T14:32:00Z"
}
```

---

# 📋 Step 2 — Select Feedback Item

For the initial assessment workflow, process one feedback item at a time.

Add a **Limit** node after `GetFeedback`.

Rename it:

```text
SetFeedbackItem
```

Configure:

```text
Max Items:
1
```

This keeps the workflow consistent and reduces unnecessary AI calls during testing.

The workflow becomes:

```text
TriggerManual
      ↓
GetFeedback
      ↓
SetFeedbackItem
```

---

# 🧠 Step 3 — Classify Customer Feedback

Add a **Basic LLM Chain** node after `SetFeedbackItem`.

Rename it:

```text
ClassifyFeedback
```

Connect a **Groq Chat Model**.

Configure:

```text
Model:
openai/gpt-oss-20b
```

The classification model is intentionally smaller because classification is a relatively structured task.

---

# 3.1 Classification User Message

Set the Prompt source to:

```text
Define Below
```

Enable expression mode.

Use:

```text
Customer: {{ $json.customer_name }}
Feedback: {{ $json.message }}
```

---

# 3.2 Classification System Message

Use:

```text
You are a customer feedback analyst.

Analyze the feedback and classify it by:
1. Sentiment: Is the customer positive, neutral, or negative?
2. Topic: Is this about billing, product, support, or general?
3. Urgency: Based on the tone and content, is this low, medium, or high urgency?
4. Key Issue: Summarize the main point in one sentence

Output Requirements

sentiment: Must be exactly one of: positive, neutral, or negative
topic: Must be exactly one of: billing, product, support, or general
urgency: Must be exactly one of: low, medium, or high
key_issue: A one-sentence summary of the main issue or praise
```

---

# 📊 Step 4 — Structured Output Parser

Free-form AI responses are difficult to use reliably in downstream automation.

For example, the AI might return:

```text
The customer appears positive. They are happy with the product and
the urgency is low.
```

A downstream workflow cannot reliably depend on this format.

The **Structured Output Parser** solves this by enforcing a defined JSON structure.

Open:

```text
ClassifyFeedback
```

Enable:

```text
Require Specific Output Format
```

Add a Structured Output Parser.

Rename it:

```text
OutputParser
```

---

# 4.1 Classification Schema

Use **Generate From JSON Example**.

```json
{
  "sentiment": "positive",
  "topic": "product",
  "urgency": "low",
  "key_issue": "Customer is satisfied with the product"
}
```

The required values are:

```text
sentiment:
positive | neutral | negative
```

```text
topic:
billing | product | support | general
```

```text
urgency:
low | medium | high
```

```text
key_issue:
One-sentence summary
```

The parser validates that the AI output conforms to the required structure.

---

# 🔁 Step 5 — Configure Retry Handling

AI output can occasionally be malformed.

For example:

```json
{
  "sentiment": "very positive"
}
```

does not satisfy the required schema.

Configure `ClassifyFeedback`:

```text
Settings
   ↓
On Error
   ↓
Retry On Fail
```

Use:

```text
Max Tries:
3
```

```text
Wait Between Tries:
1000 ms
```

The workflow becomes:

```text
AI Classification
       ↓
Validation
       │
       ├── Valid ────────→ Continue
       │
       └── Invalid
              ↓
           Retry
              ↓
           Attempt 2
              ↓
           Attempt 3
```

This makes the AI workflow more resilient.

---

# 📦 Step 6 — Prepare Classification Result

Add an **Edit Fields** node after `ClassifyFeedback`.

Rename it:

```text
SetClassificationResult
```

Configure the following fields.

### Feedback ID

```text
Name:
feedback_id
```

```text
Value:
{{ $('SetFeedbackItem').item.json.feedback_id }}
```

### Customer Name

```text
Name:
customer_name
```

```text
Value:
{{ $('SetFeedbackItem').item.json.customer_name }}
```

### Message

```text
Name:
message
```

```text
Value:
{{ $('SetFeedbackItem').item.json.message }}
```

### Sentiment

```text
Name:
sentiment
```

```text
Value:
{{ $('ClassifyFeedback').item.json.output.sentiment }}
```

### Topic

```text
Name:
topic
```

```text
Value:
{{ $('ClassifyFeedback').item.json.output.topic }}
```

### Urgency

```text
Name:
urgency
```

```text
Value:
{{ $('ClassifyFeedback').item.json.output.urgency }}
```

### Key Issue

```text
Name:
key_issue
```

```text
Value:
{{ $('ClassifyFeedback').item.json.output.key_issue }}
```

Disable:

```text
Include Other Input Fields
```

The resulting structure is:

```json
{
  "feedback_id": "FB-010",
  "customer_name": "Nexus Industries",
  "message": "...",
  "sentiment": "positive",
  "topic": "product",
  "urgency": "low",
  "key_issue": "Customer is highly satisfied with the product."
}
```

---

# ✍️ Step 7 — Generate Customer Response

Add a **Basic LLM Chain**.

Rename it:

```text
GenerateReply
```

Connect a Groq Chat Model.

Configure:

```text
Model:
openai/gpt-oss-120b
```

---

# 7.1 Reply User Message

Use:

```text
Customer: {{ $json.customer_name }}
Feedback: {{ $json.message }}

Classification:
- Sentiment: {{ $json.sentiment }}
- Topic: {{ $json.topic }}
- Urgency: {{ $json.urgency }}
- Key Issue: {{ $json.key_issue }}
```

---

# 7.2 Reply System Message

Use:

```text
You are a helpful customer service assistant.

Read the following customer feedback and write a brief, professional response acknowledging their message.

Use the classification to guide your tone:
- For negative sentiment or high urgency: Be empathetic and apologetic
- For positive sentiment: Be appreciative and warm
- For billing or support topics: Offer specific next steps

Write a 2-3 sentence response.
```

The workflow now becomes:

```text
SetClassificationResult
          ↓
     GenerateReply
          ↓
    Generated Text
```

Example output:

```text
Thank you so much for sharing your experience, Nexus Industries!
We’re thrilled to hear that our platform has helped you automate over
50 processes and deliver such impressive ROI. If there’s anything else
we can do to support your continued success, please don’t hesitate to
let us know.
```

---

# 📤 Step 8 — Submit Processed Feedback

Add an HTTP Request node after `GenerateReply`.

Rename it:

```text
SendGeneratedReply
```

### Configuration

```text
Method:
POST
```

```text
URL:
https://learn.app.n8n.cloud/webhook/course/n8n103/reply
```

Use:

```text
Authentication:
Generic Credential Type
```

```text
Credential Type:
Header Auth
```

Add:

```text
X-Assessment-ID:
[Your Assessment ID]
```

---

# 8.1 Request Body

Set:

```text
Body Content Type:
JSON
```

Use these fields.

### Feedback ID

```text
Name:
feedback_id

Value:
{{ $('SetClassificationResult').item.json.feedback_id }}
```

### Sentiment

```text
Name:
sentiment

Value:
{{ $('SetClassificationResult').item.json.sentiment }}
```

### Topic

```text
Name:
topic

Value:
{{ $('SetClassificationResult').item.json.topic }}
```

### Urgency

```text
Name:
urgency

Value:
{{ $('SetClassificationResult').item.json.urgency }}
```

### Key Issue

```text
Name:
key_issue

Value:
{{ $('SetClassificationResult').item.json.key_issue }}
```

### Generated Reply

```text
Name:
generated_reply

Value:
{{ $json.text }}
```

### Models Used

```text
Name:
model_used

Value:
openai/gpt-oss-20b,openai/gpt-oss-120b
```

The final request should evaluate to:

```json
{
  "feedback_id": "FB-010",
  "sentiment": "positive",
  "topic": "product",
  "urgency": "low",
  "key_issue": "Customer is highly satisfied with the product.",
  "generated_reply": "Thank you so much for sharing your experience...",
  "model_used": "openai/gpt-oss-20b,openai/gpt-oss-120b"
}
```

---

# 🧪 Step 9 — Test Different Feedback

For testing purposes, temporarily change:

```text
SetFeedbackItem
```

from:

```text
Max Items:
1
```

to:

```text
Max Items:
5
```

Execute the classification workflow.

This allows different customer feedback items to be tested.

Examples include:

```text
Positive + Product + Low
```

```text
Negative + Billing + High
```

```text
Neutral + Support + Medium
```

After testing, return:

```text
Max Items:
1
```

for the assessment workflow.

---

# 🔍 AI Output Examples

## Positive Feedback

```json
{
  "sentiment": "positive",
  "topic": "product",
  "urgency": "low",
  "key_issue": "Customer is highly satisfied with the product."
}
```

Expected response style:

```text
Warm
Appreciative
Professional
```

---

## Negative Billing Feedback

```json
{
  "sentiment": "negative",
  "topic": "billing",
  "urgency": "high",
  "key_issue": "Customer was charged twice and has not received a resolution."
}
```

Expected response style:

```text
Empathetic
Apologetic
Action-oriented
```

---

# 🔄 Complete Workflow

The complete workflow is:

```text
┌─────────────────────┐
│    TriggerManual    │
│    Manual Trigger   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│     GetFeedback     │
│    HTTP Request     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  SetFeedbackItem    │
│    Limit = 1        │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  ClassifyFeedback   │
│    gpt-oss-20b      │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│    OutputParser     │
│ Structured Output   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│SetClassificationResult│
│   Extract Fields    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│    GenerateReply    │
│    gpt-oss-120b     │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ SendGeneratedReply  │
│      HTTP POST      │
└─────────────────────┘
```

---

# 📝 Workflow Documentation

Add a Sticky Note to the workflow.

Suggested documentation:

```text
# AI Customer Feedback Pipeline

Owned By: Anubhav Kumar Srivastava
Last Updated: 27-Sept-2026

Fetches customer feedback from the Academy API, classifies it using
structured AI output, and generates a tailored customer response.

## Flow

1. GetFeedback: Fetches customer feedback from Academy endpoint
2. SetFeedbackItem: Limits processing to the first feedback item
3. ClassifyFeedback: Classifies sentiment, topic, urgency and key issue
4. OutputParser: Validates the structured classification output
5. SetClassificationResult: Extracts classification fields
6. GenerateReply: Generates a classification-aware customer response
7. SendGeneratedReply: Posts the final result to the Academy API

## Models

- Groq: openai/gpt-oss-20b for classification
- Groq: openai/gpt-oss-120b for response generation

## Error Handling

- Structured Output Parser validates AI output
- Retry On Fail enabled for classification
- Maximum 3 attempts
- 1000 ms wait between retries

## Dependencies

- n8n Academy API Key credential
- X-Assessment-ID
- Groq API credential

Part of: n8n Foundations Program Course N8N103
```

---

# 🧪 Testing Checklist

## Data Retrieval

```text
☐ GetFeedback executes successfully
☐ Academy authentication works
☐ X-Assessment-ID is configured
☐ Feedback data is returned
☐ feedback_id is available
☐ customer_name is available
☐ message is available
```

## Classification

```text
☐ ClassifyFeedback executes successfully
☐ Groq credential is configured
☐ gpt-oss-20b is selected
☐ OutputParser is connected
☐ sentiment is returned
☐ topic is returned
☐ urgency is returned
☐ key_issue is returned
```

## Structured Output

```text
☐ Output is valid JSON
☐ sentiment uses an allowed value
☐ topic uses an allowed value
☐ urgency uses an allowed value
☐ key_issue is present
```

## Retry Handling

```text
☐ Retry On Fail is enabled
☐ Maximum retries = 3
☐ Wait time = 1000 ms
☐ Invalid AI output can be retried
```

## Response Generation

```text
☐ GenerateReply executes successfully
☐ gpt-oss-120b is selected
☐ Classification is passed to the prompt
☐ Generated response is 2-3 sentences
☐ Response tone reflects sentiment and urgency
```

## Academy Submission

```text
☐ SendGeneratedReply executes successfully
☐ feedback_id is correct
☐ sentiment is correct
☐ topic is correct
☐ urgency is correct
☐ key_issue is correct
☐ generated_reply is populated
☐ model_used is populated
☐ Confirmation code is received
```

---

# 🛠️ Troubleshooting

## Authentication Failed

Check:

```text
☐ Header Auth credential exists
☐ Header name is X-API-Key
☐ Academy API key is correct
☐ X-Assessment-ID is configured
☐ Assessment ID is correct
☐ Generic Credential Type is selected
☐ Header Auth credential is selected
```

---

## Groq Authentication Failed

Check:

```text
☐ Groq credential exists
☐ Groq API key is correct
☐ No extra spaces exist in the API key
☐ Correct credential is selected
☐ Selected model is available
```

---

## AI Response Is Empty

Check:

```text
☐ Customer name expression is correct
☐ Feedback message expression is correct
☐ Prompt is not empty
☐ Groq model is connected
☐ Groq credential is valid
```

---

## Structured Output Parser Fails

Check:

```text
☐ OutputParser is connected to ClassifyFeedback
☐ Require Specific Output Format is enabled
☐ Schema contains all required fields
☐ sentiment values are restricted correctly
☐ topic values are restricted correctly
☐ urgency values are restricted correctly
☐ System prompt contains output requirements
```

If failures continue, inspect the actual model output.

---

## Retry Does Not Execute

Verify:

```text
ClassifyFeedback
      ↓
Settings
      ↓
On Error
      ↓
Retry On Fail
```

Use:

```text
Max Tries:
3
```

```text
Wait Between Tries:
1000 ms
```

---

## Academy Returns 422 Classification Failed

First inspect the raw request.

Every classification field must contain an evaluated value.

Correct:

```json
{
  "sentiment": "positive",
  "topic": "product",
  "urgency": "low"
}
```

Incorrect:

```json
{
  "sentiment": "positive",
  "topic": "{{ $('SetClassificationResult').item.json.topic }}",
  "urgency": "low"
}
```

Make sure the expression field is actually in **Expression mode** and that its preview displays the evaluated value.

---

## Topic Is Not Evaluating

Use:

```text
{{ $('SetClassificationResult').item.json.topic }}
```

The preview must show:

```text
product
```

or another valid topic:

```text
billing
support
general
```

Do not submit the literal expression string.

---

## Node Reference Not Found

n8n node references are case-sensitive.

Correct:

```text
$('SetClassificationResult')
```

Incorrect:

```text
$('setclassificationresult')
```

Also verify that the node has not been renamed.

---

# 📊 Evaluation Criteria

| Criteria               | Requirement                                       |
| ---------------------- | ------------------------------------------------- |
| Academy Authentication | Successfully connected using Header Auth          |
| Feedback Retrieval     | Feedback retrieved from Academy API               |
| AI Credentials         | Groq connection works                             |
| Response Generation    | AI generated a customer response                  |
| Classification         | Sentiment, topic, urgency and key issue generated |
| Structured Output      | Classification conforms to required schema        |
| Retry Handling         | Retry configured for AI classification            |
| Enhanced Reply         | Classification used to guide response generation  |
| API Submission         | Final result submitted successfully               |
| Confirmation           | Academy confirmation code received                |

---

# 🎯 Success Criteria

The pipeline is successfully completed when:

```text
✓ Academy authentication works
✓ Customer feedback is retrieved
✓ Feedback item is selected for processing
✓ AI classification executes successfully
✓ Sentiment is correctly structured
✓ Topic is correctly structured
✓ Urgency is correctly structured
✓ Key issue is generated
✓ Structured Output Parser validates the response
✓ Retry handling is configured
✓ Classification result is prepared
✓ Customer response is generated
✓ Response uses classification context
✓ Final result is submitted to the Academy API
✓ Academy returns a successful response
✓ Confirmation code is received
```

---

# 📚 Skills Demonstrated

This project demonstrates practical experience with:

* n8n workflow automation
* HTTP Request nodes
* Header authentication
* API integration
* Groq AI integration
* LLM workflows
* Basic LLM Chain
* Prompt engineering
* Structured AI output
* Structured Output Parser
* JSON schemas
* n8n expressions
* AI classification
* Text generation
* Multi-stage AI pipelines
* Model selection
* Retry On Fail
* Error handling
* API submission
* Workflow documentation
* AI-assisted automation

---

# 💡 Key Concepts Learned

## Text Generation

An LLM can generate customer-facing text based on structured workflow input.

```text
Customer Feedback
       ↓
      LLM
       ↓
Professional Response
```

---

## Structured Classification

LLMs can classify unstructured customer feedback into predictable fields.

```text
Customer Feedback
       ↓
      LLM
       ↓
┌─────────────────┐
│ sentiment       │
│ topic           │
│ urgency         │
│ key_issue       │
└─────────────────┘
```

---

## Structured Output Validation

The Structured Output Parser provides a predictable contract between the AI model and downstream automation.

```text
LLM
 ↓
JSON
 ↓
Schema Validation
 ↓
Valid Structured Data
```

---

## Classification-Aware Generation

The classification result can be reused by a second LLM call.

```text
Feedback
   +
Classification
   ↓
Response Generation
   ↓
Context-Aware Reply
```

This is more reliable than generating a response without understanding the feedback first.

---

## Model Specialization

Different AI tasks can use different models.

```text
                    AI Pipeline
                         │
             ┌───────────┴───────────┐
             │                       │
             ▼                       ▼
        Classification           Generation
             │                       │
             ▼                       ▼
     gpt-oss-20b              gpt-oss-120b
```

A smaller model can handle simpler classification tasks while a larger model can be used for nuanced response generation.

---

## Retry Handling

AI systems can occasionally produce invalid output.

```text
AI Request
    ↓
Validation
    │
    ├── Valid → Continue
    │
    └── Invalid
           ↓
         Retry
           ↓
         Valid
```

Retry handling prevents occasional AI formatting failures from immediately terminating the workflow.

---

## Prompt Context

The generation model receives both the original feedback and the classification.

```text
Original Feedback
       +
Sentiment
       +
Topic
       +
Urgency
       +
Key Issue
       ↓
GenerateReply
       ↓
Professional Response
```

---

# 🔮 Future Improvements

A production implementation could extend this prototype considerably.

## Automatic Feedback Processing

Replace the manual trigger with:

```text
Schedule Trigger
       ↓
Fetch Feedback
       ↓
Process New Feedback
```

For example:

```text
Every 15 minutes
       ↓
Get New Feedback
       ↓
Classify
       ↓
Generate Response
       ↓
Store / Send
```

---

## Human-in-the-Loop Approval

For sensitive feedback:

```text
Feedback
   ↓
AI Classification
   ↓
High Urgency?
   │
   ├── No → Auto Reply
   │
   └── Yes
        ↓
Human Review
        ↓
Approved Reply
```

---

## Sentiment-Based Routing

Different sentiment categories could trigger different workflows.

```text
Positive
   ↓
Customer Success / Marketing

Neutral
   ↓
Standard Support

Negative
   ↓
Support Escalation
```

---

## Database Storage

Store feedback and AI results in:

```text
MySQL
PostgreSQL
MongoDB
```

Example:

```text
feedback
├── feedback_id
├── customer_id
├── message
├── sentiment
├── topic
├── urgency
├── key_issue
├── generated_reply
└── processed_at
```

---

## Vector Database

A vector database could be introduced to retrieve historical customer interactions.

```text
Current Feedback
       ↓
Embedding
       ↓
Vector Search
       ↓
Similar Historical Feedback
       ↓
LLM
       ↓
Context-Aware Response
```

Potential technologies include:

```text
Qdrant
Pinecone
Weaviate
pgvector
```

---

## Customer Support Integration

The generated response could eventually be sent to:

```text
CRM
Support Desk
Email
Slack
Microsoft Teams
Customer Portal
```

---

## Monitoring

Add:

```text
Metrics
Logs
Alerts
Execution Monitoring
AI Token Usage
Model Latency
Classification Failure Rate
Retry Rate
```

This would allow the workflow to be monitored in a production environment.

---

## Model Evaluation

A production system could evaluate:

```text
Classification Accuracy
Response Quality
Schema Failure Rate
Average Latency
Token Usage
Retry Frequency
Human Approval Rate
```

This allows different models and prompts to be compared systematically.

---

# 🏁 Final Result

The completed workflow creates an end-to-end AI customer feedback pipeline:

```text
┌──────────────────────┐
│   Customer Feedback  │
│       API            │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     GetFeedback      │
│     HTTP Request     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   SetFeedbackItem    │
│      Limit = 1       │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   ClassifyFeedback   │
│     gpt-oss-20b      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     OutputParser     │
│ Structured Validation│
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│SetClassificationResult│
│  Structured Fields   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│     GenerateReply    │
│     gpt-oss-120b     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  SendGeneratedReply  │
│      HTTP POST       │
└──────────────────────┘
```

The pipeline follows the complete AI automation lifecycle:

```text
FETCH
  ↓
CLASSIFY
  ↓
VALIDATE
  ↓
STRUCTURE
  ↓
ENRICH
  ↓
GENERATE
  ↓
SUBMIT
```

The workflow combines traditional n8n automation with AI capabilities:

```text
API Integration
      +
LLM Generation
      +
Structured Classification
      +
Schema Validation
      +
Retry Handling
      +
Context-Aware Generation
      ↓
AI Customer Feedback Automation
```

---

# 📁 Repository Structure

```text
ai-customer-feedback-pipeline/
│
├── README.md
├── section-4-ai-customer-feedback-pipeline.json
└── workflow.png
```

---

# 📜 Project Notes

This project is intended for learning, experimentation, and demonstration of n8n AI workflow automation concepts.

The n8n Academy endpoints and associated course resources belong to their respective owners.

The workflow uses the Academy API for assessment and demonstration purposes.

**Do not commit real API credentials, API keys, Groq credentials, or assessment identifiers to a public repository.**

<p align="center">
  <img src="https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExMnJsbWtrbGY0NzExeTc0MmoxandodnBtY3h1OGJwOWwzcXR0ZDJkcCZlcD12MV9naWZzX3NlYXJjaCZjdD1n/rlTH4aNb0uWyQ8sKTU/giphy.gif" width="60%" alt="AI Customer Feedback Pipeline Footer">
</p>
