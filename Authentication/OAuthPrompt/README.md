## `OAuthPrompt` Behavioral Flow Charts

- In order to access a **protected resource**, the **bot** must send the **user** to the **authorization server** or identity provider (in our samples we use AAD)
- Once user is at AAD, the user must:
    - Authenticate their identity (they are who they say they are)
    - Authorize the bot to access the protected resource on the user's behalf (delegating limited power, not the user's entire power, in the form of approving certain scopes)
        - In the process of designing the bot, you already specified *what scopes (permissions)* the bot would need to perform whatever function that bot was built to do, which are the scopes that the user is prompted to authorize
        - See [Add authentication to your bot via Azure Bot Service](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-authentication?view=azure-bot-service-4.0&tabs=csharp) for more detail on scopes
- Once authenticated and authorized, AAD sends the bot the token needed to access the protected resource

Bot Framework's `OAuthPrompt` provides a way to send the User to AAD in order to obtain a token. Bot developers do not have to manage token lifecycles, storage, nor proper redirects in the OAuth flow.

#### `OAuthPrompt.BeginDialogAsync()` Flow

![OAuthPromptBeginDialogFlow](./OAuthPrompt_BeginDialog.svg "OAuthPrompt.BeginDialogFlow()")

#### `OAuthPrompt.ContinueDialogAsync()` Flow

*Higher Level*

![HigherLevelOAuthPromptContinueDialogFlow](./OAuthPrompt_ContinueDialog_HigherLevel.svg "Higher Level OAuthPrompt.ContinueDialogFlow()")

*Detailed View*

![DetailedViewOAuthPromptContinueDialogFlow](./OAuthPrompt_ContinueDialog_DetailedView.svg "Detailed View OAuthPrompt.ContinueDialogFlow()")

