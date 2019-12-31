### Adding Authentication to Bot

#### Higher Level
```mermaid
    graph LR
        Channel[/Channel/] --- AAD1{{AAD App Registration 1}}
        AAD1  --- Bot((Bot))
        
        ExternalService --- AAD2{{AAD App Registration 2}}
        AAD2  --- Bot
```
* AAD is an Identity Provider, used as the Authorization Server to which:
    * User authenticates identity to
    * If authenticated, then Authz server provides Bot with Token
* External Services separately sepcify which Authz Servers they trust to issue Tokens
* Bot can use Token obtained from Authz Server in its requests to access External Services' APIs

#### Detailed View
```mermaid
    graph LR
    
    subgraph Channel-to-Bot Access
        Channel[/Channel/] --- AAD1{{AAD App Registration 1*}}
    end
    AAD1 -- Secures Channel-To-Bot Access --- BotChannelsRegistration((Bot Channels Registration**))
    
    subgraph Bot to External Resource
        AAD2{{AAD App Registration 2}} --- ExternalService
    end
    AAD2 -- OAuth Connection with AAD's client ID, client secret, tenant ID, scopes--> BotChannelsRegistration


```
* *Created automatically when creating a Web App Bot in Azure Portal
* ** Is "bot" just the "Bot Channels Registration" or does "bot" encompass more (App Service, Bot Channels Registration, cognitive keys, etc.)?
