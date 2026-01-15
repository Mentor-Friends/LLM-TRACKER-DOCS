# LiteLLM Wrapper Usage

The package wraps LiteLLM for automatic usage tracking across all providers.

## Setup

```python
from a2a_llm_tracker import Meter, PricingRegistry
from a2a_llm_tracker.sinks.jsonl import JSONLSink
from a2a_llm_tracker.integrations.litellm import LiteLLM

pricing = PricingRegistry()
pricing.set_price(
    provider="openai",
    model="openai/gpt-4.1",
    input_per_million=2.0,
    output_per_million=8.0,
)

meter = Meter(
    pricing=pricing,
    sinks=[JSONLSink("usage.jsonl")],
    project="my-a2a-system",
)

llm = LiteLLM(meter=meter)
```

## Sync Completion

```python
response = llm.completion(
    model="openai/gpt-4.1",
    messages=[
        {"role": "user", "content": "Say hello in one sentence."}
    ],
)
print(response)
```

## Sync Streaming

```python
for chunk in llm.completion(
    model="openai/gpt-4.1",
    messages=[{"role": "user", "content": "Write a short poem."}],
    stream=True,
):
    print(chunk, end="", flush=True)
```

- Streaming output is yielded as usual
- Usage is recorded after the stream finishes
- If the provider does not return usage for streams, accuracy is marked as `unknown`

## Async Completion

```python
response = await llm.acompletion(
    model="openai/gpt-4.1",
    messages=[{"role": "user", "content": "Async hello!"}],
)
```

## Async Streaming

```python
stream = await llm.acompletion(
    model="openai/gpt-4.1",
    messages=[{"role": "user", "content": "Stream async output"}],
    stream=True,
)

async for chunk in stream:
    print(chunk, end="", flush=True)
```

## Agent and Session Context

```python
from a2a_llm_tracker import meter_context

with meter_context(
    agent_id="planner-agent",
    session_id="session-123",
    user_id="user-456",
):
    llm.completion(
        model="openai/gpt-4.1",
        messages=[{"role": "user", "content": "Plan my day"}],
    )
```
