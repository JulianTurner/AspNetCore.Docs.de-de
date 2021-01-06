<span data-ttu-id="f70c6-101">Die Indexseite (`wwwroot/index.html`) enthält ein Skript, das den `AuthenticationService` in JavaScript definiert.</span><span class="sxs-lookup"><span data-stu-id="f70c6-101">The Index page (`wwwroot/index.html`) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="f70c6-102">`AuthenticationService` behandelt die Details des OIDC-Protokolls auf niedriger Ebene.</span><span class="sxs-lookup"><span data-stu-id="f70c6-102">`AuthenticationService` handles the low-level details of the OIDC protocol.</span></span> <span data-ttu-id="f70c6-103">Die App ruft intern die im Skript definierten Methoden auf, um die Authentifizierungsvorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f70c6-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
