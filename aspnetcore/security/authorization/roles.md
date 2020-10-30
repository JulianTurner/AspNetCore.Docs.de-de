---
title: Rollenbasierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Controller und den Aktions Zugriff einschränken, indem Sie Rollen an das Autorisierungs Attribut übergeben.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051160"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Rollenbasierte Autorisierung in ASP.net Core

<a name="security-authorization-role-based"></a>

Wenn eine Identität erstellt wird, kann Sie zu einer oder mehreren Rollen gehören. Beispielsweise kann "Tracy" zu den Administrator-und Benutzer Rollen gehören, während Scott möglicherweise nur zur Benutzerrolle gehört. Die Art und Weise, wie diese Rollen erstellt und verwaltet werden, hängt vom Sicherungs Speicher des Autorisierungs Prozesses ab. Rollen werden dem Entwickler über die [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) -Methode der [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) -Klasse zur Verfügung gestellt.

## <a name="adding-role-checks"></a>Rollen Prüfungen werden hinzugefügt

Rollenbasierte Autorisierungs Überprüfungen sind deklarativ. &mdash; der Entwickler bettet diese in Ihren Code ein, und zwar für einen Controller oder eine Aktion innerhalb eines Controllers, wobei Rollen angegeben werden, in denen der aktuelle Benutzer Mitglied sein muss, um auf die angeforderte Ressource zuzugreifen.

Der folgende Code schränkt z. b. den Zugriff auf alle Aktionen für den `AdministrationController` Benutzer ein, die Mitglied der `Administrator` Rolle sind:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Sie können mehrere Rollen als durch Trennzeichen getrennte Liste angeben:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Auf diesen Controller kann nur von Benutzern zugegriffen werden, die Mitglieder der `HRManager` Rolle oder der `Finance` Rolle sind.

Wenn Sie mehrere Attribute anwenden, muss ein Benutzer, der auf einen Benutzer zugreifen muss, ein Mitglied aller angegebenen Rollen sein. Das folgende Beispiel erfordert, dass ein Benutzer Mitglied der `PowerUser` -Rolle und der- `ControlPanelUser` Rolle sein muss.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Sie können den Zugriff weiter einschränken, indem Sie zusätzliche Rollen Autorisierungs Attribute auf Aktionsebene anwenden:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

In den vorherigen Code Ausschnitten können Mitglieder der `Administrator` Rolle oder der `PowerUser` Rolle auf den Controller und die `SetTime` Aktion zugreifen, jedoch können nur Mitglieder der `Administrator` Rolle auf die `ShutDown` Aktion zugreifen.

Sie können auch einen Controller sperren, aber anonymen, nicht authentifizierten Zugriff auf einzelne Aktionen zulassen.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Für- Razor Seiten kann auf Folgendes `AuthorizeAttribute` angewendet werden:

* Verwenden einer [Konvention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)oder
* Anwenden `AuthorizeAttribute` von auf die- `PageModel` Instanz:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Filter Attribute, einschließlich `AuthorizeAttribute` , können nur auf pagemodel angewendet werden und können nicht auf bestimmte seitenhandlermethoden angewendet werden.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Richtlinien basierte Rollen Überprüfungen

Rollenanforderungen können auch mit der neuen Richtlinien Syntax ausgedrückt werden, bei der ein Entwickler eine Richtlinie beim Start als Teil der Autorisierungs Dienst Konfiguration registriert. Dies tritt normalerweise in `ConfigureServices()` Ihrer *Startup.cs* -Datei auf.

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

Richtlinien werden mithilfe der- `Policy` Eigenschaft des- `AuthorizeAttribute` Attributs angewendet:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Wenn Sie mehrere zulässige Rollen in einer Anforderung angeben möchten, können Sie Sie als Parameter für die- `RequireRole` Methode angeben:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

In diesem Beispiel werden Benutzer autorisiert, die zu `Administrator` den `PowerUser` Rollen, oder gehören `BackupAdministrator` .

### <a name="add-role-services-to-no-locidentity"></a>Rollen Dienste hinzufügen zu Identity

Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

