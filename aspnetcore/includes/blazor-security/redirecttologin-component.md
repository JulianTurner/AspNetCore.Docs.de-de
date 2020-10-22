Die `RedirectToLogin`-Komponente (`Shared/RedirectToLogin.razor`):

* Verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.
* Behält die aktuelle URL bei, auf die der Benutzer zuzugreifen versucht, sodass er bei erfolgreicher Authentifizierung zu dieser Seite zurückkehren kann.

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
