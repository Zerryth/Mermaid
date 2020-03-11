#### `BotFrameworkAdapter` Creates `TokenApiClient`
``` mermaid
    graph LR
        BotFrameworkAdapter -. with .-> AppCredsFromSetting[AppCredentials from OAuthPromptSettings]
        BotFrameworkAdapter -. or with .-> AppCredentialsMember["AppCredentials created on initialization of BotFrameworkAdapter saved in #quot;credentials#quot; member"]
        
        AppCredsFromSetting -->  TokenApiClient[creates TokenApiClient]
        AppCredentialsMember -->  TokenApiClient

```