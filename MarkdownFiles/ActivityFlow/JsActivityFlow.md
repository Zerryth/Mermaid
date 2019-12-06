```mermaid
    sequenceDiagram
        participant Channel
        participant BFS as Bot Framework Service
        participant WebServer as Web Server
        Note over WebServer: Restify or Express Server
        participant Adapter as AdapterWithErrorHandler
        participant BotFrameworkHttpAdapter
        participant BotFrameworkAdapter
        participant BotAdapter
        participant Middleware
        participant TurnContext
        participant ActivityHandler
        participant Bot
```