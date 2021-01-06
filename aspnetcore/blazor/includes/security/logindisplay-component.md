<span data-ttu-id="3adf5-101">Die `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) wird in der `MainLayout`-Komponente (`Shared/MainLayout.razor`) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="3adf5-101">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="3adf5-102">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="3adf5-102">For authenticated users:</span></span>
  * <span data-ttu-id="3adf5-103">Zeigt den aktuellen Benutzernamen an</span><span class="sxs-lookup"><span data-stu-id="3adf5-103">Displays the current username.</span></span>
  * <span data-ttu-id="3adf5-104">Bietet eine Schaltfläche zum Abmelden von der App</span><span class="sxs-lookup"><span data-stu-id="3adf5-104">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="3adf5-105">Bietet die Option zur Anmeldung für anonyme Benutzer</span><span class="sxs-lookup"><span data-stu-id="3adf5-105">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginLogout">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginLogout(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```
