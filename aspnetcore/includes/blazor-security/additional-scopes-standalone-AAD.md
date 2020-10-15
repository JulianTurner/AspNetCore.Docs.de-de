Fügen Sie <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> für `User.Read`-Berechtigung mit <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> hinzu:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
