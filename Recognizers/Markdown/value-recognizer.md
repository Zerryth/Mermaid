`AdaptiveDialog`

    OnRecognizeAsync
        - If there aren't any recognizers in the recognizerSet
            - add this.Recognizer (RegexRecgnzr)   
            - add new ValueRecognizer
    
        - recognizerSet.RecognizeAsync(DialogContext, Activity, CT, telemProps, telemMetrics) (this is actually one on RecognizerSet)
            - 