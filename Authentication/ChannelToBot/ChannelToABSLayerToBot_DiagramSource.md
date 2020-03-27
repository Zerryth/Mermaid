```mermaid
    graph LR
        Teams --- Connector
        Slack --- Connector([Connector])
        Cortana --- Connector
        WebChat --- Connector

        Connector --- Bot(Bot)
```