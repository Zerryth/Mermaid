```mermaid
    classDiagram
        class BotController {
            - IBotFrameworkHttpAdapter Adapter
            - IBot Bot
            + PostAsync()
        }
    
    ControllerBase <|-- BotController
```