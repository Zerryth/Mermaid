## Activity Flow
### *Modeled after the C# EchoBot example code in ['How bots work'](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0&tabs=csharp#bot-logic) documentation.*

#### C#:
![Activity Flow Diagram](../../GraphSVGs/CsharpActivity.svg "Activity Flow C# EchoBot")

#### JS:
![Activity Flow Diagram](../../GraphSVGs/JSActivity.svg "Activity Flow JS EchoBot")

#### Generalized:
![Activity Flow Diagram](../../GraphSVGs/GeneralActivityFlow2.svg "Generalized Activity Flow")

___

C#

### `ProcessAsync()` Flow
![Process Async](../../GraphSVGs/ProcessAsync.svg "Activity Flow C# EchoBot")

___

### `ProcessActivityAsync()` Flow
![ProcessActivityAsync](../../GraphSVGs/ProcessActivityAsync.svg "ProcessActivityAsync")

___
___
### *Hierarchies that I looked into in order to better understand and learn about the Activity flow*

### EchoBot's Adapter Class Diagram
![EchoBot's Adapter Hierarchy](../../GraphSVGs/EchoAdapterHierarchy.svg "EchoBot's Adapter Hierarchy")

### MiddlewareSet Class Diagram
![MiddlewareSet Class Diagram](../../GraphSVGs/MiddlewareSetClassDiagram.svg "MiddlewareSet Class Diagram")

### TurnContext Class Diagram
![TurnContext Class Diagram](../../GraphSVGs/TurnContext.svg "TurnContext Class Diagram")