```mermaid
    graph LR
    User(User) -- HTTP --> SimpleRootBot{{"SimpleRootBot (Parent)"}}
    SimpleRootBot -- POST Activities --> EchoSkillBot[/"EchoSkillBot (Child)"/]

    EchoSkillBot -. Return Skill responses .-> SimpleRootBot
    SimpleRootBot -. Return Skill responses .-> User
```