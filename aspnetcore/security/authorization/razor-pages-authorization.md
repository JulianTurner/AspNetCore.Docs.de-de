---
title: ':::no-loc(Razor)::: Seiten Autorisierungs Konventionen in ASP.net Core'
author: rick-anderson
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
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
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 69e1d639aeb55ae64cc54b1cda402ed6bcbb04ab
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060182"
---
# <a name="no-locrazor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="40a7d-103">:::no-loc(Razor)::: Seiten Autorisierungs Konventionen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="40a7d-103">:::no-loc(Razor)::: Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40a7d-104">Eine Möglichkeit, den Zugriff in ihrer Pages-APP zu steuern, :::no-loc(Razor)::: besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="40a7d-104">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="40a7d-105">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="40a7d-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="40a7d-106">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="40a7d-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="40a7d-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40a7d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="40a7d-108">Die Beispiel-App verwendet die- [ :::no-loc(cookie)::: Authentifizierung ohne :::no-loc(ASP.NET Core Identity)::: ](xref:security/authentication/:::no-loc(cookie):::).</span><span class="sxs-lookup"><span data-stu-id="40a7d-108">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="40a7d-109">Die Konzepte und Beispiele in diesem Thema gelten auch für apps, die verwenden :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-109">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="40a7d-110">:::no-loc(ASP.NET Core Identity):::Befolgen Sie die Anweisungen unter, um zu verwenden <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="40a7d-110">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="40a7d-111">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-111">Require authorization to access a page</span></span>

<span data-ttu-id="40a7d-112">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="40a7d-113">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den :::no-loc(Razor)::: relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="40a7d-113">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="40a7d-114">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="40a7d-115">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem Filter- `[Authorize]` Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="40a7d-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="40a7d-116">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="40a7d-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="40a7d-117">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="40a7d-118">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=4)]

<span data-ttu-id="40a7d-119">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-119">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="40a7d-120">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="40a7d-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="40a7d-121">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-121">Require authorization to access an area page</span></span>

<span data-ttu-id="40a7d-122">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="40a7d-123">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="40a7d-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="40a7d-124">Beispielsweise ist der Seiten Name für die Datei *Bereiche/ :::no-loc(Identity)::: /pages/manage/accounts.cshtml* */Manage/Accounts* .</span><span class="sxs-lookup"><span data-stu-id="40a7d-124">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="40a7d-125">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="40a7d-126">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="40a7d-127">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> allen Bereichen in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="40a7d-128">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="40a7d-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="40a7d-129">Beispielsweise ist der Ordner Pfad für die Dateien unter " *Areas/ :::no-loc(Identity)::: /pages/Manage/* " */Manage* .</span><span class="sxs-lookup"><span data-stu-id="40a7d-129">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="40a7d-130">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="40a7d-131">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="40a7d-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="40a7d-132">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention, um eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Seite unter dem angegebenen Pfad zu einer Seite hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="40a7d-133">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den :::no-loc(Razor)::: relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="40a7d-133">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="40a7d-134">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="40a7d-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="40a7d-135">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="40a7d-136">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-136">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="40a7d-137">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="40a7d-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="40a7d-138">Es ist zulässig, anzugeben, dass für einen Ordner von Seiten eine Autorisierung erforderlich ist, und dann anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="40a7d-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="40a7d-139">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="40a7d-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="40a7d-140">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="40a7d-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="40a7d-141">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="40a7d-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="40a7d-142">Wenn sowohl als <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="40a7d-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40a7d-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40a7d-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40a7d-144">Eine Möglichkeit, den Zugriff in ihrer Pages-APP zu steuern, :::no-loc(Razor)::: besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="40a7d-144">One way to control access in your :::no-loc(Razor)::: Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="40a7d-145">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="40a7d-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="40a7d-146">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="40a7d-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="40a7d-147">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="40a7d-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="40a7d-148">Die Beispiel-App verwendet die- [ :::no-loc(cookie)::: Authentifizierung ohne :::no-loc(ASP.NET Core Identity)::: ](xref:security/authentication/:::no-loc(cookie):::).</span><span class="sxs-lookup"><span data-stu-id="40a7d-148">The sample app uses [:::no-loc(cookie)::: authentication without :::no-loc(ASP.NET Core Identity):::](xref:security/authentication/:::no-loc(cookie):::).</span></span> <span data-ttu-id="40a7d-149">Die Konzepte und Beispiele in diesem Thema gelten auch für apps, die verwenden :::no-loc(ASP.NET Core Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-149">The concepts and examples shown in this topic apply equally to apps that use :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="40a7d-150">:::no-loc(ASP.NET Core Identity):::Befolgen Sie die Anweisungen unter, um zu verwenden <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="40a7d-150">To use :::no-loc(ASP.NET Core Identity):::, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="40a7d-151">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-151">Require authorization to access a page</span></span>

<span data-ttu-id="40a7d-152">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Seite auf dem angegebenen Pfad ein hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="40a7d-153">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den :::no-loc(Razor)::: relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="40a7d-153">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="40a7d-154">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="40a7d-155">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem Filter- `[Authorize]` Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="40a7d-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="40a7d-156">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="40a7d-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="40a7d-157">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="40a7d-158">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="40a7d-159">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-159">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

<span data-ttu-id="40a7d-160">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="40a7d-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="40a7d-161">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-161">Require authorization to access an area page</span></span>

<span data-ttu-id="40a7d-162">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts");
```

<span data-ttu-id="40a7d-163">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="40a7d-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="40a7d-164">Beispielsweise ist der Seiten Name für die Datei *Bereiche/ :::no-loc(Identity)::: /pages/manage/accounts.cshtml* */Manage/Accounts* .</span><span class="sxs-lookup"><span data-stu-id="40a7d-164">For example, the page name for the file *Areas/:::no-loc(Identity):::/Pages/Manage/Accounts.cshtml* is */Manage/Accounts* .</span></span>

<span data-ttu-id="40a7d-165">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage(":::no-loc(Identity):::", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="40a7d-166">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="40a7d-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="40a7d-167">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um einem <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage");
```

<span data-ttu-id="40a7d-168">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="40a7d-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="40a7d-169">Beispielsweise ist der Ordner Pfad für die Dateien unter " *Areas/ :::no-loc(Identity)::: /pages/Manage/* " */Manage* .</span><span class="sxs-lookup"><span data-stu-id="40a7d-169">For example, the folder path for the files under *Areas/:::no-loc(Identity):::/Pages/Manage/* is */Manage* .</span></span>

<span data-ttu-id="40a7d-170">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="40a7d-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder(":::no-loc(Identity):::", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="40a7d-171">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="40a7d-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="40a7d-172">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um eine einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Seite im angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="40a7d-173">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den :::no-loc(Razor)::: relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="40a7d-173">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="40a7d-174">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="40a7d-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="40a7d-175">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40a7d-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.Mvc:::no-loc(Razor):::PagesMvcBuilderExtensions.Add:::no-loc(Razor):::PagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="40a7d-176">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="40a7d-176">The specified path is the View Engine path, which is the :::no-loc(Razor)::: Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="40a7d-177">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="40a7d-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="40a7d-178">Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="40a7d-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="40a7d-179">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="40a7d-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="40a7d-180">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="40a7d-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="40a7d-181">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="40a7d-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="40a7d-182">Wenn sowohl als <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="40a7d-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40a7d-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40a7d-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
