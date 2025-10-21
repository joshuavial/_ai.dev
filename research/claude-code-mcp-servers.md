# Claude Code MCP Servers: Comprehensive Research Note

## Table of Contents
1. [Overview](#overview)
2. [Core Concepts](#core-concepts)
3. [Architecture](#architecture)
4. [Protocol Specification](#protocol-specification)
5. [Server Capabilities](#server-capabilities)
6. [Installing and Configuring MCP Servers](#installing-and-configuring-mcp-servers)
7. [Available MCP Servers](#available-mcp-servers)
8. [Creating Custom MCP Servers](#creating-custom-mcp-servers)
9. [Transports and Communication](#transports-and-communication)
10. [Security and Authentication](#security-and-authentication)
11. [Performance and Token Usage](#performance-and-token-usage)
12. [MCP vs Skills Comparison](#mcp-vs-skills-comparison)
13. [Troubleshooting and Debugging](#troubleshooting-and-debugging)
14. [Best Practices](#best-practices)
15. [Developer Resources](#developer-resources)

## Overview

### What is the Model Context Protocol (MCP)?

The Model Context Protocol (MCP) is an **open protocol that standardizes how applications provide context to LLMs**. Introduced by Anthropic in November 2024, MCP enables developers to build secure, two-way connections between their data sources and AI-powered tools.

**Official Definition (from Anthropic):**
> "The Model Context Protocol is an open standard that enables developers to build secure, two-way connections between their data sources and AI-powered tools. MCP provides a standardized way to connect AI models to different data sources and tools, similar to how USB-C standardizes device connections."

### The Problem MCP Solves

Even the most sophisticated models are constrained by their isolation from data—trapped behind information silos and legacy systems. Every new data source requires its own custom implementation, making truly connected systems difficult to scale.

**Key Challenge:**
> "MCP provides a universal, open standard for connecting AI systems with data sources, replacing fragmented integrations with a single protocol."

### Key Benefits

1. **Standardization**: One protocol instead of custom integrations for each data source
2. **Interoperability**: Works across multiple AI platforms and tools
3. **Security**: Built-in OAuth 2.1 support with proper authentication
4. **Scalability**: Connect to multiple data sources through standardized servers
5. **Open Source**: Community-driven with SDKs in Python, TypeScript, C#, Java, Go, and PHP
6. **AI-Native Design**: Dynamic capability discovery enables adaptive tool incorporation

### When to Use MCP

MCP is ideal for:
- Connecting AI systems to multiple external data sources
- Building cross-platform integrations (multiple AI hosts)
- Enterprise-grade governance and auditing requirements
- Dynamic tool discovery and scalable integrations
- Vendor-neutral, open standard approach
- Standardized access to internal systems and SaaS platforms

## Core Concepts

### MCP Components

The Model Context Protocol ecosystem consists of three main components:

1. **MCP Hosts**: Programs like Claude for Desktop, IDEs, or AI tools that want to access data through MCP
2. **MCP Clients**: Protocol clients that maintain 1:1 connections with servers
3. **MCP Servers**: Lightweight programs that each expose specific capabilities through the standardized Model Context Protocol

### Client-Host-Server Architecture

**Architecture Overview:**
> "The Model Context Protocol (MCP) follows a client-host-server architecture where each host can run multiple client instances, enabling users to integrate AI capabilities across applications while maintaining clear security boundaries and isolating concerns."

```
┌─────────────────────────────────────┐
│   MCP Host (Claude Desktop, IDE)   │
│   - Runs MCP clients                │
│   - Coordinates multiple servers    │
└──────────────┬──────────────────────┘
               │
               │ Manages
               ▼
┌─────────────────────────────────────┐
│   MCP Client (Protocol Client)      │
│   - 1:1 connection to server        │
│   - Capability negotiation          │
│   - JSON-RPC communication          │
└──────────────┬──────────────────────┘
               │
               │ Connects via Transport
               ▼
┌─────────────────────────────────────┐
│   MCP Server (Data Source)          │
│   - Exposes resources, tools, prompts│
│   - Lightweight & focused           │
│   - One server per data source      │
└─────────────────────────────────────┘
```

### Server Capabilities

MCP servers can provide three main types of capabilities:

**1. Resources**
- File-like data that can be read by clients
- Context and data for the user or AI model to use
- Each resource uniquely identified by URI
- Examples: files, database schemas, application-specific information

**2. Tools**
- Functions that can be called by the LLM with user approval
- Enable AI to perform actions on external systems
- Examples: create issues, send messages, query databases

**3. Prompts**
- Templated messages and workflows for users
- Structured messages and instructions for LLMs
- Can include embedded resources
- Examples: documentation, code samples, reference materials

### Capability Negotiation

**Core Principle:**
> "MCP uses a capability-based negotiation system where clients and servers explicitly declare their supported features during initialization, with capabilities determining which protocol features and primitives are available during a session."

Servers declare capabilities like:
- Resource subscriptions
- Tool support
- Prompt templates

Clients declare capabilities like:
- Sampling support
- Notification handling
- Root directory lists

## Architecture

### Protocol Foundation

**Built on JSON-RPC:**
> "Built on JSON-RPC, MCP provides a stateful session protocol focused on context exchange and sampling coordination between clients and servers."

**Message Types:**
All messages between MCP clients and servers MUST follow the JSON-RPC 2.0 specification. The protocol defines three fundamental types of messages:
- **Requests**: Messages requiring a response
- **Responses**: Success results or errors
- **Notifications**: One-way messages without responses

### Connection Lifecycle

The Model Context Protocol defines a rigorous lifecycle for client-server connections:

**1. Initialization Phase**
- Capability negotiation
- Protocol version agreement
- Exchange of client/server information

**2. Operation Phase**
- Normal protocol communication
- Resource access
- Tool invocation
- Prompt retrieval

**3. Shutdown Phase**
- Graceful termination of connection
- Resource cleanup

### Initialization Sequence

**Step 1: Initialize Request**

The client MUST initiate the session by sending an initialize request:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-03-26",
    "capabilities": {
      "roots": { "listChanged": true },
      "sampling": {}
    },
    "clientInfo": {
      "name": "ExampleClient",
      "version": "1.0.0"
    }
  }
}
```

**Important Rules:**
- The initialize request MUST NOT be part of a JSON-RPC batch
- No other requests/notifications are possible until initialization completes
- The client SHOULD send the latest protocol version it supports

**Step 2: Initialize Response**

The server responds with its capabilities and information:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2025-03-26",
    "capabilities": {
      "resources": { "subscribe": true, "listChanged": true },
      "tools": { "listChanged": true },
      "prompts": { "listChanged": true }
    },
    "serverInfo": {
      "name": "ExampleServer",
      "version": "1.0.0"
    }
  }
}
```

**Protocol Version Negotiation:**
- If server supports requested version, it responds with the same version
- Otherwise, server responds with another version it supports
- If client doesn't support server's version, it SHOULD disconnect

**Step 3: Initialized Notification**

After successful initialization, the client sends an initialized notification:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

### Pre-Initialization Restrictions

**Client Restrictions:**
- SHOULD NOT send requests other than pings before server responds to initialize

**Server Restrictions:**
- SHOULD NOT send requests other than pings and logging before receiving initialized notification

### Request-Response Pattern

**Key Requirements:**
- Responses MUST include the same ID as the request
- Either a result or an error MUST be set
- The ID MUST NOT be null
- Request IDs MUST NOT be reused within the same session

**Timeouts:**
> "Implementations SHOULD establish timeouts for all sent requests, to prevent hung connections and resource exhaustion. When the request has not received a success or error response within the timeout period, the sender SHOULD issue a cancellation notification for that request and stop waiting for a response."

## Protocol Specification

### Resources

**Definition:**
> "Resources allow servers to share data that provides context to language models, such as files, database schemas, or application-specific information. Each resource is uniquely identified by a URI."

**Server Requirements:**

Servers that support resources MUST:
- Declare the `resources` capability during initialization
- Indicate if `subscribe` is supported (resource change notifications)
- Indicate if `listChanged` is supported (list update notifications)

**Listing Resources:**

Clients retrieve available resources via `resources/list` request:

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "resources/list"
}
```

This operation supports pagination for large resource sets.

**Reading Resources:**

Resources are accessed by URI:

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "method": "resources/read",
  "params": {
    "uri": "file:///path/to/document.txt"
  }
}
```

**Resource Subscriptions:**

Optional feature allowing clients to receive notifications when resources change:

```json
{
  "jsonrpc": "2.0",
  "id": 4,
  "method": "resources/subscribe",
  "params": {
    "uri": "file:///path/to/document.txt"
  }
}
```

### Tools

**Definition:**
Tools are functions that can be called by the LLM to perform specific actions on external systems.

**Tool Discovery:**

Clients discover available tools via `tools/list` request:

```json
{
  "jsonrpc": "2.0",
  "id": 5,
  "method": "tools/list"
}
```

**Tool Invocation:**

Tools are called with specific arguments:

```json
{
  "jsonrpc": "2.0",
  "id": 6,
  "method": "tools/call",
  "params": {
    "name": "get-weather",
    "arguments": {
      "location": "San Francisco, CA"
    }
  }
}
```

### Prompts

**Definition:**
> "The Model Context Protocol provides a standardized way for servers to expose prompt templates to clients. Prompts allow servers to provide structured messages and instructions for interacting with language models."

**Server Requirements:**

Servers that support prompts MUST:
- Declare the `prompts` capability during initialization
- Indicate if `listChanged` is supported

**Listing Prompts:**

```json
{
  "jsonrpc": "2.0",
  "id": 7,
  "method": "prompts/list"
}
```

**Getting Prompt Details:**

```json
{
  "jsonrpc": "2.0",
  "id": 8,
  "method": "prompts/get",
  "params": {
    "name": "code-review",
    "arguments": {
      "language": "python"
    }
  }
}
```

**Embedded Resources:**
> "Embedded resources enable prompts to seamlessly incorporate server-managed content like documentation, code samples, or other reference materials directly into the conversation flow."

### Sampling

**Definition:**
> "MCP provides a standardized way for servers to request LLM sampling ('completions' or 'generations') from language models via clients. This flow allows clients to maintain control over model access, selection, and permissions while enabling servers to leverage AI capabilities—with no server API keys necessary."

**Key Benefit:**
> "Sampling in MCP allows servers to implement agentic behaviors, by enabling LLM calls to occur nested inside other MCP server features."

**Sampling Request:**

```json
{
  "jsonrpc": "2.0",
  "id": 9,
  "method": "sampling/createMessage",
  "params": {
    "messages": [
      {
        "role": "user",
        "content": {
          "type": "text",
          "text": "Analyze this data and provide insights"
        }
      }
    ],
    "maxTokens": 1000
  }
}
```

**Control Flow:**
- Client maintains control over model access and selection
- Server can request completions without API keys
- Enables nested LLM calls within server operations

## Server Capabilities

### Resources Deep Dive

**Use Cases:**
- Accessing file contents
- Reading database schemas
- Fetching application-specific configuration
- Providing reference documentation
- Sharing structured data

**URI Schemes:**
Resources use URI-based identification, supporting schemes like:
- `file://` - Local filesystem resources
- `http://` / `https://` - Web-based resources
- Custom schemes for application-specific resources

**Change Notifications:**

When subscribed, clients receive notifications:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/resources/updated",
  "params": {
    "uri": "file:///path/to/document.txt"
  }
}
```

### Tools Deep Dive

**Tool Definition Structure:**

Tools are defined with:
- Name and description
- Input schema (JSON Schema)
- Required and optional parameters

**Example Tool Definition:**

```json
{
  "name": "create-issue",
  "description": "Create a new issue in the project tracker",
  "inputSchema": {
    "type": "object",
    "properties": {
      "title": { "type": "string" },
      "description": { "type": "string" },
      "priority": {
        "type": "string",
        "enum": ["low", "medium", "high"]
      }
    },
    "required": ["title", "description"]
  }
}
```

**User Approval:**

Tools require user approval before execution, maintaining security and control.

### Prompts Deep Dive

**Prompt Templates:**

Prompts support argument-based customization:

```json
{
  "name": "code-review",
  "description": "Review code for best practices",
  "arguments": [
    {
      "name": "language",
      "description": "Programming language",
      "required": true
    },
    {
      "name": "focus",
      "description": "Areas to focus on",
      "required": false
    }
  ]
}
```

**Workflow Automation:**
> "MCP prompts enable workflow automation by combining AI capabilities with structured data access."

**Embedded Resources in Prompts:**

Prompts can reference resources for rich context:

```json
{
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Review this code following our guidelines"
      }
    },
    {
      "role": "user",
      "content": {
        "type": "resource",
        "resource": {
          "uri": "file:///docs/code-guidelines.md"
        }
      }
    }
  ]
}
```

## Installing and Configuring MCP Servers

### Claude Code MCP Integration

**Overview:**
> "Claude Code can connect to hundreds of external tools and data sources through Model Context Protocol (MCP) — an open standard for integrating AI with tools. MCP servers provide Claude Code access to your tools, databases and APIs."

### Configuration File Format

MCP servers are configured using `.mcp.json` files with the following structure:

**Basic Format:**

```json
{
  "mcpServers": {
    "server-name": {
      "command": "/path/to/server",
      "args": [],
      "env": {}
    }
  }
}
```

### Configuration Examples

**Simple Server (Sequential Thinking):**

```json
{
  "mcpServers": {
    "sequential-thinking": {
      "type": "stdio",
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-sequential-thinking"
      ]
    }
  }
}
```

**Server with Environment Variables (GitHub):**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

**Multiple Servers with Different APIs:**

```json
{
  "mcpServers": {
    "mcp-omnisearch": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "mcp-omnisearch"],
      "env": {
        "TAVILY_API_KEY": "your-tavily-key",
        "BRAVE_API_KEY": "your-brave-key",
        "KAGI_API_KEY": "your-kagi-key"
      }
    }
  }
}
```

### MCP Configuration Scopes

**Local Scope (Default):**
- Stores configurations in user-specific project settings
- Only available to you when working in the current project
- Path: Project-specific settings directory

**Project Scope:**
- Shared with entire team via `.mcp.json` in repository
- Version-controlled and consistent across team
- Central team can configure for all users to benefit

**User Scope:**
- Global configuration for all projects
- Personal MCP server preferences

### Command Line Management

**Adding MCP Servers:**

```bash
claude mcp add [options] -- <command>
```

**Syntax Rules:**
- Everything before `--` are options for Claude
- Everything after `--` is the actual command to run the MCP server

**Listing MCP Servers:**

```bash
claude mcp list
```

**Removing MCP Servers:**

```bash
claude mcp remove <server-name>
```

**Checking Status:**

Use the `/mcp` command in Claude Code to check server status at any time.

### Configuration Best Practices

**1. Use Absolute Paths:**

> "Always use absolute paths in your configuration and .env files since the working directory for servers launched via claude_desktop_config.json may be undefined (like / on macOS)."

**2. Environment Variables:**

> "MCP servers inherit only a subset of environment variables automatically, like USER, HOME, and PATH. To override the default variables or provide your own, you can specify an env key in claude_desktop_config.json."

**3. Timeout Configuration:**

Configure server startup timeout using environment variable:

```bash
MCP_TIMEOUT=10000 claude  # 10-second timeout
```

### Claude Desktop Configuration

**Configuration File Location:**

```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Example Configuration:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/files"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "<YOUR_TOKEN>"
      }
    }
  }
}
```

### Enterprise/Managed Configuration

**Managed Configuration File:**

`managed-mcp.json` uses the same format and supports:
- HTTP and stdio server types
- Company-internal server configurations
- Centralized management

## Available MCP Servers

### Official MCP Registry

**Registry Location:**
> "The official MCP Registry has been launched at https://registry.modelcontextprotocol.io, serving as a primary source of truth for publicly available MCP servers."

**Registry Features:**
- Open catalog and API for publicly available MCP servers
- Authoritative repository owned by the MCP open-source community
- OpenAPI specification for building compatible sub-registries
- Self-publishing for server creators
- Automatic discovery and listing

### Official Servers by Anthropic

Pre-built MCP servers for popular enterprise systems:

**Data Sources:**
- **Google Drive** - Access and manage Google Drive files
- **Slack** - Read and send messages, access channels
- **GitHub** - Repository management, issues, pull requests
- **Git** - Version control operations
- **Postgres** - Database queries and management
- **Puppeteer** - Web automation and scraping

### Company-Maintained Official Integrations

These are maintained by companies building production-ready MCP servers:

**Travel & Hospitality:**
- **Airbnb** - Search listings and get details

**Data & Analytics:**
- **Dolt** - Version-controlled database access
- **Twelve Data** - Real-time and historical financial market data

**Communications:**
- **Twilio** - Send SMS, manage phone numbers, configure account

### Community MCP Servers

The `modelcontextprotocol/servers` repository includes a growing collection of community servers:

**Productivity & Collaboration:**
- Apple Calendar
- Microsoft Teams
- Linear
- Asana
- Jira
- Confluence

**Development Tools:**
- MongoDB
- Redis
- Docker
- Kubernetes

**AI & Search:**
- Brave Search
- Tavily Search
- Kagi Search

**Finance:**
- PayPal
- Square
- Plaid

**Note:** Community servers are untested and should be used at your own risk, as they are not affiliated with or endorsed by Anthropic.

### Official Example Servers

**Everything Server:**

The "everything" MCP server exercises all features of the MCP protocol:
- Intended as a test server for MCP client builders
- Implements prompts, tools, resources, sampling
- Useful for testing and development

### Installing Servers via npm

Many MCP servers are available as npm packages:

```bash
# Install globally
npm install -g @modelcontextprotocol/server-github

# Use with npx (no installation)
npx -y @modelcontextprotocol/server-github
```

### Discovering Servers

**GitHub MCP Registry:**
> "Meet the GitHub MCP Registry: The fastest way to discover MCP Servers. Developers will be able to self-publish MCP servers directly to the OSS MCP Community Registry, and once published, those servers will automatically appear in the GitHub MCP Registry."

**Search and Browse:**
- Browse by category
- Search by functionality
- Filter by language (TypeScript, Python, etc.)
- Review ratings and usage statistics

## Creating Custom MCP Servers

### Development Overview

MCP servers can be built using official SDKs in multiple languages:

**Available SDKs:**
- **TypeScript** - `@modelcontextprotocol/sdk-typescript`
- **Python** - `modelcontextprotocol` (Python SDK)
- **C#** - Maintained in collaboration with Microsoft
- **Java** - Maintained in collaboration with Spring AI
- **Go** - Maintained in collaboration with Google
- **PHP** - Maintained in collaboration with The PHP Foundation

### Building with LLMs

**Accelerated Development:**
> "You can speed up MCP development using LLMs such as Claude to help build custom Model Context Protocol servers and clients."

### Python MCP Server Tutorial

**Prerequisites:**
- Python 3.9 or higher
- Understanding of async programming

**Step 1: Install Dependencies**

```bash
# Install uv (Python package manager)
pip install uv

# Create project
uv init my-mcp-server
cd my-mcp-server

# Install MCP SDK
uv add mcp
```

**Step 2: Create Server**

```python
from mcp.server.models import InitializationOptions
from mcp import types
from mcp.server import Server
import mcp.server.stdio

# Create server instance
server = Server("my-server")

# Register tool
@server.list_tools()
async def handle_list_tools() -> list[types.Tool]:
    return [
        types.Tool(
            name="get-weather",
            description="Get weather for a location",
            inputSchema={
                "type": "object",
                "properties": {
                    "location": {"type": "string"}
                },
                "required": ["location"]
            }
        )
    ]

# Implement tool
@server.call_tool()
async def handle_call_tool(
    name: str,
    arguments: dict
) -> list[types.TextContent]:
    if name == "get-weather":
        location = arguments["location"]
        # Implementation here
        return [
            types.TextContent(
                type="text",
                text=f"Weather for {location}: Sunny, 72°F"
            )
        ]

# Run server
async def main():
    async with mcp.server.stdio.stdio_server() as (read_stream, write_stream):
        await server.run(
            read_stream,
            write_stream,
            InitializationOptions(
                server_name="my-server",
                server_version="1.0.0"
            )
        )

if __name__ == "__main__":
    import asyncio
    asyncio.run(main())
```

**Step 3: Test the Server**

Use MCP Inspector (covered in Debugging section) to test your server.

### TypeScript MCP Server Tutorial

**Prerequisites:**
- Node.js 18 or higher
- TypeScript knowledge

**Step 1: Project Setup**

```bash
# Create project
mkdir my-mcp-server
cd my-mcp-server
npm init -y

# Install dependencies
npm install @modelcontextprotocol/sdk
npm install -D typescript @types/node

# Initialize TypeScript
npx tsc --init
```

**Step 2: Create Server**

```typescript
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  ListToolsRequestSchema,
  CallToolRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";

// Create server
const server = new Server(
  {
    name: "my-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// Register tool handlers
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "get-weather",
        description: "Get weather for a location",
        inputSchema: {
          type: "object",
          properties: {
            location: { type: "string" },
          },
          required: ["location"],
        },
      },
    ],
  };
});

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "get-weather") {
    const location = request.params.arguments?.location;

    return {
      content: [
        {
          type: "text",
          text: `Weather for ${location}: Sunny, 72°F`,
        },
      ],
    };
  }

  throw new Error(`Unknown tool: ${request.params.name}`);
});

// Start server
async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
}

main().catch(console.error);
```

**Step 3: Build and Run**

```bash
# Build
npx tsc

# Run
node dist/index.js
```

### Implementing Resources

**Python Example:**

```python
@server.list_resources()
async def handle_list_resources() -> list[types.Resource]:
    return [
        types.Resource(
            uri="file:///docs/guide.md",
            name="User Guide",
            description="Product user guide",
            mimeType="text/markdown"
        )
    ]

@server.read_resource()
async def handle_read_resource(uri: str) -> str:
    if uri == "file:///docs/guide.md":
        return "# User Guide\n\nWelcome to our product..."
    raise ValueError(f"Unknown resource: {uri}")
```

**TypeScript Example:**

```typescript
server.setRequestHandler(ListResourcesRequestSchema, async () => {
  return {
    resources: [
      {
        uri: "file:///docs/guide.md",
        name: "User Guide",
        description: "Product user guide",
        mimeType: "text/markdown",
      },
    ],
  };
});

server.setRequestHandler(ReadResourceRequestSchema, async (request) => {
  const uri = request.params.uri;

  if (uri === "file:///docs/guide.md") {
    return {
      contents: [
        {
          uri,
          mimeType: "text/markdown",
          text: "# User Guide\n\nWelcome to our product...",
        },
      ],
    };
  }

  throw new Error(`Unknown resource: ${uri}`);
});
```

### Implementing Prompts

**Python Example:**

```python
@server.list_prompts()
async def handle_list_prompts() -> list[types.Prompt]:
    return [
        types.Prompt(
            name="code-review",
            description="Review code for best practices",
            arguments=[
                types.PromptArgument(
                    name="language",
                    description="Programming language",
                    required=True
                )
            ]
        )
    ]

@server.get_prompt()
async def handle_get_prompt(
    name: str,
    arguments: dict
) -> types.GetPromptResult:
    if name == "code-review":
        language = arguments.get("language")
        return types.GetPromptResult(
            messages=[
                types.PromptMessage(
                    role="user",
                    content=types.TextContent(
                        type="text",
                        text=f"Review this {language} code for best practices"
                    )
                )
            ]
        )
```

### Server Best Practices

**1. Single Responsibility:**
Each server should focus on one data source or service:
- ✅ Good: `github-server`, `postgres-server`, `slack-server`
- ❌ Bad: `everything-server` (except for testing)

**2. Clear Tool Descriptions:**

```typescript
// Good
{
  name: "create-issue",
  description: "Create a new GitHub issue with title, description, and optional labels",
  // ...
}

// Bad
{
  name: "create-issue",
  description: "Creates issue",
  // ...
}
```

**3. Proper Error Handling:**

```python
@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    try:
        # Tool implementation
        pass
    except Exception as e:
        # Log error
        logging.error(f"Tool {name} failed: {e}")
        # Return user-friendly error
        return [types.TextContent(
            type="text",
            text=f"Error: {str(e)}"
        )]
```

**4. Input Validation:**

```typescript
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const args = request.params.arguments;

  // Validate required fields
  if (!args?.location) {
    throw new Error("location is required");
  }

  // Validate types
  if (typeof args.location !== "string") {
    throw new Error("location must be a string");
  }

  // Process tool call
});
```

## Transports and Communication

### Transport Overview

**Definition:**
> "The Model Context Protocol currently defines two standard transport mechanisms for client-server communication: stdio (communication over standard in and standard out) and Streamable HTTP. Clients should support stdio whenever possible."

### stdio Transport

**How It Works:**
> "In the stdio transport, the client launches the MCP server as a subprocess, and the server reads JSON-RPC messages from its standard input (stdin) and sends messages to its standard output (stdout)."

**Message Format:**
- Messages are delimited by newlines
- MUST NOT contain embedded newlines
- One complete JSON-RPC message per line

**Standard Error (stderr):**
> "The server may write UTF-8 strings to its standard error (stderr) for logging purposes, and clients may capture, forward, or ignore this logging."

**Critical Rule:**
> "MCP servers must only write JSON-RPC messages to stdout. All logs and debugging output should go to stderr instead."

**Example stdio Configuration:**

```json
{
  "mcpServers": {
    "my-server": {
      "type": "stdio",
      "command": "python",
      "args": ["/path/to/server.py"]
    }
  }
}
```

### Streamable HTTP Transport

**How It Works:**
> "In the Streamable HTTP transport, the server operates as an independent process that can handle multiple client connections using HTTP POST and GET requests, and can optionally make use of Server-Sent Events (SSE) to stream multiple server messages."

**Key Features:**
- Independent server process
- Multiple concurrent client connections
- HTTP POST for requests
- HTTP GET for server-sent events
- Replaces older HTTP+SSE transport from protocol version 2024-11-05

**Example HTTP Configuration:**

```json
{
  "mcpServers": {
    "remote-server": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer <token>"
      }
    }
  }
}
```

### Custom Transports

**Extensibility:**
> "Clients and servers may implement additional custom transport mechanisms to suit their specific needs, as the protocol is transport-agnostic and can be implemented over any communication channel that supports bidirectional message exchange, but implementers must ensure they preserve the JSON-RPC message format and lifecycle requirements defined by MCP."

**Requirements:**
- Preserve JSON-RPC message format
- Support bidirectional message exchange
- Maintain lifecycle requirements
- Handle initialization sequence

### Transport Comparison

| Aspect | stdio | Streamable HTTP |
|--------|-------|----------------|
| **Deployment** | Subprocess | Independent server |
| **Connections** | 1:1 (one client) | Many:1 (multiple clients) |
| **Complexity** | Simple | More complex |
| **Use Case** | Local tools | Remote services |
| **Networking** | No network | Requires network |
| **Scaling** | Per-client process | Shared server |

## Security and Authentication

### Security Overview

**Critical Importance:**
> "While adopting MCP brings significant advantages, it's essential for developers and organizations to implement it thoughtfully, adhering to security best practices. By verifying sources, conducting thorough code reviews, securely managing credentials, and enforcing strong authentication, organizations can confidently leverage MCP's benefits while ensuring robust security and reliability."

### OAuth 2.1 Authentication

**Official Requirement:**
> "MCP auth implementations MUST implement OAuth 2.1 with appropriate security measures for both confidential and public clients."

**PKCE Requirement:**
> "Built-in Security Baseline (PKCE): Mandating PKCE for all clients significantly raises the bar for security, protecting against common attacks right out of the box."

**March 2025 Update:**
> "In March 2025, the MCP specification took a major leap forward with the release of a new specification that standardizes Authorization using OAuth 2.1. The new specification enables MCP clients and servers to make use of OAuth 2.1, to delegate authorization."

### External Identity Provider Integration

**Recommended Architecture:**
> "The pull request titled 'Update the Authorization specification for MCP servers' suggested that MCP servers stop functioning as OAuth providers and instead integrate with external Identity Providers (IdPs). Under this model, MCP clients connect through standard OAuth 2.1 flows with established services like Auth0, Azure AD, or GitHub OAuth to obtain access tokens. The MCP server then validates these tokens when authorizing requests, functioning purely as a resource server. This approach shifts authentication responsibilities to well-tested identity platforms, significantly reducing security risks from custom authentication implementations."

**Supported Identity Providers:**
- Auth0
- Azure AD
- GitHub OAuth
- Google OAuth
- Okta
- Other OAuth 2.1 compliant providers

### Session Management

**Session Security Requirements:**

> "MCP Servers MUST NOT use sessions for authentication. MCP servers MUST use secure, non-deterministic session IDs. Generated session IDs (e.g., UUIDs) SHOULD use secure random number generators."

**Best Practices:**
- Use cryptographically secure random generators
- Implement session timeouts
- Rotate session IDs
- Clear sessions on logout

### Request Verification

**Server Authentication:**
> "MCP servers that implement authorization MUST verify all inbound requests. An MCP server should reject requests from any client that isn't properly authenticated."

**Verification Steps:**
1. Validate authentication token
2. Check token expiration
3. Verify token signature
4. Confirm scope/permissions
5. Rate limit requests

### Security Best Practices

**For Organizations:**

1. **Establish Approval Workflow**
   - Formal process for MCP server installation
   - Security review requirements
   - Change management procedures

2. **Maintain Server Allowlist**
   - Approved servers registry
   - Version pinning
   - Regular security audits

3. **Verify Sources**
   - Only install from trusted sources
   - Review server code before deployment
   - Check community reputation

4. **Credential Management**
   - Store credentials securely (vault, secrets manager)
   - Rotate credentials regularly
   - Use environment variables, never hardcode
   - Implement least-privilege access

5. **Monitoring and Auditing**
   - Log all MCP server interactions
   - Monitor for anomalous behavior
   - Track resource access patterns
   - Alert on security events

**For Developers:**

1. **Code Review**
   - Thoroughly audit server code
   - Review dependencies
   - Check for vulnerabilities
   - Validate input handling

2. **Secure Coding Practices**
   - Validate all inputs
   - Sanitize outputs
   - Use parameterized queries
   - Implement proper error handling

3. **Minimize Permissions**
   - Request only necessary scopes
   - Implement role-based access control
   - Follow principle of least privilege

4. **Testing**
   - Security testing in isolated environment
   - Penetration testing
   - Fuzzing tool inputs
   - Test authentication flows

**For Individual Users:**

1. **Verify Sources**
   - Only install from official registry or trusted developers
   - Check GitHub stars and activity
   - Read reviews and documentation

2. **Review Permissions**
   - Understand what access the server requires
   - Question excessive permissions
   - Monitor server behavior

3. **Keep Updated**
   - Update servers regularly
   - Monitor security advisories
   - Remove unused servers

### Common Security Risks

**1. Prompt Injection**

Similar risks to Claude Skills apply to MCP:
- Malicious inputs could manipulate server behavior
- Untrusted data should be sanitized
- Implement input validation

**2. Credential Exposure**

- Never hardcode credentials in configuration
- Use environment variables or secret managers
- Rotate credentials regularly
- Implement secure credential storage

**3. Unauthorized Access**

- Properly implement OAuth 2.1 flows
- Validate all tokens
- Implement rate limiting
- Monitor for abuse patterns

**4. Man-in-the-Middle Attacks**

- Use HTTPS for remote servers
- Validate TLS certificates
- Implement certificate pinning for high-security scenarios

### Secure Configuration Examples

**Using Environment Variables:**

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**With External Secrets Manager:**

```bash
# Fetch from secrets manager before starting
export GITHUB_TOKEN=$(vault kv get -field=token secret/github)
claude code
```

### Security Checklist

- [ ] OAuth 2.1 properly implemented with PKCE
- [ ] Credentials stored in secure vault/manager
- [ ] All inputs validated and sanitized
- [ ] Outputs properly escaped
- [ ] HTTPS used for all remote connections
- [ ] Rate limiting implemented
- [ ] Logging and monitoring configured
- [ ] Regular security audits scheduled
- [ ] Incident response plan in place
- [ ] Server code reviewed and approved
- [ ] Dependencies scanned for vulnerabilities
- [ ] Permissions follow least-privilege principle

## Performance and Token Usage

### Token Usage Challenges

**Core Issue:**
> "MCP experiments show that the data returned by an API can impact model token usage, which could lead to a huge LLM token bill."

**Unique Bottlenecks:**
> "MCP servers deal with AI models that can fire off hundreds of requests in a single, complex thought process, creating very unique bottlenecks."

### Token Optimization Strategies

**1. JSON Payload Optimization**

**Massive Impact:**
> "Simply trimming JSON payloads to the bare essentials can reduce their size by a staggering 60-80%."

**Why It Matters:**
> "AI models consume every byte of data you send, and unnecessary properties directly impact both response time and token usage."

**Example - Before:**

```json
{
  "id": "12345",
  "created_at": "2025-10-21T10:00:00Z",
  "updated_at": "2025-10-21T10:30:00Z",
  "metadata": {
    "internal_id": "xyz",
    "system_flags": ["flag1", "flag2"]
  },
  "user": {
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+1234567890",
    "address": "123 Main St"
  },
  "status": "active"
}
```

**After Optimization:**

```json
{
  "user": "John Doe",
  "status": "active"
}
```

**2. Alternative Response Formats**

**Plain Text Advantage:**
> "The plain text version uses roughly 80% fewer tokens because it eliminates JSON overhead: the curly braces, square brackets, property names, and quotes that don't carry semantic meaning for the AI."

**Example Comparison:**

JSON Response (120 tokens):
```json
{
  "results": [
    {"name": "Item 1", "value": 100},
    {"name": "Item 2", "value": 200},
    {"name": "Item 3", "value": 300}
  ]
}
```

Plain Text Response (24 tokens):
```
Item 1: 100
Item 2: 200
Item 3: 300
```

**3. Caching Implementation**

**Core Benefit:**
> "By caching responses and contextual data, an MCP server prevents the system from repeatedly sending the same information to the model. This reuse of previously processed context can lead to a massive reduction in token usage, directly lowering the operational costs."

**Caching Strategies:**
- Cache frequently accessed resources
- Implement time-based cache expiration
- Use content-based cache invalidation
- Share cache across multiple requests

**Example Caching Implementation (Python):**

```python
from functools import lru_cache
from datetime import datetime, timedelta

class CachedMCPServer:
    def __init__(self):
        self.cache = {}
        self.cache_ttl = timedelta(minutes=5)

    async def get_resource(self, uri: str):
        # Check cache
        if uri in self.cache:
            cached_data, cached_time = self.cache[uri]
            if datetime.now() - cached_time < self.cache_ttl:
                return cached_data

        # Fetch fresh data
        data = await self.fetch_resource(uri)

        # Update cache
        self.cache[uri] = (data, datetime.now())

        return data
```

### Cost Reduction Impact

**Real-World Results:**
> "One case study showed a 93-98% reduction in tokens, which translated from dollars to fractions of a penny for complex queries."

**Cost Formula:**
> "When you reduce token consumption, you're directly reducing your API costs, which can be substantial at an enterprise scale."

**Example Cost Calculation:**

Without optimization:
- Query: 50 requests/day
- Average tokens: 1000 per request
- Total: 50,000 tokens/day
- Cost (at $0.003/1K tokens): $0.15/day = $54.75/year

With optimization (80% reduction):
- Query: 50 requests/day
- Average tokens: 200 per request (80% reduction)
- Total: 10,000 tokens/day
- Cost (at $0.003/1K tokens): $0.03/day = $10.95/year
- **Savings: $43.80/year per feature** (80% reduction)

At enterprise scale (1000 queries/day): $876/year savings

### Performance Best Practices

**1. Measure and Monitor**

> "Cost impact assessment: Measure the cost impact of different token strategies. Efficiency evaluation: Evaluate the efficiency of current token usage. Implement various token optimization techniques based on analysis results."

**Monitoring Tools:**
- Implement token usage tracking
- Create dashboards for cost visibility
- Set up alerts for unusual patterns
- Regular cost reviews

**2. Context Window Management**

> "Choose an LLM with a context window that's large enough to meet your MCP server's operational requirements."

**Context Window Considerations:**
- Understand your data requirements
- Select appropriate model for task
- Balance capability vs. cost
- Use smaller models when possible

**3. Data Volume Optimization**

> "Ensure the MCP server provides the right volume of data to the LLM – be cautious not to supply it with too many tools or redundant data."

**Guidelines:**
- Limit number of tools exposed
- Provide only necessary data
- Use resource subscriptions wisely
- Implement pagination for large datasets

**4. Compression Techniques**

> "Compression algorithms: Implement context compression algorithms. Caching systems: Deploy intelligent caching systems for token reuse. Monitoring tools: Implement comprehensive token usage monitoring."

**Compression Strategies:**
- Summarize long documents
- Extract key information
- Use semantic compression
- Remove redundant context

**5. API Format Optimization**

> "If you control the API format, optimize it to work with your MCP server."

**Optimization Tactics:**
- Design API responses for AI consumption
- Minimize unnecessary fields
- Use consistent, concise naming
- Consider plain text alternatives

### MCP vs Skills Token Comparison

| Approach | Initial Token Cost | Loading Behavior | Scalability |
|----------|-------------------|------------------|-------------|
| **MCP** | Thousands of tokens per server | Front-loaded, always in context | Limited by context window |
| **Skills** | 30-50 tokens per skill | Progressive, on-demand | Hundreds possible |

**Token Efficiency Example:**

MCP Scenario:
- 5 MCP servers
- 5,000 tokens each (tool definitions, schemas)
- Total: 25,000 tokens always in context
- Cost: ~$0.075 per request (input tokens)

Skills Scenario:
- 50 Skills available
- 40 tokens each (metadata)
- Total: 2,000 tokens until activation
- Cost: ~$0.006 per request (input tokens)
- **~92% reduction in baseline token cost**

### Performance Optimization Checklist

- [ ] JSON payloads trimmed to essentials
- [ ] Alternative formats (plain text) considered
- [ ] Caching implemented for frequently accessed data
- [ ] Token usage monitoring enabled
- [ ] Cost alerts configured
- [ ] Context window appropriately sized
- [ ] Tool count minimized
- [ ] Data volume optimized
- [ ] Compression algorithms deployed
- [ ] API responses designed for AI consumption
- [ ] Regular performance reviews scheduled

## MCP vs Skills Comparison

### Overview

**Core Distinction:**
> "As of October 2025, teams have three distinct ways to extend LLMs: Claude Skills, the Model Context Protocol (MCP), and platform-specific tools/plugins. They overlap, but they're not substitutes."

### Claude Skills Characteristics

**Definition:**
> "Claude Skills are modular, reusable 'task packs' that teach Claude a repeatable workflow. A Skill is a folder with a Markdown file (YAML frontmatter + instructions) plus optional scripts/resources; Claude scans metadata and loads relevant Skills on demand (token-efficient)."

**Strengths:**
- Token efficiency via progressive loading
- Excellent for structured, repeatable tasks
- Self-contained with minimal external dependencies
- Can leverage Claude's native code execution
- Simple to create (Markdown + YAML)

**Ideal Use Cases:**
- Document/report generation
- Style checks and compliance
- Spreadsheet transformations
- Workflow automation
- Template-based tasks

### MCP Characteristics

**Definition:**
> "The Model Context Protocol (MCP) is an open standard for connecting AI assistants to the systems where data lives, including content repositories, business tools, and development environments. Its aim is to help frontier models produce better, more relevant responses."

**Strengths:**
- Platform-agnostic standard
- Dynamic capability discovery
- Enterprise-grade governance
- Multi-platform compatibility
- Standardized external integrations

**AI-Native Design:**
> "MCP's distinguishing feature is its AI-native design. Traditional APIs were optimized for static, predefined interactions, whereas MCP supports dynamic capability discovery. At runtime, an MCP client can query the server to learn which functions are available, enabling AI agents to adaptively incorporate new tools without hardcoded logic."

**Ideal Use Cases:**
- Connecting to multiple external data sources
- Building cross-platform integrations
- Enterprise systems requiring auditing
- Dynamic tool discovery
- Vendor-neutral architectures

### Detailed Comparison Table

| Aspect | Claude Skills | MCP |
|--------|---------------|-----|
| **Complexity** | Simple: Markdown + YAML | Complex: JSON-RPC, servers, protocols |
| **Setup** | Drop files in folder or upload ZIP | Configure servers, manage connections |
| **Token Usage** | 30-50 tokens per skill (until used) | Thousands of tokens per server (always) |
| **Context Impact** | Minimal until activated | Front-loaded, always consuming context |
| **Cross-Platform** | Claude.ai, Claude Code, API | Requires MCP-compatible client |
| **Learning Curve** | Low (Markdown knowledge) | High (protocol specification, JSON-RPC) |
| **Maintenance** | Simple file updates | Server maintenance, protocol upgrades |
| **External Data** | Limited to bundled resources | Full external system integration |
| **Dynamic Discovery** | Metadata-based, static | Runtime capability negotiation |
| **Governance** | Version control, file-based | Centralized server management |
| **Scalability** | Hundreds of skills possible | Limited by token costs |
| **Development Time** | Minutes to hours | Hours to days |

### Trade-offs

**Skills Trade-offs:**
- Limited to data bundled in skill package
- No real-time external data access
- Simpler but less flexible

**MCP Trade-offs:**
> "Requires developer time to author/operate servers; hosting and authN/Z must be designed. Adds an extra hop for remote servers (latency); monitoring and SLOs become your responsibility."

### When to Use Each

**Use Claude Skills When:**
- Tasks are structured and repeatable
- Workflows can be templated
- Data is relatively static or bundled
- Token efficiency is critical
- Simple setup is preferred
- Working primarily within Claude ecosystem
- Team needs minimal technical overhead

**Use MCP When:**
- Need real-time external data access
- Connecting to multiple enterprise systems
- Building cross-platform integrations
- Require standardized governance
- Dynamic tool discovery is valuable
- Vendor-neutral approach is important
- Have resources for server development and maintenance

### Complementary Usage

**Skills and MCP Together:**
> "A Claude Skill can instruct Claude to call tools exposed via MCP or a platform's native tool-calling. The Skill is the task abstraction; MCP (or a platform's tools) is the integration mechanism."

**Example Workflow:**

```markdown
---
name: github-workflow
description: Automated GitHub workflow management using MCP tools
---

# GitHub Workflow Skill

## Instructions

1. Use GitHub MCP to access repository data
2. Analyze pull requests and issues
3. Generate status reports using templates
4. Create automated responses following style guide

## Process

1. Connect to GitHub via MCP server
2. Fetch data using MCP tools
3. Apply skill's analysis templates
4. Generate formatted output
5. Post results back via MCP tools

[Skill provides the workflow logic, MCP provides GitHub access]
```

**Benefit of Combining:**
- Skills handle workflow and logic
- MCP handles external integrations
- Best of both: efficiency + connectivity
- Clear separation of concerns

### Decision Framework

**Step 1: Assess Requirements**

Questions to ask:
- Is real-time external data required?
- How many external systems need integration?
- What's the technical expertise of the team?
- How critical is token efficiency?
- Is cross-platform support needed?
- What's the maintenance capacity?

**Step 2: Evaluate Complexity**

Simple workflows → Skills
Complex integrations → MCP
Hybrid workflows → Both

**Step 3: Consider Scale**

Small team, simple needs → Skills
Enterprise, multiple systems → MCP
Growing organization → Start Skills, add MCP

**Step 4: Token Budget**

Tight token budget → Skills
Enterprise budget, need flexibility → MCP
Balanced approach → Selective MCP + Skills

### Migration Considerations

**From Skills to MCP:**

When to consider:
- Growing need for external data access
- Multiple systems requiring integration
- Cross-platform requirements emerging

**From MCP to Skills:**

When to consider:
- Token costs becoming unsustainable
- Workflows becoming more standardized
- External dependencies can be reduced

**Hybrid Approach:**

Best practice:
- Start with Skills for core workflows
- Add MCP servers for necessary external integrations
- Use Skills to orchestrate MCP tool calls
- Maintain clear separation between workflow logic and data access

### Real-World Architecture Example

**Hybrid Implementation:**

```
┌─────────────────────────────────────┐
│   Claude with Skills & MCP          │
│                                     │
│   ┌─────────────────────────────┐  │
│   │ Skills (Workflow Logic)     │  │
│   │ - Report generation         │  │
│   │ - Style enforcement         │  │
│   │ - Template application      │  │
│   └──────────┬──────────────────┘  │
│              │ orchestrates         │
│              ▼                      │
│   ┌─────────────────────────────┐  │
│   │ MCP Servers (Data Access)   │  │
│   │ - GitHub (code data)        │  │
│   │ - Slack (communications)    │  │
│   │ - Database (metrics)        │  │
│   └─────────────────────────────┘  │
└─────────────────────────────────────┘
```

**Benefits of This Architecture:**
- Skills provide token-efficient workflow templates
- MCP enables real-time data access
- Clear responsibility boundaries
- Optimal cost-performance balance

## Troubleshooting and Debugging

### Common Issues

**1. Connection Problems**

**Symptoms:**
- "Connection refused"
- "Error connecting to MCP server"
- "Server disconnected"
- "Client transport closed unexpectedly"

**Root Causes:**
> "The root cause is often an incorrect server command in the client's configuration, an invalid path to the runtime executable (like npx), or environment path issues. For local servers, it can also mean the server process failed to start due to a dependency issue or a syntax error in its code."

**Solutions:**

1. **Verify Configuration:**
```json
{
  "mcpServers": {
    "my-server": {
      "command": "/absolute/path/to/executable",  // Use absolute paths
      "args": ["correct", "arguments"]
    }
  }
}
```

2. **Check Server Process:**
```bash
# Test server directly
python /path/to/server.py
# or
node /path/to/server.js

# Check for errors in output
```

3. **Verify Environment:**
```bash
# Check PATH
echo $PATH

# Verify executable exists
which npx
which python
```

**2. JSON-RPC Format Problems**

**Critical Rule:**
> "A critical rule: MCP servers must only write JSON-RPC messages to stdout. All logs and debugging output should go to stderr instead. The MCP protocol relies strictly on stdout for all JSON-RPC messages. Any non-protocol output (e.g., logs, print statements, banners) sent to stdout will corrupt the protocol stream."

**Fix:**
> "Redirect all logs and non-protocol output to stderr."

**Python Example:**

```python
import sys
import logging

# Configure logging to stderr
logging.basicConfig(
    stream=sys.stderr,
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)

# Use logging instead of print
logging.info("Server starting...")  # Goes to stderr ✓
# print("Server starting...")       # Would corrupt stdout ✗
```

**TypeScript Example:**

```typescript
// Use console.error for logs (goes to stderr)
console.error("Server starting...");  // ✓

// Don't use console.log (goes to stdout)
// console.log("Server starting...");  // ✗
```

**3. Protocol Version Mismatches**

**Issue:**
Server and client protocol versions don't match

**Solution:**
> "To fix protocol version issues, ensure your server supports the version expected by the host application. Check both the initialization request and response to confirm the versions match."

**Implementation:**

```python
# Support multiple protocol versions
SUPPORTED_VERSIONS = ["2025-03-26", "2024-11-05"]

async def handle_initialize(request):
    client_version = request.params.protocolVersion

    if client_version in SUPPORTED_VERSIONS:
        # Use client's version
        return InitializeResult(
            protocolVersion=client_version,
            # ...
        )
    else:
        # Offer alternative
        return InitializeResult(
            protocolVersion=SUPPORTED_VERSIONS[0],
            # ...
        )
```

**4. Timeout Issues**

**Symptoms:**
- Server fails to start
- Connection hangs
- No response from server

**Solutions:**

1. **Increase Timeout:**
```bash
# Set 10-second timeout
MCP_TIMEOUT=10000 claude
```

2. **Check Server Startup:**
```python
# Ensure server starts quickly
async def main():
    # Avoid slow initialization
    # Load heavy resources lazily

    # Start server immediately
    await server.run(...)
```

**5. Environment Variable Issues**

**Problem:**
> "MCP servers inherit only a subset of environment variables automatically, like USER, HOME, and PATH."

**Solution:**
Explicitly set required environment variables in configuration:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_KEY": "your-key-here",
        "DATABASE_URL": "postgresql://...",
        "LOG_LEVEL": "INFO"
      }
    }
  }
}
```

**6. Path Issues**

**Problem:**
> "Always use absolute paths in your configuration and .env files since the working directory for servers launched via claude_desktop_config.json may be undefined (like / on macOS)."

**Solution:**

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "/usr/local/bin/npx",  // Absolute path
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/Users/username/allowed/path"  // Absolute path
      ]
    }
  }
}
```

### Debugging Tools

**MCP Inspector**

**Overview:**
> "The MCP Inspector is an invaluable debugging tool that lets you test MCP servers directly without a host application."

**Installation:**

```bash
npm install -g @modelcontextprotocol/inspector
```

**Usage:**

```bash
# Launch inspector with your server
mcp-inspector python /path/to/server.py

# Or with npx
mcp-inspector npx -y @modelcontextprotocol/server-github
```

**Features:**
> "This launches a web interface (default: http://localhost:5173) where you can interact with your server and see exactly what's happening."

**Web Interface Capabilities:**
- View all available tools, resources, and prompts
- Test tool invocations with custom arguments
- Inspect JSON-RPC messages
- Monitor server responses
- Debug capability negotiation
- Test error handling

### Logging Best Practices

**Foundation of Debugging:**
> "Effective logging is the foundation of MCP server debugging. Without visibility into what's happening, you're troubleshooting blind."

**For Local stdio Servers:**
> "When building a server that uses the local stdio transport, all messages logged to stderr (standard error) will be captured by the host application (e.g., Claude Desktop) automatically. Local MCP servers should not log messages to stdout (standard out), as this will interfere with protocol operation."

**Logging Implementation (Python):**

```python
import logging
import sys

# Configure logging to stderr
logging.basicConfig(
    stream=sys.stderr,
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    logger.info(f"Tool called: {name}")
    logger.debug(f"Arguments: {arguments}")

    try:
        result = await execute_tool(name, arguments)
        logger.info(f"Tool {name} succeeded")
        return result
    except Exception as e:
        logger.error(f"Tool {name} failed: {e}", exc_info=True)
        raise
```

**Logging Implementation (TypeScript):**

```typescript
// Use console.error for all logging
const log = {
  info: (msg: string) => console.error(`[INFO] ${msg}`),
  error: (msg: string) => console.error(`[ERROR] ${msg}`),
  debug: (msg: string) => console.error(`[DEBUG] ${msg}`),
};

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  log.info(`Tool called: ${request.params.name}`);
  log.debug(`Arguments: ${JSON.stringify(request.params.arguments)}`);

  try {
    const result = await executeTool(request.params);
    log.info(`Tool ${request.params.name} succeeded`);
    return result;
  } catch (error) {
    log.error(`Tool ${request.params.name} failed: ${error}`);
    throw error;
  }
});
```

### Systematic Debugging Approach

**Step-by-Step Process:**
> "Connection issues often stem from misconfiguration (paths, ports, transport type), server startup failures, or forgetting to activate the transport listener. Systematically check each step from server launch to host configuration."

**Debugging Checklist:**

1. **Verify Server Runs Standalone**
   ```bash
   # Test server independently
   python server.py
   # Should show no errors
   ```

2. **Check Configuration Syntax**
   ```bash
   # Validate JSON
   cat .mcp.json | python -m json.tool
   ```

3. **Test with MCP Inspector**
   ```bash
   mcp-inspector python server.py
   # Visit http://localhost:5173
   ```

4. **Verify Protocol Compliance**
   - Check initialization sequence
   - Verify message format
   - Confirm capability declaration

5. **Monitor Logs**
   ```bash
   # Watch Claude Desktop logs (macOS)
   tail -f ~/Library/Logs/Claude/mcp*.log
   ```

6. **Test Individual Features**
   - List resources
   - List tools
   - Call each tool
   - Get prompts

7. **Check Error Handling**
   - Test with invalid inputs
   - Verify error messages
   - Confirm graceful failures

### Testing Best Practices

**Comprehensive Testing:**
> "Test Systematically: Use unit tests for logic and integration tests (via Inspector or scripts) to simulate host interactions, covering success and failure paths for all tools/resources."

**Unit Testing (Python):**

```python
import pytest
from server import MyMCPServer

@pytest.mark.asyncio
async def test_tool_execution():
    server = MyMCPServer()

    # Test successful execution
    result = await server.call_tool(
        "get-weather",
        {"location": "San Francisco"}
    )
    assert result is not None

    # Test error handling
    with pytest.raises(ValueError):
        await server.call_tool(
            "get-weather",
            {"location": ""}  # Invalid input
        )
```

**Integration Testing:**

```python
import asyncio
from mcp import ClientSession

async def test_server_integration():
    # Start server
    async with ClientSession(...) as session:
        # Initialize
        await session.initialize()

        # List tools
        tools = await session.list_tools()
        assert len(tools) > 0

        # Call tool
        result = await session.call_tool("get-weather", {
            "location": "San Francisco"
        })
        assert result is not None
```

### Common Error Messages and Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| "Connection refused" | Server not running or wrong port | Verify server process, check configuration |
| "Protocol version mismatch" | Client/server version incompatibility | Update server or client to compatible version |
| "Invalid JSON-RPC message" | Malformed message or stdout pollution | Check for logs/prints to stdout, use stderr |
| "Unknown tool" | Tool not registered or typo in name | Verify tool name, check registration |
| "Timeout" | Server slow to start or respond | Increase timeout, optimize server startup |
| "Authentication failed" | Invalid credentials or token | Check API keys, verify OAuth configuration |

### Debug Mode Configuration

**Enable Verbose Logging:**

```json
{
  "mcpServers": {
    "my-server": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "LOG_LEVEL": "DEBUG",
        "MCP_DEBUG": "1"
      }
    }
  }
}
```

**Monitor All Traffic:**

```python
import logging

# Log all JSON-RPC messages
class DebugTransport:
    async def send(self, message):
        logging.debug(f"Sending: {message}")
        await self.transport.send(message)

    async def receive(self):
        message = await self.transport.receive()
        logging.debug(f"Received: {message}")
        return message
```

### Performance Debugging

**Identify Slow Operations:**

```python
import time

async def handle_call_tool(name: str, arguments: dict):
    start = time.time()

    result = await execute_tool(name, arguments)

    duration = time.time() - start
    logging.info(f"Tool {name} took {duration:.2f}s")

    if duration > 1.0:
        logging.warning(f"Slow tool execution: {name}")

    return result
```

**Profile Server:**

```bash
# Python profiling
python -m cProfile -o profile.stats server.py

# Analyze results
python -m pstats profile.stats
```

## Best Practices

### Server Development Best Practices

**1. Single Responsibility Principle**

Each MCP server should focus on one data source or service:

✅ **Good:**
- `github-server` - GitHub integration only
- `postgres-server` - Database access only
- `slack-server` - Slack communication only

❌ **Bad:**
- `everything-server` - Multiple unrelated services
- `company-tools` - Kitchen sink approach

**2. Clear Descriptions**

Provide detailed, specific descriptions for all capabilities:

**Tools:**
```typescript
// Good
{
  name: "create-issue",
  description: "Create a new GitHub issue with title, description, labels, and assignees. Requires repository name in owner/repo format.",
  // ...
}

// Bad
{
  name: "create-issue",
  description: "Creates issue",
  // ...
}
```

**Resources:**
```python
# Good
types.Resource(
    uri="file:///docs/api-guide.md",
    name="API Integration Guide",
    description="Complete guide for integrating with our REST API, including authentication, endpoints, rate limits, and examples",
    mimeType="text/markdown"
)

# Bad
types.Resource(
    uri="file:///docs/api-guide.md",
    name="Guide",
    description="API docs"
)
```

**3. Robust Error Handling**

Implement comprehensive error handling with user-friendly messages:

```python
@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    try:
        # Validate inputs
        if not arguments.get("required_field"):
            raise ValueError("required_field is mandatory")

        # Execute tool
        result = await execute_tool(name, arguments)

        return [types.TextContent(
            type="text",
            text=f"Success: {result}"
        )]

    except ValueError as e:
        # User input error
        logging.warning(f"Invalid input for {name}: {e}")
        return [types.TextContent(
            type="text",
            text=f"Invalid input: {str(e)}"
        )]

    except ConnectionError as e:
        # External service error
        logging.error(f"Connection failed for {name}: {e}")
        return [types.TextContent(
            type="text",
            text="Unable to connect to service. Please try again later."
        )]

    except Exception as e:
        # Unexpected error
        logging.error(f"Unexpected error in {name}: {e}", exc_info=True)
        return [types.TextContent(
            type="text",
            text="An unexpected error occurred. Please contact support."
        )]
```

**4. Input Validation**

Validate all inputs thoroughly:

```typescript
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  // Type checking
  if (typeof args?.location !== "string") {
    throw new Error("location must be a string");
  }

  // Range validation
  if (args.limit && (args.limit < 1 || args.limit > 100)) {
    throw new Error("limit must be between 1 and 100");
  }

  // Format validation
  if (args.email && !isValidEmail(args.email)) {
    throw new Error("invalid email format");
  }

  // Required fields
  if (!args.required_field) {
    throw new Error("required_field is mandatory");
  }

  // Proceed with validated inputs
  return await executeTool(name, args);
});
```

**5. Efficient Resource Management**

**Pagination for Large Datasets:**

```python
@server.list_resources()
async def handle_list_resources(
    cursor: Optional[str] = None
) -> types.ListResourcesResult:
    page_size = 50
    offset = int(cursor) if cursor else 0

    # Fetch page of resources
    resources = await fetch_resources(offset, page_size)

    # Check if more available
    next_cursor = str(offset + page_size) if len(resources) == page_size else None

    return types.ListResourcesResult(
        resources=resources,
        nextCursor=next_cursor
    )
```

**Lazy Loading:**

```python
@server.read_resource()
async def handle_read_resource(uri: str) -> str:
    # Don't load all resources at once
    # Load only when requested

    if uri.startswith("file:///large/"):
        # Stream large files
        return await stream_large_file(uri)
    else:
        # Load smaller files directly
        return await load_file(uri)
```

**6. Caching Strategy**

```python
from functools import lru_cache
from datetime import datetime, timedelta

class MCPServer:
    def __init__(self):
        self.cache = {}
        self.cache_ttl = timedelta(minutes=5)

    async def get_resource(self, uri: str):
        # Check cache
        if uri in self.cache:
            data, timestamp = self.cache[uri]
            if datetime.now() - timestamp < self.cache_ttl:
                logging.debug(f"Cache hit for {uri}")
                return data

        # Fetch fresh data
        logging.debug(f"Cache miss for {uri}")
        data = await self.fetch_resource(uri)

        # Update cache
        self.cache[uri] = (data, datetime.now())

        return data
```

**7. Security Best Practices**

**Credential Management:**

```python
import os
from cryptography.fernet import Fernet

class SecureMCPServer:
    def __init__(self):
        # Load credentials from environment
        self.api_key = os.environ.get("API_KEY")
        if not self.api_key:
            raise ValueError("API_KEY environment variable required")

        # Never log credentials
        logging.info("Server initialized")  # Don't log API key

    async def make_api_call(self, endpoint: str):
        # Use credentials securely
        headers = {
            "Authorization": f"Bearer {self.api_key}"
        }
        # ... make request
```

**Input Sanitization:**

```python
import re

def sanitize_input(input_str: str) -> str:
    # Remove potentially dangerous characters
    # Allow only alphanumeric, spaces, and safe punctuation
    return re.sub(r'[^\w\s\-.,!?]', '', input_str)

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    # Sanitize all string inputs
    clean_args = {
        k: sanitize_input(v) if isinstance(v, str) else v
        for k, v in arguments.items()
    }

    return await execute_tool(name, clean_args)
```

**8. Documentation**

Include comprehensive documentation:

```python
"""
Weather MCP Server

Provides weather information using the National Weather Service API.

Capabilities:
- Tools: get-weather, get-forecast, get-alerts
- Resources: None
- Prompts: weather-report

Requirements:
- Internet connection
- US locations only

Environment Variables:
- LOG_LEVEL: Logging level (default: INFO)
- CACHE_TTL: Cache duration in seconds (default: 300)

Usage:
    python weather_server.py
"""
```

**9. Versioning**

Implement proper versioning:

```python
SERVER_VERSION = "1.2.0"

async def handle_initialize(request):
    return InitializeResult(
        protocolVersion="2025-03-26",
        serverInfo=types.Implementation(
            name="weather-server",
            version=SERVER_VERSION
        ),
        capabilities=types.ServerCapabilities(
            tools=types.ToolsCapability(),
            resources=types.ResourcesCapability()
        )
    )
```

**10. Testing Coverage**

Maintain comprehensive tests:

```python
# Test suite structure
tests/
├── test_initialization.py     # Protocol initialization
├── test_tools.py              # Tool execution
├── test_resources.py          # Resource access
├── test_prompts.py            # Prompt handling
├── test_errors.py             # Error scenarios
└── test_integration.py        # End-to-end tests
```

### Configuration Best Practices

**1. Environment-Specific Configurations**

```json
// development.mcp.json
{
  "mcpServers": {
    "api-server": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_URL": "https://dev.api.example.com",
        "LOG_LEVEL": "DEBUG"
      }
    }
  }
}

// production.mcp.json
{
  "mcpServers": {
    "api-server": {
      "command": "python",
      "args": ["server.py"],
      "env": {
        "API_URL": "https://api.example.com",
        "LOG_LEVEL": "INFO"
      }
    }
  }
}
```

**2. Secrets Management**

```bash
# Use external secrets manager
export GITHUB_TOKEN=$(vault kv get -field=token secret/github)
export SLACK_TOKEN=$(vault kv get -field=token secret/slack)

# Then reference in config
```

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${GITHUB_TOKEN}"
      }
    }
  }
}
```

**3. Monitoring and Observability**

```python
import time
from prometheus_client import Counter, Histogram

# Metrics
tool_calls = Counter('mcp_tool_calls_total', 'Total tool calls', ['tool_name'])
tool_duration = Histogram('mcp_tool_duration_seconds', 'Tool execution time', ['tool_name'])

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    start = time.time()
    tool_calls.labels(tool_name=name).inc()

    try:
        result = await execute_tool(name, arguments)
        return result
    finally:
        duration = time.time() - start
        tool_duration.labels(tool_name=name).observe(duration)
```

### Operational Best Practices

**1. Health Checks**

```python
@server.list_tools()
async def handle_list_tools():
    # Include health check tool
    return [
        types.Tool(
            name="health-check",
            description="Check server health and connectivity",
            inputSchema={"type": "object", "properties": {}}
        ),
        # ... other tools
    ]

@server.call_tool()
async def handle_call_tool(name: str, arguments: dict):
    if name == "health-check":
        status = await check_health()
        return [types.TextContent(
            type="text",
            text=f"Server healthy: {status}"
        )]
```

**2. Rate Limiting**

```python
from asyncio import Semaphore
from collections import defaultdict
from datetime import datetime, timedelta

class RateLimiter:
    def __init__(self, max_requests=100, window=timedelta(minutes=1)):
        self.max_requests = max_requests
        self.window = window
        self.requests = defaultdict(list)

    def is_allowed(self, client_id: str) -> bool:
        now = datetime.now()
        # Clean old requests
        self.requests[client_id] = [
            req_time for req_time in self.requests[client_id]
            if now - req_time < self.window
        ]

        # Check limit
        if len(self.requests[client_id]) >= self.max_requests:
            return False

        # Record request
        self.requests[client_id].append(now)
        return True
```

**3. Graceful Shutdown**

```python
import signal
import asyncio

class MCPServer:
    def __init__(self):
        self.running = True
        signal.signal(signal.SIGTERM, self.handle_shutdown)
        signal.signal(signal.SIGINT, self.handle_shutdown)

    def handle_shutdown(self, signum, frame):
        logging.info("Shutdown signal received")
        self.running = False

    async def run(self):
        try:
            while self.running:
                # Server loop
                await self.process_requests()
        finally:
            # Cleanup
            await self.cleanup()
            logging.info("Server shutdown complete")
```

## Developer Resources

### Official Documentation

**Primary Resources:**

1. **Model Context Protocol Official Site**
   - Main site: https://modelcontextprotocol.io
   - Introduction: https://modelcontextprotocol.io/introduction
   - Quickstart: https://modelcontextprotocol.io/quickstart
   - Architecture: https://modelcontextprotocol.io/docs/concepts/architecture

2. **MCP Specification**
   - Latest spec: https://modelcontextprotocol.io/specification/2025-06-18
   - Draft spec: https://spec.modelcontextprotocol.io/specification/draft/
   - Architecture: https://modelcontextprotocol.io/specification/2025-06-18/architecture

3. **Anthropic Documentation**
   - MCP Overview: https://docs.anthropic.com/en/docs/agents-and-tools/mcp
   - Claude Code MCP: https://docs.claude.com/en/docs/claude-code/mcp
   - MCP Announcement: https://www.anthropic.com/news/model-context-protocol

### SDKs and Tools

**Official SDKs:**

1. **TypeScript SDK**
   - Repository: https://github.com/modelcontextprotocol/typescript-sdk
   - Package: `@modelcontextprotocol/sdk`
   - Documentation: Included in repository

2. **Python SDK**
   - Repository: https://github.com/modelcontextprotocol/python-sdk
   - Package: `modelcontextprotocol`
   - Requirements: Python 3.9+

3. **C# SDK**
   - Repository: https://github.com/modelcontextprotocol/csharp-sdk
   - Maintained in collaboration with Microsoft
   - .NET compatible

4. **Java SDK**
   - Repository: https://github.com/modelcontextprotocol/java-sdk
   - Maintained in collaboration with Spring AI
   - Spring Boot integration

5. **Go SDK**
   - Repository: https://github.com/modelcontextprotocol/go-sdk
   - Maintained in collaboration with Google
   - Idiomatic Go design

6. **PHP SDK**
   - Repository: https://github.com/modelcontextprotocol/php-sdk
   - Maintained in collaboration with The PHP Foundation
   - Framework-agnostic

**Development Tools:**

1. **MCP Inspector**
   ```bash
   npm install -g @modelcontextprotocol/inspector
   ```
   - Interactive testing interface
   - Protocol debugging
   - Message inspection

2. **MCP CLI**
   ```bash
   npm install -g @modelcontextprotocol/cli
   ```
   - Command-line management
   - Server testing utilities

### Server Repositories

**Official Servers:**

1. **MCP Servers Repository**
   - Main repo: https://github.com/modelcontextprotocol/servers
   - Official and community servers
   - Reference implementations
   - README: https://github.com/modelcontextprotocol/servers/blob/main/README.md

2. **Quickstart Resources**
   - Repository: https://github.com/modelcontextprotocol/quickstart-resources
   - Tutorial code
   - Example implementations

### Registry and Discovery

**MCP Registry:**

1. **Official Registry**
   - Site: https://registry.modelcontextprotocol.io
   - API documentation
   - Server discovery
   - Publishing guidelines

2. **GitHub MCP Registry**
   - Integrated with GitHub
   - Community contributions
   - Automatic indexing

### Tutorials and Guides

**Official Tutorials:**

1. **Quickstart Guide**
   - Building your first server: https://modelcontextprotocol.io/quickstart
   - Client development: https://modelcontextprotocol.io/quickstart/client
   - Building with LLMs: https://modelcontextprotocol.io/tutorials/building-mcp-with-llms

2. **Claude Code Tutorials**
   - Getting started: https://docs.anthropic.com/en/docs/claude-code/getting-started
   - Common workflows: https://docs.anthropic.com/en/docs/claude-code/tutorials
   - Troubleshooting: https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/troubleshooting

3. **Language-Specific Guides**
   - Python: https://modelcontextprotocol.io/docs/first-server/python
   - TypeScript: Multiple community tutorials available
   - Node.js client: https://modelcontextprotocol.info/docs/tutorials/building-a-client-node/

**Community Tutorials:**

1. **FreeCodeCamp**
   - "How to Build a Custom MCP Server with TypeScript"
   - Comprehensive handbook with real-world examples

2. **Medium Articles**
   - "The Model Context Protocol (MCP) — A Complete Tutorial"
   - "Building MCP Servers: Part 1 — Getting Started with Resources"
   - "A Quick Introduction to Model Context Protocol (MCP) in Python"

3. **DEV Community**
   - "How to build MCP servers with TypeScript SDK"
   - Step-by-step walkthroughs

### Blog and Updates

**Official Blog:**

1. **MCP Blog**
   - Site: http://blog.modelcontextprotocol.io
   - Introducing the MCP Registry: http://blog.modelcontextprotocol.io/posts/2025-09-08-mcp-registry-preview/
   - MCP Prompts for Automation: http://blog.modelcontextprotocol.io/posts/2025-07-29-prompts-for-automation/
   - Protocol updates: http://blog.modelcontextprotocol.io/posts/2025-09-26-mcp-next-version-update/

2. **Anthropic Engineering Blog**
   - Engineering posts: https://www.anthropic.com/engineering
   - Claude Code best practices: https://www.anthropic.com/engineering/claude-code-best-practices
   - Writing tools for agents: https://www.anthropic.com/engineering/writing-tools-for-agents

### Community Resources

**GitHub Organization:**

1. **Model Context Protocol**
   - Organization: https://github.com/modelcontextprotocol
   - All official repositories
   - Discussions and issues

**Community Discussions:**

1. **GitHub Discussions**
   - MCP Server Registry discussion: https://github.com/orgs/modelcontextprotocol/discussions/159
   - Feature requests and feedback

2. **Stack Overflow**
   - Tag: `model-context-protocol`
   - Community Q&A

### Enterprise Resources

**Webinars and Events:**

1. **Anthropic Webinars**
   - Deploying Multi-Agent Systems: https://www.anthropic.com/webinars/deploying-multi-agent-systems-using-mcp-and-a2a-with-claude-on-vertex-ai
   - Practical implementations
   - Enterprise use cases

2. **Case Studies**
   - Block integration
   - Apollo implementation
   - Intercom AI agent

### Third-Party Tools and Platforms

**Development Platforms Integrating MCP:**

- **Zed** - Code editor with MCP support
- **Replit** - Online IDE with MCP
- **Codeium** - AI coding assistant
- **Sourcegraph** - Code intelligence platform

**Service Integrations:**

- **Atlassian** (Jira, Confluence)
- **Zapier** - Automation platform
- **Cloudflare** - Edge computing
- **Asana** - Project management
- **Linear** - Issue tracking
- **Slack** - Team communication

### Learning Paths

**Beginner Path:**

1. Read Introduction: https://modelcontextprotocol.io/introduction
2. Complete Quickstart: https://modelcontextprotocol.io/quickstart
3. Install and test official server
4. Use MCP Inspector to explore capabilities
5. Try Claude Code with MCP servers

**Intermediate Path:**

1. Build first custom server (Python or TypeScript)
2. Implement resources, tools, and prompts
3. Add authentication and security
4. Test with MCP Inspector
5. Deploy to Claude Code or Claude Desktop

**Advanced Path:**

1. Design multi-capability servers
2. Implement custom transports
3. Build enterprise integrations
4. Optimize for performance and cost
5. Contribute to MCP ecosystem
6. Publish to MCP Registry

### Getting Help

**Official Support:**

- Documentation: https://modelcontextprotocol.io
- GitHub Issues: Report bugs and request features
- Anthropic Support: For Claude-specific issues

**Community Support:**

- GitHub Discussions
- Stack Overflow
- Community forums
- Social media (#ModelContextProtocol)

### Staying Updated

**Follow These Sources:**

1. **Official Channels**
   - MCP Blog: http://blog.modelcontextprotocol.io
   - Anthropic News: https://www.anthropic.com/news
   - GitHub Releases: Watch MCP repositories

2. **Social Media**
   - Anthropic announcements
   - Developer community discussions
   - Conference talks and presentations

3. **Roadmap**
   - Official roadmap: https://modelcontextprotocol.io/development/roadmap
   - Upcoming features
   - Community priorities

---

## Sources

1. **Model Context Protocol Official Site**: https://modelcontextprotocol.io
2. **MCP Introduction**: https://modelcontextprotocol.io/introduction
3. **MCP Specification (2025-06-18)**: https://modelcontextprotocol.io/specification/2025-06-18
4. **MCP Architecture**: https://modelcontextprotocol.io/specification/2025-06-18/architecture
5. **MCP Quickstart**: https://modelcontextprotocol.io/quickstart
6. **Anthropic MCP Announcement**: https://www.anthropic.com/news/model-context-protocol
7. **Claude Docs - MCP**: https://docs.anthropic.com/en/docs/agents-and-tools/mcp
8. **Claude Code MCP Integration**: https://docs.claude.com/en/docs/claude-code/mcp
9. **MCP Servers Repository**: https://github.com/modelcontextprotocol/servers
10. **MCP TypeScript SDK**: https://github.com/modelcontextprotocol/typescript-sdk
11. **MCP Python SDK**: https://github.com/modelcontextprotocol/python-sdk
12. **MCP Specification - Architecture**: https://spec.modelcontextprotocol.io/specification/draft/architecture/
13. **MCP Specification - Base Protocol**: https://spec.modelcontextprotocol.io/specification/2024-11-05/basic/
14. **MCP Specification - Lifecycle**: https://modelcontextprotocol.io/specification/2025-03-26/basic/lifecycle
15. **MCP Specification - Transports**: https://modelcontextprotocol.io/specification/2025-06-18/basic/transports
16. **MCP Specification - Resources**: https://spec.modelcontextprotocol.io/specification/server/resources/
17. **MCP Specification - Prompts**: https://modelcontextprotocol.io/specification/2025-06-18/server/prompts
18. **MCP Specification - Sampling**: https://spec.modelcontextprotocol.io/specification/2025-03-26/client/sampling/
19. **MCP Specification - Security**: https://modelcontextprotocol.io/specification/draft/basic/security_best_practices
20. **MCP Registry**: https://registry.modelcontextprotocol.io
21. **MCP Blog - Registry Announcement**: http://blog.modelcontextprotocol.io/posts/2025-09-08-mcp-registry-preview/
22. **MCP Blog - Prompts for Automation**: http://blog.modelcontextprotocol.io/posts/2025-07-29-prompts-for-automation/
23. **GitHub MCP Registry**: https://github.blog/ai-and-ml/github-copilot/meet-the-github-mcp-registry-the-fastest-way-to-discover-mcp-servers/
24. **Anthropic Webinar - Multi-Agent Systems**: https://www.anthropic.com/webinars/deploying-multi-agent-systems-using-mcp-and-a2a-with-claude-on-vertex-ai
25. **InfoWorld - MCP Introduction**: https://www.infoworld.com/article/3613143/anthropic-introduces-the-model-context-protocol.html
26. **VentureBeat - MCP Release**: https://venturebeat.com/data-infrastructure/anthropic-releases-model-context-protocol-to-standardize-ai-data-integration
27. **Wikipedia - Model Context Protocol**: https://en.wikipedia.org/wiki/Model_Context_Protocol
28. **Stainless - MCP Error Handling**: https://www.stainless.com/mcp/error-handling-and-debugging-mcp-servers
29. **MCP Debugging Guide**: https://modelcontextprotocol.io/legacy/tools/debugging
30. **MCPEvals - Debugging Best Practices**: https://www.mcpevals.io/blog/debugging-mcp-servers-tips-and-best-practices
31. **Auth0 - MCP Authorization**: https://auth0.com/blog/an-introduction-to-mcp-and-authorization/
32. **Microsoft - MCP Security Risks**: https://techcommunity.microsoft.com/blog/microsoft-security-blog/understanding-and-mitigating-security-risks-in-mcp-implementations/4404667
33. **Stytch - MCP Security**: https://stytch.com/blog/mcp-security/
34. **Stytch - MCP Auth Guide**: https://stytch.com/blog/MCP-authentication-and-authorization-guide/
35. **Tetrate - Token Optimization**: https://tetrate.io/learn/ai/mcp/token-optimization-strategies
36. **Arsturn - Hidden Costs of MCP**: https://www.arsturn.com/blog/hidden-cost-of-mcp-monitor-reduce-token-usage
37. **Apollo GraphQL - Efficient AI Agents**: https://www.apollographql.com/blog/building-efficient-ai-agents-with-graphql-and-apollo-mcp-server
38. **Skywork AI - Skills vs MCP Comparison**: https://skywork.ai/blog/ai-agent/claude-skills-vs-mcp-vs-llm-tools-comparison-2025/
39. **FreeCodeCamp - TypeScript MCP Server**: https://www.freecodecamp.org/news/how-to-build-a-custom-mcp-server-with-typescript-a-handbook-for-developers/
40. **Hackteam - Build First MCP Server**: https://hackteam.io/blog/build-your-first-mcp-server-with-typescript-in-under-10-minutes/

*Last Updated: October 21, 2025*
*Research conducted based on official Anthropic documentation, Model Context Protocol specification, and community resources*
*Note: MCP is actively evolving; features and specifications may change*
