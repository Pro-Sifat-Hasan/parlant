# OpenAI NLP Service Documentation

The OpenAI NLP service provides seamless integration with OpenAI's powerful language models, offering enterprise-grade conversational AI capabilities. This adapter supports flexible model selection, automatic fallback mechanisms, and optimized configurations for different use cases.

## 🚀 Quick Start

### Prerequisites

1. **OpenAI API Key**: Obtain an API key from [OpenAI Platform](https://platform.openai.com/api-keys)
2. **Account Setup**: Ensure your OpenAI account has sufficient credits and appropriate usage limits
3. **Environment Configuration**: Set up your environment variables

### Basic Setup

```bash
# Set your OpenAI API key
export OPENAI_API_KEY="sk-your-api-key-here"
```

### Simple Usage

```python
import parlant.sdk as p

# Use default configuration (gpt-4o-mini)
async with p.Server(
    nlp_service=p.NLPServices.openai()
) as server:
    agent = await server.create_agent(
        name="Assistant",
        description="A helpful AI assistant"
    )
```

## 🎯 Model Selection

### Default Behavior

When no model is specified, the service uses **gpt-4o-mini** for all schemas, providing:
- ⚡ **Fast responses** - Optimized for speed
- 💰 **Cost-effective** - Lower token costs
- 🎯 **High quality** - Excellent performance for most tasks

### Single Model Configuration

Specify a single model for all operations:

```python
import parlant.sdk as p

# Use GPT-4o for all schemas
async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name="gpt-4o")
) as server:
    agent = await server.create_agent(
        name="Premium Assistant",
        description="A high-performance AI assistant"
    )
```

### Multiple Models with Fallback

Configure multiple models for improved reliability:

```python
import parlant.sdk as p

# Primary: gpt-4o, Fallback: gpt-4o-mini
async with p.Server(
    nlp_service=p.NLPServices.openai(
        generative_model_name=["gpt-4o", "gpt-4o-mini"]
    )
) as server:
    agent = await server.create_agent(
        name="Reliable Assistant",
        description="An AI assistant with automatic fallback"
    )
```

## 📋 Supported Models

### Predefined Models

| Model | API Name | Description | Context Window | Best For |
|-------|----------|-------------|----------------|----------|
| **GPT-4o** | `gpt-4o-2024-11-20` | Latest GPT-4 Omni model | 128K tokens | Complex reasoning, high accuracy |
| **GPT-4o (Aug 2024)** | `gpt-4o-2024-08-06` | GPT-4 Omni from August 2024 | 128K tokens | Stable, proven performance |
| **GPT-4.1** | `gpt-4.1` | Latest GPT-4 model | 128K tokens | Advanced reasoning tasks |
| **GPT-4o Mini** | `gpt-4o-mini` | Smaller, faster GPT-4 variant | 128K tokens | Fast responses, cost optimization |

### Model Mapping

```python
# These model names are automatically mapped:
"gpt-4o"           → GPT_4o (gpt-4o-2024-11-20)
"gpt-4o-2024-11-20" → GPT_4o
"gpt-4o-2024-08-06" → GPT_4o_24_08_06  
"gpt-4.1"          → GPT_4_1
"gpt-4o-mini"      → GPT_4o_Mini

# Unknown models automatically fallback to GPT_4o_Mini
```

### Custom Models

Use any OpenAI model by specifying its exact name:

```python
# Use GPT-3.5 Turbo
async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name="gpt-3.5-turbo")
) as server:
    # Custom models fallback to GPT_4o_Mini configuration
    pass
```

## ⚙️ Configuration Examples

### Development Environment

```python
import parlant.sdk as p

# Fast and cost-effective for development
async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name="gpt-4o-mini")
) as server:
    agent = await server.create_agent(
        name="Dev Assistant",
        description="Development environment assistant"
    )
```

### Production Environment

```python
import parlant.sdk as p

# High performance with fallback
async with p.Server(
    nlp_service=p.NLPServices.openai(
        generative_model_name=["gpt-4o", "gpt-4o-2024-08-06", "gpt-4o-mini"]
    )
) as server:
    agent = await server.create_agent(
        name="Production Assistant", 
        description="Production-grade AI assistant"
    )
```

### Cost-Optimized Setup

```python
import parlant.sdk as p

# Start with mini, fallback to larger models if needed
async with p.Server(
    nlp_service=p.NLPServices.openai(
        generative_model_name=["gpt-4o-mini", "gpt-4o"]
    )
) as server:
    agent = await server.create_agent(
        name="Budget Assistant",
        description="Cost-optimized AI assistant"
    )
```

### Environment-Specific Configuration

```python
import os
import parlant.sdk as p

# Dynamic model selection based on environment
model_config = {
    "development": "gpt-4o-mini",
    "staging": ["gpt-4o-mini", "gpt-4o"],
    "production": ["gpt-4o", "gpt-4o-2024-08-06", "gpt-4o-mini"]
}

environment = os.getenv("ENVIRONMENT", "development")
selected_models = model_config[environment]

async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name=selected_models)
) as server:
    agent = await server.create_agent(
        name=f"{environment.title()} Assistant",
        description=f"AI assistant for {environment} environment"
    )
```

## 🔧 Advanced Features

### Schema-Specific Model Selection

The service automatically handles different schema types:

- **SingleToolBatchSchema**: Tool calling operations
- **JourneyNodeSelectionSchema**: Journey navigation
- **CannedResponseDraftSchema**: Response generation
- **CannedResponseSelectionSchema**: Response selection

All schemas use the same model when `generative_model_name` is specified, ensuring consistent behavior across all operations.

### Automatic Fallback Mechanism

When multiple models are specified, the service automatically handles:

- **Rate Limiting**: Switches to next model if current one hits rate limits
- **Model Unavailability**: Falls back if a model is temporarily unavailable  
- **API Errors**: Attempts alternative models on connection issues
- **Quota Exceeded**: Tries backup models when quota is reached

### Token Optimization

Each model class includes optimized token limits:

```python
# All models support 128K token context windows
GPT_4o.max_tokens = 128 * 1024
GPT_4o_Mini.max_tokens = 128 * 1024
GPT_4_1.max_tokens = 128 * 1024
```

## 🛠️ Best Practices

### 1. Cost Optimization

```python
# Start with smaller models, fallback to larger ones
generative_model_name=["gpt-4o-mini", "gpt-4o"]
```

### 2. Performance Optimization

```python
# Use fastest models first for better response times
generative_model_name=["gpt-4o-mini", "gpt-4o-2024-08-06"]
```

### 3. Reliability Optimization

```python
# Include multiple models for maximum uptime
generative_model_name=["gpt-4o", "gpt-4o-2024-08-06", "gpt-4o-mini"]
```

### 4. Environment Variables

```bash
# Production environment setup
export OPENAI_API_KEY="sk-your-production-key"
export ENVIRONMENT="production"
```

## 🚨 Error Handling & Troubleshooting

### Common Issues

#### 1. Authentication Errors
```
OpenAI API key not found
```
**Solution**: Ensure `OPENAI_API_KEY` is set in your environment
```bash
export OPENAI_API_KEY="sk-your-api-key-here"
```

#### 2. Model Not Found
```
Model 'custom-model' not found
```
**Solution**: Verify the model name exists in your OpenAI account and is properly spelled

#### 3. Rate Limit Errors
```
Rate limit exceeded
```
**Solution**: Use multiple models with fallback to automatically handle rate limits
```python
generative_model_name=["gpt-4o-mini", "gpt-4o"]
```

#### 4. Quota Exceeded
```
You have exceeded your quota
```
**Solution**: Check your OpenAI account billing and usage limits

### Monitoring & Logging

The service provides detailed logging for troubleshooting:

```python
# Enable detailed logging
import logging
logging.getLogger("parlant.adapters.nlp.openai_service").setLevel(logging.DEBUG)
```

### Rate Limit Management

The service includes built-in retry policies:

- **Connection errors**: Automatic retry with exponential backoff
- **Rate limits**: Automatic fallback to alternative models
- **Temporary failures**: Retry with configurable wait times

## 📊 Performance Considerations

### Model Selection Guidelines

| Use Case | Recommended Model | Reasoning |
|----------|------------------|-----------|
| **Development** | `gpt-4o-mini` | Fast, cost-effective |
| **Production (High Volume)** | `["gpt-4o-mini", "gpt-4o"]` | Cost optimization with quality fallback |
| **Production (High Quality)** | `["gpt-4o", "gpt-4o-mini"]` | Quality first with cost fallback |
| **Critical Applications** | `["gpt-4o", "gpt-4o-2024-08-06", "gpt-4o-mini"]` | Maximum reliability |

### Token Usage Optimization

- **Context Management**: All models support 128K tokens
- **Prompt Optimization**: Use concise, well-structured prompts
- **Response Caching**: Consider implementing response caching for repeated queries

## 🔗 Integration Examples

### With Custom Tools

```python
import parlant.sdk as p

async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name="gpt-4o")
) as server:
    agent = await server.create_agent(
        name="Tool Assistant",
        description="AI assistant with custom tools"
    )
    
    # Add custom tools
    @agent.tool()
    async def calculate(expression: str) -> str:
        """Calculate mathematical expressions."""
        # Tool implementation
        pass
```

### With Custom Guidelines

```python
import parlant.sdk as p

async with p.Server(
    nlp_service=p.NLPServices.openai(generative_model_name="gpt-4o-mini")
) as server:
    agent = await server.create_agent(
        name="Guided Assistant",
        description="AI assistant with custom guidelines"
    )
    
    # Add guidelines
    await agent.add_guideline(
        condition="User asks about pricing",
        action="Always mention our competitive rates and free trial"
    )
```

## 📚 Additional Resources

- [OpenAI API Documentation](https://platform.openai.com/docs)
- [OpenAI Model Pricing](https://openai.com/pricing)
- [OpenAI Rate Limits Guide](https://platform.openai.com/docs/guides/rate-limits)
- [Parlant SDK Documentation](../../README.md)

## 🆘 Support

For issues specific to the OpenAI integration:

1. **Check API Key**: Verify your OpenAI API key is valid and has sufficient credits
2. **Review Logs**: Enable debug logging to see detailed error messages
3. **Test Connectivity**: Ensure your environment can reach OpenAI's API endpoints
4. **Fallback Models**: Use multiple models to handle temporary issues

For general Parlant support, refer to the main documentation or open an issue in the repository.
