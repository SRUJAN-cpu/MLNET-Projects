# weatherMCP

A [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server built with C# and .NET 10 that exposes US weather data as tools for AI assistants.

## Overview

This project implements an MCP server backed by the [US National Weather Service API](https://api.weather.gov) (free, no API key required). It allows MCP-compatible clients (like Claude Desktop) to query real-time weather alerts and forecasts for any US location.

## Tools

| Tool | Description | Parameters |
|------|-------------|------------|
| `GetAlerts` | Retrieves active weather alerts for a US state | `state` — two-letter US state code (e.g. `TX`, `CA`) |
| `GetForecast` | Retrieves a multi-period weather forecast for a location | `latitude`, `longitude` — decimal coordinates |

## Tech Stack

- **.NET 10**
- **ModelContextProtocol** `1.2.0` — MCP server SDK
- **Microsoft.Extensions.Hosting** `10.0.5` — dependency injection & app lifetime
- **Transport** — stdio (standard input/output)

## Project Structure

```
weatherMCP/
├── Program.cs              # Host setup, DI registration, MCP server config
├── weatherMCP.csproj       # Project file
└── Tools/
    ├── WeatherTools.cs     # MCP tool definitions (GetAlerts, GetForecast)
    └── HttpClientExt.cs    # HttpClient extension for JSON responses
```

## Getting Started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)

### Project Setup from Scratch

If you want to recreate this project yourself:

```bash
# Create a new directory for the project
mkdir weather
cd weather

# Initialize a new C# console project
dotnet new console
```

Then add the required NuGet packages:

```bash
# Add the Model Context Protocol SDK
dotnet add package ModelContextProtocol --prerelease

# Add the .NET Hosting package
dotnet add package Microsoft.Extensions.Hosting
```

> You can also create the project via the Visual Studio or Rider project wizard, then add the packages through the NuGet Package Manager UI.

### Build & Run

```bash
dotnet build
dotnet run
```

The server communicates over stdio and is intended to be launched by an MCP host/client, not run directly in a terminal.

### Connecting to Claude Desktop

Add the following to your Claude Desktop MCP config (`claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "weather": {
      "command": "dotnet",
      "args": ["run", "--project", "path/to/weatherMCP"]
    }
  }
}
```

Or point to the compiled executable:

```json
{
  "mcpServers": {
    "weather": {
      "command": "path/to/weatherMCP.exe"
    }
  }
}
```

## Data Source

All weather data is sourced from the **US National Weather Service** at `https://api.weather.gov`. This API is free to use, requires no authentication, and covers the contiguous United States, Alaska, Hawaii, and US territories.
