# Simple RAG (Retrieval-Augmented Generation) Example

## What is RAG?

RAG combines **retrieval** (pulling relevant information from a source) with **generation** (creating an answer based on that information). Instead of relying only on the AI's training data, RAG grounds the answer in actual project documents, code, or specifications.

---

## Simple Example: Using Project Documentation

### Scenario
You have a software project with a README and API documentation. You want to ask the AI a question about your specific project, and it should answer based on your actual docs—not generic knowledge.

### Step 1: The Source Material (Retrieved Content)

**File: `project-docs.md`** (This is what you retrieve)
```markdown
# MyPayment API Documentation

## Authentication
- All requests require an Authorization header with a Bearer token
- Tokens are issued by the Auth service at /auth/login
- Token expiry: 1 hour

## POST /api/payments/create
Request body:
{
  "amount": number,
  "currency": "USD" | "EUR",
  "userId": string
}

Response:
{
  "transactionId": string,
  "status": "pending" | "completed" | "failed",
  "timestamp": ISO8601
}

## Error Handling
- 401: Invalid or expired token
- 400: Missing required fields
- 500: Internal server error
```

---

### Step 2: The RAG Prompt

**Without RAG (Generic Answer):**
```
Q: "How do I authenticate to the MyPayment API?"

A: "Typically, you'd use OAuth 2.0 or API keys. Many APIs use JWT tokens..."
```
❌ This is generic and might not match your actual system.

---

**With RAG (Grounded Answer):**
```
You are a technical support assistant. Answer questions based ONLY on the provided documentation.

Documentation:
---
# MyPayment API Documentation

## Authentication
- All requests require an Authorization header with a Bearer token
- Tokens are issued by the Auth service at /auth/login
- Token expiry: 1 hour
---

Question: How do I authenticate to the MyPayment API?
```

✅ **Expected Answer:**
```
To authenticate to the MyPayment API:

1. Get a Bearer token from the Auth service at POST /auth/login
2. Add an Authorization header with the token to all requests
3. Note that tokens expire after 1 hour, so you'll need to refresh them

Example:
Authorization: Bearer <your-token-here>
```

---

### Step 3: Another RAG Example

**Question with documentation:**
```
You are a technical support assistant. Answer questions based ONLY on the provided documentation.

Documentation:
---
## POST /api/payments/create
Request body:
{
  "amount": number,
  "currency": "USD" | "EUR",
  "userId": string
}

Response:
{
  "transactionId": string,
  "status": "pending" | "completed" | "failed",
  "timestamp": ISO8601
}

## Error Handling
- 401: Invalid or expired token
- 400: Missing required fields
- 500: Internal server error
---

Question: What fields do I need to send when creating a payment?
```

✅ **Expected Answer:**
```
When creating a payment, you need to send:

- amount (required): A number representing the payment amount
- currency (required): Either "USD" or "EUR"
- userId (required): A string identifying the user

The response will include a transactionId, status (pending/completed/failed), and timestamp.

If you get a 400 error, check that all three fields are present and properly formatted.
```

---

## Why RAG is Powerful for Software Engineering

| Scenario | Without RAG | With RAG |
|----------|-------------|----------|
| Q: "What's your API response format?" | Generic JSON format | **Your actual API schema** |
| Q: "How do I handle errors?" | Generic error handling | **Your project's specific error codes and meanings** |
| Q: "What's the auth flow?" | OAuth/JWT explanation | **Your exact authentication implementation** |
| Q: "What's in the config?" | Generic config advice | **Your actual config keys and values** |

---

## Real-World RAG in Software Engineering

### Use Case 1: Code Review
```
Retrieve: The team's coding standards document
Question: "Is this function following our naming conventions?"
→ AI checks the code against YOUR standards, not generic ones
```

### Use Case 2: Debugging
```
Retrieve: Your system architecture document
Question: "Why is the payment service failing?"
→ AI knows YOUR system's flow and dependencies
```

### Use Case 3: Documentation
```
Retrieve: Your API documentation
Question: "Generate code examples for the /users endpoint"
→ AI generates examples matching YOUR actual API
```

### Use Case 4: On-boarding
```
Retrieve: Your project README and setup guide
Question: "How do I set up the dev environment?"
→ AI gives YOUR exact setup steps, not generic advice
```

---

## How to Implement RAG (Simplified Steps)

1. **Collect** relevant documents (README, API specs, architecture docs, code comments)
2. **Store** them in a retrievable format (vector database, file system, or simple text)
3. **Retrieve** relevant chunks when a question is asked
4. **Build** the prompt with both the question AND the retrieved content
5. **Generate** an answer grounded in your actual information

---

## Prompt Structure for RAG

```
[System role or instruction]

[Retrieved Documentation/Context]
---
[Question from user]

Answer based ONLY on the provided information. If the information doesn't contain the answer, say so.
```

---

## Key Principle

**RAG = Your Docs + AI Reasoning = Accurate, Project-Specific Answers**

Instead of:
- AI guessing based on its training data

You get:
- AI answering based on YOUR actual project information

This is why RAG is critical for enterprise software engineering—it keeps AI grounded in reality.
