---
name: Chat via OpenAI-compatible endpoint
description: Point an existing OpenAI client at Bytez to run chat completions against open or closed models.
api: openapi/bytez-openapi.yaml
operations: [getModels, runModel]
---

# Chat via the Bytez OpenAI-compatible endpoint

Bytez exposes OpenAI-compatible Chat Completions, Completions, and Responses
endpoints, so existing OpenAI SDK code works by changing the base URL and key.

## Auth
Use your Bytez key as the OpenAI API key and set the base URL to Bytez. For a
closed-source model, prefix the model id with its provider
(e.g. `openai/gpt-4o-mini`, `anthropic/claude-3-5-sonnet`) and pass your
provider key via the `provider-key` header.

## Steps
1. (Optional) Discover a chat-capable model with `getModels` —
   `GET /models/v2/list/models?task=chat`.
2. Send an OpenAI-style chat request to the Chat Completions endpoint with a
   `messages` array (`role` + `content`). `content` may be a string or an array
   of typed blocks (`text`, `image`, `audio`, `video`) for multimodal models.
3. Set `stream: true` for token streaming; supported reasoning models accept a
   `reasoning` object for "thinking".
4. Handle the response with your existing OpenAI client parsing.

## Rules
- Reference: https://docs.bytez.com/http-reference/oaiCompliant/chatCompletions
- `429` = rate limited (see rate-limits/bytez-rate-limits.yml); `401` = bad key.
- Closed-source usage is billed by provider pricing; open models bill per second.
