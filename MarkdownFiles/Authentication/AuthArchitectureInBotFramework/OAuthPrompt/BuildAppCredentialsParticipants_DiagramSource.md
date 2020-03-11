#### Participants Involved in Building `AppCredentials`

```mermaid
    classDiagram
        BotFrameworkAdapter *-- ICredentialProvider: uses to get password
        ICredentialProvider <|-- SimpleCredentialProvider: default implementation
```