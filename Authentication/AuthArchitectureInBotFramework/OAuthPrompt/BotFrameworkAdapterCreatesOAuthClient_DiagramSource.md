#### `BotFrameworkAdapter` Creates `OAuthClient`
``` mermaid
    graph LR
        BotFrameworkAdapter -. with .-> AppCredsFromSetting[AppCredentials from OAuthPromptSettings]
        BotFrameworkAdapter -. or with .-> AppCredsFromCache[AppCredentials from Adapter's credential cache]
        BotFrameworkAdapter -. or with .-> builtAppCredentials[newly built AppCredentials]
        
        AppCredsFromSetting -->  OAuthClient[creates OAuthClient]
        AppCredsFromCache -->  OAuthClient
        builtAppCredentials -->  OAuthClient

```