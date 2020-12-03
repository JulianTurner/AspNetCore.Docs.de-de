<span data-ttu-id="969e8-101">Das Framework ist standardmäßig auf den Popup-Anmeldemodus festgelegt und greift auf den Umleitungsanmeldemodus zurück, wenn kein Popup geöffnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="969e8-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="969e8-102">Konfigurieren Sie MSAL, um den Umleitungsanmeldemodus zu verwenden, indem Sie die `LoginMode`-Eigenschaft von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> auf `redirect` festlegen:</span><span class="sxs-lookup"><span data-stu-id="969e8-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="969e8-103">Die Standardeinstellung ist `popup`, und der Zeichenfolgenwert berücksichtigt keine Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="969e8-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
