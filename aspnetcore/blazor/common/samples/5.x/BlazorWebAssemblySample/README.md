# <a name="blazor-webassembly-sample-app"></a>Blazor WebAssembly-Beispiel-App

Dieses Beispiel veranschaulicht die Verwendung von Blazor-Szenarios, die in der Blazor-Dokumentation beschrieben werden.

## <a name="call-web-api-example"></a>Aufrufen von Web-API-Beispielen

Das Web-API-Beispiel erfordert eine Web-API, die auf der Beispiel-App für das Thema <a href="https://docs.microsoft.com/aspnet/core/tutorials/first-web-api">Tutorial: Erstellen einer Web-API mit ASP.NET Core</a> basiert und standardmäßig denselben HTTPS-Port (5001) verwendet wie die Blazor-Beispiel-App. Ändern Sie den Port der Web-API (verwenden Sie z. B. Port 10000), wenn beide Apps gleichzeitig auf demselben Computer verwendet werden sollen. Die Beispiel-App sendet unter `https://localhost:10000/api/TodoItems` Anforderungen an die Web-API. Wenn eine andere Web-API-Adresse verwendet wird, aktualisieren Sie den `ServiceEndpoint`-Konstantenwert im `@code`-Block der Razor-Komponente.</p>

Die Beispiel-App erstellt eine <a href="https://docs.microsoft.com/aspnet/core/security/cors">Anforderung für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-origin resource sharing, CORS)</a> von `http://localhost:5000` oder `https://localhost:5001` an die Web-API. Anmeldeinformationen (Autorisierungscookies/-header) sind zulässig. Fügen Sie die folgende CORS-Middleware-Konfiguration zur `Startup.Configure`-Methode der Web-API hinzu:</p>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization)
    .AllowCredentials());
```

Passen Sie die Domänen und Ports von `WithOrigins` nach Bedarf für die Blazor-App an.

Die Web-API ist für CORS konfiguriert, um die Autorisierung von Cookies/Headern und Anforderungen vom Clientcode zuzulassen, aber die im Tutorial erstellte Web-API autorisiert keine Anforderungen. Informationen zur Implementierung finden Sie in den Artikeln zur <a href="https://docs.microsoft.com/aspnet/core/security/">Übersicht über die ASP.NET Core-Sicherheit</a>.
