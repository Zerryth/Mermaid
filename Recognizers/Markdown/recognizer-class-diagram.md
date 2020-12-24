```mermaid
graph TD
    Recognizer --- LuisAdaptiveRecognizer
    Recognizer --- OrchestratorAdaptiveRecognizer
    Recognizer --- QnAMakerRecognizer
    Recognizer --- MockLuisRecognizer
    Recognizer --- CrossTrainedRecognizerSet
    Recognizer --- ChannelMentionEntityRecognizer
    Recognizer --- EntityRecognizer
    Recognizer --- MultiLanguageRecognizer
    Recognizer --- RecognizerSet
    Recognizer --- RegexRecognizer
    Recognizer --- ValueRecognizer
```

- Classes that have `logPersonalInformation` property:
    - `LuisAdaptiveRecognizer`
    - `QnAmakerRecognizer`

- Classes that override base `Recognizer`'s `FillRecognizerResultTelemetryProperties`:
    - `LuisAdaptiveRecognizer`
