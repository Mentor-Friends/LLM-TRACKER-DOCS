# Pricing Configuration

Pricing is fully user-controlled, allowing you to set custom rates for any model.

## Setting Prices

```python
from a2a_llm_tracker import PricingRegistry

pricing = PricingRegistry()

pricing.set_price(
    provider="openai",
    model="openai/gpt-4.1",
    input_per_million=2.0,
    output_per_million=8.0,
)
```

## Use Cases

This supports:

- **Enterprise pricing**: Use your negotiated rates
- **Price changes over time**: Update prices as providers change them
- **Multiple vendors**: Different rates for different providers

## Default Pricing

The package includes default pricing for common models when using `init()`:

### OpenAI Models
- gpt-4o: $2.50 / $10.00 per million tokens
- gpt-4o-mini: $0.15 / $0.60 per million tokens
- gpt-4.1: $2.00 / $8.00 per million tokens
- gpt-4-turbo: $10.00 / $30.00 per million tokens
- o1: $15.00 / $60.00 per million tokens

### Anthropic Models
- claude-3-5-sonnet-latest: $3.00 / $15.00 per million tokens
- claude-3-opus-latest: $15.00 / $75.00 per million tokens
- claude-3-haiku: $0.25 / $1.25 per million tokens

## Image Generation Pricing

```python
pricing.set_image_generation_price(
    "openai", "openai/dall-e-3",
    prices={
        "1024x1024:standard": 0.040,
        "1024x1024:hd": 0.080,
        "1792x1024:standard": 0.080,
        "default": 0.040,
    }
)
```
