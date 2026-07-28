---
sidebar_position: 2
---

# OpenAI (Default)

### Provider Selection

```env
CHAT_PROVIDER=openai
EMBEDDING_PROVIDER=openai
```

### Required Environment Variables

```env
OPENAI_API_KEY=your_api_key_here
```

### Optional: Reasoning Models

OpenAI reasoning models (such as `gpt-5.6-sol` and `gpt-5.6-terra`) require the
Responses API to combine reasoning with Ragpi's retrieval tools:

```env
DEFAULT_CHAT_MODEL=gpt-5.6-sol
CHAT_USE_RESPONSES_API=true
REASONING_EFFORT=low
```

See [Reasoning Models](../configuration.md#reasoning-models-openai-responses-api) in
the configuration reference for details, including the `store=true` privacy note.
