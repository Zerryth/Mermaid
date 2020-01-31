```mermaid

    sequenceDiagram
    
    participant BFS as Bot Framework Service
    participant Bot

    Note over Bot: "api/messages"
    BFS ->> Bot: HTTP POST Message "hi" (Req. 1)
    activate BFS
    activate Bot
        opt REST calls (1)
            Note over BFS: ReplyToActivity (2)
            Bot ->> BFS: HTTP POST w/Bot's reply
            activate Bot
            activate BFS
                BFS -->> Bot: { activityId } (3)
            deactivate BFS
            deactivate Bot

            Bot ->> BFS: HTTP 
            activate Bot
            activate BFS
                BFS -->> Bot: { activityId }
            deactivate BFS
            deactivate Bot

            Bot ->> BFS: HTTP 
            activate Bot
            activate BFS
                BFS -->> Bot: { activityId }
            deactivate BFS
            deactivate Bot

            Bot ->> BFS: HTTP 
            activate Bot
            activate BFS
                BFS -->> Bot: { activityId }
            deactivate BFS
            deactivate Bot
            end

        Bot -->> BFS: 200 to Req. 1
    deactivate Bot
    deactivate BFS
```
1. Calls from Bot to the Bot Framework Service are industry-standard REST API calls with JSON over HTTPS.
2. Example showing Bot calling the Bot Framework Service's `ReplyToActivity` endpoint (`POST v3/conversations/{conversationId}/activities/{activityId}`)
    * Bot can have 1 call in reply to Req. 1 (for example just Sending "Echo: 'hi'" message back) or the bot can have multiple REST calls in response to the Bot Framework Service's Req. 1, as illustrated with the subsequent HTTP calls from the bot.
3. A `ResourceResponse` that contains an id property which specifies the ID of the Activity that was sent to the bot.