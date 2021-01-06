Die Indexseite (`wwwroot/index.html`) enthält ein Skript, das den `AuthenticationService` in JavaScript definiert. `AuthenticationService` behandelt die Details des OIDC-Protokolls auf niedriger Ebene. Die App ruft intern die im Skript definierten Methoden auf, um die Authentifizierungsvorgänge auszuführen.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
