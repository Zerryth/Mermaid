```mermaid
    sequenceDiagram
        participant OAuthPrompt
        participant BFA as BotFrameworkAdapter
        participant ICredentialProvider

        OAuthPrompt ->> BFA: .GetUserTokenAsync()
            BFA ->> BFA: CreateOAuthApiClientAsync()
            BFA ->> BFA: GetOAuthScope()
            
            alt OAuthPromptSetting has credentials
                BFA ->> BFA: use AppCredentials from OAuthPromptSetting
                else No credentials in OAuthPromptSetting
                    BFA ->> BFA: GetAppCredentialsAsync()
                    alt has cached credentials
                        BFA ->> BFA: return AppCredentials from cache
                        
                        else no cached credentials
                            Note over BFA: using appId
                            BFA ->> BFA: BuildCredentialsAsync()
                            BFA ->> ICredentialProvider: GetAppPasswordAsync()
                            ICredentialProvider -->> BFA: return app password
                            BFA ->> BFA: cache AppCredentials
                            BFA ->> BFA: return AppCredentials *
                    end
            end


```
-  `BuildCredentialsAsync()` returns either `MicrosoftAppCredentials` or `MicrosoftGovernmentAppCredentials` type of `AppCredentials`
    - override this method if you want to derive a different type of `AppCredentials`, such as `CertificateAppCredentials`.