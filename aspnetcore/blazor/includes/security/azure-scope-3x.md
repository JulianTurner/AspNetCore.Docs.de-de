Das Azure-Portal stellt abhängig davon, ob der AAD-Mandant über eine [verifizierte oder nicht verifizierte Herausgeberdomäne](/azure/active-directory/develop/howto-configure-publisher-domain) verfügt, eines der folgenden App-ID-URI-Formate bereit:

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

Wenn der zweite der beiden vorangehenden App-ID-URIs in der Client-App verwendet wird, um den Bereichs-URI zu bilden, und die Autorisierung nicht erfolgreich ist, versuchen Sie, den Bereichs-URI ohne Schema und Host anzugeben:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

Beispiel:

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
