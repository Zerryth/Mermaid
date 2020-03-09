```mermaid
    graph LR
        AuthServer[/Authorization Server\] -- Issues Token --> Bot(Bot) -- Makes Request with Token --> ProtectedResource{{Protected Resource}}
```