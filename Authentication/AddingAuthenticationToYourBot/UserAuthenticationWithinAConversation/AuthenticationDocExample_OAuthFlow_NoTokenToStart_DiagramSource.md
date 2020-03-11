### Bot does not have token yet, OAuth flow

```mermaid
sequenceDiagram

participant User
participant BFCS as BF Channel Service
participant BFTS as BF Token Service
participant ASAuth as AS *1 Auth Endpoint
participant ASToken as AS Token Endpoint
Note over ASAuth, ASToken: Authorization Server
Note over ASAuth, ASToken: e.g. AAD, GitHub, Facebook, etc.
participant Bot
participant ProtectedResource as Protected Resource


User ->> BFCS: Message Activity "Please check my email"
BFCS ->> Bot: Receives Msg Activity & Determines intent is to check email
Bot ->> BFTS: Do we have Token already? (https://api.botframework.com/api/usertoken/GetToken)
Note over BFTS, Bot: Token for the given userId for the OAuth connection setting called "GraphConnection"

alt Does Not Have Token
    BFTS -->> Bot: Return [Token] NotFound result
    Note over ASAuth, ASToken: Such as 1st time User interacts w/Bot
end

Bot ->> BFCS: Creates OAuthCard, asks User to sign in
BFCS ->> BFTS: Create Valid OAuth sign-in URL for request
BFTS -->> BFCS: Valid sign-in URL added to OAuthCard
BFCS ->> User: Send Message with OAuthCard that has Sign-In Button
User ->> BFCS: User clicks Sign-In button
BFCS ->> ASAuth: Calls Authorization Server to load Sign-In page/pop-up
ASAuth ->> User: Serves Sign-In page/pop-up

opt Grant Type: Authorization Code *2
    User ->> ASAuth: User signs in & authorizes Bot
    ASAuth -->> BFTS: Returns authorization code
    Note over BFTS, ASToken: https://token.botframework.com/.auth/web/redirect
    BFTS ->> ASToken: Uses authorization code to obtain Token
    ASToken -->> BFTS: Returns Token
    BFTS ->> BFTS: Securely stores Token
    BFTS -->> Bot: Returns Token

end

Bot ->> ProtectedResource: Makes request with Token to access Protected Resource
Note over Bot, ProtectedResource: Calls "check email" API

```

1. Authorization Server (AS).
    * The AS can be within Azure, such as using AAD as our token provider, or outside of Azure as well, like in the case of using GitHub as the AS.
2. There are many different authorization grants, or OAuth flows, that detail how exactly how to use the identity of the owner of the protected resource in exchange for an access token (e.g. authorization code, client credentials, device code, refresh token, implicit, etc.)