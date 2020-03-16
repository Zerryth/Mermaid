`OAuthPrompt` uses a `BotFrameworkAdapter` that implements `ICredentialTokenProvider` to acquire tokens.

```mermaid
    classDiagram
        class OAuthPrompt {
            - OAuthPromptSettings
            - PromptValidator
        }

        class BotAdapter {
        }
        <<abstract>> BotAdapter

        class IExtendedUserTokenProvider {
            + GetUserTokenAsync()
            + GetOauthSignInLinkAsync()
            + SignOutUserAsync()
            + GetTokenStatusAsync()
            + GetAadTokenAsync()
            + GetSignInResourceAsync()
            + ExchangeTokenAsync()
        }
        <<Interface>> IExtendedUserTokenProvider

        OAuthPrompt o-- IExtendedUserTokenProvider: uses as token provider
        BotFrameworkAdapter --|> BotAdapter: derives from
        BotAdapter --o TurnContext: is a member of
        IExtendedUserTokenProvider <|-- BotFrameworkAdapter
        BotFrameworkAdapter --> OAuthClient : creates to get tokens
```