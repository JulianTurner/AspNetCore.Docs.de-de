<span data-ttu-id="3b429-101">Die `RedirectToLogin`-Komponente (`Shared/RedirectToLogin.razor`):</span><span class="sxs-lookup"><span data-stu-id="3b429-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="3b429-102">Verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="3b429-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="3b429-103">Behält die aktuelle URL bei, auf die der Benutzer zuzugreifen versucht, sodass er bei erfolgreicher Authentifizierung zu dieser Seite zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="3b429-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
