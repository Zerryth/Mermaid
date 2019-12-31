```mermaid
    graph LR
        Channel[/Channel/] --- AAD1{{AAD App Registration 1}}
        AAD1  --- Bot((Bot))
        
        ExternalService --- AAD2{{AAD App Registration 2}}
        AAD2  --- Bot
```