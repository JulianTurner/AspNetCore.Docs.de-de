Die `App`-Komponente (`App.razor`) ähnelt der in den Blazor-Server-Apps gefundenen `App`-Komponente:

* Die <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>-Komponente verwaltet das Verfügbarmachen der <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>-Komponente für die restliche App.
* Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>-Komponente stellt sicher, dass der aktuelle Benutzer autorisiert ist, auf eine bestimmte Seite zuzugreifen; andernfalls wird die `RedirectToLogin`-Komponente gerendert.
* Die `RedirectToLogin`-Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    @if (!context.User.Identity.IsAuthenticated)
                    {
                        <RedirectToLogin />
                    }
                    else
                    {
                        <p>
                            You are not authorized to access 
                            this resource.
                        </p>
                    }
                </NotAuthorized>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!include[](../prefer-exact-matches.md)]
