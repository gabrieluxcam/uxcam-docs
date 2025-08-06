---
title: MCP
hidden: true
---
The UXCam Documentation Model Context Protocol (MCP) server enables AI-powered code editors like Cursor and Windsurf, plus general-purpose tools like Claude Desktop, to interact directly with your UXCam Documentation API and documentation.

<br />

<a href="https://cursor.com/install-mcp?name=test-server&config=JTdCJTIydHlwZSUyMiUzQSUyMnN0cmVhbWFibGUtaHR0cCUyMiUyQyUyMnVybCUyMiUzQSUyMmh0dHBzJTNBJTJGJTJGaW50ZWdyYXRpb24ubWNwLnV4Y2FtLmNvbSUyRm1jcCUyRmh0dHAlMjIlN0Q%3D">
  <img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Add test-server MCP server to Cursor" height="32" />
</a>

<br />

## What is MCP?

Model Context Protocol (MCP) is an open standard that allows AI applications to securely access external data sources and tools. The UXCam Documentation MCP server provides AI agents with:

* **Direct API access** to UXCam Documentation functionality
* **Documentation search** capabilities
* **Real-time data** from your UXCam Documentation account
* **Code generation** assistance for UXCam Documentation integrations

## UXCam Documentation MCP Server Setup

UXCam Documentation hosts a remote MCP server at `https://developer.uxcam.com/mcp`. Configure your AI development tools to connect to this server. If your APIs require authentication, you can pass in headers via query parameters or however headers are configured in your MCP client.

<Tabs>
  <Tab title="Cursor">
    **Add to`~/.cursor/mcp.json`:**

    ```json
    {
      "mcpServers": {
        "uxcam-documentation": {
          "url": "https://developer.uxcam.com/mcp"
        }
      }
    }
    ```
  </Tab>

  <Tab title="Windsurf">
    **Add to`~/.codeium/windsurf/mcp_config.json`:**

    ```json
    {
      "mcpServers": {
        "uxcam-documentation": {
          "url": "https://developer.uxcam.com/mcp"
        }
      }
    }
    ```
  </Tab>

  <Tab title="Claude Desktop">
    **Add to`claude_desktop_config.json`:**

    ```json
    {
      "mcpServers": {
        "uxcam-documentation": {
          "url": "https://developer.uxcam.com/mcp"
        }
      }
    }
    ```
  </Tab>
</Tabs>

## Testing Your MCP Setup

Once configured, you can test your MCP server connection:

1. **Open your AI editor** (Cursor, Windsurf, etc.)
2. **Start a new chat** with the AI assistant
3. **Ask about UXCam Documentation** - try questions like:
   * "How do I \[common use case]?"
   * "Show me an example of \[API functionality]"
   * "Create a \[integration type] using UXCam Documentation"

The AI should now have access to your UXCam Documentation account data and documentation through the MCP server.

<ConfigurationBuilder />