```mermaid
    classDiagram
        JwtTokenValidation .. ChannelValidation
        JwtTokenValidation .. EnterpriseChannelChannelValidation
        JwtTokenValidation .. EmulatorValidation:  validates Channel with
        JwtTokenValidation .. SkillValidation
        JwtTokenValidation .. GovernmentChannelValidation
        ChannelValidation --> JwtTokenExtractor
        EnterpriseChannelChannelValidation --> JwtTokenExtractor
        EmulatorValidation --> JwtTokenExtractor: extracts & validates token with
        SkillValidation --> JwtTokenExtractor
        GovernmentChannelValidation --> JwtTokenExtractor


        JwtTokenValidation --> AppCredentials: trusts serviceUrl with
```