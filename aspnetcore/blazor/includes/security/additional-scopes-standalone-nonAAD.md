<span data-ttu-id="c5e41-101">Fügen Sie ein Paar von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> für `openid` und `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> hinzu:</span><span class="sxs-lookup"><span data-stu-id="c5e41-101">Add a pair of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `openid` and `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("openid");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("offline_access");
});
```
