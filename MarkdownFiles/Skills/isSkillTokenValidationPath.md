        Note over ChannelValidationClass, Middleware: for Skills path, verify if is Skill Token:
        ChannelValidationClass ->> ChannelValidationClass: isValidTokenFormat()
        ChannelValidationClass ->> ChannelValidationClass: decode the Bearer Token, then get claims
        ChannelValidationClass ->> ChannelValidationClass: isSkillClaim()
        Note over ChannelValidationClass, Middleware: Skill Token must have:
        Note over ChannelValidationClass, Middleware: "ver", "aud", (v1: "appId" or v2: "azp")
        Note over ChannelValidationClass, Middleware: and "aud" !== "appId"/"azp" -- communication is bot-to-bot