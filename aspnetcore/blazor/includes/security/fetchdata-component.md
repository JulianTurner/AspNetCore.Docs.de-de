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
ms.openlocfilehash: 8772f383a830936881564ca95a7f034ba08a5798
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552709"
---
Die `FetchData`-Komponente zeigt Folgendes:

* Stellen Sie ein Zugriffstoken bereit.
* Verwenden Sie das Zugriffstoken zum Aufrufen einer geschützten Ressourcen-API in der *Server*-App.

Die [`@attribute [Authorize]`](xref:mvc/views/razor#attribute)-Anweisung zeigt dem Blazor WebAssembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente anzuzeigen. Das Vorhandensein des Attributs in der `Client`-App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmeldeinformationen aufgerufen wird. Die `Server`-App muss auch `[Authorize]` auf den entsprechenden Endpunkten verwenden, um sie ordnungsgemäß zu schützen.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> übernimmt die Anforderung eines Zugriffstokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann. Wenn das Token zwischengespeichert wurde oder der Dienst ohne Benutzerinteraktion ein neues Zugriffstoken bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt. Andernfalls schlägt die Tokenanforderung mit einer <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> fehl, die in einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung abgefangen wird.

Um das eigentliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die App überprüfen, ob die Anforderung erfolgreich war, indem [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) aufgerufen wird.

Wenn die Anforderung erfolgreich ausgeführt wurde, wird die Tokenvariable mit dem Zugriffstoken aufgefüllt. Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>-Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die im `Authorization`-Anforderungsheader enthalten sein soll.

Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das Tokenergebnis eine Umleitungs-URL. Wenn der Benutzer zu dieser URL navigiert, gelangt er zur Anmeldeseite und nach einer erfolgreichen Authentifizierung zurück zur aktuellen Seite.

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
