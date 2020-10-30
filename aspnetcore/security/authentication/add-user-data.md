---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten Identity in einem ASP.net Core Projekt
author: rick-anderson
description: Erfahren Sie, wie Sie Identity in einem ASP.net Core Projekt benutzerdefinierte Benutzerdaten hinzufügen. Löschen von Daten pro dsgvo.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061417"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a>Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten Identity in einem ASP.net Core Projekt

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel werden die folgenden Aufgaben erläutert:

* Fügen Sie benutzerdefinierte Benutzerdaten zu einer ASP.net Core-Web-App hinzu.
* Markieren Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> -Attribut, damit es automatisch heruntergeladen und gelöscht werden kann. Das herunterladen und Löschen der Daten hilft bei [der Einhaltung der](xref:security/gdpr) dsgvo-Anforderungen.

Das Projektbeispiel wird aus einer Razor pages-Web-App erstellt, die Anweisungen ähneln jedoch einer ASP.net Core MVC-Web-App.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a>Erstellen einer Razor Web-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt** . Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.
* **ASP.net Core Webanwendung** > **OK** auswählen
* Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.
* **Webanwendung** auswählen > **OK**
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt** . Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.
* **ASP.net Core Webanwendung** > **OK** auswählen
* Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.
* **Webanwendung** auswählen > **OK**
* Erstellen Sie das Projekt, und führen Sie es aus.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a>Das Identity Gerüst ausführen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, > **Add**  >  **Neues Gerüst Element** hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identity**  >  **Hinzufügen** aus.
* Im Dialog **Feld Identity Hinzufügen** werden die folgenden Optionen angezeigt:
  * Wählen Sie die vorhandene Layoutdatei aus  *~/pages/Shared/_Layout. cshtml*
  * Wählen Sie die folgenden zu über schreibenden Dateien aus:
    * **Konto/Registrierung**
    * **Konto/Verwaltung/Index**
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen. Akzeptieren Sie den Typ ( **"WebApp1". Models. WebApp1Context** , wenn das Projekt den Namen **"WebApp1"** hat).
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Benutzerklasse** zu erstellen. Akzeptieren Sie den Typ ( **WebApp1User** , wenn das Projekt den Namen **"WebApp1"** hat), > **Hinzufügen** .
* Wählen Sie **Hinzufügen** .

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie der Projektdatei (. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu. Führen Sie den folgenden Befehl im Projektverzeichnis aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner das Identity Gerüst aus:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) , um die folgenden Schritte auszuführen:

* Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.
* `UseAuthentication` wurde `Startup.Configure` hinzugefügt.
* Fügen Sie `<partial name="_LoginPartial" />` der Layoutdatei hinzu.
* Testen der App:
  * Registrieren eines Benutzers
  * Wählen Sie den neuen Benutzernamen (neben dem Link **Logout) aus** . Möglicherweise müssen Sie das Fenster erweitern oder das Navigationsleisten Symbol auswählen, um den Benutzernamen und andere Links anzuzeigen.
  * Wählen Sie die Registerkarte **persönliche Daten** aus.
  * Wählen Sie die Schaltfläche **herunterladen** aus, und untersuchen Sie die Datei *PersonalData.js*
  * Testen Sie die Schaltfläche **Löschen** , mit der der angemeldete Benutzer gelöscht wird.

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a>Hinzufügen von benutzerdefinierten Benutzer Identity Daten zur Datenbank

Aktualisieren Sie die `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften. Wenn Sie das Projekt "WebApp1" benannt haben, heißt die Datei " *Areas/ Identity /Data/WebApp1User.cs* ". Aktualisieren Sie die Datei mit dem folgenden Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":

* Wird gelöscht, wenn die Seite " *Bereiche/ Identity /pages/Account/Manage/DeletePersonalData.cshtml* " Razor aufruft `UserManager.Delete` .
* In den heruntergeladenen Daten von der Seite " *Bereiche/ Identity /pages/Account/Manage/DownloadPersonalData.cshtml* " enthalten Razor .

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualisieren Sie die Seite "Account/Manage/Index. cshtml".

Aktualisieren Sie die `InputModel` in *Areas/ Identity /pages/Account/Manage/Index.cshtml.cs* mit dem folgenden markierten Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aktualisieren Sie die *Bereiche/ Identity /pages/Account/Manage/Index.cshtml* mit dem folgenden markierten Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualisieren Sie die *Bereiche/ Identity /pages/Account/Manage/Index.cshtml* mit dem folgenden markierten Markup:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualisieren Sie die Seite "Account/Register. cshtml".

Aktualisieren Sie die `InputModel` in *Areas/ Identity /pages/Account/Register.cshtml.cs* mit dem folgenden markierten Code:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aktualisieren Sie die *Bereiche/ Identity /pages/Account/Register.cshtml* mit dem folgenden markierten Markup:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualisieren Sie die *Bereiche/ Identity /pages/Account/Register.cshtml* mit dem folgenden markierten Markup:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Erstellen Sie das Projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio- **Paket-Manager-Konsole** :

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Test erstellen, anzeigen, herunterladen, benutzerdefinierte Benutzerdaten löschen

Testen der App:

* Registrieren Sie einen neuen Benutzer.
* Zeigen Sie die benutzerdefinierten Benutzerdaten auf der `/Identity/Account/Manage` Seite an.
* Herunterladen und Anzeigen der persönlichen Daten der Benutzer auf der `/Identity/Account/Manage/PersonalData` Seite.

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Hinzufügen von Ansprüchen zu Identity mithilfe von iuserclaimsprincipalfactory<ApplicationUser>

> [!NOTE]
> Dieser Abschnitt ist keine Erweiterung des vorherigen Tutorials. Informationen zum Anwenden der folgenden Schritte auf die APP, die mit dem Tutorial erstellt wurde, finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/18797).

Zusätzliche Ansprüche können ASP.NET Core Identity mithilfe der-Schnittstelle hinzugefügt werden `IUserClaimsPrincipalFactory<T>` . Diese Klasse kann der app in der-Methode hinzugefügt werden `Startup.ConfigureServices` . Fügen Sie die benutzerdefinierte Implementierung der-Klasse wie folgt hinzu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Der Democode verwendet die- `ApplicationUser` Klasse. Diese Klasse fügt eine `IsAdmin` Eigenschaft hinzu, die zum Hinzufügen des zusätzlichen Anspruchs verwendet wird.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

Die `AdditionalUserClaimsPrincipalFactory` implementiert die `UserClaimsPrincipalFactory`-Schnittstelle. Dem wird ein neuer Rollen Anspruch hinzugefügt `ClaimsPrincipal` .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Der zusätzliche Anspruch kann dann in der APP verwendet werden. Auf einer Razor Seite kann die `IAuthorizationService` Instanz verwendet werden, um auf den Anspruchs Wert zuzugreifen.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
