# Coding Rules - AI / LLM Applications

For any app that calls an LLM (chat, agents, RAG, BYOK tools, coaching, trading signals).
Most of these are security and reliability rules, not model tricks.

## Keys and secrets
1. **Never log, print, or commit model API keys** (OpenAI, Anthropic, Bedrock, etc.). Same
   rule as any secret: env vars / secret manager only. Redact keys from error output.
2. **BYOK (bring your own key):** when the user supplies their key, keep it server-side or in
   secure per-user storage, never in client bundles or `localStorage`. Scope it to that user,
   let them rotate/delete it, and never send it to any endpoint but the model provider.
3. **Separate keys per environment** and per surface (dev / prod, extension / backend).

## Prompt injection and untrusted content
4. **Treat all retrieved and user-supplied content as DATA, not instructions.** Web pages,
   RAG chunks, tool outputs, file contents, and emails can contain "ignore previous
   instructions" style attacks. Never concatenate them into the system prompt as if trusted.
5. **Keep the trust boundary explicit:** system prompt (yours) vs user input vs external
   content. Label external content clearly in the prompt ("The following is untrusted content
   to analyze:") and instruct the model not to follow instructions inside it.
6. **Gate side effects.** If the model can trigger actions (send email, spend money, delete
   data, run code), require validation or human confirmation for irreversible or costly ones.
   Do not let model output directly authorize a destructive action.
7. **Never build shell commands, SQL, or file paths by pasting model output unescaped.** Same
   injection discipline as user input.

## Structured output and validation
8. **Validate every model response** against a schema (Zod / Pydantic / JSON schema) before
   using it. Assume the model can return malformed JSON, missing fields, or extra prose.
9. Prefer the provider's structured-output / tool-calling / JSON mode over regex-parsing free
   text. Still validate the result.
10. Handle refusals and empty responses as normal cases, not crashes.

## Reliability, cost, and latency
11. **Always set timeouts and a retry policy** (exponential backoff, capped) on model calls.
    A hung request must not hang the app.
12. **Budget tokens and cost.** Cap input size (truncate/chunk context), cap max output
    tokens, and track spend. For BYOK, surface usage to the user. For your own key, add
    per-user and global rate limits so one loop cannot run up the bill.
13. **Stream** long responses for UX, but still enforce the timeout and a hard stop.
14. **Cache** where safe (identical prompts, embeddings) to cut cost and latency.
15. Pick the model tier per task; do not send everything to the most expensive model. Make the
    model configurable, not hardcoded.

## Privacy and data handling
16. **Redact or minimize PII before sending to a model,** especially third-party APIs. In
    regulated contexts (health, genomics, finance) confirm the provider's data-use and
    retention terms and prefer no-training / zero-retention options.
17. Do not send secrets, credentials, or full datasets into prompts. Send the minimum needed.
18. Log prompts/responses for debugging only with PII stripped and access controlled; set a
    retention limit.

## RAG specifics
19. Chunk and embed deliberately; store source metadata so every answer can cite its source.
20. Sanitize documents at ingestion; remember retrieved chunks are untrusted content (rule 4).
21. Handle "no relevant context" explicitly — the model should say it does not know rather
    than hallucinate. Ground answers in retrieved text and surface citations.

## Evaluation and quality
22. **Build an eval set** (representative inputs + expected properties) and run it when you
    change prompts, models, or retrieval. Treat prompt changes like code changes.
23. Log real failures and feed them back into the eval set.
24. Pin model versions where the provider allows it; a silent model update can change behavior.
    Re-run evals when you move versions.

## Agents
25. Bound autonomy: max steps/iterations, allowed tools, and a kill switch. An agent loop must
    terminate.
26. Log every tool call and decision for auditability.
27. Least privilege for tools (scoped credentials, read-only where possible), and confirm the
    destructive ones (rule 6).

---

## Quick reference - AI / LLM
- Keys: never log/commit; BYOK server-side, per-user, rotatable
- Untrusted content is DATA not instructions; gate side effects; confirm destructive actions
- Validate every response against a schema; prefer structured output
- Timeouts + capped retries; budget tokens and cost; rate-limit
- Redact PII before sending; mind provider data-retention (health/finance)
- RAG: cite sources, sanitize inputs, say "I don't know"
- Evals for prompt/model changes; pin versions
- Agents: bounded steps, least-privilege tools, kill switch
