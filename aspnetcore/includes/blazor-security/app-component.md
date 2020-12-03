<span data-ttu-id="3f090-101">Die `App`-Komponente (`App.razor`) ähnelt der in den Blazor-Server-Apps gefundenen `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="3f090-101">The `App` component (`App.razor`) is similar to the `App` component found in Blazor Server apps:</span></span>

* <span data-ttu-id="3f090-102">Die <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>-Komponente verwaltet das Verfügbarmachen der <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>-Komponente für die restliche App.</span><span class="sxs-lookup"><span data-stu-id="3f090-102">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component manages exposing the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> to the rest of the app.</span></span>
* <span data-ttu-id="3f090-103">Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>-Komponente stellt sicher, dass der aktuelle Benutzer autorisiert ist, auf eine bestimmte Seite zuzugreifen; andernfalls wird die `RedirectToLogin`-Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="3f090-103">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component makes sure that the current user is authorized to access a given page or otherwise renders the `RedirectToLogin` component.</span></span>
* <span data-ttu-id="3f090-104">Die `RedirectToLogin`-Komponente verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="3f090-104">The `RedirectToLogin` component manages redirecting unauthorized users to the login page.</span></span>

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
