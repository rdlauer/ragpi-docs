---
sidebar_position: 3
---

# Configuration

Ragpi uses the following environment variables to configure its behavior. These settings control everything from API access and provider configurations to database connections and document processing.

## Application Configuration

| Variable                  | Description                           | Default                                                                                                                             | Notes                                                                                                                                                                     |
| ------------------------- | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `PROJECT_NAME`            | Name of the project                   | `the current project`                                                                                                               | Used to scope and focus the AI assistant's responses                                                                                                                      |
| `PROJECT_DESCRIPTION`     | Description of the project            | `determined by the available sources`                                                                                               | Defines the project's scope for the AI assistant                                                                                                                          |
| `RAGPI_VERSION`           | API version of Ragpi                  | `v0.5.x`                                                                                                                            | Used in the OpenAPI spec and in `docker-compose.prod.yml` to specify the Ragpi image version                                                                              |
| `API_NAME`                | Name of the API service               | `Ragpi`                                                                                                                             | Used in the OpenAPI spec                                                                                                                                                  |
| `API_SUMMARY`             | Summary of the API service            | `Ragpi is an AI assistant specialized in retrieving and synthesizing technical information to provide relevant answers to queries.` | Used in the OpenAPI spec                                                                                                                                                  |
| `RAGPI_API_KEY`           | API key for authenticated requests    | None                                                                                                                                | If not set, the API will be accessible without authentication. When set, this key must be a self-generated secret and included in the `x-api-key` header of each request. |
| `WORKERS_ENABLED`         | Enable/disable background workers     | `True`                                                                                                                              | When disabled, endpoints requiring Celery workers will return a `503`                                                                                                     |
| `TASK_RETENTION_DAYS`     | Number of days to retain task history | `7`                                                                                                                                 | -                                                                                                                                                                         |
| `LOG_LEVEL`               | Logging level                         | `INFO`                                                                                                                              | Options: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`                                                                                                                  |
| `USER_AGENT`              | User agent string for HTTP requests   | `Ragpi`                                                                                                                             | -                                                                                                                                                                         |
| `MAX_CONCURRENT_REQUESTS` | Maximum number of concurrent requests | `10`                                                                                                                                | -                                                                                                                                                                         |

## Provider Configuration

| Variable                               | Description                                     | Default  | Notes                                                       |
| -------------------------------------- | ----------------------------------------------- | -------- | ----------------------------------------------------------- |
| `CHAT_PROVIDER`                        | Chat service provider                           | `openai` | Options: `openai`,`ollama`,`deepseek`,`openai_compatible`   |
| `EMBEDDING_PROVIDER`                   | Embedding service provider                      | `openai` | Options: `openai`,`ollama`,`openai_compatible`              |
| `OPENAI_API_KEY`                       | API key for OpenAI services                     | None     | Required if using `openai` as chat/embedding provider       |
| `OLLAMA_BASE_URL`                      | Base URL for Ollama provider                    | None     | Required if using `ollama` as chat/embedding provider       |
| `DEEPSEEK_API_KEY`                     | API key for DeepSeek services                   | None     | Required if using `deepseek` as chat provider               |
| `CHAT_OPENAI_COMPATIBLE_BASE_URL`      | Base URL for OpenAI-compatible chat models      | None     | Required if using `openai_compatible` as chat provider      |
| `CHAT_OPENAI_COMPATIBLE_API_KEY`       | API key for OpenAI-compatible chat models       | None     | Required if using `openai_compatible` as chat provider      |
| `EMBEDDING_OPENAI_COMPATIBLE_BASE_URL` | Base URL for OpenAI-compatible embedding models | None     | Required if using `openai_compatible` as embedding provider |
| `EMBEDDING_OPENAI_COMPATIBLE_API_KEY`  | API key for OpenAI-compatible embedding models  | None     | Required if using `openai_compatible` as embedding provider |

## Database Configuration

| Variable                    | Description                                    | Default                             | Notes                                                                                    |
| --------------------------- | ---------------------------------------------- | ----------------------------------- | ---------------------------------------------------------------------------------------- |
| `REDIS_URL`                 | Redis connection URL                           | `redis://localhost:6379`            | **Required**                                                                             |
| `POSTGRES_URL`              | PostgreSQL database URL                        | `postgresql://localhost:5432/ragpi` | **Required if using postgres backend**                                                   |
| `DOCUMENT_STORE_BACKEND`    | Document store backend (`postgres`, `redis`)   | `postgres`                          | -                                                                                        |
| `DOCUMENT_STORE_NAMESPACE`  | Namespace for document storage                 | `document_store`                    | When using `postgres`, this is the table name; when using `redis`, it is the key prefix. |
| `SOURCE_METADATA_BACKEND`   | Metadata storage backend (`postgres`, `redis`) | `postgres`                          | -                                                                                        |
| `SOURCE_METADATA_NAMESPACE` | Namespace for metadata storage                 | `source_metadata`                   | When using `postgres`, this is the table name; when using `redis`, it is the key prefix. |

## Chat Settings

| Variable              | Description                                                                    | Default     |
| --------------------- | ------------------------------------------------------------------------------ | ----------- |
| `BASE_SYSTEM_PROMPT`  | Default system prompt for the AI assistant                                     | _See below_ |
| `CHAT_HISTORY_LIMIT`  | Maximum number of messages retained in the chat history and sent to the model. | `20`        |
| `MAX_CHAT_ITERATIONS` | Maximum steps allowed for generating a response                                | `5`         |
| `RETRIEVAL_TOP_K`     | Number of top retrieval results                                                | `10`        |

## Model Settings

| Variable                         | Description                                                          | Default                  | Notes                                                                                                                                                                                                     |
| -------------------------------- | -------------------------------------------------------------------- | ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `DEFAULT_CHAT_MODEL`             | Default model for chat interactions                                  | `gpt-4o`                 | Only models that support tool/function callings are supported.                                                                                                                                             |
| `CHAT_USE_RESPONSES_API`         | Use the OpenAI Responses API for chat                                | `False`                  | Requires `CHAT_PROVIDER=openai`. Needed for OpenAI reasoning models (e.g. `gpt-5.6-sol`, `gpt-5.6-terra`) to combine active reasoning with tool calling. See [Reasoning Models](#reasoning-models-openai-responses-api). |
| `REASONING_EFFORT`               | Default reasoning effort for reasoning models                        | None                     | Options: `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`. Only sent when set and only on the Responses API path; can be overridden per request via `reasoning_effort`. Not every model supports every value (GPT-5.6 does not support `minimal`). |
| `OPENAI_RESPONSES_STORE`         | Store Responses API state with OpenAI                                | `True`                   | Responses API path only. `True` is the only supported value today — `False` (required for Zero Data Retention) is reserved for future work and currently fails startup when the Responses path is enabled. See the privacy note under [Reasoning Models](#reasoning-models-openai-responses-api).                                        |
| `EMBEDDING_MODEL`                | Model used for embeddings                                            | `text-embedding-3-small` | -  |
| `EMBEDDING_DIMENSIONS`           | Dimensions for embedding vectors                                     | `1536`                   | Must match dimensions of selected embedding model. Dimensions above 2000 (e.g. `text-embedding-3-large` at 3072) are supported — see [Large Embedding Models](#large-embedding-models). Changing this on an existing deployment requires re-embedding. |
| `EMBEDDING_CANDIDATE_MULTIPLIER` | Candidate over-fetch factor for the >2000-dimension retrieval path   | `10`                     | Candidates fetched per search = `RETRIEVAL_TOP_K` × this value, then reranked by exact full-precision cosine. Postgres backend only; no effect at ≤2000 dimensions.                                        |
| `HNSW_EF_SEARCH`                 | Lower bound for pgvector's `hnsw.ef_search` during candidate fetch   | None                     | `1`–`1000`. Only affects >2000-dimension searches that PostgreSQL serves via the HNSW index; when unset, derived from the candidate count.                                                                  |
| `EMBEDDING_SPACE_ID`             | Explicit embedding-space identity recorded in the store manifest     | None                     | Only needed for `ollama`/`openai_compatible` embedding providers where a model alias can change meaning without the endpoint URL changing. When unset, derived from the provider/endpoint.                  |
| `EMBEDDING_ADOPT_EXISTING`       | Allow adopting a pre-existing store that has no manifest             | `False`                  | Only needed when upgrading an existing deployment that uses a **non-default** embedding configuration. Set once for the first startup after upgrading, then remove.                                        |
| `PG_UPDATE_VECTOR_EXTENSION`     | Run `ALTER EXTENSION vector UPDATE` at startup                       | `False`                  | Only needed when an existing PostgreSQL database has a pgvector extension older than required. **Upgrades the extension for the entire database** — back up and check other pgvector-dependent applications first. |

### Reasoning Models (OpenAI Responses API Only) {#reasoning-models-openai-responses-api}

OpenAI reasoning models (such as `gpt-5.6-sol` and `gpt-5.6-terra`) cannot combine
active reasoning with tool calling on the Chat Completions API. To use them with
Ragpi's retrieval tools, enable the Responses API path:

```env
CHAT_PROVIDER=openai
DEFAULT_CHAT_MODEL=gpt-5.6-sol
CHAT_USE_RESPONSES_API=true
REASONING_EFFORT=low
```

When `CHAT_USE_RESPONSES_API` is off (the default), all providers use the Chat
Completions API exactly as before. Reasoning continuity is preserved across the
tool-call loop within a single chat request. `reasoning_effort` may also be set per
request in the `/chat` payload.

:::info Privacy
The Responses API path sends `store=true`, meaning conversation state is retained in
OpenAI's stored-responses workflow for at least 30 days to support reasoning
continuity across tool calls. Using `store=false`, as required for Zero Data
Retention, is not yet supported.
:::

### Large Embedding Models

`text-embedding-3-large` is supported at its full 3072 dimensions:

```env
EMBEDDING_MODEL=text-embedding-3-large
EMBEDDING_DIMENSIONS=3072
```

On the PostgreSQL backend, embeddings are always stored as full-precision float32.
Above 2000 dimensions (pgvector's index limit for the `vector` type) Ragpi builds a
half-precision (`halfvec`) HNSW index and reranks candidates by exact full-precision
cosine. This requires the **pgvector server extension ≥ 0.8.2** (the
`pgvector/pgvector:pg17` image satisfies it; for an older extension in an existing
database, see `PG_UPDATE_VECTOR_EXTENSION`). The Redis backend supports 3072
dimensions without additional configuration.

Note that PostgreSQL chooses the access path per query: for small and medium sources
it typically serves the candidate stage with a sequential scan and switches to the
HNSW index only when a source grows large enough for it to win on cost.
`EMBEDDING_CANDIDATE_MULTIPLIER` controls the number of candidates on both paths,
while `HNSW_EF_SEARCH` only influences queries served by the index.

Ragpi records a manifest for each document store (embedding provider, model,
dimensions, and index configuration) and validates it at startup, failing fast with
actionable guidance on an incompatible change. Note that **changing the embedding
model requires re-embedding even when the dimensions stay the same.**

#### Changing the Embedding Model or Dimensions in an Existing Ragpi Deployment

Changing the embedding identity (provider, model, or dimensions) requires
re-embedding all documents; there is no automatic data migration:

1. Back up the database / Redis data, then stop the API and workers.
2. Remove the document vectors **and** the store manifest, keeping source metadata:
   - **PostgreSQL:** `DROP TABLE <DOCUMENT_STORE_NAMESPACE>;` and delete its row from
     `ragpi_store_manifest` (leave the `source_metadata` table intact).
   - **Redis:** drop the index, delete its `<namespace>:sources:*` keys, and delete
     the `<namespace>:__manifest__` key.
3. Restart with the new `EMBEDDING_MODEL` / `EMBEDDING_DIMENSIONS` (startup recreates
   the schema, index, and manifest), then re-sync every source.

## Document Processing

| Variable                   | Description                                         | Default                                |
| -------------------------- | --------------------------------------------------- | -------------------------------------- |
| `DOCUMENT_UUID_NAMESPACE`  | UUID namespace for document IDs                     | `ee747eb2-fd0f-4650-9785-a2e9ae036ff2` |
| `CHUNK_SIZE`               | Size of document chunks for processing (in tokens)  | `512`                                  |
| `CHUNK_OVERLAP`            | Overlap size between document chunks (in tokens)    | `50`                                   |
| `DOCUMENT_SYNC_BATCH_SIZE` | Number of documents processed per batch during sync | `500`                                  |

## GitHub

| Variable             | Description                            | Default      |
| -------------------- | -------------------------------------- | ------------ |
| `GITHUB_TOKEN`       | GitHub token used by GitHub connectors | None         |
| `GITHUB_API_VERSION` | GitHub API version                     | `2022-11-28` |

## OpenTelemetry Settings

| Variable                      | Description                      | Default                 |
| ----------------------------- | -------------------------------- | ----------------------- |
| `OTEL_ENABLED`                | Enable/disable OpenTelemetry     | `False`                 |
| `OTEL_SERVICE_NAME`           | Service name for OpenTelemetry   | `ragpi`                 |
| `OTEL_EXPORTER_OTLP_ENDPOINT` | OpenTelemetry collector endpoint | `http://localhost:4318` |

When enabled, Ragpi provides basic tracing capabilities through OpenTelemetry instrumentation using the `http/protobuf` protocol. This includes automatic tracing of FastAPI endpoints and LLM API calls, with spans exported to the endpoint specified in `OTEL_EXPORTER_OTLP_ENDPOINT`.

Additionally, Ragpi respects any standard OTEL environment variables (e.g., `OTEL_RESOURCE_ATTRIBUTES`, `OTEL_EXPORTER_OTLP_HEADERS`, etc.) supported by the OpenTelemetry specification.

## Default System Prompt

The default value for `BASE_SYSTEM_PROMPT` is:

```
You are an AI assistant specialized in retrieving and synthesizing technical information to provide relevant answers to queries.
```

## API Key Configuration

If you want to restrict access to the Ragpi API, you can enable API authentication using `RAGPI_API_KEY`. When set, this key must be included in all API requests using the `x-api-key` header.

### Generating an API Key

You can generate a secure API key using the following command in your terminal:

```bash
openssl rand -hex 32
```
