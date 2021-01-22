---
title: Einführung in die Authentifizierung für Single-Page-apps auf ASP.net Core
author: javiercn
description: Verwenden Sie dies Identity mit einer einseitigen APP, die in einer ASP.net Core-App gehostet wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658663"
---
# <a name="authentication-and-authorization-for-spas"></a>Authentifizierung und Autorisierung für Spas

Der ASP.net Core 3,1-Vorlagen und spätere Vorlagen bieten die Authentifizierung in Single-Page-Apps (Spas) mithilfe der Unterstützung für API-Autorisierung. ASP.NET Core Identityzum Authentifizieren und Speichern von Benutzern wird der [ Identity Server](https://identityserver.io/) zum Implementieren von OpenID Connect kombiniert.

Der **Angular** -und **der-Projekt** Vorlagen, die dem-Authentifizierungs Parameter in den Projektvorlagen für **Webanwendungen (Model-View-Controller)** (MVC) und **Webanwendungen** (Seiten) ähneln, wurde ein Authentifizierungs Parameter hinzugefügt Razor . Die zulässigen Parameterwerte sind **None** und **Individual**. Die Projektvorlage " **React.js und Redux** " unterstützt derzeit den Authentifizierungs Parameter nicht.

## <a name="create-an-app-with-api-authorization-support"></a>Erstellen einer APP mit Unterstützung für die API-Autorisierung

Benutzerauthentifizierung und-Autorisierung können mit Angular-und reagingspas verwendet werden. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

**Angular**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagieren**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Der vorherige Befehl erstellt eine ASP.net Core-App mit einem *ClientApp* -Verzeichnis, das die Spa enthält.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Allgemeine Beschreibung der ASP.net Core Komponenten der APP

In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist:

### <a name="startup-class"></a>Startklasse

Die folgenden Codebeispiele basieren auf [Microsoft. aspnetcore. apiauthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Das nuget-Paket des Servers. In den Beispielen werden die API-Authentifizierung und-Autorisierung mithilfe der <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> Erweiterungs Methoden und konfiguriert. Projekte, die die Projektvorlagen "Projekt" oder "Angular Spa" mit Authentifizierung verwenden, enthalten einen Verweis auf dieses Paket.

Die- `Startup` Klasse verfügt über die folgenden Ergänzungen:

* In der- `Startup.ConfigureServices` Methode:
  * Identity mit der Standardbenutzer Oberfläche:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer mit einer zusätzlichen `AddApiAuthorization` Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf dem Server eingerichtet werden Identity :

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einer zusätzlichen `AddIdentityServerJwt`-Hilfsmethode, die die App so konfiguriert, dass sie von IdentityServer generierte JWT-Token überprüft:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In der- `Startup.Configure` Methode:
  * Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:

    ```csharp
    app.UseAuthentication();
    ```

  * Die Identity Server Middleware, die die OpenID Connect-Endpunkte verfügbar macht:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a>Azure App Service unter Linux

Geben Sie für Azure App Service Bereitstellungen unter Linux den Aussteller explizit in an `Startup.ConfigureServices` :

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

Im vorangehenden Code ist der `{AUTHORITY}` Platzhalter der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> , der beim Erstellen von OpenID Connect-aufrufen verwendet wird.

Beispiel:

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a>AddApiAuthorization

Diese Hilfsmethode konfiguriert Identity den Server für die Verwendung unserer unterstützten Konfiguration. IdentityServer ist ein leistungsfähiges und erweiterbares Framework für Überlegungen zum Thema „App-Sicherheit“. Gleichzeitig stellt dies unnötige Komplexität für die gängigsten Szenarien bereit. Folglich werden Ihnen eine Reihe von Konventionen und Konfigurationsoptionen zur Verfügung gestellt, die als guter Ausgangspunkt angesehen werden. Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistung des Identity Servers weiterhin verfügbar, um die Authentifizierung an Ihre Bedürfnisse anzupassen.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

Diese Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler. Die Richtlinie ist so konfiguriert, Identity dass alle Anforderungen verarbeitet werden können, die an einen Unterpfad im Identity URL-Bereich "/" weitergeleitet werden Identity . `JwtBearerHandler` verarbeitet alle anderen Anforderungen. Darüber hinaus registriert diese Methode eine `<<ApplicationName>>API` API-Ressource bei Identity einem Server mit einem Standardbereich von `<<ApplicationName>>API` und konfiguriert die JWT-bearertoken-Middleware, um Token zu überprüfen, Identity die vom Server für die APP ausgegeben werden.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

Beachten Sie in der Datei " *controllers\weatherforecastcontroller.cs* " das-Attribut, das `[Authorize]` auf die-Klasse angewendet wird und angibt, dass der Benutzer auf der Basis der Standard Richtlinie für den Zugriff auf die Ressource autorisiert werden muss. Die Standard Autorisierungs Richtlinie wird so konfiguriert, dass das standardmäßige Authentifizierungsschema verwendet wird, das von `AddIdentityServerJwt` dem oben erwähnten Richtlinien Schema festgelegt wird `JwtBearerHandler` . Dadurch wird der von dieser Hilfsmethode konfigurierte Standard Handler für Anforderungen an die app.

### <a name="applicationdbcontext"></a>ApplicationDbContext

Beachten Sie in der Datei *data\applicationdbcontext.cs* , dass die gleiche `DbContext` in verwendet wird, Identity mit der Ausnahme, dass Sie erweitert wird `ApiAuthorizationDbContext` (eine stärker abgeleitete Klasse von `IdentityDbContext` ), um das Schema für den Server einzuschließen Identity .

Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identity `DbContext` Klassen und konfigurieren den Kontext so, dass das Identity Schema durch Aufrufen von `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` für die- `OnModelCreating` Methode eingeschlossen wird.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

Beachten Sie in der Datei " *controllers\oidcconfigurationcontroller.cs* " den Endpunkt, der bereitgestellt wird, um die oidc-Parameter bereitzustellen, die vom Client verwendet werden müssen.

### appsettings.json

In der *appsettings.json* -Datei des Projekt Stamms gibt es einen neuen `IdentityServer` Abschnitt, in dem die Liste der konfigurierten Clients beschrieben wird. Im folgenden Beispiel gibt es einen einzelnen Client. Der Clientname entspricht dem App-Namen, und er wird durch Konventionen dem OAuth-`ClientId`-Parameter zugeordnet. Das Profil gibt den App-Typ an, der konfiguriert wird. Sie wird intern verwendet, um Konventionen zu fördern, die den Konfigurationsprozess für den Server vereinfachen. Es stehen mehrere Profile zur Verfügung, wie im Abschnitt [Anwendungsprofile](#application-profiles) erläutert.

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.js

Im *appsettings.Development.jsfür* die Datei des Projekt Stamms gibt es einen `IdentityServer` Abschnitt, in dem der Schlüssel zum Signieren von Token beschrieben wird. Beim Bereitstellen in der Produktion muss neben der APP ein Schlüssel bereitgestellt und bereitgestellt werden, wie im Abschnitt bereitstellen [in der Produktion](#deploy-to-production) erläutert.

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Allgemeine Beschreibung der Angular-App

Die Unterstützung von Authentifizierung und API-Autorisierung in der Angular-Vorlage befindet sich in einem eigenen Angular-Modul im Verzeichnis *clientapp\src\api-Authorization* . Das Modul besteht aus den folgenden Elementen:

* 3 Komponenten:
  * *Login. Component. TS*: verarbeitet den Anmelde Fluss der app.
  * *Abmelde. Component. TS*: verarbeitet den Abmelde Fluss der app.
  * *Login-Menu. Component. TS*: ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
* Ein Weiterleitungs Wächter `AuthorizeGuard` , der Routen hinzugefügt werden kann und erfordert, dass ein Benutzer vor dem Besuch der Route authentifiziert wird.
* Ein HTTP-Interceptor `AuthorizeInterceptor` , der das Zugriffs Token an ausgehende HTTP-Anforderungen anbindet, die auf die API abzielen, wenn der Benutzer authentifiziert wird.
* Ein Dienst `AuthorizeService` , der die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.
* Ein Angular-Modul, das Routen definiert, die den Authentifizierungs Teilen der APP zugeordnet sind. Er macht die Anmelde Menü Komponente, den Interceptor, den Wächter und den Dienst für die Nutzung durch den Rest der app verfügbar.

## <a name="general-description-of-the-react-app"></a>Allgemeine Beschreibung der App "reagieren"

Die Unterstützung für die Authentifizierung und API-Autorisierung in der "reagieren"-Vorlage befindet sich im Verzeichnis " *clientapp\src\components\api-Authorization* ". Sie besteht aus den folgenden Elementen:

* 4 Komponenten:
  * *Login.js*: übernimmt den Anmelde Fluss der app.
  * *Logout.js*: verarbeitet den Abmelde Fluss der app.
  * *LoginMenu.js*: ein Widget, das eine der folgenden Gruppen von Links anzeigt:
    * Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.
    * Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.
  * *AuthorizeRoute.js*: eine Routen Komponente, für die vor dem Rendern der im-Parameter angegeben Komponente eine Authentifizierung des Benutzers erforderlich ist `Component` .
* Eine exportierte `authService` Instanz der `AuthorizeService` -Klasse, die die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.

Nachdem Sie die Hauptkomponenten der Lösung kennengelernt haben, können Sie sich einen tieferen Einblick in die einzelnen Szenarios für die APP machen.

## <a name="require-authorization-on-a-new-api"></a>Autorisierung für eine neue API erforderlich

Standardmäßig ist das System so konfiguriert, dass die Autorisierung für neue APIs problemlos erforderlich ist. Erstellen Sie hierzu einen neuen Controller, und fügen Sie das- `[Authorize]` Attribut zur Controller Klasse oder zu einer beliebigen Aktion im Controller hinzu.

## <a name="customize-the-api-authentication-handler"></a>Anpassen des API-Authentifizierungs Handlers

Konfigurieren Sie zum Anpassen der Konfiguration des JWT-Handlers der API seine <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> Instanz:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Der JWT-Handler der API löst Ereignisse aus, die die Steuerung des Authentifizierungsprozesses mithilfe von ermöglichen `JwtBearerEvents` . Um Unterstützung für die API-Autorisierung zu bieten, `AddIdentityServerJwt` registriert seine eigenen Ereignishandler.

Um die Behandlung eines Ereignisses anzupassen, binden Sie den vorhandenen Ereignishandler nach Bedarf mit zusätzlicher Logik ein. Zum Beispiel:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

Im vorangehenden Code wird der `OnTokenValidated` Ereignishandler durch eine benutzerdefinierte Implementierung ersetzt. Diese Implementierung:

1. Ruft die ursprüngliche, von der API-Autorisierungs Unterstützung bereitgestellte Implementierung auf.
1. Führen Sie eine eigene benutzerdefinierte Logik aus.

## <a name="protect-a-client-side-route-angular"></a>Schützen einer Client seitigen Route (Angular)

Der Schutz einer Client seitigen Route erfolgt durch Hinzufügen von Autorisierungs Wächter zur Liste der Wächter, die beim Konfigurieren einer Route ausgeführt werden. Beispielsweise können Sie sehen, wie die `fetch-data` Route innerhalb des Haupt-App-Angular-Moduls konfiguriert ist:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Beachten Sie, dass der Schutz einer Route nicht den eigentlichen Endpunkt schützt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist), sondern dass der Benutzer nur die Navigation zur angegebenen Client seitigen Route verhindert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-angular"></a>Authentifizieren von API-Anforderungen (Angular)

Die Authentifizierung von Anforderungen an APIs, die zusammen mit der APP gehostet werden, erfolgt automatisch durch die Verwendung des von der APP definierten http-Client-Interceptors.

## <a name="protect-a-client-side-route-react"></a>Schützen einer Client seitigen Route (reagieren)

Schützen Sie eine Client seitige Route mithilfe der `AuthorizeRoute` Komponente anstelle der einfachen `Route` Komponente. Beachten Sie z. b., wie die `fetch-data` Route innerhalb der-Komponente konfiguriert ist `App` :

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Schützen einer Route:

* Schützt nicht den eigentlichen Endpunkt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist).
* Verhindert, dass der Benutzer zur angegebenen Client seitigen Route navigiert, wenn er nicht authentifiziert ist.

## <a name="authenticate-api-requests-react"></a>Authentifizieren von API-Anforderungen (reagieren)

Das Authentifizieren von Anforderungen mit reagieren erfolgt durch das erste Importieren der- `authService` Instanz aus `AuthorizeService` . Das Zugriffs Token wird aus dem abgerufen `authService` und wie unten dargestellt an die Anforderung angefügt. In reagiere Komponenten wird diese Aufgabe in der Regel in der `componentDidMount` Lebenszyklus Methode oder als Ergebnis einer Benutzerinteraktion ausgeführt.

### <a name="import-the-authservice-into-your-component"></a>Importieren Sie den authService in Ihre Komponente.

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Abrufen und Anfügen des Zugriffs Tokens an die Antwort

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Bereitstellen für die Produktion

Um die app in der Produktionsumgebung bereitzustellen, müssen die folgenden Ressourcen bereitgestellt werden:

* Eine Datenbank, in der die Identity Benutzerkonten und die Identity Server erteilt werden.
* Ein Produktions Zertifikat, das zum Signieren von Token verwendet werden soll.
  * Es gibt keine speziellen Anforderungen für dieses Zertifikat. Dabei kann es sich um ein selbst signiertes Zertifikat oder um ein Zertifikat handeln, das über eine Zertifizierungsstelle bereitgestellt wird.
  * Sie kann über Standard Tools wie PowerShell oder OpenSSL generiert werden.
  * Sie kann im Zertifikat Speicher auf den Ziel Computern installiert oder als *PFX* -Datei mit einem sicheren Kennwort bereitgestellt werden.

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a>Beispiel: Bereitstellen für einen nicht-Azure-Webhostinganbieter

Erstellen oder laden Sie Ihr Zertifikat im webhostingpanel. Ändern Sie dann in der Datei der APP den Abschnitt so, dass er *appsettings.json* `IdentityServer` die Schlüssel Details enthält. Zum Beispiel:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

Im vorherigen Beispiel:

* `StoreName` stellt den Namen des Zertifikat Speicher dar, in dem das Zertifikat gespeichert ist. In diesem Fall verweist Sie auf den webhostingspeicher.
* `StoreLocation` Gibt an, wo das Zertifikat geladen werden soll ( `CurrentUser` in diesem Fall).
* `Name` entspricht dem Distinguished Subject für das Zertifikat.

### <a name="example-deploy-to-azure-app-service"></a>Beispiel: Bereitstellen für Azure App Service

In diesem Abschnitt wird beschrieben, wie Sie die APP für Azure App Service mithilfe eines im Zertifikat Speicher gespeicherten Zertifikats bereitstellen. Wenn Sie die APP so ändern möchten, dass ein Zertifikat aus dem Zertifikat Speicher geladen wird, ist beim Konfigurieren der app in der Azure-Portal in einem späteren Schritt ein Serviceplan vom Typ Standard oder besser erforderlich.

Ändern Sie in der *appsettings.json* Datei der APP den `IdentityServer` Abschnitt, um die Schlüssel Details einzuschließen:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Der Speicher Name steht für den Namen des Zertifikat Speicher, in dem das Zertifikat gespeichert ist. In diesem Fall verweist Sie auf den persönlichen Benutzerspeicher.
* Der Speicherort stellt dar, wo das Zertifikat geladen werden soll ( `CurrentUser` oder `LocalMachine` ).
* Die Name-Eigenschaft für das Zertifikat entspricht dem Distinguished Subject für das Zertifikat.

Befolgen Sie zum Bereitstellen von auf Azure App Service die Schritte unter Bereitstellen [der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), in der erläutert wird, wie Sie die erforderlichen Azure-Ressourcen erstellen und die app in der Produktionsumgebung bereitstellen.

Nachdem Sie die obigen Anweisungen ausgeführt haben, wird die app in Azure bereitgestellt, ist aber noch nicht funktionsfähig. Das von der APP verwendete Zertifikat muss in der Azure-Portal konfiguriert werden. Suchen Sie den Fingerabdruck für das Zertifikat, und befolgen Sie die Schritte unter [Laden von Zertifikaten](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Obwohl diese Schritte SSL erwähnen, gibt es einen Abschnitt mit **privaten Zertifikaten** in der Azure-Portal, in dem Sie das bereitgestellte Zertifikat für die Verwendung mit der APP hochladen können.

Nachdem Sie die APP und die App-Einstellungen im Azure-Portal konfiguriert haben, starten Sie die APP im Portal neu.

## <a name="other-configuration-options"></a>Andere Konfigurationsoptionen

Die Unterstützung für die API-Autorisierung basiert auf dem Identity Server mit einem Satz von Konventionen, Standardwerten und Verbesserungen, um die Bedienung für Spas zu vereinfachen. Natürlich ist die volle Leistungsfähigkeit von Server im Hintergrund Identity verfügbar, wenn die ASP.net Core Integrationen Ihr Szenario nicht abdecken. Die ASP.net Core-Unterstützung konzentriert sich auf "erst Anbieter-Apps", bei denen alle apps von unserer Organisation erstellt und bereitgestellt werden. Daher wird die Unterstützung für Dinge wie die Zustimmung oder den Verbund nicht angeboten. Verwenden Sie für diese Szenarien Identity Server, und befolgen Sie die zugehörigen Dokumentationen.

### <a name="application-profiles"></a>Anwendungsprofile

Anwendungsprofile sind vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren. Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:

* `IdentityServerSPA`: Stellt eine mit dem Server gehostete Spa Identity als einzelne Einheit dar.
  * Der `redirect_uri` Standardwert ist `/authentication/login-callback` .
  * Der `post_logout_redirect_uri` Standardwert ist `/authentication/logout-callback` .
  * Der Satz von Bereichen umfasst `openid` , und alle Bereiche, die `profile` für die APIs in der APP definiert sind.
  * Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder jeweils einzeln ( `id_token` , `token` ).
  * Der zulässige Antwortmodus ist `fragment` .
* `SPA`: Stellt eine Spa dar, die nicht mit dem Server gehostet wird Identity .
  * Der Satz von Bereichen umfasst `openid` , und alle Bereiche, die `profile` für die APIs in der APP definiert sind.
  * Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder jeweils einzeln ( `id_token` , `token` ).
  * Der zulässige Antwortmodus ist `fragment` .
* `IdentityServerJwt`: Stellt eine API dar, die zusammen mit dem-Server gehostet wird Identity .
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.
* `API`: Stellt eine API dar, die nicht mit dem Server gehostet wird Identity .
  * Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.

### <a name="configuration-through-appsettings"></a>Konfiguration über appSettings

Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie Sie der Liste `Clients` oder hinzufügen `Resources` .

Konfigurieren Sie die- `redirect_uri` Eigenschaft und die-Eigenschaft des Clients `post_logout_redirect_uri` , wie im folgenden Beispiel gezeigt:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Wenn Sie Ressourcen konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Konfiguration durch Code

Sie können die Clients und Ressourcen auch über den Code konfigurieren, indem Sie eine Überladung von verwenden `AddApiAuthorization` , die eine Aktion zum Konfigurieren von Optionen übernimmt.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
