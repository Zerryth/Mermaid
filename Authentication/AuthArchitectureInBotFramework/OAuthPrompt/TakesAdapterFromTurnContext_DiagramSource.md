`OAuthPrompt` has various methods* that uses `BotAdapter` within its logic:
```mermaid
    graph LR
    OAuthPrompt -- takes --> Adapter -. from .->  TurnContext
```
* `OAuthPrompt` methods that use `BotAdapter`: `BeginDialogAsync()`, `GetUserTokenAsync()`, `SignUserOutAsync()`, `SendOAuthCardAsync()`, `RecognizeTokenAsync()`