# Skill Response into Consumer's Skill Handler

```mermaid
sequenceDiagram

    participant Skill
    Note left of Skill: Message "skill" 
    participant ConsumerController as Consumer Controller *1
    participant SkillHandler as Skill Handler *2
    participant JwtTokenValidation
    participant ConvoIdFactory
    participant Activity
    participant Adapter
    participant TurnContext

    Note over Skill, SkillHandler: "v3/conversations/{conversationId}/activities/{activityId}"
    Skill ->> ConsumerController: HTTP POST
    activate Skill
    activate ConsumerController
        ConsumerController ->> ConsumerController: ReplyToActivityAsync()
        activate ConsumerController
            ConsumerController ->> SkillHandler: HandleReplyToActivityAsync()
            activate ConsumerController
            activate SkillHandler
                SkillHandler ->> SkillHandler: AuthenticateAsync()
                activate SkillHandler
                    SkillHandler ->> JwtTokenValidation: ValidateAuthHeader()
                    activate SkillHandler
                    activate JwtTokenValidation
                        JwtTokenValidation ->> JwtTokenValidation: AuthenticateToken()
                        JwtTokenValidation ->> JwtTokenValidation: ValidateClaimsAsync()
                        JwtTokenValidation -->> SkillHandler: return ClaimsIdentity
                    deactivate JwtTokenValidation
                    deactivate SkillHandler
                deactivate SkillHandler

                SkillHandler ->> SkillHandler: OnReplyToActivityAsync()
                activate SkillHandler
                    SkillHandler ->> SkillHandler: ProcessActivityAsync()
                    activate SkillHandler
                        SkillHandler ->> ConvoIdFactory: GetConversationReferenceAsync()
                        activate SkillHandler
                        activate ConvoIdFactory
                            ConvoIdFactory -->> SkillHandler: return original channel-consumer conversation reference
                        deactivate ConvoIdFactory
                        deactivate SkillHandler

                        SkillHandler ->> Activity: GetConversationReference()
                        activate SkillHandler
                        activate Activity
                            Activity -->> SkillHandler: return consumer-skill conversation reference
                        deactivate Activity
                        deactivate SkillHandler

                        SkillHandler ->> Adapter: ContinueConversationAsync()
                        activate SkillHandler
                        activate Adapter
                            Note over Adapter, TurnContext: Create new TurnContext
                            Note over Adapter, TurnContext: for user-consumer conversation

                            Adapter ->> TurnContext: Add BotIdentity (ClaimsIdentity) and BotCallbackHandler to TurnState
                            activate Adapter
                            activate TurnContext
                                TurnContext -->> Adapter: 
                            deactivate TurnContext
                            deactivate Adapter

                            Adapter ->> Adapter: EnsureChannelConnectorClientIsCreatedAsync()
                            activate Adapter
                                Note over Adapter: Note *3

                                
                            deactivate Adapter
                        deactivate Adapter
                        deactivate SkillHandler
                    deactivate SkillHandler
                deactivate SkillHandler
            deactivate SkillHandler
            deactivate ConsumerController
        deactivate ConsumerController
    deactivate ConsumerController
    deactivate Skill
```
- *1 Consumer's Skill controller is a `ChannelServiceController`
- *2 Skill Handler is a `ChannelServiceHandler`
- *3 When a parent bot is deployed to multiple instances the cache AppIds, ConnectorClients, and Trusted URL are not initialized if the server instance hasn't been hit by a request form the channel. THis code ensures that the required objects are created.