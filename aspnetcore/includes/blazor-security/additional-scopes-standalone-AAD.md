<span data-ttu-id="e8d71-101">Fügen Sie <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> für `User.Read`-Berechtigung mit <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> hinzu:</span><span class="sxs-lookup"><span data-stu-id="e8d71-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
