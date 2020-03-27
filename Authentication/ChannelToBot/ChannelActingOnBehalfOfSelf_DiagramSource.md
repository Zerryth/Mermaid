```mermaid
    sequenceDiagram
        participant Channel
        participant Connector
        participant Bot

        Channel ->> Connector: authenticates and authorizes itself
        Connector ->> Connector: creates and signs access and identity tokens
        Connector ->> Channel: returns access and identity tokens
        Channel ->> Connector: makes request with access token to send Activity
        Connector ->> Bot: sends Activity with access token

```