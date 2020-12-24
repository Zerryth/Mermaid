```mermaid
classDiagram
    class LuisRecognizer {
        + stringDeclarativeType
        + string LuisTraceType
        + string LuisTraceLabel
        - LuisRecognizerOptions _luisRecognizerOptions
        + HttpClient DefaultHttpClient
        + bool LogPersonalInformation
        + IBotTelemetryClient TelemetryClient
        - internal HttpClient HttpClient
        + TopIntent(RecognizerResult, defaultIntent, minScore)
        + RecognizeAsync(ITurnContext)
        + RecognizeAsync(DialogContext)
        + RecognizeAsync(ITurnContext, LuisPredictionOptions)
        + RecognizeAsyncOfT(ITurnContext)
        + RecognizeAsyncOfT(DialogContext, Activity)
        + RecognizeAsyncOfT(ITurnContext, LuisPredictionOptions)
        + RecognizeAsync(ITurnContext, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(DialogContext, Activity, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(ITurnContext, LuisPredictionOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsyncOfT(ITurnContext, telemetryProperties, telemetryMetrics)
        + RecognizeAsyncOfT(DialogContext, Activity, telemetryProperties, telemetryMetrics)
        + RecognizeAsyncOfT(ITurnContext, LuisPredictionOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(ITurnContext, LuisRecognizerOptions)
        + RecognizeAsync(DialogContext, Activity, LuisRecognizerOptions)
        + RecognizeAsyncOfT(ITurnContext, LuisRecognizerOptions)
        + RecognizeAsyncOfT(DialogContext, Activity, LuisRecognizerOptions)
        + RecognizeAsync(ITurnContext, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsync(DialogContext, Activity, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsyncOfT(ITurnContext, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        + RecognizeAsyncOfT(DialogContext, Activity, LuisRecognizerOptions, telemetryProperties, telemetryMetrics)
        
    }

```

- `LuisRecognizer`
    - Has 4 ctor overloads that are `Obsoilete` -- `"please use LuisRecognizer(LuisRecognizerOptions recognizer)"`:
        - `LuisRecognizer(LuisApplication, LuisPredictionOptions, includeApiResults, HttpClientHandler)`
        - `LuisRecognizer(LuisApplication, IBotTelemetryClient, logPersonalInformation, LuisPredictionOptions, includeApiResults, HttpClientHandler)`
        - `LuisRecognizer(LuisService, LuisPRedictionOptions, includeApiResults, HttpClientHandler)`
    - ctor we should use: `LuisRecognizer(LuisRecognizerOptions, HttpClientHandler)`
    - `RecognizeAsync`:
        - Obsolete:
            - `RecognizeAsync(ITurnContext, LuisPredictionOptions)`
            - `RecognizeAsync<T>(ITurnContext, LuisPredictionOptions)`
            - `RecognizeAsync(ITurnContext, LuisPredictionOptions, telemetryProperties, telemetryMetrics)`
            - `RecognizeAsync<T>(ITurnContext, LuisPredictionOptions, telemetryProperties, telemetryMetrics)`
        - First 3 `RecognizeAsync` return `Task<RecognizerResult>`
        - Second 3 `RecognizeAsync<T>` return `Task<T>`