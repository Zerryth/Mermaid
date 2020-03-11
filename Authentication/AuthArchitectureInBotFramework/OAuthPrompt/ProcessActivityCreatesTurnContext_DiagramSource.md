```mermaid
    graph LR
        BotEndpoint["#quot;api/messages#quot; endpoint in controller"] -- calls --> adapter.ProcessActivity["ProcessActivity()"] -- which creates --> TurnContext 
```