## Activity Flow
### *Modeled after the C# EchoBot example code in ['How bots work'](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0&tabs=csharp#bot-logic) documentation.*

```mermaid
    sequenceDiagram
        participant Channel
        participant BFS as Bot Framework Service
        participant WebServer as Web Server
        Note over WebServer: ASP.NET BotController
        participant Adapter as AdapterWithErrorHandler
        participant BotFrameworkHttpAdapter
        participant BotFrameworkAdapter
        participant BotAdapter
        participant Middleware
        participant TurnContext
        participant ActivityHandler
        participant Bot

        activate Channel

        Channel ->> BFS: HTTP POST
        activate BFS
        Note right of Channel: JSON payload of Activity info

        BFS ->> WebServer: HTTP POST
        activate WebServer
        Note right of BFS: JSON Payload of Activity info

        WebServer ->> Adapter: PostAsync()
        activate Adapter
        rect rgba(0, 0, 255, .1)
            Adapter ->> BotFrameworkHttpAdapter: ProcessAsync()
            activate BotFrameworkHttpAdapter
            BotFrameworkHttpAdapter ->> BotFrameworkAdapter: ProcessAsync()
            activate BotFrameworkAdapter
            BotFrameworkAdapter ->> BotAdapter : ProcessActivityAsync()
            activate BotAdapter
            BotAdapter ->> Middleware: RunPipeline()
            activate Middleware
        end
        Note over Adapter, BotAdapter: Adapter
        loop If uncalled Middleware
            Middleware ->> Middleware: OnTurnAsync()
        end

        Middleware ->> ActivityHandler: OnTurnAsync()
        activate ActivityHandler
        Note over Middleware, Bot: Call Turn Handler of Bot registered in Adapter's .ProcessAysnc() call
        alt is Message
                ActivityHandler ->> Bot: OnMessageAsync()
                activate Bot
            
            else is ConversationUpdate
                ActivityHandler ->> Bot: OnConversationUpdateActivityAsync()
            
            else is MessageReaction
                ActivityHandler ->> Bot: OnMessageReactionActivityAsync()
            
            else is Message
                ActivityHandler ->> Bot: OnMessageAsync()
            
            else is Event
                ActivityHandler ->> Bot: OnEventActivityAsync()
            
            else is Unreognized Activity Type
                ActivityHandler ->> Bot: OnUnrecognizedActivityTypeAsync()
        end

        Bot ->> TurnContext: SendActivityAsync()
        activate TurnContext

        alt no callbacks registered
                TurnContext ->> BotAdapter: SendActivitiesThroughAdapter() calls Adapter.SendActivitiesAsync()
            else if registered Middleware
                TurnContext ->> Middleware: SendActivitiesThroughCallbackPipeline()
        end

        loop If uncalled Middleware
            Middleware ->> Middleware: Call next callback in Send Activities List in TurnContext
        end

        Middleware ->> BotAdapter: SendActivitiesThroughAdapter(): 
        BotAdapter ->> BotFrameworkAdapter: SendActivitiesAsync()
        alt has ReplyToId
            BotFrameworkAdapter ->> BFS: ReplyToActivityAsync() 
        else no ReplyToId
            BotFrameworkAdapter ->> BFS: SendToConversationAsync()
        end

        BFS ->> Channel: ReplyToActivityWithHttpMessagesAsync()

        Channel -->> Bot: 200 OK
        Bot -->> Channel: 200 OK

        deactivate TurnContext
        deactivate Bot
        deactivate ActivityHandler
        deactivate Middleware
        deactivate BotAdapter
        deactivate BotFrameworkAdapter
        deactivate BotFrameworkHttpAdapter
        deactivate Adapter
        deactivate WebServer
        deactivate BFS
        deactivate Channel
        
```