---
name: Run a Bytez model
description: Discover an available model for a task and run serverless inference on it through the Bytez unified API.
api: openapi/bytez-openapi.yaml
operations: [getTasks, getModels, runModel]
---

# Run a Bytez model

Bytez is a unified, serverless inference gateway to 100,000+ open and closed AI
models. One API key, one endpoint shape. Use this skill to pick a model and run it.

## Auth
Send your Bytez key in the `Authorization` header (the raw key, e.g.
`Authorization: BYTEZ_KEY`). Base URL: `https://api.bytez.com`. To run a
closed-source model (OpenAI, Anthropic, Cohere, Google, Mistral) also send a
`provider-key` header with your provider account key — Bytez pass-through routes
it and never stores it.

## Steps
1. (Optional) List task types with `getTasks` — `GET /models/v2/list/tasks` —
   to find a valid task string (e.g. `chat`, `text-to-image`, `translation`).
2. Find a model with `getModels` — `GET /models/v2/list/models?task=chat`.
   Each result has `modelId`, `task`, `params` (billions), `ramRequired` (GB),
   and `meterPrice` (cost per second for open models).
3. Run it with `runModel` — `POST /models/v2/{modelId}`. The request body
   depends on the task: `text` for most NLP tasks, `messages` for chat, `url`
   or `base64` for image/audio/video, `question`+`context` for QA,
   `candidate_labels` for zero-shot. Set `stream: true` to stream tokens.
4. Read the response envelope `{ "error": null, "output": ... }`. If `error` is
   non-null the call failed; `output` is the model result otherwise.

## Rules
- Free tier: open models up to 7B, 1 concurrent request; closed models 10 req/s.
  A `429` envelope error `"Rate limit exceeded"` means back off — limits scale
  with credits purchased (see rate-limits/bytez-rate-limits.yml).
- `401` means the `Authorization` header is missing or wrong.
- No idempotency key exists; inference calls are single-shot — just retry on 429/5xx.
- Prefer passing media by `url` over `base64` to keep payloads small.
