---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erläutert, wie Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: bc5f3c85d7ace120dec8369f4550a097b4f258ce
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417642"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="a074c-103">Einführung in Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a074c-103">Introduction to Razor Pages in ASP.NET Core</span></span>


<span data-ttu-id="a074c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="a074c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="a074c-105">Razor Pages kann im Vergleich zu Controllern und Ansichten das Programmieren seitenbasierter Anwendungen vereinfachen und die Produktivität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="a074c-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="a074c-106">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="a074c-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="a074c-107">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a074c-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="a074c-108">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="a074c-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="a074c-109">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="a074c-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="a074c-110">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="a074c-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a074c-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a074c-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="a074c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a074c-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a074c-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a074c-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a074c-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a074c-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="a074c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a074c-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a074c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a074c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a074c-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a074c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="a074c-118">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="a074c-118">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a074c-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a074c-119">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a074c-120">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="a074c-120">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a074c-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a074c-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a074c-122">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="a074c-122">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a074c-123">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a074c-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a074c-124">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="a074c-124">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="a074c-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-125">Razor Pages</span></span>

<span data-ttu-id="a074c-126">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="a074c-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="a074c-127">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="a074c-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="a074c-128">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="a074c-129">Der Unterschied besteht in der [`@page`](xref:mvc/views/razor#page)-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="a074c-129">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="a074c-130">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="a074c-131">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="a074c-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a074c-132">`@page` wirkt sich auf das Verhalten aller anderen [Razor](xref:mvc/views/razor)-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="a074c-132">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="a074c-133">Razor Pages-Dateinamen haben das Suffix *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a074c-133">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="a074c-134">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a074c-134">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="a074c-135">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-135">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="a074c-136">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a074c-136">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="a074c-137">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *.cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-137">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="a074c-138">Die vorherige Datei mit Razor Razor lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a074c-138">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="a074c-139">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a074c-139">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="a074c-140">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="a074c-140">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="a074c-141">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="a074c-141">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="a074c-142">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="a074c-142">File name and path</span></span>               | <span data-ttu-id="a074c-143">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="a074c-143">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="a074c-144">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-144">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="a074c-145">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="a074c-145">`/` or `/Index`</span></span> |
| <span data-ttu-id="a074c-146">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-146">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="a074c-147">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-147">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="a074c-148">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-148">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="a074c-149">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="a074c-149">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="a074c-150">Notizen:</span><span class="sxs-lookup"><span data-stu-id="a074c-150">Notes:</span></span>

* <span data-ttu-id="a074c-151">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit RRazor Pages.</span><span class="sxs-lookup"><span data-stu-id="a074c-151">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="a074c-152">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="a074c-152">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="a074c-153">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="a074c-153">Write a basic form</span></span>

<span data-ttu-id="a074c-154">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="a074c-154">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="a074c-155">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="a074c-155">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="a074c-156">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="a074c-156">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="a074c-157">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-157">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

<span data-ttu-id="a074c-158">Für die In-Memory-Datenbank ist das NuGet-Paket `Microsoft.EntityFrameworkCore.InMemory` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a074c-158">The in memory database requires the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="a074c-159">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="a074c-159">The data model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="a074c-160">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="a074c-160">The db context:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="a074c-161">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-161">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="a074c-162">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a074c-162">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="a074c-163">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-163">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="a074c-164">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-164">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="a074c-165">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="a074c-165">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="a074c-166">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-166">This separation allows:</span></span>

* <span data-ttu-id="a074c-167">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a074c-167">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="a074c-168">Komponententests</span><span class="sxs-lookup"><span data-stu-id="a074c-168">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="a074c-169">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="a074c-169">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="a074c-170">Für alle HTTP-Verben können Handlermethoden hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-170">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="a074c-171">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="a074c-171">The most common handlers are:</span></span>

* <span data-ttu-id="a074c-172">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-172">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="a074c-173">Im vorangehenden Code wird die Razor-Seite *CreateModel.cshtml* durch die `OnGet`-Methode dargestellt.</span><span class="sxs-lookup"><span data-stu-id="a074c-173">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="a074c-174">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="a074c-174">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="a074c-175">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-175">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="a074c-176">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a074c-176">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="a074c-177">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="a074c-177">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="a074c-178">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="a074c-178">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="a074c-179">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden in Controllern und Razor Pages auf dieselbe Weise eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="a074c-179">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="a074c-180">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a074c-180">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="a074c-181">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="a074c-181">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="a074c-182">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="a074c-182">Check for validation errors.</span></span>

* <span data-ttu-id="a074c-183">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="a074c-183">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="a074c-184">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="a074c-184">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="a074c-185">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="a074c-185">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="a074c-186">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-186">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="a074c-187">Der von *Pages/Create.cshtml* gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a074c-187">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="a074c-188">Im vorherigen Code gilt für die Formularübermittlung mittels POST Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-188">In the previous code, posting the form:</span></span>

* <span data-ttu-id="a074c-189">Bei gültigen Daten:</span><span class="sxs-lookup"><span data-stu-id="a074c-189">With valid data:</span></span>

  * <span data-ttu-id="a074c-190">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="a074c-190">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="a074c-191">`RedirectToPage` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-191">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="a074c-192">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="a074c-192">`RedirectToPage`:</span></span>

    * <span data-ttu-id="a074c-193">ist ein Aktionsergebnis.</span><span class="sxs-lookup"><span data-stu-id="a074c-193">Is an action result.</span></span>
    * <span data-ttu-id="a074c-194">ähnelt `RedirectToAction` oder `RedirectToRoute` (wird in Controllern und Ansichten verwendet).</span><span class="sxs-lookup"><span data-stu-id="a074c-194">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="a074c-195">ist an Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="a074c-195">Is customized for pages.</span></span> <span data-ttu-id="a074c-196">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="a074c-196">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="a074c-197">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="a074c-197">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="a074c-198">Bei Validierungsfehlern, die an den Server übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="a074c-198">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="a074c-199">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="a074c-199">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="a074c-200">`Page` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-200">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="a074c-201">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a074c-201">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="a074c-202">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="a074c-202">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="a074c-203">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-203">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="a074c-204">Wenn im vorangehenden Beispiel das Formular mithilfe von POST übermittelt und dabei kein Wert angegeben wird, gibt [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) „false“ zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-204">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="a074c-205">In diesem Beispiel werden keine Validierungsfehler auf dem Client angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a074c-205">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="a074c-206">Die Verarbeitung von Validierungsfehlern wird weiter unten in diesem Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="a074c-206">Validation error handing is covered later in this document.</span></span>

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="a074c-207">Bei Validierungsfehlern, die durch eine clientseitige Validierung erkannt werden:</span><span class="sxs-lookup"><span data-stu-id="a074c-207">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="a074c-208">Die Daten werden **nicht** per POST an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-208">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="a074c-209">Die clientseitige Validierung wird weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="a074c-209">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="a074c-210">Die Eigenschaft `Customer` verwendet das [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attribut, um die Modellbindung zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="a074c-210">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="a074c-211">`[BindProperty]` sollte **nicht** in Modellen mit Eigenschaften verwendet werden, die vom Client nicht geändert werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="a074c-211">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="a074c-212">Weitere Informationen finden Sie unter [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="a074c-212">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="a074c-213">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="a074c-213">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="a074c-214">Durch die Bindung an Eigenschaften entfällt das Schreiben von Code, mit dem HTTP-Daten in den Modelltyp konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-214">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="a074c-215">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-215">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="a074c-216">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="a074c-216">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="a074c-217">Im vorangehenden Code bindet das [Eingabetag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` das HTML-Element `<input>` an den Modellausdruck `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="a074c-217">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="a074c-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) stellt Taghilfsprogramme zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="a074c-218">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="a074c-219">Die Homepage</span><span class="sxs-lookup"><span data-stu-id="a074c-219">The home page</span></span>

<span data-ttu-id="a074c-220">*Index.cshtml* ist die Homepage:</span><span class="sxs-lookup"><span data-stu-id="a074c-220">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="a074c-221">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="a074c-221">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a074c-222">Die Datei *Index.cshtml* enthält das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="a074c-222">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="a074c-223">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-223">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="a074c-224">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="a074c-224">The link contains route data with the contact ID.</span></span> <span data-ttu-id="a074c-225">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="a074c-225">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="a074c-226">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="a074c-226">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="a074c-227">Die Datei *Index.cshtml* enthält das Markup zum Erstellen der Schaltfläche „delete“ (Löschen) für jeden Kundenkontakt:</span><span class="sxs-lookup"><span data-stu-id="a074c-227">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="a074c-228">Der gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a074c-228">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="a074c-229">Wenn die „delete“-Schaltfläche in HTML gerendert wird, enthält das zugehörige [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-229">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="a074c-230">die Kundenkontakt-ID, die durch das `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="a074c-230">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="a074c-231">den `handler`, der durch das `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="a074c-231">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="a074c-232">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-232">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="a074c-233">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="a074c-233">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="a074c-234">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a074c-234">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="a074c-235">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="a074c-235">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="a074c-236">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a074c-236">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="a074c-237">ruft die `id` der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="a074c-237">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="a074c-238">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="a074c-238">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="a074c-239">Wenn der Kundenkontakt gefunden wird, wird er entfernt, und die Datenbank wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-239">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="a074c-240">Ruft <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="a074c-240">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="a074c-241">Die Datei „Edit.cshtml“</span><span class="sxs-lookup"><span data-stu-id="a074c-241">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="a074c-242">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="a074c-242">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="a074c-243">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-243">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="a074c-244">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-244">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="a074c-245">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="a074c-245">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="a074c-246">Die Datei *Edit.cshtml.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a074c-246">The *Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="a074c-247">Validierung</span><span class="sxs-lookup"><span data-stu-id="a074c-247">Validation</span></span>

<span data-ttu-id="a074c-248">Für Validierungsregeln gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-248">Validation rules:</span></span>

* <span data-ttu-id="a074c-249">Sie werden deklarativ in der Modellklasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="a074c-249">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="a074c-250">Sie werden überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="a074c-250">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="a074c-251">Der Namespace <xref:System.ComponentModel.DataAnnotations> stellt eine Gruppe integrierter Validierungsattribute bereit, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-251">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="a074c-252">„DataAnnotations“ enthält auch Formatierungsattribute wie [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a074c-252">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="a074c-253">Sehen Sie sich das `Customer`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="a074c-253">Consider the `Customer` model:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="a074c-254">Für die folgende Ansichtsdatei *Create.cshtml* gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-254">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="a074c-255">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="a074c-255">The preceding code:</span></span>

* <span data-ttu-id="a074c-256">umfasst jQuery-Skripts einschließlich solcher zur Validierung.</span><span class="sxs-lookup"><span data-stu-id="a074c-256">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="a074c-257">Verwendet die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) `<div />` und `<span />`, um Folgendes zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="a074c-257">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="a074c-258">clientseitiger Validierung.</span><span class="sxs-lookup"><span data-stu-id="a074c-258">Client-side validation.</span></span>
  * <span data-ttu-id="a074c-259">Rendering von Validierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="a074c-259">Validation error rendering.</span></span>

* <span data-ttu-id="a074c-260">wird der folgende HTML-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="a074c-260">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="a074c-261">Wenn Sie das Formular „Create“ (Erstellen) ohne einen Wert für den Namen mit POST übermitteln, wird die Fehlermeldung „The Name field is required.“ (Für das Namensfeld muss ein Wert angegeben werden.)</span><span class="sxs-lookup"><span data-stu-id="a074c-261">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="a074c-262">auf dem Formular angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a074c-262">on the form.</span></span> <span data-ttu-id="a074c-263">Wenn JavaScript auf dem Client aktiviert ist, zeigt der Browser den Fehler an, ohne dass Daten per POST an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-263">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="a074c-264">Das `[StringLength(10)]`-Attribut generiert `data-val-length-max="10"` für den gerenderten HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="a074c-264">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="a074c-265">`data-val-length-max` verhindert, dass im Browser ein Wert eingegeben wird, der die angegebene Maximallänge überschreitet.</span><span class="sxs-lookup"><span data-stu-id="a074c-265">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="a074c-266">Wenn ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Bearbeiten und erneuten Senden einer POST-Anforderung verwendet wird</span><span class="sxs-lookup"><span data-stu-id="a074c-266">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="a074c-267">und die Länge des Namens 10 Zeichen überschreitet,</span><span class="sxs-lookup"><span data-stu-id="a074c-267">With the name longer than 10.</span></span>
* <span data-ttu-id="a074c-268">geschieht Folgendes: Die Fehlermeldung „The field Name must be a string with a maximum length of 10.“ (Das Namensfeld muss eine Zeichenfolge mit maximal 10 Zeichen enthalten.)</span><span class="sxs-lookup"><span data-stu-id="a074c-268">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="a074c-269">wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a074c-269">is returned.</span></span>

<span data-ttu-id="a074c-270">Sehen Sie sich das folgende `Movie`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="a074c-270">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="a074c-271">Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:</span><span class="sxs-lookup"><span data-stu-id="a074c-271">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="a074c-272">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="a074c-272">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="a074c-273">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="a074c-273">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="a074c-274">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-274">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="a074c-275">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="a074c-275">Must only use letters.</span></span>
  * <span data-ttu-id="a074c-276">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="a074c-276">The first letter is required to be uppercase.</span></span> <span data-ttu-id="a074c-277">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="a074c-277">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="a074c-278">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-278">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="a074c-279">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="a074c-279">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="a074c-280">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="a074c-280">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="a074c-281">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="a074c-281">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="a074c-282">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="a074c-282">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="a074c-283">Mit dem Attribut `StringLength` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-283">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="a074c-284">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="a074c-284">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="a074c-285">Auf der Seite „Create“ (Erstellen) für das `Movie`-Modell werden ungültige Werte und die daraus resultierenden Fehler angezeigt:</span><span class="sxs-lookup"><span data-stu-id="a074c-285">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="a074c-287">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="a074c-287">For more information, see:</span></span>

* [<span data-ttu-id="a074c-288">Hinzufügen von Validierungscode zur Movie-App</span><span class="sxs-lookup"><span data-stu-id="a074c-288">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="a074c-289">[Modellvalidierung in ASP.NET Core](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="a074c-289">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="a074c-290">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="a074c-290">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="a074c-291">`HEAD`-Anforderungen ermöglichen das Abrufen der Header für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="a074c-291">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="a074c-292">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-292">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="a074c-293">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="a074c-293">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="a074c-294">Razor Pages ruft den `OnGet`-Handler auf, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="a074c-294">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="a074c-295">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-295">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="a074c-296">Razor Pages wird durch [Validierungsmaßnahmen vor XSRF/CSRF-Angriffen](xref:security/anti-request-forgery) geschützt.</span><span class="sxs-lookup"><span data-stu-id="a074c-296">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="a074c-297">Das [Formulartag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert Anti-XSRF/CSRF-Token in HTML-Formularelemente.</span><span class="sxs-lookup"><span data-stu-id="a074c-297">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="a074c-298">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-298">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="a074c-299">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="a074c-299">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="a074c-300">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* und *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="a074c-300">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="a074c-301">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="a074c-301">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="a074c-302">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="a074c-302">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="a074c-303">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="a074c-303">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="a074c-304">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a074c-304">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="a074c-305">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="a074c-305">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="a074c-306">Der Inhalt der Razor-Seite wird gerendert, wenn `@RenderBody()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-306">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="a074c-307">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a074c-307">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="a074c-308">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-308">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a074c-309">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="a074c-309">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="a074c-310">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-310">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="a074c-311">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-311">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="a074c-312">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-312">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="a074c-313">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-313">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="a074c-314">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="a074c-314">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="a074c-315">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="a074c-315">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="a074c-316">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-316">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="a074c-317">Die Layouts, Vorlagen und Teilansichten, die mit MVC-Controllern und herkömmlichen Razor-Ansichten verwendet werden, *funktionieren problemlos*.</span><span class="sxs-lookup"><span data-stu-id="a074c-317">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="a074c-318">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="a074c-318">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="a074c-319">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="a074c-319">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="a074c-320">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-320">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="a074c-321">Die `@namespace`-Anweisung wird wie folgt für eine Seite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-321">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="a074c-322">Die `@namespace`-Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="a074c-322">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="a074c-323">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-323">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="a074c-324">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-324">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="a074c-325">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="a074c-325">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="a074c-326">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="a074c-326">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="a074c-327">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="a074c-327">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="a074c-328">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="a074c-328">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="a074c-329">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="a074c-329">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="a074c-330">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="a074c-330">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="a074c-331">Die aktualisierte Ansichtsdatei *Pages/Create.cshtml* mit *_ViewImports.cshtml* und der vorherigen Layoutdatei sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a074c-331">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="a074c-332">Im vorangehenden Code werden von *_ViewImports.cshtml* der Namespace und die Taghilfsprogramme importiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-332">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="a074c-333">Die JavaScript-Dateien werden von der Layoutdatei importiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-333">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="a074c-334">Das [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="a074c-334">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="a074c-335">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="a074c-335">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="a074c-336">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="a074c-336">URL generation for Pages</span></span>

<span data-ttu-id="a074c-337">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="a074c-337">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="a074c-338">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="a074c-338">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="a074c-339">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="a074c-339">*/Pages*</span></span>

  * <span data-ttu-id="a074c-340">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-340">*Index.cshtml*</span></span>
  * <span data-ttu-id="a074c-341">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-341">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="a074c-342">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="a074c-342">*/Customers*</span></span>

    * <span data-ttu-id="a074c-343">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-343">*Create.cshtml*</span></span>
    * <span data-ttu-id="a074c-344">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-344">*Edit.cshtml*</span></span>
    * <span data-ttu-id="a074c-345">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-345">*Index.cshtml*</span></span>

<span data-ttu-id="a074c-346">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* führen bei Erfolg eine Umleitung zu *Pages/Customers/Index.cshtml* durch.</span><span class="sxs-lookup"><span data-stu-id="a074c-346">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="a074c-347">Die Zeichenfolge `./Index` stellt einen relativen Seitennamen dar, der für den Zugriff auf die vorherige Seite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-347">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="a074c-348">Sie wird für das Generieren von URIs für die Seite *Pages/Customers/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-348">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="a074c-349">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a074c-349">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="a074c-350">Der absolute Seitenname `/Index` wird zum Generieren der URLs für die Seite *Pages/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-350">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="a074c-351">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a074c-351">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="a074c-352">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="a074c-352">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="a074c-353">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="a074c-353">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="a074c-354">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-354">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="a074c-355">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="a074c-355">URL generation for pages supports relative names.</span></span> <span data-ttu-id="a074c-356">In der folgenden Tabelle wird dargestellt, welche Indexseite durch verschiedene `RedirectToPage`-Parameter in *Pages/Customers/Create.cshtml* ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-356">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="a074c-357">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="a074c-357">RedirectToPage(x)</span></span>| <span data-ttu-id="a074c-358">Seite</span><span class="sxs-lookup"><span data-stu-id="a074c-358">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="a074c-359">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-359">RedirectToPage("/Index")</span></span> | <span data-ttu-id="a074c-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-360">*Pages/Index*</span></span> |
| <span data-ttu-id="a074c-361">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="a074c-361">RedirectToPage("./Index");</span></span> | <span data-ttu-id="a074c-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-362">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="a074c-363">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-363">RedirectToPage("../Index")</span></span> | <span data-ttu-id="a074c-364">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-364">*Pages/Index*</span></span> |
| <span data-ttu-id="a074c-365">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-365">RedirectToPage("Index")</span></span>  | <span data-ttu-id="a074c-366">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-366">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="a074c-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="a074c-367">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="a074c-368">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="a074c-368">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="a074c-369">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="a074c-369">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="a074c-370">Wenn durch relative Namen Seiten in einem Ordner verknüpft werden, hat das folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="a074c-370">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="a074c-371">Relative Links funktionieren weiterhin, wenn ein Ordner umbenannt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-371">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="a074c-372">Links funktionieren weiterhin, da sie keinen Ordnernamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-372">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="a074c-373">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="a074c-373">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="a074c-374">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas> und <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="a074c-374">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="a074c-375">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="a074c-375">ViewData attribute</span></span>

<span data-ttu-id="a074c-376">Daten können mit <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> an eine Seite übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-376">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="a074c-377">Für Eigenschaften mit dem `[ViewData]`-Attribut werden die Werte in <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="a074c-377">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="a074c-378">Im folgenden Beispiel wendet `AboutModel` das `[ViewData]`-Attribut auf die `Title`-Eigenschaft an:</span><span class="sxs-lookup"><span data-stu-id="a074c-378">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="a074c-379">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="a074c-379">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="a074c-380">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="a074c-380">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="a074c-381">TempData</span><span class="sxs-lookup"><span data-stu-id="a074c-381">TempData</span></span>

<span data-ttu-id="a074c-382">ASP.NET Core macht <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a074c-382">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="a074c-383">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="a074c-383">This property stores data until it's read.</span></span> <span data-ttu-id="a074c-384">Die Methoden <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> und <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="a074c-384">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="a074c-385">`TempData` eignet sich für die Umleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-385">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="a074c-386">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-386">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="a074c-387">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="a074c-387">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="a074c-388">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="a074c-388">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="a074c-389">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="a074c-389">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="a074c-390">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="a074c-390">Multiple handlers per page</span></span>

<span data-ttu-id="a074c-391">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="a074c-391">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="a074c-392">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="a074c-392">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="a074c-393">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="a074c-393">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="a074c-394">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="a074c-394">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="a074c-395">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="a074c-395">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="a074c-396">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="a074c-396">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="a074c-397">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="a074c-397">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="a074c-398">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="a074c-398">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="a074c-399">Im vorherigen Beispiel sind OnPost **JoinList** Async und OnPost **JoinListUC** Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="a074c-399">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="a074c-400">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-400">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="a074c-401">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="a074c-401">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="a074c-402">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-402">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="a074c-403">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="a074c-403">Custom routes</span></span>

<span data-ttu-id="a074c-404">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-404">Use the `@page` directive to:</span></span>

* <span data-ttu-id="a074c-405">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-405">Specify a custom route to a page.</span></span> <span data-ttu-id="a074c-406">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-406">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="a074c-407">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-407">Append segments to a page's default route.</span></span> <span data-ttu-id="a074c-408">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-408">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="a074c-409">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-409">Append parameters to a page's default route.</span></span> <span data-ttu-id="a074c-410">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-410">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="a074c-411">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-411">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="a074c-412">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="a074c-412">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="a074c-413">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-413">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="a074c-414">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="a074c-414">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="a074c-415">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="a074c-415">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="a074c-416">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-416">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="a074c-417">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="a074c-417">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="a074c-418">Erweiterte Konfigurationen und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="a074c-418">Advanced configuration and settings</span></span>

<span data-ttu-id="a074c-419">Die Konfigurationen und Einstellungen in den folgenden Abschnitten sind für die meisten Apps nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a074c-419">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="a074c-420">Verwenden Sie die Überladung <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, die <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> konfiguriert, um die erweiterten Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a074c-420">To configure advanced options, use the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> overload that configures <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="a074c-421">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a074c-421">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="a074c-422">Weitere Informationen zu Konventionen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="a074c-422">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="a074c-423">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="a074c-423">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="a074c-424">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-424">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="a074c-425">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-425">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="a074c-426">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="a074c-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="a074c-427">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-427">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="a074c-428">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten in einem benutzerdefinierten Stammverzeichnis der App befinden. Geben Sie dabei einen relativen Pfad an:</span><span class="sxs-lookup"><span data-stu-id="a074c-428">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a074c-429">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a074c-429">Additional resources</span></span>

* <span data-ttu-id="a074c-430">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="a074c-430">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>
* [<span data-ttu-id="a074c-431">Authorize-Attribut und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-431">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* [<span data-ttu-id="a074c-432">Herunterladen und Anzeigen des Beispielcodes</span><span class="sxs-lookup"><span data-stu-id="a074c-432">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studio"></a>[<span data-ttu-id="a074c-433">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a074c-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a074c-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a074c-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a074c-435">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a074c-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a><span data-ttu-id="a074c-436">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="a074c-436">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a074c-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a074c-437">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a074c-438">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="a074c-438">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a074c-439">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="a074c-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a074c-440">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="a074c-440">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="a074c-441">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="a074c-441">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a074c-442">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a074c-442">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a074c-443">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="a074c-443">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="no-locrazor-pages"></a><span data-ttu-id="a074c-444">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-444">Razor Pages</span></span>

<span data-ttu-id="a074c-445">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="a074c-445">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="a074c-446">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="a074c-446">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="a074c-447">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-447">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="a074c-448">Der Unterschied besteht in der `@page`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="a074c-448">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="a074c-449">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-449">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="a074c-450">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="a074c-450">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="a074c-451">`@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="a074c-451">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="a074c-452">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a074c-452">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="a074c-453">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-453">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="a074c-454">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a074c-454">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="a074c-455">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *.cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-455">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="a074c-456">Die vorherige Datei mit Razor Razor lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="a074c-456">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="a074c-457">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="a074c-457">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="a074c-458">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="a074c-458">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="a074c-459">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="a074c-459">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="a074c-460">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="a074c-460">File name and path</span></span>               | <span data-ttu-id="a074c-461">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="a074c-461">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="a074c-462">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-462">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="a074c-463">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="a074c-463">`/` or `/Index`</span></span> |
| <span data-ttu-id="a074c-464">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-464">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="a074c-465">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-465">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="a074c-466">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-466">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="a074c-467">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="a074c-467">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="a074c-468">Notizen:</span><span class="sxs-lookup"><span data-stu-id="a074c-468">Notes:</span></span>

* <span data-ttu-id="a074c-469">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit RRazor Pages.</span><span class="sxs-lookup"><span data-stu-id="a074c-469">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="a074c-470">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="a074c-470">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="a074c-471">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="a074c-471">Write a basic form</span></span>

<span data-ttu-id="a074c-472">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="a074c-472">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="a074c-473">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="a074c-473">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="a074c-474">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="a074c-474">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="a074c-475">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-475">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="a074c-476">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="a074c-476">The data model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="a074c-477">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="a074c-477">The db context:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="a074c-478">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-478">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="a074c-479">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a074c-479">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="a074c-480">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-480">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="a074c-481">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-481">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="a074c-482">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="a074c-482">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="a074c-483">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-483">This separation allows:</span></span>

* <span data-ttu-id="a074c-484">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a074c-484">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="a074c-485">[Komponententests](xref:test/razor-pages-tests) für Seiten</span><span class="sxs-lookup"><span data-stu-id="a074c-485">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="a074c-486">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="a074c-486">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="a074c-487">Sie können Handlermethoden für alle HTTP-Verben hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="a074c-487">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="a074c-488">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="a074c-488">The most common handlers are:</span></span>

* <span data-ttu-id="a074c-489">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-489">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="a074c-490">[OnGet](#OnGet)-Beispiel</span><span class="sxs-lookup"><span data-stu-id="a074c-490">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="a074c-491">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="a074c-491">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="a074c-492">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-492">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="a074c-493">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="a074c-493">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="a074c-494">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="a074c-494">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="a074c-495">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="a074c-495">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="a074c-496">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden gemeinsam verwendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-496">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="a074c-497">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a074c-497">The previous `OnPostAsync` method:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="a074c-498">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="a074c-498">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="a074c-499">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="a074c-499">Check for validation errors.</span></span>

* <span data-ttu-id="a074c-500">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="a074c-500">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="a074c-501">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="a074c-501">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="a074c-502">Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen,</span><span class="sxs-lookup"><span data-stu-id="a074c-502">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="a074c-503">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="a074c-503">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="a074c-504">Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="a074c-504">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="a074c-505">`RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="a074c-505">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="a074c-506">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="a074c-506">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="a074c-507">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="a074c-507">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="a074c-508">Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="a074c-508">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="a074c-509">`Page` gibt eine Instanz von `PageResult` zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-509">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="a074c-510">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a074c-510">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="a074c-511">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="a074c-511">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="a074c-512">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-512">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="a074c-513">Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-513">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="a074c-514">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="a074c-514">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="a074c-515">Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="a074c-515">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="a074c-516">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-516">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="a074c-517">Die Startseite (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="a074c-517">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="a074c-518">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="a074c-518">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="a074c-519">Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="a074c-519">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="a074c-520">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-520">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="a074c-521">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="a074c-521">The link contains route data with the contact ID.</span></span> <span data-ttu-id="a074c-522">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="a074c-522">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="a074c-523">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="a074c-523">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="a074c-524">Taghilfsprogramme werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a074c-524">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="a074c-525">Die Datei *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-525">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="a074c-526">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="a074c-526">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="a074c-527">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-527">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="a074c-528">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-528">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="a074c-529">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="a074c-529">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="a074c-530">Die Datei *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="a074c-530">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="a074c-531">Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:</span><span class="sxs-lookup"><span data-stu-id="a074c-531">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="a074c-532">Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-532">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="a074c-533">Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="a074c-533">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="a074c-534">Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="a074c-534">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="a074c-535">Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:</span><span class="sxs-lookup"><span data-stu-id="a074c-535">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="a074c-536">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="a074c-536">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="a074c-537">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="a074c-537">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="a074c-538">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a074c-538">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="a074c-539">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="a074c-539">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="a074c-540">Im folgenden Code wird der `OnPostDeleteAsync`-Handler definiert:</span><span class="sxs-lookup"><span data-stu-id="a074c-540">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="a074c-541">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a074c-541">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="a074c-542">Akzeptiert die `id` der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="a074c-542">Accepts the `id` from the query string.</span></span> <span data-ttu-id="a074c-543">Wenn die Anweisung der Seite *Index.cshtml* die Routingeinschränkung `"{id:int?}"` enthielte, würde `id` aus den Routendaten abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-543">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="a074c-544">Die Routendaten für `id` werden beispielsweise wie folgt im URI angegeben: `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="a074c-544">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="a074c-545">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="a074c-545">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="a074c-546">Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt.</span><span class="sxs-lookup"><span data-stu-id="a074c-546">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="a074c-547">Die Datenbank wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-547">The database is updated.</span></span>
* <span data-ttu-id="a074c-548">Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="a074c-548">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="a074c-549">Markieren von Eigenschaften als „Required“ (Erforderlich)</span><span class="sxs-lookup"><span data-stu-id="a074c-549">Mark page properties as required</span></span>

<span data-ttu-id="a074c-550">Eigenschaften in einem `PageModel` können mit dem [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)-Attribut markiert werden:</span><span class="sxs-lookup"><span data-stu-id="a074c-550">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="a074c-551">Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="a074c-551">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="a074c-552">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="a074c-552">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="a074c-553">`HEAD`-Anforderungen ermöglichen Ihnen das Abrufen des Headers für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="a074c-553">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="a074c-554">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="a074c-554">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="a074c-555">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="a074c-555">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="a074c-556">In ASP.NET Core 2.1 oder höher greift Razor Pages auf den `OnGet`-Handler zurück, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="a074c-556">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="a074c-557">Dieses Verhalten wird durch den Aufruf von [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="a074c-557">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="a074c-558">Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2.</span><span class="sxs-lookup"><span data-stu-id="a074c-558">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="a074c-559">Tatsächlich legt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="a074c-559">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="a074c-560">Sie müssen nicht alle Verhalten in `SetCompatibilityVersion` aktivieren, sondern können sich nur *bestimmte* Verhalten aussuchen.</span><span class="sxs-lookup"><span data-stu-id="a074c-560">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="a074c-561">Der folgende Code gibt an, dass `HEAD`-Anforderungen dem `OnGet`-Handler zugeordnet werden dürfen:</span><span class="sxs-lookup"><span data-stu-id="a074c-561">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a><span data-ttu-id="a074c-562">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-562">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="a074c-563">Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben.</span><span class="sxs-lookup"><span data-stu-id="a074c-563">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="a074c-564">Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-564">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a><span data-ttu-id="a074c-565">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-565">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="a074c-566">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="a074c-566">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="a074c-567">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="a074c-567">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="a074c-568">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="a074c-568">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="a074c-569">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="a074c-569">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="a074c-570">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="a074c-570">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="a074c-571">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a074c-571">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="a074c-572">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="a074c-572">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="a074c-573">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="a074c-573">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="a074c-574">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-574">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="a074c-575">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="a074c-575">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="a074c-576">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-576">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="a074c-577">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-577">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="a074c-578">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-578">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="a074c-579">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="a074c-579">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="a074c-580">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="a074c-580">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="a074c-581">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="a074c-581">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="a074c-582">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-582">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="a074c-583">Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach*.</span><span class="sxs-lookup"><span data-stu-id="a074c-583">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="a074c-584">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="a074c-584">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="a074c-585">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="a074c-585">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="a074c-586">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-586">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="a074c-587">Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="a074c-587">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="a074c-588">Die Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="a074c-588">The directive sets the namespace for the page.</span></span> <span data-ttu-id="a074c-589">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-589">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="a074c-590">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="a074c-590">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="a074c-591">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="a074c-591">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="a074c-592">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="a074c-592">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="a074c-593">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="a074c-593">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="a074c-594">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="a074c-594">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="a074c-595">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="a074c-595">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="a074c-596">Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-596">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="a074c-597">Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a074c-597">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="a074c-598">Das [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="a074c-598">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="a074c-599">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="a074c-599">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="a074c-600">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="a074c-600">URL generation for Pages</span></span>

<span data-ttu-id="a074c-601">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="a074c-601">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="a074c-602">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="a074c-602">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="a074c-603">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="a074c-603">*/Pages*</span></span>

  * <span data-ttu-id="a074c-604">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-604">*Index.cshtml*</span></span>
  * <span data-ttu-id="a074c-605">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="a074c-605">*/Customers*</span></span>

    * <span data-ttu-id="a074c-606">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-606">*Create.cshtml*</span></span>
    * <span data-ttu-id="a074c-607">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-607">*Edit.cshtml*</span></span>
    * <span data-ttu-id="a074c-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a074c-608">*Index.cshtml*</span></span>

<span data-ttu-id="a074c-609">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter.</span><span class="sxs-lookup"><span data-stu-id="a074c-609">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="a074c-610">Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll.</span><span class="sxs-lookup"><span data-stu-id="a074c-610">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="a074c-611">Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-611">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="a074c-612">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a074c-612">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="a074c-613">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="a074c-613">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="a074c-614">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="a074c-614">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="a074c-615">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-615">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="a074c-616">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="a074c-616">URL generation for pages supports relative names.</span></span> <span data-ttu-id="a074c-617">In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="a074c-617">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="a074c-618">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="a074c-618">RedirectToPage(x)</span></span>| <span data-ttu-id="a074c-619">Seite</span><span class="sxs-lookup"><span data-stu-id="a074c-619">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="a074c-620">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-620">RedirectToPage("/Index")</span></span> | <span data-ttu-id="a074c-621">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-621">*Pages/Index*</span></span> |
| <span data-ttu-id="a074c-622">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="a074c-622">RedirectToPage("./Index");</span></span> | <span data-ttu-id="a074c-623">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-623">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="a074c-624">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-624">RedirectToPage("../Index")</span></span> | <span data-ttu-id="a074c-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-625">*Pages/Index*</span></span> |
| <span data-ttu-id="a074c-626">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="a074c-626">RedirectToPage("Index")</span></span>  | <span data-ttu-id="a074c-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="a074c-627">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="a074c-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="a074c-628">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="a074c-629">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="a074c-629">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="a074c-630">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="a074c-630">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="a074c-631">Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen.</span><span class="sxs-lookup"><span data-stu-id="a074c-631">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="a074c-632">Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.</span><span class="sxs-lookup"><span data-stu-id="a074c-632">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="a074c-633">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="a074c-633">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="a074c-634">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="a074c-634">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="a074c-635">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="a074c-635">ViewData attribute</span></span>

<span data-ttu-id="a074c-636">Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-636">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="a074c-637">Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit dem `[ViewData]`-Attribut versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="a074c-637">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="a074c-638">Im folgenden Beispiel enthält das `AboutModel` eine `Title`-Eigenschaft, die mit `[ViewData]` markiert ist.</span><span class="sxs-lookup"><span data-stu-id="a074c-638">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="a074c-639">Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-639">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="a074c-640">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="a074c-640">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="a074c-641">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="a074c-641">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="a074c-642">TempData</span><span class="sxs-lookup"><span data-stu-id="a074c-642">TempData</span></span>

<span data-ttu-id="a074c-643">ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a074c-643">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="a074c-644">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="a074c-644">This property stores data until it's read.</span></span> <span data-ttu-id="a074c-645">Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="a074c-645">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="a074c-646">`TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-646">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="a074c-647">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a074c-647">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="a074c-648">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="a074c-648">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="a074c-649">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="a074c-649">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="a074c-650">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="a074c-650">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="a074c-651">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="a074c-651">Multiple handlers per page</span></span>

<span data-ttu-id="a074c-652">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="a074c-652">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="a074c-653">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="a074c-653">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="a074c-654">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="a074c-654">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="a074c-655">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="a074c-655">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="a074c-656">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="a074c-656">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="a074c-657">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="a074c-657">The page model:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="a074c-658">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="a074c-658">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="a074c-659">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="a074c-659">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="a074c-660">Im vorherigen Beispiel sind OnPost **JoinList** Async und OnPost **JoinListUC** Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="a074c-660">In the preceding example, the page methods are OnPost **JoinList** Async and OnPost **JoinListUC** Async.</span></span> <span data-ttu-id="a074c-661">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-661">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="a074c-662">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="a074c-662">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="a074c-663">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-663">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="a074c-664">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="a074c-664">Custom routes</span></span>

<span data-ttu-id="a074c-665">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a074c-665">Use the `@page` directive to:</span></span>

* <span data-ttu-id="a074c-666">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-666">Specify a custom route to a page.</span></span> <span data-ttu-id="a074c-667">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-667">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="a074c-668">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-668">Append segments to a page's default route.</span></span> <span data-ttu-id="a074c-669">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-669">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="a074c-670">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="a074c-670">Append parameters to a page's default route.</span></span> <span data-ttu-id="a074c-671">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="a074c-671">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="a074c-672">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-672">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="a074c-673">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="a074c-673">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="a074c-674">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="a074c-674">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="a074c-675">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="a074c-675">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="a074c-676">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="a074c-676">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="a074c-677">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="a074c-677">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="a074c-678">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="a074c-678">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="a074c-679">Konfiguration und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="a074c-679">Configuration and settings</span></span>

<span data-ttu-id="a074c-680">Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:</span><span class="sxs-lookup"><span data-stu-id="a074c-680">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="a074c-681">Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a074c-681">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="a074c-682">Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.</span><span class="sxs-lookup"><span data-stu-id="a074c-682">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="a074c-683">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="a074c-683">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="a074c-684">[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="a074c-684">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="a074c-685">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="a074c-685">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a><span data-ttu-id="a074c-686">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-686">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="a074c-687">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="a074c-687">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="a074c-688">Fügen Sie [ WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="a074c-688">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="a074c-689">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-689">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="a074c-690">Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):</span><span class="sxs-lookup"><span data-stu-id="a074c-690">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="a074c-691">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a074c-691">Additional resources</span></span>

* [<span data-ttu-id="a074c-692">Authorize-Attribut und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="a074c-692">Authorize attribute and Razor Pages</span></span>](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
