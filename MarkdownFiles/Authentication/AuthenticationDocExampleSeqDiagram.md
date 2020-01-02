Illustrating example in [Authentication docs](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-authentication?view=azure-bot-service-4.0#about-the-bot-framework-token-service) describing the "Bot Framework Token Service"

> For example, a bot that can check a user's recent emails, using the Microsoft Graph API, will require an Azure Active Directory user token. At design time, the bot developer would register an Azure Active Directory application with the Bot Framework Token Service (via the Azure Portal), and then configure an OAuth connection setting ( named GraphConnection) for the bot.

#### Goal

```mermaid
graph LR

User -- "#quot;Please check my email#quot;" --> Bot

```

#### Detailed View

```mermaid
sequenceDiagram

participant User
participant BFCS as BF Channel Service
participant BFTS as BF Token Service
participant TokenStorage as Token Storage
Note over BFCS, TokenStorage: Part of Azure Bot Service
participant Bot
participant ExternalService as External Service


User ->> BFCS: Message Activity "Please check my email"
BFCS ->> Bot: Receives Msg Activity & Determines intent is to check email
Bot ->> BFTS: Do we have Token already?
Note over BFCS, Bot: For the given userId for the OAuth connection setting called GraphConnection
alt No Token
    BFTS -->> Bot: Return [Token] NotFound result
    Note over BFTS, Bot: Such as 1st time user interacts w/Bot
    Bot ->> BFCS: Creates OAuthCard, asks user to sign in
    BFCS ->> BFTS: Create Valid OAuth sign-in URL for request
    BFTS -->> BFCS: Valid sign-in URL added to OAuthCard
    BFCS ->> User: Send Message with OAuthCard that has Sign-In Button
    User ->> BFCS: User clicks Sign-In button
    BFCS ->> ExternalService: Opens browser/pop-up & calls External Service to open Sign-In page
    ExternalService -->> User: Serves Sign-In page
    User ->> BFCS: User signs in
    BFCS ->> BFTS: User signs in
    BFTS -->> BFCS: Authorization code sent to BFCS
    BFCS ->> BFTS: Uses Authorization code to obtain Token
    BFTS ->> BFTS: Creates Token
    BFTS ->> TokenStorage: Securely stores Token
    BFTS ->> Bot: Returns Token
else Has Token
    BFTS -->> Bot: Returns stored Token
end

Bot ->> ExternalService: Makes request with Token to External Service
Note over Bot, ExternalService: Calls "check email" API

```
* Does the Token Return to BF Channel Service upon creation of Token or does it Return directly from Token Service to Bot?
* This part confuses me in the docs, because as far as I'm aware, External Service doesn't issue Tokens itself, that's the Authorization Server's job (which in this example is the "BF Token Service" as I understand it):
    > The user signs-in to this page for the external service. Once complete, the external service completes the OAuth protocol exchange with the Bot Framework Token Service, resulting in the external service sending the Bot Framework Token Service the user token. The Bot Framework Token Service securely stores this token and sends an activity to the bot with this token.

    * If it's true that the External Service makes Tokens itself, then the diagram would look like:

```mermaid
sequenceDiagram

participant User
participant BFCS as BF Channel Service
participant BFTS as BF Token Service
participant TokenStorage as Token Storage
Note over BFCS, TokenStorage: Part of Azure Bot Service
participant Bot
participant ExternalService as External Service


User ->> BFCS: Message Activity "Please check my email"
BFCS ->> Bot: Receives Msg Activity & Determines intent is to check email
Bot ->> BFTS: Do we have Token already?
Note over BFCS, Bot: For the given userId for the OAuth connecting called GraphConnection
alt No Token
    BFTS -->> Bot: Return [Token] NotFound result
    Note over BFTS, Bot: Like 1st time user interacts w/Bot
    Bot ->> BFCS: Creates OAuthCard, asks user to sign in
    BFCS ->> BFTS: Create Valid OAuth sign-in URL for request
    BFTS -->> BFCS: Valid sign-in URL added to OAuthCard
    BFCS ->> User: Send Message with OAuthCard that has Sign-In Button
    User ->> BFCS: User clicks Sign-In button
    BFCS ->> ExternalService: Opens Browser & calls External Service to open Sign-In page
    ExternalService -->> User: Serves Sign-In page
    User ->> ExternalService: User signs in
    ExternalService -->> BFTS: Returns Token
    BFTS ->> TokenStorage: Securely stores Token
    BFTS ->> Bot: Sends Activity to Bot with Token
else Has Token
    BFTS -->> Bot: Returns stored Token
end

Bot ->> ExternalService: Makes request with Token to External Service
Note over Bot, ExternalService: Calls "check email" API
```

Using my knowledge of OAuth 2.0 in general though, and not following word-for-word what the docs say, I think the OAuth flow actually looks like this:

```mermaid
sequenceDiagram

participant User
participant BFCS as BF Channel Service
participant IdPAuth as IdP Auth Endpoint
participant IdPToken as IdP Token Endpoint
participant TokenStorage as Token Storage
Note over IdPAuth, IdPToken: Identity Provider
Note over IdPAuth, IdPToken: e.g. AAD, GitHub, Facebook, etc.
Note over BFCS, TokenStorage: Setup within Azure Bot Service
participant Bot
participant ExternalService as External Service


User ->> BFCS: Message Activity "Please check my email"
BFCS ->> Bot: Receives Msg Activity & Determines intent is to check email
Bot ->> TokenStorage: Do we have Token already?
Note over BFCS, Bot: For the given userId for the OAuth connection setting called GraphConnection
alt No Token
    TokenStorage -->> Bot: Return [Token] NotFound result
    Note over TokenStorage, Bot: Such as 1st time user interacts w/Bot
    Bot ->> BFCS: Creates OAuthCard, asks user to sign in
    BFCS ->> IdPAuth: Create Valid OAuth sign-in URL for request
    IdPAuth -->> BFCS: Valid sign-in URL added to OAuthCard
    BFCS ->> User: Send Message with OAuthCard that has Sign-In Button
    User ->> BFCS: User clicks Sign-In button
    BFCS ->> IdPAuth: Redirects User to Sign-In to authenticate
    IdPAuth -->> User: Serves Sign-In page/pop-up
    User ->> BFCS: User signs in
    BFCS ->> IdPAuth: User signs in
    IdPAuth -->> BFCS: Authorization code
    BFCS ->> IdPToken: Uses Authorization code to obtain Token
    IdPToken ->> IdPToken: Creates Token
    IdPToken ->> TokenStorage: Securely stores Token
    IdPToken ->> BFCS: Returns Token
    BFCS ->> Bot: Returns Token
else Has Token
    TokenStorage -->> BFCS: Returns stored Token
    BFCS -->> Bot: Returns stored Token
end

Bot ->> ExternalService: Makes request with Token to External Service
Note over Bot, ExternalService: Calls "check email" API

```