---
title: Rollenbasierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Controller und den Aktions Zugriff einschränken, indem Sie Rollen an das Autorisierungs Attribut übergeben.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/roles
ms.openlocfilehash: 0a2e62afebbcda9710ef82857c87cae8af0375fe
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051160"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="100f6-103">Rollenbasierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="100f6-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="100f6-104">Wenn eine Identität erstellt wird, kann Sie zu einer oder mehreren Rollen gehören.</span><span class="sxs-lookup"><span data-stu-id="100f6-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="100f6-105">Beispielsweise kann "Tracy" zu den Administrator-und Benutzer Rollen gehören, während Scott möglicherweise nur zur Benutzerrolle gehört.</span><span class="sxs-lookup"><span data-stu-id="100f6-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="100f6-106">Die Art und Weise, wie diese Rollen erstellt und verwaltet werden, hängt vom Sicherungs Speicher des Autorisierungs Prozesses ab.</span><span class="sxs-lookup"><span data-stu-id="100f6-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="100f6-107">Rollen werden dem Entwickler über die [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) -Methode der [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) -Klasse zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="100f6-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="100f6-108">Rollen Prüfungen werden hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="100f6-108">Adding role checks</span></span>

<span data-ttu-id="100f6-109">Rollenbasierte Autorisierungs Überprüfungen sind deklarativ. &mdash; der Entwickler bettet diese in Ihren Code ein, und zwar für einen Controller oder eine Aktion innerhalb eines Controllers, wobei Rollen angegeben werden, in denen der aktuelle Benutzer Mitglied sein muss, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="100f6-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="100f6-110">Der folgende Code schränkt z. b. den Zugriff auf alle Aktionen für den `AdministrationController` Benutzer ein, die Mitglied der `Administrator` Rolle sind:</span><span class="sxs-lookup"><span data-stu-id="100f6-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="100f6-111">Sie können mehrere Rollen als durch Trennzeichen getrennte Liste angeben:</span><span class="sxs-lookup"><span data-stu-id="100f6-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="100f6-112">Auf diesen Controller kann nur von Benutzern zugegriffen werden, die Mitglieder der `HRManager` Rolle oder der `Finance` Rolle sind.</span><span class="sxs-lookup"><span data-stu-id="100f6-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="100f6-113">Wenn Sie mehrere Attribute anwenden, muss ein Benutzer, der auf einen Benutzer zugreifen muss, ein Mitglied aller angegebenen Rollen sein. Das folgende Beispiel erfordert, dass ein Benutzer Mitglied der `PowerUser` -Rolle und der- `ControlPanelUser` Rolle sein muss.</span><span class="sxs-lookup"><span data-stu-id="100f6-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="100f6-114">Sie können den Zugriff weiter einschränken, indem Sie zusätzliche Rollen Autorisierungs Attribute auf Aktionsebene anwenden:</span><span class="sxs-lookup"><span data-stu-id="100f6-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

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

<span data-ttu-id="100f6-115">In den vorherigen Code Ausschnitten können Mitglieder der `Administrator` Rolle oder der `PowerUser` Rolle auf den Controller und die `SetTime` Aktion zugreifen, jedoch können nur Mitglieder der `Administrator` Rolle auf die `ShutDown` Aktion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="100f6-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="100f6-116">Sie können auch einen Controller sperren, aber anonymen, nicht authentifizierten Zugriff auf einzelne Aktionen zulassen.</span><span class="sxs-lookup"><span data-stu-id="100f6-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

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

<span data-ttu-id="100f6-117">Für- :::no-loc(Razor)::: Seiten kann auf Folgendes `AuthorizeAttribute` angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="100f6-117">For :::no-loc(Razor)::: Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="100f6-118">Verwenden einer [Konvention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)oder</span><span class="sxs-lookup"><span data-stu-id="100f6-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="100f6-119">Anwenden `AuthorizeAttribute` von auf die- `PageModel` Instanz:</span><span class="sxs-lookup"><span data-stu-id="100f6-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

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
> <span data-ttu-id="100f6-120">Filter Attribute, einschließlich `AuthorizeAttribute` , können nur auf pagemodel angewendet werden und können nicht auf bestimmte seitenhandlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="100f6-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="100f6-121">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="100f6-121">Policy based role checks</span></span>

<span data-ttu-id="100f6-122">Rollenanforderungen können auch mit der neuen Richtlinien Syntax ausgedrückt werden, bei der ein Entwickler eine Richtlinie beim Start als Teil der Autorisierungs Dienst Konfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="100f6-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="100f6-123">Dies tritt normalerweise in `ConfigureServices()` Ihrer *Startup.cs* -Datei auf.</span><span class="sxs-lookup"><span data-stu-id="100f6-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.Add:::no-loc(Razor):::Pages();

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

<span data-ttu-id="100f6-124">Richtlinien werden mithilfe der- `Policy` Eigenschaft des- `AuthorizeAttribute` Attributs angewendet:</span><span class="sxs-lookup"><span data-stu-id="100f6-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="100f6-125">Wenn Sie mehrere zulässige Rollen in einer Anforderung angeben möchten, können Sie Sie als Parameter für die- `RequireRole` Methode angeben:</span><span class="sxs-lookup"><span data-stu-id="100f6-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="100f6-126">In diesem Beispiel werden Benutzer autorisiert, die zu `Administrator` den `PowerUser` Rollen, oder gehören `BackupAdministrator` .</span><span class="sxs-lookup"><span data-stu-id="100f6-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="100f6-127">Rollen Dienste hinzufügen zu :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="100f6-127">Add Role services to :::no-loc(Identity):::</span></span>

<span data-ttu-id="100f6-128">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="100f6-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_:::no-loc(Identity):::_:::no-loc(Identity):::Builder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

