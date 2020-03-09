```mermaid
    sequenceDiagram
        participant Channel
        participant BFS as Bot Framework Service
        participant WebServer as Web Server *1
        Note over WebServer: CSharp: ASP.NET
        Note over WebServer: JS: Restify or Express
        participant Connector
        participant Adapter 
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

        WebServer ->> Adapter: process() via POST
        activate Adapter
        
        Note right of Adapter: Deserialize Activity
        Note right of Adapter: Create TurnContext
        Note right of Adapter: Call Middleware
        
        Adapter ->> Middleware: run middleware pipeline
        activate Middleware
        loop if registered Middleware
            Middleware ->> Middleware: Call all registered Middleware in pipeline
        end

        Middleware ->> ActivityHandler: onTurn()
        activate ActivityHandler
        Note over Middleware, ActivityHandler: Call Bot's Turn Handler

        alt is Message
                ActivityHandler ->> Bot: onMessage()
                activate Bot
            
            else is ConversationUpdate
                ActivityHandler ->> Bot: onConversationUpdateActivity()
            
            else is MessageReaction
                ActivityHandler ->> Bot: onMessageReactionActivity()
            
            else is Event
                ActivityHandler ->> Bot: onEventActivity()
            
            else is Unreognized Activity Type
                ActivityHandler ->> Bot: onUnrecognizedActivityType()
        end

        Note right of Bot: Bot Message "Echo: Hi"
        Note right of Bot: Outbound Message
        Bot ->> TurnContext: sendActivity()
        activate TurnContext

        TurnContext ->> Middleware: SendActivitiesThroughCallbackPipeline()

        loop if registered Middleware
            Middleware ->> Middleware: Call Middleware registered on Send Activities
        end

        Middleware ->> Adapter: sendActivities(): 
        alt has replyToId
            Adapter ->> Connector: replyToActivity() 
            activate Connector
        else no replyToId
            Adapter ->> Connector: sendToConversation()
        end

        Connector ->> BFS: Reply to Activity With Http Messages *2

        BFS ->> Channel: HTTP POST

        Channel -->> Bot: 200 OK to Outbound Message
        Bot -->> Channel: 200 OK to Inbound Message

        deactivate TurnContext
        deactivate Bot
        deactivate ActivityHandler
        deactivate Middleware
        deactivate Adapter
        deactivate WebServer
        deactivate Connector
        deactivate BFS
        deactivate Channel
        
```

