# Mermaid UML Diagrams
Diagrams created using [mermaid](https://github.com/mermaid-js/mermaid), modeling portions of Microsoft Bot Framework.

## Sequence Diagrams
### Message Sequence Diagram
Clone of diagram from documentation on [How bots work](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-basics?view=azure-bot-service-4.0&tabs=csharp).

![Message Sequence](GraphSVGs/MessageSequence.svg "Message Sequence Diagram")

Note: I'm not quite sure why default styling made this diagram so GIANT, but at least with mermaid you have the option of tinkering around with [styling of your sequence diagrams](https://mermaidjs.github.io/#/sequenceDiagram?id=styling), if your heart so desired.

____

### State Sequence Diagram
Clone of diagram from documentation on [Managing state](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-concept-state?view=azure-bot-service-4.0).

![State Sequence](GraphSVGs/StateSequence.svg "State Sequence Diagram")

____

## Class Diagrams

### Prompt Class Diagram
Abbreviated work by only showing details of NumberPrompt class out of the subclasses of Prompt class.

![Prompt Class Diagram](GraphSVGs/PromptClassDiagram.svg "Prompt Class Diagram")

### ChoicePrompt Association
![ChoicePrompt Association](GraphSVGs/ChoicePromptAssociation.svg "ChoicePrompt Association")

____

## How were these graphs created?
1. In VS Code, created md file to write the mermaid code for the graphs
    * Made sure to use mermaid extension that would render graphs in VS Code to preview md while building the models

2. Went to mermaid's [live editor](https://mermaidjs.github.io/mermaid-live-editor/#/edit/eyJjb2RlIjoiZ3JhcGggVERcbkFbQ2hyaXN0bWFzXSAtLT58R2V0IG1vbmV5fCBCKEdvIHNob3BwaW5nKVxuQiAtLT4gQ3tMZXQgbWUgdGhpbmt9XG5DIC0tPnxPbmV8IERbTGFwdG9wXVxuQyAtLT58VHdvfCBFW2lQaG9uZV1cbkMgLS0-fFRocmVlfCBGW2ZhOmZhLWNhciBDYXJdXG4iLCJtZXJtYWlkIjp7InRoZW1lIjoiZGVmYXVsdCJ9fQ) to create SVGs of the models coded up in VS Code
    * Note: should dig into using [mermaid CLI](https://github.com/mermaidjs/mermaid.cli) to generate svg/png/pdf files
    
3. Included SVGs in GH repo to refer to in README