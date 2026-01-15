# CCS Integration

The package supports sending usage events to **mftsccs** (CCS) for centralized tracking and analytics.

## Setup

1. Set your CCS credentials as environment variables:

```bash
export CLIENT_ID=your_client_id
export CLIENT_SECRET=your_client_secret
```

Or create a `.env` file:

```
CLIENT_ID=your_client_id
CLIENT_SECRET=your_client_secret
```

2. Initialize with CCS using the async `init` function:

```python
import os
import asyncio
from a2a_llm_tracker import init, get_llm

async def setup_ccs_meter():
    """Setup meter with CCS sink for mftsccs integration."""

    client_id = os.getenv("CLIENT_ID")
    client_secret = os.getenv("CLIENT_SECRET")

    if not client_id or not client_secret:
        raise ValueError(
            "CLIENT_ID and CLIENT_SECRET environment variables are required.\n"
            "Set them in your .env file or export them."
        )

    # Initialize meter with CCS sink
    meter = await init(
        client_id=client_id,
        client_secret=client_secret,
        application_name="my-app-name",
    )

    # Get the LiteLLM wrapper
    return get_llm(meter)

# Run the async setup
async def main():
    llm = await setup_ccs_meter()

    # Now use llm for completions
    response = await llm.acompletion(
        model="openai/gpt-4.1",
        messages=[{"role": "user", "content": "Hello!"}],
    )
    print(response)

asyncio.run(main())
```

## Parameters

- `client_id`: Your mftsccs client ID (used for authentication and entity connection)
- `client_secret`: Your mftsccs client secret (authentication)
- `application_name`: Name of your application (creates a tracker concept in CCS)

## What Gets Tracked

The CCS sink creates and connects the following concepts:

- `the_llm_tracker` - Your application tracker
- `the_llm_usage` - Individual usage events with all metadata
- `the_llm_provider` - Provider concepts (OpenAI, Anthropic, etc.)
- `the_llm_model` - Model concepts
- `the_cost` - Cost tracking
- `the_token_count` - Token usage
