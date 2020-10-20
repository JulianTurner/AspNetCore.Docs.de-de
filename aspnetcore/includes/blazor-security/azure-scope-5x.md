Wenn Sie mit einer Server-API arbeiten, die bei AAD registriert ist, und sich die AAD-Registrierung der App in einem Mandanten befindet, der auf einer [nicht überprüften Herausgeberdomäne](/azure/active-directory/develop/howto-configure-publisher-domain) basiert, ist der App-ID-URI Ihrer Server-API-App nicht `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`, sondern hat das `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`-Format. Wenn dies der Fall ist, wird der Standardzugriffstoken-Bereich in `Program.Main` (`Program.cs`) der *`Client`* -App ähnlich wie folgt angezeigt:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

Legen Sie zum Konfigurieren der Server-API-App für eine passende Zielgruppe die `Audience` in der *`Server`* -API-App-Einstellungsdatei (`appsettings.json`) so fest, dass Sie der vom Azure-Portal bereitgestellten Zielgruppe der App entspricht:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

In der vorhergehenden Konfiguration enthält das Ende des `Audience`-Werts **nicht** den Standardbereich `/{DEFAULT SCOPE}`.

Beispiel:

`Program.Main` (`Program.cs`) der *`Client`* -App:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

Konfigurieren Sie die *`Server`* -API-App-Einstellungsdatei (`appsettings.json`) mit einer passenden Zielgruppe (`Audience`):

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

Im vorhergehenden Beispiel enthält das Ende des `Audience`-Werts **nicht** den Standardbereich `/API.Access`.
