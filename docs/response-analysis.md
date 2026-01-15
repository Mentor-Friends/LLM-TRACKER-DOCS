# Direct Response Analysis

If you're making LLM calls directly using provider SDKs (OpenAI, Gemini, Anthropic, etc.) and want to track usage without routing through the LiteLLM wrapper, use `analyze_response`.

## Supported Providers

| Provider | ResponseType |
|----------|-------------|
| OpenAI | `ResponseType.OPENAI` |
| Google Gemini | `ResponseType.GEMINI` |
| Anthropic | `ResponseType.ANTHROPIC` |
| Cohere | `ResponseType.COHERE` |
| Mistral | `ResponseType.MISTRAL` |
| Groq | `ResponseType.GROQ` |
| Together AI | `ResponseType.TOGETHER` |
| AWS Bedrock | `ResponseType.BEDROCK` |
| Google Vertex AI | `ResponseType.VERTEX` |
| LiteLLM | `ResponseType.LITELLM` |

## Example: Track OpenAI Direct Calls

```python
import asyncio
from openai import OpenAI
from a2a_llm_tracker import init, get_meter, analyze_response, ResponseType

async def main():
    # Initialize the tracker with CCS
    meter = await init(
        client_id="your_client_id",
        client_secret="your_client_secret",
        application_name="my-app",
    )

    # Make a direct OpenAI call (not through LiteLLM wrapper)
    openai_client = OpenAI()
    response = openai_client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content": "Say hello!"},
        ],
    )

    # Analyze and record the response to CCS
    event = analyze_response(
        response=response,
        response_type=ResponseType.OPENAI,
        meter=meter,
        agent_id="my-agent",
    )

    print(f"Tracked: {event.total_tokens} tokens, ${event.cost_usd:.6f}")

asyncio.run(main())
```

## Example: Track Gemini Direct Calls

```python
import google.generativeai as genai
from a2a_llm_tracker import get_meter, analyze_response, ResponseType

genai.configure(api_key="your_gemini_api_key")
model = genai.GenerativeModel("gemini-1.5-flash")

response = model.generate_content("Hello!")

event = analyze_response(
    response=response,
    response_type=ResponseType.GEMINI,
    meter=get_meter(),
)
```

## Example: Track Anthropic Direct Calls

```python
from anthropic import Anthropic
from a2a_llm_tracker import get_meter, analyze_response, ResponseType

client = Anthropic()
response = client.messages.create(
    model="claude-3-5-sonnet-latest",
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello!"}],
)

event = analyze_response(
    response=response,
    response_type=ResponseType.ANTHROPIC,
    meter=get_meter(),
)
```

## Async Version

For async code, use `analyze_response_async` for better performance:

```python
from a2a_llm_tracker import analyze_response_async, ResponseType

event = await analyze_response_async(
    response=response,
    response_type=ResponseType.OPENAI,
    meter=meter,
    agent_id="my-agent",
)
```

## Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `response` | Any | Yes | Raw response from the LLM provider (dict or SDK object) |
| `response_type` | ResponseType or str | Yes | Provider type (e.g., `ResponseType.OPENAI` or `"openai"`) |
| `meter` | Meter | Yes | The meter instance for cost calculation and recording |
| `model_override` | str | No | Override the model name from the response |
| `latency_ms` | int | No | Request latency in milliseconds |
| `agent_id` | str | No | Agent ID for attribution |
| `user_id` | str | No | User ID for attribution |
| `session_id` | str | No | Session ID for attribution |
| `trace_id` | str | No | Trace ID for attribution |
| `metadata` | dict | No | Additional metadata to include |
| `record` | bool | No | If True (default), record to sinks. Set False to only analyze. |

## What Gets Extracted

The analyzer extracts provider-specific information:

- **Tokens**: input/output/total tokens
- **Cost**: calculated from meter's pricing registry
- **Finish reason**: why the generation stopped
- **Request ID**: provider's request identifier
- **Cached tokens**: (OpenAI) prompt caching info
- **Safety ratings**: (Gemini) content safety metadata
