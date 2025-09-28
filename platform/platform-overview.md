# Agentflare Platform Overview

*Generated from [/context/vision.md](../../context/vision.md) and [/context/product.md](../../context/product.md)*

## What is Agentflare?

Agentflare is the essential observability platform for AI agents, designed to be the "Cloudflare of AI Agent Infrastructure." Our flagship service, **Telepathy**, provides comprehensive observability for AI agents that captures decision reasoning, performance metrics, and cost attribution across multiple protocols.

## Core Value Proposition

> "Change one import line, instantly understand every agent decision with reasoning, performance, and cost tracking."

## Key Features

### 1. Multi-Tenant Proxy Service
- **Custom domains**: Professional endpoints like `mcp.company.com`
- **Zero deployment**: Point existing MCP clients to proxy URL
- **Model identification**: Automatic detection via self-identification
- **Cost estimation**: Real-time token usage and cost tracking
- **Multi-tenant routing**: Single infrastructure serves thousands of users

### 2. Agent Decision Intelligence
- **Live thought tracing**: Real-time stream of agent reasoning
- **Decision timeline**: Chronological view of decision sequences
- **Performance metrics**: Latency, success rates, bottleneck identification
- **Cost analytics**: Token usage and cost attribution per tool/session/model

### 3. Memory Replay System
- **Complete reconstruction**: Step-by-step replay of agent decisions
- **Reasoning visibility**: See why agents made specific choices
- **Cost breakdown**: Detailed cost analysis per decision step
- **Performance analysis**: Identify bottlenecks and optimization opportunities

### 4. Enterprise Observability Integration
Export to existing infrastructure:
- Jaeger (distributed tracing)
- Grafana (metrics and dashboards)
- Datadog (enterprise observability)
- New Relic (application performance)
- Prometheus (metrics collection)
- Custom OTLP endpoints

## Use Cases

### For Agent Developers
- **Debugging**: Understand why agents make specific decisions
- **Optimization**: Identify performance bottlenecks and improve efficiency
- **Cost Management**: Track and optimize token usage and costs
- **Compliance**: Complete audit trails for regulatory requirements

### For MCP Server Owners
- **Usage Analytics**: See how your tools are being used
- **Model Performance**: Success rates by model type (Claude vs GPT-4 vs Gemini)
- **Optimization Insights**: Improve server performance based on usage patterns
- **A/B Testing**: Route different models to different implementations

### For Enterprise Teams
- **Compliance**: Complete audit trails for regulatory compliance
- **Vendor Analysis**: Evaluate third-party MCP server performance
- **Cost Accountability**: Financial reporting and cost management
- **Risk Management**: Identify and mitigate potential issues

## Getting Started

### Quick Start
1. **For Agent Developers**: Replace your MCP SDK import with our enhanced version
2. **For Proxy Users**: Point your existing MCP clients to our proxy endpoint
3. **For Server Owners**: Connect your server to our analytics platform

### Documentation
- [Installation Guide](content/installation.mdx)
- [Quickstart Tutorial](content/quickstart.mdx)
- [API Reference](api-reference/introduction.mdx)

## Technical Architecture

The platform is built on:
- **Go-based proxy**: Multi-tenant with model-aware routing
- **Enhanced SDKs**: Drop-in replacements with OTEL instrumentation
- **Next.js dashboard**: Real-time visualization and analytics
- **PostgreSQL pipeline**: Scalable data processing and storage

## Protocol Support

Agentflare is **protocol agnostic** with first-class support for:
- MCP (Model Context Protocol)
- Future protocols as they emerge
- Custom protocol implementations

For more technical details, see our [Architecture Documentation](../dev/high-level-architecture.md).