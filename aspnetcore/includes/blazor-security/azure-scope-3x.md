<span data-ttu-id="98257-101">Das Azure-Portal stellt abhängig davon, ob der AAD-Mandant über eine [verifizierte oder nicht verifizierte Herausgeberdomäne](/azure/active-directory/develop/howto-configure-publisher-domain) verfügt, eines der folgenden App-ID-URI-Formate bereit:</span><span class="sxs-lookup"><span data-stu-id="98257-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="98257-102">Wenn der zweite der beiden vorangehenden App-ID-URIs in der Client-App verwendet wird, um den Bereichs-URI zu bilden, und die Autorisierung nicht erfolgreich ist, versuchen Sie, den Bereichs-URI ohne Schema und Host anzugeben:</span><span class="sxs-lookup"><span data-stu-id="98257-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="98257-103">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="98257-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
