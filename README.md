# Reva AI

AI-powered pricing intelligence assistant for retailers. Automates SKU matching, promotional analysis, and pricing scenario simulation.

## Overview

Reva AI helps retailers optimize pricing by:
- **Matching SKUs** across competitors using embeddings and fuzzy matching
- **Normalizing promos** from raw text into structured data
- **Simulating scenarios** to find optimal pricing strategies

Built with [LangGraph](https://langchain-ai.github.io/langgraph/) for reliable, production-ready AI workflows.

## Quick Start

### Prerequisites
- Python 3.12+
- [uv](https://github.com/astral-sh/uv) (fast Python package manager)

### Installation

```bash
# Clone the repository
https://github.com/akmukherjee/reva-ai-test
cd reva-ai-test

# Install dependencies
uv sync

# Set up environment variables
cp .env.example .env
# Edit .env and add your API keys
```

### Required API Keys

Add these to your `.env` file:

```bash
OPENAI_API_KEY=your-openai-key
TAVILY_API_KEY=your-tavily-key
LANGSMITH_API_KEY=your-langsmith-key  # Optional: for tracing
OPIK_API_KEY=your-opik-key            # Optional: for evaluation
```

### Run Locally

```bash
# Start the LangGraph development server
uv run langgraph dev

# The server will start at http://localhost:8123
# Open the LangGraph Studio UI to interact with workflows
```

## Architecture

The pricing workflow follows a three-stage pipeline:

```
START → Matcher → Promo Normalizer → Simulator → END
```

### Workflow Stages

1. **Matcher** - Match internal SKUs to competitor products
   - Uses embeddings + fuzzy string matching
   - Outputs confidence scores for each match

2. **Promo Normalizer** - Parse promotional text
   - Converts raw promo strings into structured data
   - Extracts discount amounts, types, and conditions

3. **Simulator** - Generate pricing scenarios
   - Models elastic demand
   - Recommends optimal prices with expected margins

### State Management

Data flows through the workflow via a typed state object:

```python
class PricingState(TypedDict):
    product_catalog: List[dict]      # Your products
    competitor_data: List[dict]      # Competitor data
    matched_skus: List[dict]         # Match results
    normalized_promos: List[dict]    # Parsed promos
    recommendation: dict             # Final output
```

## Development

### Project Structure

```
reva-ai/
├── src/
│   ├── agents/          # LangGraph workflows
│   ├── models/          # Data models
│   ├── nodes/           # Workflow nodes
│   └── services/        # External integrations
├── tests/               # Test files
├── deployment/          # Deployment configs
├── langgraph.json       # LangGraph configuration
└── pyproject.toml       # Project dependencies
```

### Commands

```bash
# Run development server
uv run langgraph dev

# Run tests
uv run pytest

# Lint code
uv run ruff check src/

# Format code
uv run ruff format src/

# Add a new dependency
uv add package-name

# Update dependencies
uv sync --upgrade
```

### Testing Workflows

```bash
# Run all tests
uv run pytest

# Test specific workflow
uv run python -c "
from src.agents.pricing_workflow import create_workflow
wf = create_workflow()
result = wf.invoke({
    'product_catalog': [{'sku': 'TV-123', 'name': 'Samsung 65\" 4K'}],
    'competitor_data': [{'sku': 'COMP-456', 'name': 'Samsung 65 inch 4K'}]
})
print(result)
"
```

## Deployment

### LangGraph Platform

This project is configured for deployment to [LangGraph Platform](https://langchain-ai.github.io/langgraph/cloud/).

1. **Connect GitHub**: Link your repository in LangGraph Platform
2. **Deploy**: Push to main branch to auto-deploy
3. **Monitor**: View traces in LangSmith

```bash
# Deploy from CLI (alternative)
langgraph deploy
```

### AWS AgentCore

Alternative deployment option using AWS Bedrock AgentCore (see deployment docs).

## Configuration

### langgraph.json

Defines available workflows:

```json
{
  "dependencies": ["."],
  "graphs": {
    "reva_pricing": "./src/agents/pricing_workflow.py:create_workflow"
  },
  "env": ".env"
}
```

### Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `OPENAI_API_KEY` | Yes | OpenAI API access |
| `TAVILY_API_KEY` | Yes | Search API access |
| `LANGSMITH_API_KEY` | Yes | LangSmith tracing |


## Resources

- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/)
- [LangGraph Platform](https://langchain-ai.github.io/langgraph/cloud/)
- [Project Proposal](docs/proposal.md) (if available)

## License

Internal project - All rights reserved
