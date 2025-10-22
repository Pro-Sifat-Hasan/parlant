# Google Gemini NLP Service Documentation

The Google Gemini NLP service provides seamless integration with Google's advanced Gemini language models, offering state-of-the-art conversational AI capabilities. This adapter supports flexible model selection, automatic fallback mechanisms, and optimized configurations for different use cases.

## 🚀 Quick Start

### Prerequisites

1. **Google API Key**: Obtain an API key from [Google AI Studio](https://aistudio.google.com/app/apikey)
2. **Account Setup**: Ensure your Google account has sufficient credits and appropriate usage limits
3. **Environment Configuration**: Set up your environment variables

### Basic Setup

```bash
# Set your Google Gemini API key
export GEMINI_API_KEY="your-gemini-api-key-here"
```

### Simple Usage

```python
import parlant.sdk as p

# Use default configuration (Gemini 2.5 Flash with fallback to 2.5 Pro)
async with p.Server(
    nlp_service=p.NLPServices.gemini()
) as server:
    agent = await server.create_agent(
        name="Assistant",
        description="A helpful AI assistant powered by Gemini"
    )
```

## 🎯 Model Selection

### Default Behavior

When no model is specified, the service uses a **FallbackSchematicGenerator** with:
- 🚀 **Primary**: Gemini 2.5 Flash - Fast and efficient
- 🎯 **Fallback**: Gemini 2.5 Pro - High-quality backup
- 🔄 **Automatic switching** when rate limits or errors occur

### Single Model Configuration

Specify a single model for all operations:

```python
import parlant.sdk as p

# Use Gemini 2.5 Pro for all schemas
async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-2.5-pro")
) as server:
    agent = await server.create_agent(
        name="Premium Assistant",
        description="A high-performance AI assistant powered by Gemini Pro"
    )
```

### Multiple Models with Fallback

Configure multiple models for improved reliability:

```python
import parlant.sdk as p

# Primary: Gemini 2.5 Pro, Fallback: Gemini 2.5 Flash
async with p.Server(
    nlp_service=p.NLPServices.gemini(
        generative_model_name=["gemini-2.5-pro", "gemini-2.5-flash"]
    )
) as server:
    agent = await server.create_agent(
        name="Reliable Assistant",
        description="An AI assistant with automatic fallback"
    )
```

## 📋 Supported Models

### Gemini 2.x Series (Latest)

| Model | API Name | Description | Context Window | Best For |
|-------|----------|-------------|----------------|----------|
| **Gemini 2.5 Flash** | `gemini-2.5-flash` | Latest flash model with thinking | 1M tokens | Fast responses, most tasks |
| **Gemini 2.5 Pro** | `gemini-2.5-pro` | Latest pro model | 1M tokens | Complex reasoning, high accuracy |
| **Gemini 2.0 Flash** | `gemini-2.0-flash` | Stable 2.0 flash model | 1M tokens | Reliable performance |
| **Gemini 2.0 Flash Lite** | `gemini-2.0-flash-lite-preview-02-05` | Lightweight 2.0 variant | 1M tokens | Fast, lightweight tasks |

### Gemini 1.x Series (Stable)

| Model | API Name | Description | Context Window | Best For |
|-------|----------|-------------|----------------|----------|
| **Gemini 1.5 Flash** | `gemini-1.5-flash` | Proven flash model | 1M tokens | Production stability |
| **Gemini 1.5 Pro** | `gemini-1.5-pro` | Proven pro model | 2M tokens | Large context, complex tasks |

### Model Mapping

```python
# These model names are automatically mapped:
"gemini-1.5-flash"     → Gemini_1_5_Flash
"gemini-2.0-flash"     → Gemini_2_0_Flash  
"gemini-2.0-flash-lite-preview-02-05" → Gemini_2_0_Flash_Lite
"gemini-1.5-pro"       → Gemini_1_5_Pro
"gemini-2.5-flash"     → Gemini_2_5_Flash
"gemini-2.5-pro"       → Gemini_2_5_Pro

# Unknown models automatically fallback to Gemini_2_5_Flash
```

### Special Features

#### Gemini 2.5 Flash - Thinking Configuration
Gemini 2.5 Flash includes advanced thinking capabilities:

```python
# Automatically configured with thinking_budget: 0 for optimal performance
# This provides fast responses while maintaining high quality reasoning
```

## ⚙️ Configuration Examples

### Development Environment

```python
import parlant.sdk as p

# Fast and efficient for development
async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-2.5-flash")
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
    nlp_service=p.NLPServices.gemini(
        generative_model_name=["gemini-2.5-pro", "gemini-2.5-flash", "gemini-1.5-pro"]
    )
) as server:
    agent = await server.create_agent(
        name="Production Assistant", 
        description="Production-grade AI assistant"
    )
```

### Large Context Tasks

```python
import parlant.sdk as p

# Use Gemini 1.5 Pro for maximum context window (2M tokens)
async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-1.5-pro")
) as server:
    agent = await server.create_agent(
        name="Context Assistant",
        description="AI assistant for large document processing"
    )
```

### Speed-Optimized Setup

```python
import parlant.sdk as p

# Prioritize speed with flash models
async with p.Server(
    nlp_service=p.NLPServices.gemini(
        generative_model_name=["gemini-2.5-flash", "gemini-2.0-flash", "gemini-1.5-flash"]
    )
) as server:
    agent = await server.create_agent(
        name="Speed Assistant",
        description="Fast-response AI assistant"
    )
```

### Environment-Specific Configuration

```python
import os
import parlant.sdk as p

# Dynamic model selection based on environment
model_config = {
    "development": "gemini-2.5-flash",
    "staging": ["gemini-2.5-flash", "gemini-2.5-pro"],
    "production": ["gemini-2.5-pro", "gemini-2.5-flash", "gemini-1.5-pro"]
}

environment = os.getenv("ENVIRONMENT", "development")
selected_models = model_config[environment]

async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name=selected_models)
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

When multiple models are specified or using default configuration, the service automatically handles:

- **Rate Limiting**: Switches to next model if current one hits rate limits
- **Model Unavailability**: Falls back if a model is temporarily unavailable
- **API Errors**: Attempts alternative models on connection issues  
- **Quota Exceeded**: Tries backup models when quota is reached

### Context Window Optimization

Each model class includes optimized context limits:

```python
# Context window sizes
Gemini_1_5_Flash.max_tokens = 1024 * 1024    # 1M tokens
Gemini_2_0_Flash.max_tokens = 1024 * 1024    # 1M tokens  
Gemini_2_5_Flash.max_tokens = 1024 * 1024    # 1M tokens
Gemini_2_5_Pro.max_tokens = 1024 * 1024      # 1M tokens
Gemini_1_5_Pro.max_tokens = 2 * 1024 * 1024  # 2M tokens
```

### Supported Hints

Gemini models support advanced configuration hints:

```python
supported_hints = ["temperature", "thinking_config"]

# Example usage (handled automatically by the service):
# temperature: Control randomness (0.0 to 1.0)
# thinking_config: Configure thinking behavior for 2.5 models
```

## 🛠️ Best Practices

### 1. Speed Optimization

```python
# Use Flash models for fastest responses
generative_model_name=["gemini-2.5-flash", "gemini-2.0-flash"]
```

### 2. Quality Optimization

```python  
# Use Pro models for highest quality
generative_model_name=["gemini-2.5-pro", "gemini-1.5-pro"]
```

### 3. Reliability Optimization

```python
# Include multiple models for maximum uptime
generative_model_name=["gemini-2.5-pro", "gemini-2.5-flash", "gemini-1.5-pro"]
```

### 4. Large Context Tasks

```python
# Use 1.5 Pro for maximum context window
generative_model_name="gemini-1.5-pro"  # 2M tokens
```

### 5. Environment Variables

```bash
# Production environment setup
export GEMINI_API_KEY="your-production-key"
export ENVIRONMENT="production"
```

## 🚨 Error Handling & Troubleshooting

### Common Issues

#### 1. Authentication Errors
```
GEMINI_API_KEY is not set
```
**Solution**: Ensure `GEMINI_API_KEY` is set in your environment
```bash
export GEMINI_API_KEY="your-gemini-api-key-here"
```

#### 2. Rate Limit Errors
```
Google API rate limit exceeded
```
**Solution**: Use multiple models with fallback to automatically handle rate limits
```python
generative_model_name=["gemini-2.5-flash", "gemini-2.5-pro"]
```

The service provides detailed guidance:
- Check your Google API account balance and billing status
- Review your API usage limits in the Google Cloud Console  
- Learn more about quotas and limits at Google's documentation

#### 3. Model Not Available
```
Model 'custom-model' not found
```
**Solution**: Verify the model name exists and is properly spelled. The service will automatically fallback to Gemini 2.5 Flash.

#### 4. Quota Exceeded
```
You have exceeded your quota
```
**Solution**: Check your Google Cloud account billing and usage limits

### Monitoring & Logging

The service provides detailed logging for troubleshooting:

```python
# Enable detailed logging
import logging
logging.getLogger("parlant.adapters.nlp.gemini_service").setLevel(logging.DEBUG)
```

### Rate Limit Management

The service includes built-in retry policies for:

- **Connection errors**: Automatic retry with exponential backoff
- **Rate limits**: Automatic fallback to alternative models
- **Server errors**: Retry with configurable wait times
- **Resource exhaustion**: Intelligent model switching

## 📊 Performance Considerations

### Model Selection Guidelines

| Use Case | Recommended Model | Reasoning |
|----------|------------------|-----------|
| **Development** | `gemini-2.5-flash` | Fast, efficient |
| **Production (Speed)** | `["gemini-2.5-flash", "gemini-2.0-flash"]` | Speed optimization with reliability |
| **Production (Quality)** | `["gemini-2.5-pro", "gemini-2.5-flash"]` | Quality first with speed fallback |
| **Large Documents** | `gemini-1.5-pro` | 2M token context window |
| **Critical Applications** | `["gemini-2.5-pro", "gemini-2.5-flash", "gemini-1.5-pro"]` | Maximum reliability |

### Context Usage Optimization

- **Large Context**: Use Gemini 1.5 Pro for 2M token contexts
- **Standard Context**: Use 2.5 models for 1M token contexts  
- **Prompt Optimization**: Use concise, well-structured prompts
- **Response Caching**: Consider implementing response caching for repeated queries

## 🔗 Integration Examples

### With Custom Tools

```python
import parlant.sdk as p

async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-2.5-pro")
) as server:
    agent = await server.create_agent(
        name="Tool Assistant",
        description="AI assistant with custom tools powered by Gemini"
    )
    
    # Add custom tools
    @agent.tool()
    async def analyze_document(document: str) -> str:
        """Analyze documents with Gemini's advanced capabilities."""
        # Tool implementation
        pass
```

### With Custom Guidelines

```python
import parlant.sdk as p

async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-2.5-flash")
) as server:
    agent = await server.create_agent(
        name="Guided Assistant",
        description="AI assistant with custom guidelines"
    )
    
    # Add guidelines
    await agent.add_guideline(
        condition="User asks about product features",
        action="Provide detailed technical specifications using Gemini's reasoning capabilities"
    )
```

### Large Context Processing

```python
import parlant.sdk as p

# Use Gemini 1.5 Pro for large document processing
async with p.Server(
    nlp_service=p.NLPServices.gemini(generative_model_name="gemini-1.5-pro")
) as server:
    agent = await server.create_agent(
        name="Document Assistant",
        description="AI assistant for processing large documents (up to 2M tokens)"
    )
    
    @agent.tool()
    async def process_large_document(document: str) -> str:
        """Process documents up to 2M tokens with Gemini 1.5 Pro."""
        # Can handle very large documents
        pass
```

## 🌟 Gemini-Specific Features

### Advanced Reasoning

Gemini models excel at:
- **Complex reasoning tasks**
- **Mathematical problem solving**
- **Code generation and analysis**
- **Multi-step logical reasoning**
- **Creative content generation**

### Multimodal Capabilities

While the NLP service focuses on text, Gemini models support:
- **Text understanding and generation**
- **Code comprehension**
- **Logical reasoning**
- **Creative writing**

### Thinking Configuration

Gemini 2.5 models include thinking capabilities:
- **Automatic optimization** for response speed
- **Advanced reasoning** when needed
- **Transparent decision-making** process

## 📚 Additional Resources

- [Google AI Studio](https://aistudio.google.com/)
- [Gemini API Documentation](https://ai.google.dev/docs)
- [Google Cloud Pricing](https://cloud.google.com/vertex-ai/generative-ai/pricing)
- [Gemini Model Garden](https://ai.google.dev/models/gemini)
- [Parlant SDK Documentation](../../README.md)

## 🆘 Support

For issues specific to the Gemini integration:

1. **Check API Key**: Verify your GEMINI_API_KEY is valid and has sufficient credits
2. **Review Logs**: Enable debug logging to see detailed error messages  
3. **Test Connectivity**: Ensure your environment can reach Google's API endpoints
4. **Fallback Models**: Use multiple models to handle temporary issues
5. **Model Availability**: Verify the model you're trying to use is available in your region

For general Parlant support, refer to the main documentation or open an issue in the repository.

---

*The Google Gemini NLP service provides cutting-edge AI capabilities with enterprise-grade reliability and performance. Choose the right model configuration for your specific use case to get the best results.*
