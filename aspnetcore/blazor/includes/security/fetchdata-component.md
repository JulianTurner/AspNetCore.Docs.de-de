<span data-ttu-id="a1645-101">Die `FetchData`-Komponente zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a1645-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="a1645-102">Stellen Sie ein Zugriffstoken bereit.</span><span class="sxs-lookup"><span data-stu-id="a1645-102">Provision an access token.</span></span>
* <span data-ttu-id="a1645-103">Verwenden Sie das Zugriffstoken zum Aufrufen einer geschützten Ressourcen-API in der *Server*-App.</span><span class="sxs-lookup"><span data-stu-id="a1645-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="a1645-104">Die [`@attribute [Authorize]`](xref:mvc/views/razor#attribute)-Anweisung zeigt dem Blazor WebAssembly-Autorisierungssystem an, dass der Benutzer autorisiert werden muss, um diese Komponente anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a1645-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="a1645-105">Das Vorhandensein des Attributs in der `Client`-App verhindert nicht, dass die API auf dem Server ohne die richtigen Anmeldeinformationen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a1645-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="a1645-106">Die `Server`-App muss auch `[Authorize]` auf den entsprechenden Endpunkten verwenden, um sie ordnungsgemäß zu schützen.</span><span class="sxs-lookup"><span data-stu-id="a1645-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="a1645-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> übernimmt die Anforderung eines Zugriffstokens, das der Anforderung zum Aufrufen der API hinzugefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a1645-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="a1645-108">Wenn das Token zwischengespeichert wurde oder der Dienst ohne Benutzerinteraktion ein neues Zugriffstoken bereitstellen kann, wird die Tokenanforderung erfolgreich ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a1645-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="a1645-109">Andernfalls schlägt die Tokenanforderung mit einer <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> fehl, die in einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung abgefangen wird.</span><span class="sxs-lookup"><span data-stu-id="a1645-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="a1645-110">Um das eigentliche Token abzurufen, das in die Anforderung aufgenommen werden soll, muss die App überprüfen, ob die Anforderung erfolgreich war, indem [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a1645-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="a1645-111">Wenn die Anforderung erfolgreich ausgeführt wurde, wird die Tokenvariable mit dem Zugriffstoken aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="a1645-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="a1645-112">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>-Eigenschaft des Tokens macht die Literalzeichenfolge verfügbar, die im `Authorization`-Anforderungsheader enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="a1645-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="a1645-113">Wenn die Anforderung fehlgeschlagen ist, weil das Token ohne Benutzerinteraktion nicht bereitgestellt werden konnte, enthält das Tokenergebnis eine Umleitungs-URL.</span><span class="sxs-lookup"><span data-stu-id="a1645-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="a1645-114">Wenn der Benutzer zu dieser URL navigiert, gelangt er zur Anmeldeseite und nach einer erfolgreichen Authentifizierung zurück zur aktuellen Seite.</span><span class="sxs-lookup"><span data-stu-id="a1645-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
