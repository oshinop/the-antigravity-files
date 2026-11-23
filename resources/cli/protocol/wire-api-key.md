# Model requests with a Gemini API key

With `GEMINI_API_KEY` set, `agy` 1.5.14 sends model calls to the Gemini Developer API through the Google Gen AI Go SDK instead of Cloud Code. The key travels in a header; the body is a plain `GenerateContentRequest` without the Cloud Code envelope.

```text
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-pro-preview:streamGenerateContent?alt=sse
user-agent: google-genai-sdk/1.79.1 gl-go/go1.28-[…]
x-goog-api-client: google-genai-sdk/1.79.1 gl-go/go1.28-[…]
x-goog-api-key: [key]
content-type: application/json

body keys: contents, generationConfig, sessionId, systemInstruction, tools
  generationConfig: { "maxOutputTokens": 65535, "thinkingConfig": { "includeThoughts": true, […] } }
  systemInstruction: "<identity> You are Antigravity, a powerful agentic AI assistant designed by the Google Dee[…]"
  tools: 14 declarations

POST https://generativelanguage.googleapis.com/v1beta/models/gemini-3.1-flash-lite-preview:streamGenerateContent?alt=sse
  systemInstruction: "You are a conversation title generator. […]"
  tools: none

POST https://play.googleapis.com/log          (Clearcut batches continue)
```

No request goes to `daily-cloudcode-pa.googleapis.com`: no `loadCodeAssist`, `listExperiments`, `fetchAvailableModels` or trajectory upload.
