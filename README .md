# Suki Ambient Session API

The Suki Ambient Session API lets you start a session where audio from a clinical visit is captured and automatically turned into structured medical notes. It is mainly used by healthcare applications so that clinicians do not have to type notes manually during a visit.

One important thing to keep in mind — this API is **asynchronous**. When you call the start endpoint, it does not mean the notes are ready. It just means the session has started. The actual processing happens in the background and usually takes around 2 to 5 minutes. You will get notified when it is done through a webhook.

---

## Table of Contents

- [Authentication](#authentication)
- [Endpoint](#endpoint)
- [Request Body](#request-body)
- [Parameters](#parameters)
- [Response Examples](#response-examples)
- [Webhooks](#webhooks)
- [Example Usage](#example-usage)
- [Best Practices](#best-practices)

---

## Authentication

Every request to this API needs a Bearer token in the `Authorization` header. Think of it as your ID card — without it, the server will reject your request. You also need to tell the server that you are sending JSON data using the `Content-Type` header.

```http
Authorization: Bearer <your_token_here>
Content-Type: application/json
```

> **Note:** Keep your token private. Do not paste it directly in your code or push it to GitHub.

---

## Endpoint

Use this endpoint to start an ambient session:

```
POST /v1/ambient/sessions/start
```

The API also accepts a singular version of the URL. Both work the same way:

```
POST /v1/ambient/session/start
```

The plural form `/sessions/` is recommended going forward.

---

## Request Body

Send a JSON object in the body of your request. Here is an example:

```json
{
  "patientId": "patient-001",
  "visitId": "visit-789",
  "clinicianId": "dr-smith-42",
  "diagnoses": ["hypertension", "type-2-diabetes"],
  "metadata": {
    "department": "cardiology",
    "priority": "high"
  }
}
```

---

## Parameters

| Parameter | Required | Description |
|---|---|---|
| `patientId` | Yes | The unique ID of the patient. |
| `visitId` | Yes | The unique ID of the clinical visit. Only one active session is allowed per `visitId`. |
| `clinicianId` | No | The ID of the clinician conducting the visit. |
| `diagnoses` | No | A list of known diagnoses for this visit. Providing this can help improve the accuracy of the generated notes. |
| `metadata.department` | No | The department where the visit is happening, for example `"cardiology"` or `"pediatrics"`. |
| `metadata.priority` | No | How urgent the session is. Accepted values are `"low"`, `"medium"`, or `"high"`. |

---

## Response Examples

### Success

If the request is valid, the API responds with a `201 Created` status and returns a session object. This means the session has started — not that the notes are ready.

```json
{
  "sessionId": "abc123",
  "status": "started",
  "createdAt": "2025-01-15T10:30:00Z"
}
```

> Save the `sessionId` from this response. You will need it later to identify which session the webhook is referring to.

### Errors

| Status Code | Meaning | Common Cause |
|---|---|---|
| `400` | Bad Request | A required field is missing or the JSON format is wrong. |
| `401` | Unauthorized | The `Authorization` header is missing or the token is invalid. |
| `409` | Conflict | A session for this `visitId` already exists and is still active. |
| `500` | Internal Server Error | Something went wrong on Suki's end. Wait a moment and try again. |

---

## Webhooks

A webhook is a way for Suki to notify your server when the session is complete — instead of you having to keep asking. Once the audio is processed and the notes are ready, Suki sends a POST request to a URL you provide.

Here is what that request body looks like:

```json
{
  "sessionId": "abc123",
  "status": "completed",
  "summaryAvailable": true
}
```

| Field | Description |
|---|---|
| `sessionId` | The ID of the session that just finished. Use this to match it to the right patient visit. |
| `status` | Will be `"completed"` when the notes are ready. |
| `summaryAvailable` | If this is `true`, you can now fetch the structured summary using the `sessionId`. |

When Suki calls your webhook URL, your server should respond with `HTTP 200` as quickly as possible to confirm you received it. Do the actual processing after sending the response.

---

## Example Usage


#### JavaScript

```javascript
const response = await fetch('https://api.suki.ai/v1/ambient/sessions/start', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer <your_token_here>',
    'Content-Type': 'application/json'
  },
  
  body: JSON.stringify({
    patientId: 'patient-001',
    visitId: 'visit-789',
    clinicianId: 'dr-smith-42',
    diagnoses: ['hypertension'],
    metadata: {
      department: 'cardiology',
      priority: 'medium'
    }
  })
});

const data = await response.json();
console.log('Session started:', data.sessionId);
```

---

## Best Practices

**1. Do not assume the session is complete on start**
The `201` response only means the session has been created. The audio processing happens in the background and takes 2 to 5 minutes. Always wait for the webhook before trying to fetch the notes.

**2. Use webhooks instead of polling**
Polling means your app keeps sending requests every few seconds to check if the session is done. This is wasteful and slows things down, especially if many sessions are running at the same time. Webhooks are faster and more efficient — Suki will call you when it is ready.

**3. Always save the sessionId**
As soon as you get the success response, store the `sessionId`. When the webhook fires, you will need it to know which patient's notes are ready.

**4. Never expose your Bearer token**
Store it in an environment variable like `process.env.SUKI_API_TOKEN`, not directly in your code. Committing a token to GitHub is a security risk.

**5. Handle 409 errors carefully**
A `409` means a session for that `visitId` is already running. Before retrying, check if the original session is still active. Do not blindly start a new one — it will fail again with the same error.
