<span data-ttu-id="7976c-101">Die von der Komponente `Authentication` (`Pages/Authentication.razor`) erstellte Seite definiert die Routen, die für die Verarbeitung unterschiedlicher Authentifizierungsstufen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="7976c-101">The page produced by the `Authentication` component (`Pages/Authentication.razor`) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="7976c-102">Die Komponente <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>:</span><span class="sxs-lookup"><span data-stu-id="7976c-102">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> component:</span></span>

* <span data-ttu-id="7976c-103">Wird vom Paket [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7976c-103">Is provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="7976c-104">Verwaltet die entsprechenden Aktionen in jeder Phase der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7976c-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
