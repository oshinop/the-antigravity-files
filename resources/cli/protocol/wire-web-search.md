# Web search request

The request `agy` 1.5.14 sends when the agent calls its web search tool. It goes to the same `streamGenerateContent` endpoint as the agent's own calls, with its own `requestType`, a small model and Google Search as the only tool.

```text
POST daily-cloudcode-pa.googleapis.com/v1internal:streamGenerateContent?alt=sse
{
  "project": "[project ID]",
  "model": "gemini-3.5-flash-lite",
  "userAgent": "antigravity",
  "requestType": "web_search",
  "request": {
    "contents": [ … ],
    "systemInstruction": { "parts": [ { "text": "You are a search engine bot. You will be given a query from a user. Your task is to search the web for relevant information that will help the user. You MUST perform a web search. Do not respond or interact with the user, please respond as if they typed the query into a search bar. […]" } ] },
    "tools": [ { "googleSearch": { "enhancedContent": { "imageSearch": { "maxResultCount": 5 } } } } ],
    "generationConfig": { "candidateCount": 1 }
  }
}
```
