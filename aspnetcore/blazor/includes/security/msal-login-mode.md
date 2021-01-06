Das Framework ist standardmäßig auf den Popup-Anmeldemodus festgelegt und greift auf den Umleitungsanmeldemodus zurück, wenn kein Popup geöffnet werden kann. Konfigurieren Sie MSAL, um den Umleitungsanmeldemodus zu verwenden, indem Sie die `LoginMode`-Eigenschaft von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> auf `redirect` festlegen:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Die Standardeinstellung ist `popup`, und der Zeichenfolgenwert berücksichtigt keine Groß-/Kleinschreibung.
