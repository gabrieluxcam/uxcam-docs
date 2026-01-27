---
title: AI Integration
hidden: false
---
# UXCam Integration MCP Server

> **⚠️ Beta Release**
>
> This MCP server is currently in beta. We appreciate your feedback! Please report any issues or suggestions to [support@uxcam.com](mailto:support@uxcam.com) or through your UXCam dashboard.

The UXCam Integration Model Context Protocol (MCP) server enables AI-powered code editors like Cursor and Windsurf, plus general-purpose tools like Claude Desktop, to automatically integrate the UXCam SDK into your mobile applications through guided, step-by-step prompts.

<br />

<a href="https://cursor.com/en-US/install-mcp?name=uxcam-integration&config=eyJ0eXBlIjoic3RyZWFtYWJsZS1odHRwIiwidXJsIjoiaHR0cHM6Ly9pbnRlZ3JhdGlvbi5tY3AudXhjYW0uY29tL21jcC9odHRwIn0%3D">
  <img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Add uxcam-integration MCP server to Cursor" height="32" />
</a>

<br />

## What is MCP?

Model Context Protocol (MCP) is an open standard that allows AI applications to securely access external data sources and tools. The UXCam Integration MCP server provides AI agents with:

* **Guided SDK integration** through 5 progressive prompts
* **Platform-specific code generation** for Android (Kotlin/Java) and iOS (Swift/Objective-C)
* **Implementation validation** to ensure correct SDK setup
* **Best practices enforcement** for session recording, privacy, and analytics

## What It Does

Instead of manually implementing the UXCam SDK, this MCP server guides your AI assistant through a complete integration workflow with 5 specialized prompts:

1. **/integration_setup** - Add SDK dependencies and initialize UXCam
2. **/screen_tagging** - Track screen views and user navigation
3. **/mask_sensitive_data** - Protect sensitive data (passwords, credit cards, PII)
4. **/event_tracking** - Log custom events and user actions
5. **/user_properties** - Set user identity and custom properties

Each prompt provides platform-specific code examples, validates your implementation, and guides you to the next step.

**Note:** You'll need your UXCam app key to begin the integration. Find it at **Dashboard → Settings → App Key** or [sign up for free](https://dashboard.uxcam.com/signup).

## Supported Platforms

* ✅ Android (Kotlin/Java)
* ✅ iOS (Swift/Objective-C)
* 🚧 React Native (Coming Soon)
* 🚧 Flutter (Coming Soon)
* 🚧 Web (Coming Soon)

## UXCam Integration MCP Server Setup

UXCam hosts a remote MCP server at `https://integration.mcp.uxcam.com/mcp/http`. Configure your AI development tools to connect to this server.

<Tabs>
  <Tab title="Cursor">
    **Add to`~/.cursor/mcp.json`:**

    ```json
    {
      "mcpServers": {
        "uxcam-integration": {
          "type": "streamable-http",
          "url": "https://integration.mcp.uxcam.com/mcp/http",
          "headers": {
            "Accept": "application/json, text/event-stream",
            "Content-Type": "application/json"
          }
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
        "uxcam-integration": {
          "type": "streamable-http",
          "url": "https://integration.mcp.uxcam.com/mcp/http",
          "headers": {
            "Accept": "application/json, text/event-stream",
            "Content-Type": "application/json"
          }
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
        "uxcam-integration": {
          "type": "streamable-http",
          "url": "https://integration.mcp.uxcam.com/mcp/http",
          "headers": {
            "Accept": "application/json, text/event-stream",
            "Content-Type": "application/json"
          }
        }
      }
    }
    ```
  </Tab>

  <Tab title="Claude CLI">
    **Run this command:**

    ```bash
    claude mcp add --transport http uxcam-integration-mcp https://integration-uat.mcp.uxcam.com/mcp/http
    ```
  </Tab>
</Tabs>

## Testing Your MCP Setup

Once configured, test your MCP server connection:

1. **Open your AI editor** (Cursor, Windsurf, etc.) with an Android or iOS project
2. **Start a new chat** with the AI assistant
3. **Begin the integration** by typing: `/uxcam-integration/integration_setup`
4. **Follow the prompts** - the AI will ask for your UXCam app key and guide you through each step

The AI should recognize the prompts and begin the guided integration workflow.

## Available Prompts

### /integration_setup

**Start here for new integrations.** Adds SDK dependencies, initializes UXCam with your app key, and validates the build configuration.

**Required:** Your UXCam app key from Dashboard → Settings → App Key

### /screen_tagging

**Use after:** Phase 1 is complete

Implements automatic screen tracking and names screens for better analytics visibility.

### /mask_sensitive_data

**Use for:** Login screens, payment forms, personal information

Adds privacy protection to prevent sensitive data from being recorded in session replays.

### /event_tracking

**Use for:** Button clicks, purchases, form submissions

Implements custom event logging for tracking business-critical user actions.

### /user_properties

**Use for:** User segmentation, cohort analysis

Sets user identity and custom properties for advanced analytics and filtering.

## Troubleshooting

**Prompts not appearing?**

* Verify the MCP server URL in your configuration file
* Ensure `type` is set to `"streamable-http"` (for Cursor)
* Restart your AI editor completely
* Check that headers are properly configured

**"Platform not supported" error?**

* Only Android and iOS projects are currently supported
* React Native and Flutter support is coming soon
* Ensure you're working in an Android or iOS project directory
* Check that your project has the standard structure (build.gradle, Podfile, etc.)

**Integration not working?**

* Verify your UXCam app key is correct
* Check that you completed previous steps in order
* Review the AI's validation messages for specific errors
* Ensure your project builds successfully before proceeding

## Get Your UXCam App Key

Don't have a UXCam account yet? [Sign up for free](https://dashboard.uxcam.com/signup)

Find your app key at: **Dashboard → Settings → App Key**

## Additional Resources

* <Anchor label="UXCam Documentation" target="_blank" href="https://developer.uxcam.com/docs/so-what-does-uxcam-do">UXCam Documentation</Anchor>
* [MCP Specification](https://modelcontextprotocol.io)
