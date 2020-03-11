#### `OAuthClient` Class Diagram

```mermaid
    classDiagram
        class OAuthClient {
            - OAuthPromptSettings
            - PromptValidator
        }

        class ServiceClient {
        }
        <<abstract>> ServiceClient

        class IOAuthClient {
            + BaseUri
            + SerializationSettings
            + DeserializationSettings
            + Credentials
            + BotSignIn
            + UserToken
        }

        OAuthClient --|> ServiceClient: derives from
        OAuthClient --|> IOAuthClient: implements
        IOAuthClient --|> IDisposable

        class IUserToken {
            + GetTokenWithHttpMessagesAsync()
            + GetAadTokensWithHttpMessagesAsync()
            + SignOutWithHttpMessagesAsync()
            + GetTokenStatusWithHttpMessagesAsync()
        }

        OAuthClient *-- IUserToken: uses to get token

        class UserToken {
            + GetTokenWithHttpMessagesAsync()
        }

        IUserToken <|-- UserToken: performs token operations with
        UserToken --> HttpOperationResponseTokenResponse: receives Task of
        HttpOperationResponseTokenResponse *-- Token: which has a
```
* `HttpOperationResponseTokenResponse` should be `HttpOperationResponse<TokenResponse>` (diagram tool breaks on special chars in class diagram).
* `ServiceClient` is an [MS REST class](https://docs.microsoft.com/en-us/dotnet/api/microsoft.rest.serviceclient-1?view=azure-dotnet).