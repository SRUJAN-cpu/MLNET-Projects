# MCPClient

A general-purpose [Model Context Protocol (MCP)](https://modelcontextprotocol.io) client built with C# and .NET 10. It connects to any MCP server, discovers its tools, and starts an interactive chat session powered by Claude via the Anthropic API.

## Overview

This client is **not specific to any one MCP server** — it connects to whatever server you point it at, discovers available tools automatically, and lets Claude use them to answer your queries. It was built alongside the `weatherMCP` server but works with any MCP server.

## How It Works

1. Launches the target MCP server as a child process (stdio transport) or connects over HTTP
2. Calls `ListToolsAsync()` to discover all available tools
3. Passes those tools to Claude (`claude-haiku-4-5-20251001`) via `Microsoft.Extensions.AI`
4. Runs an interactive chat loop — Claude calls tools automatically when needed

## Tech Stack

- **.NET 10**
- **Anthropic** `12.13.0` — official Anthropic C# SDK
- **Microsoft.Extensions.AI** `10.4.1` — chat client abstractions + function invocation
- **ModelContextProtocol** `1.2.0` — MCP client transport
- **Microsoft.Extensions.Hosting** `10.0.5` — configuration & dependency injection

## Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- An [Anthropic API key](https://console.anthropic.com/)

## Setup

### 1. Set your API key

```bash
dotnet user-secrets set "ANTHROPIC_API_KEY" "your-key-here" --project "C:\MLNET-Projects\MCPClient\MCPClient.csproj"
```

Or set it as an environment variable:

```bash
set ANTHROPIC_API_KEY=your-key-here
```

### 2. Project Setup from Scratch

```bash
mkdir MCPClient
cd MCPClient
dotnet new console
```

Add the required packages:

```bash
dotnet add package Anthropic
dotnet add package Microsoft.Extensions.AI
dotnet add package Microsoft.Extensions.Hosting
dotnet add package ModelContextProtocol
```

## Running

### Against a local MCP server (stdio)

Pass the path to the server's `.csproj` or directory:

```bash
dotnet run --project "C:\MLNET-Projects\MCPClient\MCPClient.csproj" -- "C:\MLNET-Projects\weatherMCP\weatherMCP.csproj"
```

### Against an HTTP MCP server

Make sure the server is running on `http://localhost:3001`, then:

```bash
dotnet run --project "C:\MLNET-Projects\MCPClient\MCPClient.csproj" -- http
```

### Default (no arguments)

If no argument is provided, it defaults to launching the `weatherMCP` server from the sibling directory:

```bash
dotnet run --project "C:\MLNET-Projects\MCPClient\MCPClient.csproj"
```

## Usage

```
Connected to server with tools: get_forecast
Connected to server with tools: get_alerts
MCP Client Started!

Enter a command (or 'exit' to quit):
> what's the weather in Seattle, WA?
> are there any weather alerts in TX?
> exit
```

Type `exit` to quit the session.

## Supported Server Types

The client auto-detects the server type from the argument passed:

| Argument | Behavior |
|----------|----------|
| `http` | Connects via HTTP to `localhost:3001` |
| `path/to/script.py` | Runs with `python` |
| `path/to/script.js` | Runs with `node` |
| `path/to/project.csproj` or directory | Runs with `dotnet run --project` |
| _(none)_ | Defaults to `../weatherMCP` |
