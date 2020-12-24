# The Issue
- Recognizers always log personal identifiable information (PII). They should only log it if log PII flag is true.
- Only 2 of 29 recognizers deriving from `Recognizer` actually have a log PII flag, even though all have the ability, through the base class to log telemetry.

# Context
In the SDK, we have `IRecognizer` and `Recognizer` that classes implement/derive from `IRecognizer` & `Recognizer`. See comparison:

| `IRecognizer`                       | `Recognizer`                                                                          | Both                                                     |
|   :----:                            |   :----:                                                                              |   :----:                                                 |
| Implemented by non-adaptive classes | Derived from by adaptive classes                                                      | Have `RecognizeAsync` method (diff. signatures though)   |
| `LogPersonalInformation` is `bool`  | `LogPersonalInformation` not on base class                                            |                                                          |
|                                     | `LogPersonalInformation` in 2 subclasses is `BoolExpression`                          |                                                          |
|                                     | `LogPersonalInformation` exists only in `LuisAdaptiveRecognizer` & `QnAMakerRecognizer` |                                                          |
|                                     | All subclasses can log telemetry, but most don't have log PII flag                    |                                                          |



### Potential Confusion
- The "gotcha" first looking into the recognizers into our SDK that you'll discover is **`Recognizer` does not implement `IRecognizer`.**
    - It can't, since their `RecognizerAsync` methods have different signatures
- *However* when looking at `tests.schema`, you can see that *all* recognizers, regardless of deriving/implementing `Recognizer`/`IRecognizer` are categoriezed as `Microsoft.IRecognizer` kind.

### Class Diagram of Recognizers in the SDK Currently
```mermaid
graph TD
    Recognizer --- AdaptiveRecognizer

    AdaptiveRecognizer --- LuisAdaptiveRecognizer
    AdaptiveRecognizer --- OrchestratorAdaptiveRecognizer
    AdaptiveRecognizer --- QnAMakerRecognizer
    AdaptiveRecognizer --- CrossTrainedRecognizerSet
    AdaptiveRecognizer --- MockLuisRecognizer

    AdaptiveRecognizer --- ChannelMentionEntityRecognizer
    AdaptiveRecognizer --- EntityRecognizer
        EntityRecognizer -. List of EntityRecognizer .- EntityRecognizerSet
        EntityRecognizer --- TextEntityRecognizer
            TextEntityRecognizer --- AgeEntityRecognizer
            TextEntityRecognizer --- ConfirmationEntityRecognizer
            TextEntityRecognizer --- CurrencyEntityRecognizer
            TextEntityRecognizer --- DateTimeEntityRecognizer
            TextEntityRecognizer --- DimensionEntityRecognizer
            TextEntityRecognizer --- EmailEntityRecognizer
            TextEntityRecognizer --- GuidEntityRecognizer
            TextEntityRecognizer --- HashtagEntityRecognizer
            TextEntityRecognizer --- IpEntityRecognizer
            TextEntityRecognizer --- MentionEntityRecognizer
            TextEntityRecognizer --- NumberRangeEntityRecognizer
            TextEntityRecognizer --- OrdinalEntityRecognizer
            TextEntityRecognizer --- PercentageEntityRecognizer
            TextEntityRecognizer --- PhoneNumberEntityRecognizer
            TextEntityRecognizer --- TemperatureEntityRecognizer
            TextEntityRecognizer --- UrlEntityRecognizer

    AdaptiveRecognizer --- MultiLanguageRecognizer
    AdaptiveRecognizer --- RecognizerSet
    AdaptiveRecognizer --- RegexAdaptiveRecognizer
    AdaptiveRecognizer --- ValueRecognizer
```

### IRecognizer Class Diagram
```mermaid
classDiagram
    IRecognizer <|-- ITelemetryRecognizer
    IRecognizer <|-- OrchestratorRecognizer
    IRecognizer <|-- RuleRecognizer
    ITelemetryRecognizer <|-- LuisRecognizer

    class IRecognizer {
        + RecognizeAsync(ITurnContext)
        + RecognizeAsync(ITurnContext)
    }

    class ITelemetryRecognizer {
        + bool LogPersonalInformation
        + IBotTelemetryClient TelemetryClient

        + RecognizeAsync(ITurnContext, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(ITurnContext)
    }

    class OrchestratorRecognizer {
        + string Id
        + string ModelPath
        + string SnapshotPath
        + Recognizer ExternalEntityRecognizer
        + float DisambiguationScoreThreshold
        + bool DetectAmbiguousIntents

        + RecognizerAsync(ITurnContext)
    }

    class LuisRecognizer {
        + string DeclarativeType
        + string LuisTraceType
        + string LuisTraceLabel
        - LuisRecognizerOptions _luisRecognizerOptions
        + HttpClient DefaultHttpClient
        + bool LogPersonalInformation
        + IBotTelemetryClient TelemetryClient
        - HttpClient HttpClient

        + TopIntent(RecognizerResult, defaultIntent, minScore)

        + RecognizeAsync(ITurnContext)
        + RecognizeAsync(DialogContext, Activity)
        + RecognizeAsync(ITurnContext, LuisPredictionOptions)

        + RecognizeAsync(ITurnContext, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(DialogContext, Activity, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(ITurnContext, LuisPredictionOptions, telemetryProperties, telemetryMetrics)

        + RecognizeAsync(ITurnContext, LuisRecognizerOptions)
        + RecognizeAsync(DialogContext, Activity, LuisRecognizerOptions)

        + RecognizeAsync(ITurnContext, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(DialogContext, Activity, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)

        # OnRecognizerResultAsync(RecognizerResult, ITurnContext, telemetryProperties, telemetryMetrics)
        # FillLuisEvntPropertiesAsync(RecognizerResult, ITurnContext, telemetryProperties)
        - BuildLuisRecognizerOptionsV2(LuisApplication, LuisPredictionOptions, includeApiResults)
        - RecognizeInternalAsync(ITurnContext, LuisRecognizerOptions, telmetryProperties, telemetryMetrics)
        - RecognizeInternalAsync(DialogContext, Activity, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        - MergeDefaultOptionsWithProvidedOptionsV2(LuisPredictionOptions)
        - CreateHttpHandlerPipeline(HttpClientHandler)
        - CreateRootHandler()
    }

    class RuleRecognizer {
        - string DefaultIntent
        
        + Dict_of_string Rules

        + RecognizeAsync(ITurnContext)
    }

```

# Proposed Solution

Add a base `AdaptiveRecognizer` class, from which the adaptive recognizers can inherit from and can gain `LogPersonalInformation` flag with a `FillRecognizerResultTelemetryProperties` method override to determine whether or not to log PII in telemetry.

```mermaid
classDiagram
    Recognzier <|-- AdaptiveRecognizer
    AdaptiveRecognizer <|-- LuisAdaptiveRecognizer
    AdaptiveRecognizer <|-- OrchestratorAdaptiveRecognizer
    AdaptiveRecognizer <|-- QnAMakerRecognizer
    AdaptiveRecognizer <|-- MockLuisRecognizer
    AdaptiveRecognizer <|-- CrossTrainedRecognizerSet
    AdaptiveRecognizer <|-- ChannelMentionEntityRecognizer
    AdaptiveRecognizer <|-- EntityRecognizer
    EntityRecognizer <|.. AllOtherEntitySubclasses
    AdaptiveRecognizer <|-- MultiLanguageRecognizer
    AdaptiveRecognizer <|-- RecognizerSet
    AdaptiveRecognizer <|-- RegexRecognizer
    AdaptiveRecognizer <|-- ValueRecognizer

    class AdaptiveRecognizer {
        + BoolExpression LogPersonalInformation
        
        #FillRecognizerTelemetryProperties(RecognizerResult, telemetryProperties, DialogContext)
    }
```
Sequence diagram illustrating the behavior of logging event.
```mermaid
sequenceDiagram
    Note over OutsideParticipant: Dialog, Bot, etc.
    participant OutsideParticipant
    participant SubclassRecognizer
    participant AdaptiveRecognizer
    Note over AdaptiveRecognizer: Has LogPersonalInformation flag
    Note over SubclassRecognizer, Recognizer: Compiles to one class
    participant Recognizer
    participant TelemetryClient

    OutsideParticipant ->>+ SubclassRecognizer: RecognizeAsync
        
        SubclassRecognizer ->>+ AdaptiveRecognizer: FillRecognizerResultTelemetryProperties
        AdaptiveRecognizer -->>- SubclassRecognizer: Return telemetry properties created based on Log PII
        
        SubclassRecognizer ->>+ Recognizer: TrackRecognizerResult
        Note over SubclassRecognizer, Recognizer: Log telemetry properties for the recognizer result event
            Recognizer ->>+ TelemetryClient: TrackEvent
            Note over Recognizer, TelemetryClient: Log telemetry
            TelemetryClient -->>- Recognizer: 
        Recognizer -->>- SubclassRecognizer: 
    SubclassRecognizer -->>- OutsideParticipant: return RecognizerResult
```
1. Something calls RecognizeAsync on SubclassRecognizer (sub classes such as `LuisAdaptiveRecognizer`, `QnARecognizer`, `RegexRecognizer`, etc.)
2. SubclassRecognizer by default will use `AdaptiveRecognizer.FillRecognizerResultTelemetryProperties` and `Recognizer.TrackRecognizerResult` methods to log telemetry
3. Returns `RecognizerResult` to original caller of `RecognizeAsync`

# Why Can't We Just...
Why can't we just add `LogPerosnalInformation` to the already-existing `Recognizer` class?
* `Recognizer` class is in `Microsoft.Bot.Builder.Dialogs` library, which does not have the `BoolExpression` type, which we get from `AdaptiveExpressions` library.
* We need `LogPersonalInformation` to be `BoolExpression` for adaptive recognizers to be able to set flag's value in configuration files like appsettings.json (also necessary for Composer)
* In non-adaptive classes like `LuisRecognizer`, which implements `IRecognizer`, `LogPersonalInformation` is `bool`.
    * This might indicate that we should probably also create a base class for non-adaptive recognizers
        * IDK if this will break backwards compat
        * It also seems less likely that any new recognizer classes we'll add wouldn't derive instead from `Recognizer`/`AdaptiveRecognizer`, and therefore less pressing