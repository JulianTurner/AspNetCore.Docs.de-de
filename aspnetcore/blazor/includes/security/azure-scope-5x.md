---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: 5964554c36e2242b70faee390374828acd2bd860
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551754"
---
<span data-ttu-id="19f6a-101">Wenn Sie mit einer Server-API arbeiten, die bei AAD registriert ist, und sich die AAD-Registrierung der App in einem Mandanten befindet, der auf einer [nicht überprüften Herausgeberdomäne](/azure/active-directory/develop/howto-configure-publisher-domain) basiert, ist der App-ID-URI Ihrer Server-API-App nicht `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`, sondern hat das `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`-Format.</span><span class="sxs-lookup"><span data-stu-id="19f6a-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="19f6a-102">Wenn dies der Fall ist, wird der Standardzugriffstoken-Bereich in `Program.Main` (`Program.cs`) der *`Client`* -App ähnlich wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="19f6a-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="19f6a-103">Legen Sie zum Konfigurieren der Server-API-App für eine passende Zielgruppe die `Audience` in der *`Server`* -API-App-Einstellungsdatei (`appsettings.json`) so fest, dass Sie der vom Azure-Portal bereitgestellten Zielgruppe der App entspricht:</span><span class="sxs-lookup"><span data-stu-id="19f6a-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="19f6a-104">In der vorhergehenden Konfiguration enthält das Ende des `Audience`-Werts **nicht** den Standardbereich `/{DEFAULT SCOPE}`.</span><span class="sxs-lookup"><span data-stu-id="19f6a-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="19f6a-105">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="19f6a-105">Example:</span></span>

<span data-ttu-id="19f6a-106">`Program.Main` (`Program.cs`) der *`Client`* -App:</span><span class="sxs-lookup"><span data-stu-id="19f6a-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="19f6a-107">Konfigurieren Sie die *`Server`* -API-App-Einstellungsdatei (`appsettings.json`) mit einer passenden Zielgruppe (`Audience`):</span><span class="sxs-lookup"><span data-stu-id="19f6a-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="19f6a-108">Im vorhergehenden Beispiel enthält das Ende des `Audience`-Werts **nicht** den Standardbereich `/API.Access`.</span><span class="sxs-lookup"><span data-stu-id="19f6a-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
