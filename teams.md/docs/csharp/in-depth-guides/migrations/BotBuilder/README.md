---
sidebar_position: 1
summary: Comprehensive migration guide from Bot Framework SDK (BotBuilder) to Teams AI Library v2 for C#/.NET, covering architecture changes, activity handlers, and migration strategies.
llms: ignore
---

# From BotBuilder

The Teams AI Library v2 represents a complete reimagining of how to build Microsoft Teams applications in C#/.NET. This guide will help you migrate your existing Bot Framework SDK (BotBuilder) applications to the new Teams AI Library.

## Overview

The Teams AI Library v2 introduces several key improvements over the traditional BotBuilder approach:

- **Simplified architecture**: Move from complex adapter patterns to a streamlined app-centric design
- **Modern ASP.NET Core integration**: Built on ASP.NET Core with dependency injection and middleware
- **Improved activity handling**: Replace inheritance-based handlers with attribute-based or functional routing
- **Enhanced authentication**: Simplified OAuth flows without complex dialog management
- **Better testability**: Dependency injection throughout makes unit testing easier
- **AI-first design**: Native support for AI features and prompt engineering

## Migration Strategy

We recommend an **incremental migration approach**:

1. **Understand the new architecture** - Familiarize yourself with Teams AI concepts
2. **Set up a new project** - Start with the Teams AI project structure
3. **Migrate configuration** - Move app settings and credentials
4. **Convert activity handlers** - Transform your BotBuilder handlers to Teams AI handlers
5. **Update state management** - Migrate to the new storage system
6. **Migrate authentication** - Simplify OAuth implementation
7. **Test thoroughly** - Validate each migrated feature

## Key Differences

### Architecture Changes

| BotBuilder | Teams AI Library v2 |
|------------|-------------------|
| `BotFrameworkAdapter` | Built-in ASP.NET Core integration |
| `ActivityHandler` inheritance | Attribute-based or functional handlers |
| `DialogSet` and `ComponentDialog` | Simplified state and flow management |
| Manual dependency injection | ASP.NET Core DI throughout |
| Separate web server setup | Integrated with ASP.NET Core |

### Activity Handling

**BotBuilder:**
```csharp
public class MyBot : ActivityHandler
{
    protected override async Task OnMessageActivityAsync(
        ITurnContext<IMessageActivity> turnContext, 
        CancellationToken cancellationToken)
    {
        await turnContext.SendActivityAsync("Hello!");
    }
}
```

**Teams AI Library v2:**
```csharp
// Controller approach
[TeamsController("main")]
public class MainController
{
    [Message]
    public async Task OnMessage(
        [Context] MessageActivity activity, 
        [Context] IContext.Client client)
    {
        await client.Send("Hello!");
    }
}

// Or Minimal approach
app.OnMessage(async context =>
{
    await context.Send("Hello!");
});
```

### Configuration

**BotBuilder:**
```csharp
var adapter = new CloudAdapter(
    new ConfigurationBotFrameworkAuthentication(configuration));

var bot = new MyBot();

app.MapPost("/api/messages", async (HttpRequest req, HttpResponse res) =>
{
    await adapter.ProcessAsync(req, res, bot);
});
```

**Teams AI Library v2:**
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.AddTeams();

var app = builder.Build();
app.UseTeams();
app.Run();
```

## What's Included in This Guide

This migration guide covers the following topics:

- **[Activity Handlers](./activity-handlers)** - Converting BotBuilder activity handlers to Teams AI handlers
- **[Turn Context](./turn-context/)** - Understanding the new context model
  - **[Sending Activities](./turn-context/sending-activities)** - Migrating message sending patterns
  - **[Proactive Messaging](./turn-context/proactive-activities)** - Simplified proactive messaging
  - **[API Access](./turn-context/the-api-client)** - Using the Teams API client
- **[User Authentication](./user-authentication)** - Simplifying OAuth flows without dialogs
- **[State Management](./state-management)** - Working with the new storage system
- **[Middleware](./middleware)** - Adapting middleware patterns

## Getting Started

Before migrating, ensure you have:

- **.NET 8.0 SDK** or higher installed
- **Visual Studio 2022** or **Visual Studio Code** with C# extension
- **Teams Toolkit** extension (optional but recommended)
- Access to your existing BotBuilder source code
- Azure Bot Service credentials from your current bot

Ready to begin? Start with [Activity Handlers](./activity-handlers) to learn how to convert your bot's core logic.

## Additional Resources

- [C# Getting Started Guide](../../getting-started/quickstart)
- [Teams AI Library Documentation](/main/welcome)
- [Bot Framework SDK Migration Overview](https://learn.microsoft.com/en-us/azure/bot-service/)
