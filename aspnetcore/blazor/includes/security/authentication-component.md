Die von der Komponente `Authentication` (`Pages/Authentication.razor`) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungsstufen erforderlich sind.

Die Komponente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>:

* Wird vom Paket [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) bereitgestellt.
* Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
