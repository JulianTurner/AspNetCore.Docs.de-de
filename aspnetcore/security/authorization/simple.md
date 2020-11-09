---
title: Einfache Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie das Attribut "autorisieren" verwenden, um den Zugriff auf ASP.net Core Controller und Aktionen einzuschränken.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/simple
ms.openlocfilehash: ae8fb47e58924d559f1c2c4ed7c9545c37141209
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061339"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="759e2-103">Einfache Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="759e2-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="759e2-104">Die Autorisierung in ASP.net Core wird mit <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> und den verschiedenen Parametern gesteuert.</span><span class="sxs-lookup"><span data-stu-id="759e2-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="759e2-105">In seiner einfachsten Form schränkt das Anwenden des- `[Authorize]` Attributs auf einen Controller, eine Aktion oder eine Razor Seite den Zugriff auf diese Komponente auf einen beliebigen authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="759e2-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="759e2-106">Der folgende Code schränkt z. b. den Zugriff auf `AccountController` alle authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="759e2-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="759e2-107">Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das- `AuthorizeAttribute` Attribut auf die eigentliche Aktion an:</span><span class="sxs-lookup"><span data-stu-id="759e2-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="759e2-108">Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="759e2-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="759e2-109">Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="759e2-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="759e2-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="759e2-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="759e2-111">Dies erlaubt nur authentifizierte Benutzer, `AccountController` außer bei der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.</span><span class="sxs-lookup"><span data-stu-id="759e2-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="759e2-112">`[AllowAnonymous]` umgeht alle Autorisierungs Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="759e2-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="759e2-113">Wenn Sie `[AllowAnonymous]` und ein beliebiges Attribut kombinieren `[Authorize]` , `[Authorize]` werden die Attribute ignoriert.</span><span class="sxs-lookup"><span data-stu-id="759e2-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="759e2-114">Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.</span><span class="sxs-lookup"><span data-stu-id="759e2-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<a name="aarp"></a>

## <a name="authorize-attribute-and-no-locrazor-pages"></a><span data-ttu-id="759e2-115">Authorize-Attribut und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="759e2-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="759e2-116">Das <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> kann \* **Not** _ auf Razor Seiten Handler angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="759e2-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can \* **not** _ be applied to Razor Page handlers.</span></span> <span data-ttu-id="759e2-117">`[Authorize]`Kann z. b. nicht auf `OnGet` , `OnPost` oder einen anderen Seiten Handler angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="759e2-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span> <span data-ttu-id="759e2-118">Verwenden Sie ggf. einen ASP.net Core MVC-Controller für Seiten mit unterschiedlichen Autorisierungs Anforderungen für verschiedene Handler.</span><span class="sxs-lookup"><span data-stu-id="759e2-118">Consider using an ASP.NET Core MVC controller for pages with different authorization requirements for different handlers.</span></span>

<span data-ttu-id="759e2-119">Die folgenden zwei Ansätze können verwendet werden, um die Autorisierung auf Razor seitenhandlermethoden anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="759e2-119">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

<span data-ttu-id="759e2-120">_ Verwenden Sie separate Seiten für Seiten Handler, die eine unterschiedliche Autorisierung erfordern.</span><span class="sxs-lookup"><span data-stu-id="759e2-120">_ Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="759e2-121">Freigegebenen Inhalt in eine oder mehrere [Teilansichten](xref:mvc/views/partial)verschoben.</span><span class="sxs-lookup"><span data-stu-id="759e2-121">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="759e2-122">Wenn möglich, ist dies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="759e2-122">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="759e2-123">Für Inhalte, die eine gemeinsame Seite freigeben müssen, schreiben Sie einen Filter, der eine Autorisierung als Teil von [iasyncpagefilter. onpagehandlerselectionasync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)ausführt.</span><span class="sxs-lookup"><span data-stu-id="759e2-123">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="759e2-124">Das [pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) -GitHub-Projekt veranschaulicht diesen Ansatz:</span><span class="sxs-lookup"><span data-stu-id="759e2-124">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="759e2-125">Der [authorizeingedexpagehandlerfilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implementiert den Autorisierungs Filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="759e2-125">The [AuthorizeIndexPageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="759e2-126">Das Attribut [[Autorität Page Handler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) wird auf den `OnGet` Seiten Handler angewendet: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="759e2-126">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="759e2-127">Der [pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) -Beispiel Ansatz bewirkt \* **nicht** _: _ Compose mit Autorisierungs Attributen, die auf die Seite, das Seiten Modell oder Global angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="759e2-127">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does \* **not** _: _ Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="759e2-128">Das Verfassen von Autorisierungs Attributen führt dazu, dass die Authentifizierung und Autorisierung mehrmals durchgeführt `AuthorizeAttribute` wird, wenn eine oder mehrere `AuthorizeFilter` Instanzen auch auf die Seite angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="759e2-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="759e2-129">Arbeiten Sie in Verbindung mit dem Rest ASP.net Core Authentifizierungs-und Autorisierungssystem.</span><span class="sxs-lookup"><span data-stu-id="759e2-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="759e2-130">Sie müssen überprüfen, ob dieser Ansatz für Ihre Anwendung ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="759e2-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="759e2-131">Es ist nicht geplant, den `AuthorizeAttribute` auf Razor Seiten Handlern zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="759e2-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
