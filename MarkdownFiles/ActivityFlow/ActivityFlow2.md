## Activity Flow
### *Modeled after the C# EchoBot example code in ['How bots work'](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0&tabs=csharp#bot-logic) documentation.*

```mermaid
    sequenceDiagram
        participant Channel
        participant BFS as Bot Framework Service
        participant WebServer as Web Server
        Note over WebServer: ASP.NET BotController
        participant Connector
        participant Adapter as AdapterWithErrorHandler
        participant BotFrameworkHttpAdapter
        participant BotFrameworkAdapter
        participant BotAdapter
        Note over Adapter, BotAdapter: Adapter
        participant Middleware
        participant TurnContext
        participant ActivityHandler
        participant Bot

        Note left of Channel: User Message, "Hi"
        Note left of Channel: Inbound Message
        activate Channel
        Channel ->> BFS: HTTP POST
        activate BFS
        Note right of Channel: JSON payload of Activity

        BFS ->> WebServer: HTTP POST
        activate WebServer
        Note right of BFS: JSON Payload of Activity

        WebServer ->> Adapter: PostAsync()
        activate Adapter
        
        Adapter ->> BotFrameworkHttpAdapter: ProcessAsync()
        activate BotFrameworkHttpAdapter
        BotFrameworkHttpAdapter ->> BotFrameworkAdapter: ProcessAsync()
        Note right of BotFrameworkHttpAdapter: Deserialize Activity
        activate BotFrameworkAdapter
        BotFrameworkAdapter ->> BotAdapter : ProcessActivityAsync()
        Note right of BotFrameworkAdapter: Create TurnContext
        Note right of BotFrameworkAdapter: Call Middleware
        activate BotAdapter
            BotAdapter ->> Middleware: RunPipeline()
            activate Middleware
        loop Uncalled Middleware
            Middleware ->> Middleware: OnTurnAsync()
        end

        Middleware ->> ActivityHandler: OnTurnAsync()
        activate ActivityHandler
        Note over Middleware, ActivityHandler: Call Bot's Turn Handler
        alt is Message
                ActivityHandler ->> Bot: OnMessageAsync()
                activate Bot
            
            else is ConversationUpdate
                ActivityHandler ->> Bot: OnConversationUpdateActivityAsync()
            
            else is MessageReaction
                ActivityHandler ->> Bot: OnMessageReactionActivityAsync()
            
            else is Event
                ActivityHandler ->> Bot: OnEventActivityAsync()
            
            else is Unreognized Activity Type
                ActivityHandler ->> Bot: OnUnrecognizedActivityTypeAsync()
        end

        Note right of Bot: Bot Message "Echo: Hi"
        Note right of Bot: Outbound Message
        Bot ->> TurnContext: SendActivityAsync()
        activate TurnContext

        alt no callbacks registered
                TurnContext ->> BotAdapter: SendActivitiesThroughAdapter() calls Adapter.SendActivitiesAsync()
            else if registered Middleware
                TurnContext ->> Middleware: SendActivitiesThroughCallbackPipeline()
        end

        loop Uncalled Middleware
            Middleware ->> Middleware: Call next callback in Send Activities List in TurnContext
        end

        Middleware ->> BotAdapter: SendActivitiesThroughAdapter(): 
        BotAdapter ->> BotFrameworkAdapter: SendActivitiesAsync()
        alt has ReplyToId
            BotFrameworkAdapter ->> Connector: ReplyToActivityAsync() 
            activate Connector
        else no ReplyToId
            BotFrameworkAdapter ->> Connector: SendToConversationAsync()
        end

        Connector ->> BFS: ReplyToActivityWithHttpMessagesAsync()

        BFS ->> Channel: HTTP POST

        Channel -->> Bot: 200 OK to Outbound Message
        Bot -->> Channel: 200 OK to Inbound Message

        deactivate TurnContext
        deactivate Bot
        deactivate ActivityHandler
        deactivate Middleware
        deactivate BotAdapter
        deactivate BotFrameworkAdapter
        deactivate BotFrameworkHttpAdapter
        deactivate Adapter
        deactivate WebServer
        deactivate Connector
        deactivate BFS
        deactivate Channel
        
```