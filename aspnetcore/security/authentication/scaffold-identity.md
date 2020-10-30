---
title: Gerüst Identity in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie Identity ein Gerüst in einem ASP.net Core Projekt aufbauen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053318"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>Gerüst Identity in ASP.net Core Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Anwendungen, die einschließen, Identity können das Gerüsten anwenden, um den Quellcode, der in der Identity Razor Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek Identity. Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen der vollständigen Identity Benutzeroberflächen Quelle](#full).

Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL- Identity Pakets anwenden. Sie können Code der Klassenbibliothek Identity auswählen, der generiert werden soll.

Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen. In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines Identity Gerüstbau Updates erläutert.

Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen. Überprüfen Sie die Änderungen, nachdem Sie das Gerüst ausgeführt haben Identity .

Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen mit verwendet werden Identity . Dienste oder Service-stubdienste werden beim Gerüstbau nicht generiert Identity . Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden. Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)

Beim Gerüstbau Identity mit einem neuen Datenkontext in einem Projekt mit vorhandenen individuellen Konten:

* `Startup.ConfigureServices`Entfernen Sie in die Aufrufe von:
  * `AddDbContext`
  * `AddDefaultIdentity`

Beispielsweise `AddDbContext` werden und `AddDefaultIdentity` im folgenden Code auskommentiert:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Der vorangehende Code kommentiert den Code, der in *Bereichen/ Identity / Identity HostingStartup.cs* dupliziert wird.

Apps, die mit einzelnen Konten erstellt wurden, sollten in der Regel "* **Not** _" einen neuen Datenkontext erstellen.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Gerüst Identity in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Gerüst Identity in ein Razor Projekt ohne vorhandene Autorisierung

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identitywird in _Areas/ Identity / Identity HostingStartup.cs * konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und Layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Authentifizierung aktivieren

Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Layoutänderungen

Optional: Fügen Sie den Anmelde Namen partiell ( `_LoginPartial` ) der Layoutdatei hinzu:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Gerüst Identity in ein Razor Projekt mit Autorisierung

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs* " konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Gerüst für Identity ein MVC-Projekt ohne vorhandene Autorisierung

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Optional: Fügen Sie der `_LoginPartial` Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*

Identitywird in *Areas/ Identity / Identity HostingStartup.cs* konfiguriert. Weitere Informationen finden Sie unter ihostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aktualisieren Sie die- `Startup` Klasse mit Code, der dem folgenden ähnelt:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Gerüst für Identity ein MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Gerüst Identity in ein Blazor Server Projekt ohne vorhandene Autorisierung

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identitywird in *Areas/ Identity / Identity HostingStartup.cs* konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migrationen

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Übergeben Sie ein XSRF-Token an die app.

Token können an-Komponenten übermittelt werden:

* Wenn Authentifizierungs Token bereitgestellt und in der Authentifizierung gespeichert werden cookie , können Sie an-Komponenten übermittelt werden.
* Razor Komponenten können nicht `HttpContext` direkt verwenden. Daher gibt es keine Möglichkeit, ein [Anti-Request-fälschungstoken (XSRF)](xref:security/anti-request-forgery) zu erhalten, um an Identity den Abmelde Endpunkt in bereitzustellen `/Identity/Account/Logout` . Ein XSRF-Token kann an-Komponenten übermittelt werden.

Weitere Informationen finden Sie unter <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

Stellen Sie in der Datei *pages/_Host. cshtml* das Token her, nachdem Sie es der `InitialApplicationState` -Klasse und der-Klasse hinzugefügt haben `TokenProvider` :

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Aktualisieren `App` Sie die Komponente ( *app. Razor* ), um Folgendes zuzuweisen `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

Der `TokenProvider` im Thema gezeigte Dienst wird in der `LoginDisplay` Komponente im folgenden Abschnitt Änderungen des [Layouts und des Authentifizierungs Flusses](#layout-and-authentication-flow-changes) verwendet.

### <a name="enable-authentication"></a>Authentifizierung aktivieren

In der- `Startup` Klasse:

* Vergewissern Sie sich, dass Razor Seiten Dienste in hinzugefügt wurden `Startup.ConfigureServices` .
* Wenn Sie den-Registrierungs [Anbieter](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)verwenden, registrieren Sie den Dienst.
* Ruft `UseDatabaseErrorPage` für den Anwendungs-Generator in `Startup.Configure` für die Entwicklungsumgebung auf.
* `UseAuthentication`Und `UseAuthorization` nach `UseRouting` .
* Fügen Sie einen Endpunkt für Razor Seiten hinzu.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Änderungen beim Layout und bei der Authentifizierung

Fügen Sie `RedirectToLogin` dem frei *gegebenen* Ordner der APP im Projektstamm eine Komponente ( *redirecttologin. Razor* ) hinzu:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Fügen Sie `LoginDisplay` dem frei *gegebenen* Ordner der App eine Komponente ( *logindisplay. Razor* ) hinzu. Der [TokenProvider-Dienst](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) stellt das XSRF-Token für das HTML-Formular bereit, das in den Identity Abmelde-Endpunkt von Beiträgen sendet:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

`MainLayout`Fügen Sie die Komponente in der-Komponente ( *Shared/MainLayout. Razor* ) dem `LoginDisplay` Inhalt des Elements der obersten Zeile hinzu `<div>` :

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Endpunkte für die Stil Authentifizierung

Da Blazor Server Razor Seiten Identity Seiten verwendet, ändert sich das Formatieren der Benutzeroberfläche, wenn ein Besucher zwischen Identity Seiten und Komponenten navigiert. Sie haben zwei Möglichkeiten, die nicht passenden Stile zu berücksichtigen:

#### <a name="build-no-locidentity-components"></a>IdentityBuildkomponenten

Ein Ansatz zur Verwendung von Komponenten für Identity anstelle von Seiten besteht darin, Komponenten zu erstellen Identity . Da `SignInManager` und `UserManager` in-Komponenten nicht unterstützt Razor werden, verwenden Sie API-Endpunkte in der Blazor Server app, um Benutzerkonto Aktionen zu verarbeiten.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Verwenden eines benutzerdefinierten Layouts mit Blazor App-Stilen

Das Identity Seitenlayout und die Stile können so geändert werden, dass Seiten erzeugt werden, die das Standarddesign verwenden Blazor .

> [!NOTE]
> Das Beispiel in diesem Abschnitt stellt lediglich einen Ausgangspunkt für die Anpassung dar. Es ist wahrscheinlich zusätzlicher Aufwand erforderlich, um die bestmögliche Benutzer Leistung zu erzielen.

Erstellen Sie eine neue `NavMenu_IdentityLayout` Komponente ( *Shared/NavMenu_ Identity Layout. Razor* ). Verwenden Sie für das Markup und den Code der Komponente denselben Inhalt wie die Komponente der APP `NavMenu` ( *Shared/NavMenu. Razor* ). Entfernen Sie alle- `NavLink` Komponenten, die nicht anonym erreicht werden können, da die automatische Umleitung in der `RedirectToLogin` Komponente für Komponenten fehlschlägt, die eine Authentifizierung oder Autorisierung erfordern.

Nehmen Sie in der Datei *pages/Shared/Layout. cshtml* die folgenden Änderungen vor:

* Fügen Sie am Razor Anfang der Datei Direktiven hinzu, um taghilfsprogramme und die Komponenten der APP im frei *gegebenen* Ordner zu verwenden:

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Ersetzen Sie dies `{APPLICATION ASSEMBLY}` durch den Assemblynamen der app.

* Fügen Sie `<base>` dem Inhalt ein Tag und ein Blazor Stylesheet hinzu `<link>` `<head>` :

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Ändern Sie den Inhalt des- `<body>` Tags wie folgt:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Gerüst Identity in ein Blazor Server Projekt mit Autorisierung

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs* " konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Vollständige Identity Benutzeroberflächen Quelle erstellen

Um die vollständige Kontrolle über die Identity Benutzeroberfläche zu behalten, führen Sie das Gerüst aus, Identity und wählen Sie **alle Dateien überschreiben**

Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche durch Identity in einer ASP.net Core 2,1-Web-App. Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identity Benutzeroberfläche zu haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Der Standardwert Identity wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren Sie eine `IEmailSender` Implementierung, z. b.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Kenn Wort Konfiguration

Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity . `InputModel``Password`die Eigenschaften werden in den folgenden Dateien gefunden:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Seite deaktivieren

In diesem Abschnitt wird gezeigt, wie Sie die Registerseite deaktivieren, aber der Ansatz kann verwendet werden, um eine beliebige Seite zu deaktivieren.

So deaktivieren Sie die Benutzerregistrierung:

* Gerüstbau Identity . Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein. Zum Beispiel:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Update *Areas/ Identity /pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualisieren Sie *Bereiche/ Identity /pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Kommentieren Sie den Registrierungs Link aus *Bereichen/ Identity /pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Aktualisieren Sie die Seite " *Bereiche/ Identity /pages/Account/RegisterConfirmation* ".

  * Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.
  * Entfernen Sie den Bestätigungscode aus dem `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Verwenden einer anderen APP zum Hinzufügen von Benutzern

Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit. Zu den Optionen zum Hinzufügen von Benutzern gehören:

* Eine dedizierte admin-Web-App.
* Eine Konsolen-app.

Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:

* Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.
* Für jeden Benutzer wird ein sicheres Kennwort generiert.
* Der Benutzer wird der-Datenbank hinzugefügt Identity .
* Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Veröffentlichen statischer Identity Assets verhindern

Informationen zum Verhindern der Veröffentlichung statischer Identity Assets im Webstamm finden Sie unter <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.net Core 2,1 und höher stellt [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit. Anwendungen, die einschließen, Identity können das Gerüsten anwenden, um den Quellcode, der in der Identity Razor Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek Identity. Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.

Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL- Identity Pakets anwenden. Sie können Code der Klassenbibliothek Identity auswählen, der generiert werden soll.

Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen. In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines Identity Gerüstbau Updates erläutert.

Wenn das Identity Gerüst ausgeführt wird, wird eine *ScaffoldingReadme.txt* -Datei im Projektverzeichnis erstellt. Die *ScaffoldingReadme.txt* -Datei enthält allgemeine Anweisungen dazu, was erforderlich ist, um das Identity Gerüstbau Update abzuschließen. Dieses Dokument enthält ausführlichere Anweisungen als die *ScaffoldingReadme.txt* Datei.

Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen. Überprüfen Sie die Änderungen, nachdem Sie das Gerüst ausgeführt haben Identity .

> [!NOTE]
> Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen mit verwendet werden Identity . Dienste oder Service-stubdienste werden beim Gerüstbau nicht generiert Identity . Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden. Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Gerüst Identity in ein leeres Projekt

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Fügen Sie die folgenden markierten Aufrufe der- `Startup` Klasse hinzu:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Gerüst Identity in ein Razor Projekt ohne vorhandene Autorisierung

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identitywird in *Areas/ Identity / Identity HostingStartup.cs* konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrationen, UseAuthentication und Layout

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Authentifizierung aktivieren

In der- `Configure` Methode der- `Startup` Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach folgenden Aktionen aufgerufen `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Layoutänderungen

Optional: Fügen Sie den Anmelde Namen partiell ( `_LoginPartial` ) der Layoutdatei hinzu:

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Gerüst Identity in ein Razor Projekt mit Autorisierung

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Einige Identity Optionen sind in " *Bereiche/ Identity / Identity HostingStartup.cs* " konfiguriert. Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Gerüst für Identity ein MVC-Projekt ohne vorhandene Autorisierung

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Optional: Fügen Sie der `_LoginPartial` Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*

Identitywird in *Areas/ Identity / Identity HostingStartup.cs* konfiguriert. Weitere Informationen finden Sie unter ihostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach folgenden Aktionen abrufen `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Gerüst für Identity ein MVC-Projekt mit Autorisierung

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Vollständige Identity Benutzeroberflächen Quelle erstellen

Um die vollständige Kontrolle über die Identity Benutzeroberfläche zu behalten, führen Sie das Gerüst aus, Identity und wählen Sie **alle Dateien überschreiben**

Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche durch Identity in einer ASP.net Core 2,1-Web-App. Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identity Benutzeroberfläche zu haben.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Der Standardwert Identity wird im folgenden Code ersetzt:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Registrieren Sie eine `IEmailSender` Implementierung, z. b.:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Kenn Wort Konfiguration

Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity . `InputModel``Password`die Eigenschaften werden in den folgenden Dateien gefunden:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Registerseite deaktivieren

So deaktivieren Sie die Benutzerregistrierung:

* Gerüstbau Identity . Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein. Zum Beispiel:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Update *Areas/ Identity /pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualisieren Sie *Bereiche/ Identity /pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Kommentieren Sie den Registrierungs Link aus *Bereichen/ Identity /pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualisieren Sie die Seite " *Bereiche/ Identity /pages/Account/RegisterConfirmation* ".

  * Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.
  * Entfernen Sie den Bestätigungscode aus dem `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Verwenden einer anderen APP zum Hinzufügen von Benutzern

Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit. Zu den Optionen zum Hinzufügen von Benutzern gehören:

* Eine dedizierte admin-Web-App.
* Eine Konsolen-app.

Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:

* Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.
* Für jeden Benutzer wird ein sicheres Kennwort generiert.
* Der Benutzer wird der-Datenbank hinzugefügt Identity .
* Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
