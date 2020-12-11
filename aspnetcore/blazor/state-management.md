---
title: Blazor-Zustandsverwaltung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie den Zustand in Blazor Server-Apps beibehalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 24f845bc1d98331f2ee54710d17beb6ffa95ad88
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855416"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="90f0b-103">Blazor-Zustandsverwaltung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90f0b-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="90f0b-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90f0b-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="90f0b-105">Der Benutzerzustand, der in einer Blazor WebAssembly-App erstellt wird, wird im Arbeitsspeicher des Browsers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="90f0b-106">Beispiele für den Benutzerzustand im Arbeitsspeicher des Browsers sind:</span><span class="sxs-lookup"><span data-stu-id="90f0b-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="90f0b-107">Die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe in der gerenderten Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="90f0b-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="90f0b-108">Die Werte der Felder und Eigenschaften in Komponenteninstanzen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="90f0b-109">Daten, die in [DI](xref:fundamentals/dependency-injection)-Dienstinstanzen (Dependency Injection, Abhängigkeitsinjektion) gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="90f0b-110">Werte, die durch [JavaScript-Interop](xref:blazor/call-javascript-from-dotnet)-Aufrufe festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="90f0b-111">Wenn ein Benutzer seinen Browser schließt und erneut öffnet oder die Seite erneut lädt, geht der Benutzerzustand im Arbeitsspeicher des Browsers verloren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="90f0b-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (im Namespace <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName>) basiert auf ASP.NET Core-Datenschutz und wird nur für Blazor Server-Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="90f0b-113">Persistentes Speichern des Zustands über Browsersitzungen hinweg</span><span class="sxs-lookup"><span data-stu-id="90f0b-113">Persist state across browser sessions</span></span>

<span data-ttu-id="90f0b-114">Im Allgemeinen gilt: Die Beibehaltung des Zustands über mehrere Browsersitzungen hinweg erfolgt in Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="90f0b-115">Um den Zustand über Browsersitzungen hinweg beizubehalten, muss die App die Daten an einem anderen Speicherort als im Arbeitsspeicher des Browsers persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="90f0b-116">Die Zustandspersistenz erfolgt nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="90f0b-116">State persistence isn't automatic.</span></span> <span data-ttu-id="90f0b-117">Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="90f0b-118">Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von Benutzern mit großem Aufwand erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="90f0b-119">In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:</span><span class="sxs-lookup"><span data-stu-id="90f0b-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="90f0b-120">Webformulare mit mehreren Schritten: Es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Webformulars wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="90f0b-121">Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom Formular weg navigiert und später zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="90f0b-122">Warenkörbe: Kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="90f0b-123">Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="90f0b-124">Eine App kann nur den *App-Zustand* beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-124">An app can only persist *app state*.</span></span> <span data-ttu-id="90f0b-125">Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="90f0b-126">Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="90f0b-127">Um den Benutzeroberflächenzustand (z. B. von den erweiterten Knoten einer Strukturansicht) zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten des Benutzeroberflächenzustands als serialisierbaren App-Zustand modellieren zu können.</span><span class="sxs-lookup"><span data-stu-id="90f0b-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="90f0b-128">Speicherort des Zustands</span><span class="sxs-lookup"><span data-stu-id="90f0b-128">Where to persist state</span></span>

<span data-ttu-id="90f0b-129">Es gibt allgemeine Speicherorte für die Beibehaltung des Zustands:</span><span class="sxs-lookup"><span data-stu-id="90f0b-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="90f0b-130">Serverseitige Speicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="90f0b-131">URL</span><span class="sxs-lookup"><span data-stu-id="90f0b-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="90f0b-132">Browserspeicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="90f0b-133">Arbeitsspeicherinterner Zustandscontainer des Diensts</span><span class="sxs-lookup"><span data-stu-id="90f0b-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="90f0b-134">Serverseitige Speicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-134">Server-side storage</span></span></h2>

<span data-ttu-id="90f0b-135">Für permanente Datenspeicherung, die mehrere Benutzer und Geräte umfasst, kann die App unabhängigen serverseitigen Speicher verwenden, auf den über eine Web-API zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="90f0b-136">Folgende Optionen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="90f0b-136">Options include:</span></span>

* <span data-ttu-id="90f0b-137">Blob-Speicher</span><span class="sxs-lookup"><span data-stu-id="90f0b-137">Blob storage</span></span>
* <span data-ttu-id="90f0b-138">Schlüssel-Wert-Speicher</span><span class="sxs-lookup"><span data-stu-id="90f0b-138">Key-value storage</span></span>
* <span data-ttu-id="90f0b-139">Relationale Datenbank</span><span class="sxs-lookup"><span data-stu-id="90f0b-139">Relational database</span></span>
* <span data-ttu-id="90f0b-140">Table Storage</span><span class="sxs-lookup"><span data-stu-id="90f0b-140">Table storage</span></span>

<span data-ttu-id="90f0b-141">Nachdem die Daten gespeichert wurden, wird der Zustand des Benutzers beibehalten und ist in jeder neuen Browsersitzung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="90f0b-142">Da Blazor WebAssembly-Apps vollständig im Browser des Benutzers ausgeführt werden, benötigen sie zusätzliche Maßnahmen für den Zugriff auf sichere externe Systeme, z. B. auf Speicherdienste und Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="90f0b-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="90f0b-143">Blazor WebAssembly-Apps werden auf die gleiche Weise gesichert wie Single-Page-Anwendungen (SPAs).</span><span class="sxs-lookup"><span data-stu-id="90f0b-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="90f0b-144">In der Regel authentifiziert eine App einen Benutzer über [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) und interagiert dann über Web-API-Aufrufe einer serverseitigen App mit Speicherdiensten und Datenbanken.</span><span class="sxs-lookup"><span data-stu-id="90f0b-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="90f0b-145">Die serverseitige Anwendung vermittelt die Übertragung von Daten zwischen der Blazor WebAssembly-Anwendung und dem Speicherdienst oder der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="90f0b-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="90f0b-146">Die Blazor WebAssembly-App unterhält eine kurzlebige Verbindung mit der serverseitigen App, während die serverseitige App über eine permanente Verbindung mit dem Speicher verfügt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="90f0b-147">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="90f0b-148">Blazor *Sicherheit und Identity* -Artikel</span><span class="sxs-lookup"><span data-stu-id="90f0b-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="90f0b-149">Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie hier:</span><span class="sxs-lookup"><span data-stu-id="90f0b-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="90f0b-150">Azure-Datenbanken</span><span class="sxs-lookup"><span data-stu-id="90f0b-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="90f0b-151">Azure Storage-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="90f0b-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="90f0b-152">URL</span><span class="sxs-lookup"><span data-stu-id="90f0b-152">URL</span></span></h2>

<span data-ttu-id="90f0b-153">Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL.</span><span class="sxs-lookup"><span data-stu-id="90f0b-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="90f0b-154">Beispiele für in der URL modellierte Benutzerzustände:</span><span class="sxs-lookup"><span data-stu-id="90f0b-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="90f0b-155">Die ID einer angezeigten Entität.</span><span class="sxs-lookup"><span data-stu-id="90f0b-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="90f0b-156">Die aktuelle Seitenzahl in einem ausgelagerten Raster.</span><span class="sxs-lookup"><span data-stu-id="90f0b-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="90f0b-157">Der Inhalt der Adressleiste des Browsers wird beibehalten, wenn der Benutzer die Seite manuell erneut lädt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="90f0b-158">Weitere Informationen zum Definieren von URL-Mustern mit der [`@page`](xref:mvc/views/razor#page)-Direktive finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="90f0b-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="90f0b-159">Browserspeicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-159">Browser storage</span></span></h2>

<span data-ttu-id="90f0b-160">Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage)- und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)-Sammlungen des Browsers ein häufig verwendeter Speicherort:</span><span class="sxs-lookup"><span data-stu-id="90f0b-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="90f0b-161">`localStorage` bezieht sich auf das Fenster des Browsers.</span><span class="sxs-lookup"><span data-stu-id="90f0b-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="90f0b-162">Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="90f0b-163">Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben.</span><span class="sxs-lookup"><span data-stu-id="90f0b-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="90f0b-164">Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="90f0b-165">`sessionStorage` bezieht sich auf die Registerkarte des Browsers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="90f0b-166">Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="90f0b-167">Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="90f0b-168">`localStorage` und `sessionStorage` können in Blazor WebAssembly-Apps verwendet werden, jedoch nur durch Schreiben von benutzerdefiniertem Code oder Verwenden eines Drittanbieterpakets.</span><span class="sxs-lookup"><span data-stu-id="90f0b-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="90f0b-169">Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer.</span><span class="sxs-lookup"><span data-stu-id="90f0b-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="90f0b-170">`sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:</span><span class="sxs-lookup"><span data-stu-id="90f0b-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="90f0b-171">Fehler in Zustandsspeicher über mehrere Registerkarten hinweg</span><span class="sxs-lookup"><span data-stu-id="90f0b-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="90f0b-172">Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt</span><span class="sxs-lookup"><span data-stu-id="90f0b-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="90f0b-173">`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.</span><span class="sxs-lookup"><span data-stu-id="90f0b-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="90f0b-174">Benutzer können die Daten anzeigen oder bearbeiten, die in `localStorage` und `sessionStorage` gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="90f0b-175">Arbeitsspeicherinterner Zustandscontainer des Diensts</span><span class="sxs-lookup"><span data-stu-id="90f0b-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="90f0b-176">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="90f0b-176">Additional resources</span></span>

* [<span data-ttu-id="90f0b-177">Speichern des App-Zustands vor einem Authentifizierungsvorgang</span><span class="sxs-lookup"><span data-stu-id="90f0b-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="90f0b-178">Blazor Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="90f0b-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="90f0b-179">In den meisten Fällen unterhält die App eine Verbindung mit dem Server.</span><span class="sxs-lookup"><span data-stu-id="90f0b-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="90f0b-180">Der Benutzerzustand wird in einer *Verbindung* im Speicher des Servers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="90f0b-181">Beispiele für den in einer Verbindung gespeicherten Benutzerzustand sind:</span><span class="sxs-lookup"><span data-stu-id="90f0b-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="90f0b-182">Die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe in der gerenderten Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="90f0b-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="90f0b-183">Die Werte der Felder und Eigenschaften in Komponenteninstanzen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="90f0b-184">Daten, die in [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection)-Dienstinstanzen gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="90f0b-185">Der Benutzerzustand ist möglicherweise auch in JavaScript-Variablen im Arbeitsspeicher des Browsers enthalten, die über [JavaScript-Interop](xref:blazor/call-javascript-from-dotnet)-Aufrufe festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="90f0b-186">Wenn die Netzwerkverbindung vorübergehend getrennt wird, versucht Blazor, den Benutzer nochmals mit der ursprünglichen Verbindung mit seinem ursprünglichen Zustand zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="90f0b-187">Es ist jedoch nicht immer möglich, einen Benutzer noch mal mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers zu verbinden:</span><span class="sxs-lookup"><span data-stu-id="90f0b-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="90f0b-188">Der Server kann eine getrennte Verbindung nicht dauerhaft beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="90f0b-189">Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="90f0b-190">In Bereitstellungsumgebungen mit mehreren Servern und Lastenausgleich tritt bei einzelnen Servern möglicherweise ein Fehler auf, oder sie werden automatisch entfernt, wenn nicht mehr das gesamte Anforderungsvolumen verarbeitet werden muss.</span><span class="sxs-lookup"><span data-stu-id="90f0b-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="90f0b-191">Die ursprünglichen Serververarbeitungsanforderungen für einen Benutzer sind möglicherweise nicht mehr verfügbar, wenn der Benutzer versucht, erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="90f0b-192">Der Benutzer kann den Browser schließen und noch mal öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="90f0b-193">So gehen beispielsweise JavaScript-Variablenwerte, die durch JavaScript-Interop-Aufrufe festgelegt wurden, verloren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="90f0b-194">Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand.</span><span class="sxs-lookup"><span data-stu-id="90f0b-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="90f0b-195">Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.</span><span class="sxs-lookup"><span data-stu-id="90f0b-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="90f0b-196">Persistentes verbindungsübergreifendes Speichern des Zustands</span><span class="sxs-lookup"><span data-stu-id="90f0b-196">Persist state across circuits</span></span>

<span data-ttu-id="90f0b-197">Im Allgemeinen gilt: Die Beibehaltung des Zustands über Verbindungen hinweg erfolgt in Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="90f0b-198">Um den Zustand über Verbindungen hinweg beizubehalten, muss die App die Daten an einem anderen Speicherort als im Arbeitsspeicher des Servers persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="90f0b-199">Die Zustandspersistenz erfolgt nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="90f0b-199">State persistence isn't automatic.</span></span> <span data-ttu-id="90f0b-200">Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="90f0b-201">Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von Benutzern mit großem Aufwand erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="90f0b-202">In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:</span><span class="sxs-lookup"><span data-stu-id="90f0b-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="90f0b-203">Webformulare mit mehreren Schritten: Es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Webformulars wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="90f0b-204">Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom Formular weg navigiert und später zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="90f0b-205">Warenkörbe: Kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="90f0b-206">Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="90f0b-207">Eine App kann nur den *App-Zustand* beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-207">An app can only persist *app state*.</span></span> <span data-ttu-id="90f0b-208">Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="90f0b-209">Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="90f0b-210">Um den Benutzeroberflächenzustand (z. B. von den erweiterten Knoten einer Strukturansicht) zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten des Benutzeroberflächenzustands als serialisierbaren App-Zustand modellieren zu können.</span><span class="sxs-lookup"><span data-stu-id="90f0b-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="90f0b-211">Speicherort des Zustands</span><span class="sxs-lookup"><span data-stu-id="90f0b-211">Where to persist state</span></span>

<span data-ttu-id="90f0b-212">Es gibt allgemeine Speicherorte für die Beibehaltung des Zustands:</span><span class="sxs-lookup"><span data-stu-id="90f0b-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="90f0b-213">Serverseitige Speicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="90f0b-214">URL</span><span class="sxs-lookup"><span data-stu-id="90f0b-214">URL</span></span>](#url-server)
* [<span data-ttu-id="90f0b-215">Browserspeicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="90f0b-216">Arbeitsspeicherinterner Zustandscontainer des Diensts</span><span class="sxs-lookup"><span data-stu-id="90f0b-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="90f0b-217">Serverseitige Speicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-217">Server-side storage</span></span></h2>

<span data-ttu-id="90f0b-218">Für permanente Datenspeicherung, die mehrere Benutzer und Geräte umfasst, kann die App serverseitigen Speicher verwenden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="90f0b-219">Folgende Optionen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="90f0b-219">Options include:</span></span>

* <span data-ttu-id="90f0b-220">Blob-Speicher</span><span class="sxs-lookup"><span data-stu-id="90f0b-220">Blob storage</span></span>
* <span data-ttu-id="90f0b-221">Schlüssel-Wert-Speicher</span><span class="sxs-lookup"><span data-stu-id="90f0b-221">Key-value storage</span></span>
* <span data-ttu-id="90f0b-222">Relationale Datenbank</span><span class="sxs-lookup"><span data-stu-id="90f0b-222">Relational database</span></span>
* <span data-ttu-id="90f0b-223">Table Storage</span><span class="sxs-lookup"><span data-stu-id="90f0b-223">Table storage</span></span>

<span data-ttu-id="90f0b-224">Nachdem die Daten gespeichert wurden, wird der Zustand des Benutzers beibehalten und ist in jeder neuen Verbindung verfügbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="90f0b-225">Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie hier:</span><span class="sxs-lookup"><span data-stu-id="90f0b-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="90f0b-226">Azure-Datenbanken</span><span class="sxs-lookup"><span data-stu-id="90f0b-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="90f0b-227">Azure Storage-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="90f0b-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="90f0b-228">URL</span><span class="sxs-lookup"><span data-stu-id="90f0b-228">URL</span></span></h2>

<span data-ttu-id="90f0b-229">Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL.</span><span class="sxs-lookup"><span data-stu-id="90f0b-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="90f0b-230">Beispiele für in der URL modellierte Benutzerzustände:</span><span class="sxs-lookup"><span data-stu-id="90f0b-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="90f0b-231">Die ID einer angezeigten Entität.</span><span class="sxs-lookup"><span data-stu-id="90f0b-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="90f0b-232">Die aktuelle Seitenzahl in einem ausgelagerten Raster.</span><span class="sxs-lookup"><span data-stu-id="90f0b-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="90f0b-233">Der Inhalt der Adressleiste des Browsers wird in folgenden Fällen beibehalten:</span><span class="sxs-lookup"><span data-stu-id="90f0b-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="90f0b-234">Der Benutzer lädt die Seite nochmals manuell.</span><span class="sxs-lookup"><span data-stu-id="90f0b-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="90f0b-235">Wenn der Webserver nicht mehr verfügbar ist, ist der Benutzer gezwungen, die Seite nochmals zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="90f0b-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="90f0b-236">Weitere Informationen zum Definieren von URL-Mustern mit der [`@page`](xref:mvc/views/razor#page)-Direktive finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="90f0b-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="90f0b-237">Browserspeicherung</span><span class="sxs-lookup"><span data-stu-id="90f0b-237">Browser storage</span></span></h2>

<span data-ttu-id="90f0b-238">Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage)- und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)-Sammlungen des Browsers ein häufig verwendeter Speicherort:</span><span class="sxs-lookup"><span data-stu-id="90f0b-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="90f0b-239">`localStorage` bezieht sich auf das Fenster des Browsers.</span><span class="sxs-lookup"><span data-stu-id="90f0b-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="90f0b-240">Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="90f0b-241">Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben.</span><span class="sxs-lookup"><span data-stu-id="90f0b-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="90f0b-242">Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="90f0b-243">`sessionStorage` bezieht sich auf die Registerkarte des Browsers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="90f0b-244">Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="90f0b-245">Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="90f0b-246">Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer.</span><span class="sxs-lookup"><span data-stu-id="90f0b-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="90f0b-247">`sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:</span><span class="sxs-lookup"><span data-stu-id="90f0b-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="90f0b-248">Fehler in Zustandsspeicher über mehrere Registerkarten hinweg</span><span class="sxs-lookup"><span data-stu-id="90f0b-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="90f0b-249">Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt</span><span class="sxs-lookup"><span data-stu-id="90f0b-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="90f0b-250">`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.</span><span class="sxs-lookup"><span data-stu-id="90f0b-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="90f0b-251">Einschränkungen bei der Verwendung des Browserspeichers:</span><span class="sxs-lookup"><span data-stu-id="90f0b-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="90f0b-252">Ähnlich wie bei der Verwendung einer serverseitigen Datenbank erfolgt das Laden und Speichern von Daten asynchron.</span><span class="sxs-lookup"><span data-stu-id="90f0b-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="90f0b-253">Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während des Prerenderings nicht verfügbar, da die angeforderte Seite im Browser während der Prerenderingphase nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="90f0b-254">Es ist ohne Weiteres möglich, einige wenige Kilobyte Daten für Blazor Server-Apps zu speichern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="90f0b-255">Bei mehr Kilobyte müssen Beeinträchtigungen der Leistung berücksichtigt werden, da die Daten über das Netzwerk geladen und gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="90f0b-256">Benutzer können die Daten anzeigen oder manipulieren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="90f0b-257">Die [Datenschutzlösung von ASP.NET Core](xref:security/data-protection/introduction) reduziert die damit verbundenen Risiken.</span><span class="sxs-lookup"><span data-stu-id="90f0b-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="90f0b-258">Beispielsweise verwendet [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) ASP.NET Core-Datenschutz.</span><span class="sxs-lookup"><span data-stu-id="90f0b-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="90f0b-259">NuGet-Pakete von Drittanbietern stellen APIs für die Verwendung mit `localStorage` und `sessionStorage` bereit.</span><span class="sxs-lookup"><span data-stu-id="90f0b-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="90f0b-260">Erwägen Sie die Auswahl eines Pakets, das die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core transparent verwendet.</span><span class="sxs-lookup"><span data-stu-id="90f0b-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="90f0b-261">Die Datenschutzlösung verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko, dass gespeicherte Daten manipuliert werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="90f0b-262">Wenn JSON-serialisierte Daten als Nur-Text gespeichert werden, können Benutzer die Daten mithilfe von Browserentwicklertools anzeigen und auch die gespeicherten Daten ändern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="90f0b-263">Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="90f0b-264">Beispielsweise stellt das Lesen oder Ändern der gespeicherten Farbe eines Benutzeroberflächenelements für den Benutzer oder die Organisation kein ernsthaftes Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="90f0b-265">Vermeiden Sie, dass Benutzer *sensible Daten* überprüfen oder manipulieren können.</span><span class="sxs-lookup"><span data-stu-id="90f0b-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="90f0b-266">ASP.NET Core Protected Browser Storage</span><span class="sxs-lookup"><span data-stu-id="90f0b-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="90f0b-267">[ASP.NET Core Protected Browser Storage](xref:security/data-protection/introduction) nutzt ASP.NET Core-Datenschutz für [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="90f0b-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="90f0b-268">Protected Browser Storage basiert auf ASP.NET Core Datenschutz und wird nur für Blazor Server-Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="90f0b-269">Speichern und Laden von Daten in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="90f0b-269">Save and load data within a component</span></span>

<span data-ttu-id="90f0b-270">Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, die [`@inject`](xref:mvc/views/razor#inject)-Anweisung, um eine Instanz von einem der folgenden Komponenten einzufügen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="90f0b-271">Die Auswahl hängt davon ab, welchen Browserspeicherort Sie verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="90f0b-272">Im folgenden Beispiel wird `sessionStorage` verwendet:</span><span class="sxs-lookup"><span data-stu-id="90f0b-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="90f0b-273">Die `@using`-Anweisung kann in einer Datei `_Imports.razor` der App anstatt in der Komponente platziert werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="90f0b-274">Bei Verwendung der `_Imports.razor`-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="90f0b-275">Um den `currentCount`-Wert in der `Counter`-Komponente einer App basierend auf der Blazor Server-Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="90f0b-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="90f0b-276">Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario.</span><span class="sxs-lookup"><span data-stu-id="90f0b-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="90f0b-277">Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="90f0b-278">`ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch, um komplexe Zustandsobjekte zu speichern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="90f0b-279">Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="90f0b-280">Die Daten werden nicht als Nur-Text gespeichert, sondern mit der Datenschutzlösung von ASP.NET Core geschützt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="90f0b-281">Die verschlüsselten Daten können untersucht werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="90f0b-282">Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer neuen Verbindung befindet):</span><span class="sxs-lookup"><span data-stu-id="90f0b-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="90f0b-283">Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie ein Ergebnis ungleich `null` in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zu, nicht <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span><span class="sxs-lookup"><span data-stu-id="90f0b-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="90f0b-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird nur ein Mal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="90f0b-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="90f0b-286">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="90f0b-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="90f0b-287">Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="90f0b-288">Bei aktiviertem PreRendering wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="90f0b-289">Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="90f0b-290">Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="90f0b-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="90f0b-291">Handhabung des Ladezustands</span><span class="sxs-lookup"><span data-stu-id="90f0b-291">Handle the loading state</span></span>

<span data-ttu-id="90f0b-292">Da die Browserspeicherung asynchron über eine Netzwerkverbindung erfolgt, vergeht immer eine gewisse Zeit, bis die Daten geladen werden und für eine Komponente zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="90f0b-293">Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="90f0b-294">Ein Ansatz besteht darin, nachzuverfolgen, ob die Daten den Zustand `null` aufweisen. Dies bedeutet, dass die Daten noch geladen werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="90f0b-295">In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="90f0b-296">[Legen Sie fest, dass `currentCount` NULL-Werte aufweisen kann](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="90f0b-297">Anstatt bedingungslos die Anzahl und die Schaltfläche **`Increment`** anzuzeigen, zeigen Sie diese Elemente nur dann an, wenn die Daten geladen wurden, indem Sie <xref:System.Nullable%601.HasValue%2A> überprüfen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="90f0b-298">Handhabung von Prerendering</span><span class="sxs-lookup"><span data-stu-id="90f0b-298">Handle prerendering</span></span>

<span data-ttu-id="90f0b-299">Während des Prerenderings:</span><span class="sxs-lookup"><span data-stu-id="90f0b-299">During prerendering:</span></span>

* <span data-ttu-id="90f0b-300">Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="90f0b-301">Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="90f0b-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="90f0b-302">`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="90f0b-303">Wenn die Komponente versucht,mit dem Speicher zu interagieren, wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="90f0b-304">Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="90f0b-305">Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="90f0b-306">Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="90f0b-307">Öffnen Sie die Datei `Pages/_Host.cshtml`, und ändern Sie das `render-mode`-Attribut des [Komponententag-Hilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um PreRendering zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="90f0b-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="90f0b-308">Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="90f0b-309">Damit PreRendering beibehalten wird, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="90f0b-310">Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:</span><span class="sxs-lookup"><span data-stu-id="90f0b-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="90f0b-311">Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort</span><span class="sxs-lookup"><span data-stu-id="90f0b-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="90f0b-312">Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes.</span><span class="sxs-lookup"><span data-stu-id="90f0b-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="90f0b-313">Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen*, die die Zustandsanbieterlogik kapselt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="90f0b-314">Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="90f0b-315">Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten in `sessionStorage` persistent gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="90f0b-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="90f0b-316">Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="90f0b-317">Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="90f0b-318">Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den <xref:Microsoft.AspNetCore.Components.Routing.Router> in der `App`-Komponente (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="90f0b-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="90f0b-319">Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="90f0b-320">Die folgende `Counter`-Komponente implementiert das Muster:</span><span class="sxs-lookup"><span data-stu-id="90f0b-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="90f0b-321">Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="90f0b-322">Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="90f0b-323">Weitere Informationen finden Sie im Abschnitt [Verarbeiten von PreRendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="90f0b-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="90f0b-324">In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="90f0b-325">Um den Zustand über viele Komponenten hinweg zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-325">To consume state across many components.</span></span>
* <span data-ttu-id="90f0b-326">Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="90f0b-327">Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das globale persistente Speichern des Zustands zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="90f0b-328">Experimentelles NuGet-Paket für Protected Browser Storage</span><span class="sxs-lookup"><span data-stu-id="90f0b-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="90f0b-329">[ASP.NET Core Protected Browser Storage](xref:security/data-protection/introduction) nutzt ASP.NET Core-Datenschutz für [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span><span class="sxs-lookup"><span data-stu-id="90f0b-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="90f0b-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das nicht für den Einsatz in der Produktion geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="90f0b-331">Das Paket ist nur für die Verwendung in ASP.NET Core 3.1 Blazor Server-Apps verfügbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="90f0b-332">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="90f0b-332">Configuration</span></span>

1. <span data-ttu-id="90f0b-333">Fügen Sie [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="90f0b-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="90f0b-334">Fügen Sie in der Datei `Pages/_Host.cshtml` das folgende Skript innerhalb des schließenden Tags `</body>` hinzu:</span><span class="sxs-lookup"><span data-stu-id="90f0b-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="90f0b-335">Rufen Sie in `Startup.ConfigureServices` `AddProtectedBrowserStorage` auf, um der Dienstsammlung die Dienste `localStorage` und `sessionStorage` hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="90f0b-336">Speichern und Laden von Daten in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="90f0b-336">Save and load data within a component</span></span>

<span data-ttu-id="90f0b-337">Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, die [`@inject`](xref:mvc/views/razor#inject)-Anweisung, um eine Instanz von einem der folgenden Komponenten einzufügen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="90f0b-338">Die Auswahl hängt davon ab, welchen Browserspeicherort Sie verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="90f0b-339">Im folgenden Beispiel wird `sessionStorage` verwendet:</span><span class="sxs-lookup"><span data-stu-id="90f0b-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="90f0b-340">Die `@using`-Anweisung kann in einer `_Imports.razor`-Datei statt in der Komponente abgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="90f0b-341">Bei Verwendung der `_Imports.razor`-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="90f0b-342">Um den `currentCount`-Wert in der `Counter`-Komponente einer App basierend auf der Blazor Server-Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="90f0b-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="90f0b-343">Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario.</span><span class="sxs-lookup"><span data-stu-id="90f0b-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="90f0b-344">Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="90f0b-345">`ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch.</span><span class="sxs-lookup"><span data-stu-id="90f0b-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="90f0b-346">Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="90f0b-347">Die Daten werden nicht als Nur-Text gespeichert, sondern mit der Datenschutzlösung von ASP.NET Core geschützt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="90f0b-348">Die verschlüsselten Daten können untersucht werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="90f0b-349">Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer vollständig neuen Verbindung befindet):</span><span class="sxs-lookup"><span data-stu-id="90f0b-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="90f0b-350">Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie das Ergebnis in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> und nicht in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zu.</span><span class="sxs-lookup"><span data-stu-id="90f0b-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="90f0b-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird nur ein Mal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="90f0b-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="90f0b-353">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="90f0b-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="90f0b-354">Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="90f0b-355">Bei aktiviertem PreRendering wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="90f0b-356">Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="90f0b-357">Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="90f0b-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="90f0b-358">Handhabung des Ladezustands</span><span class="sxs-lookup"><span data-stu-id="90f0b-358">Handle the loading state</span></span>

<span data-ttu-id="90f0b-359">Da die Browserspeicherung asynchron über eine Netzwerkverbindung erfolgt, vergeht immer eine gewisse Zeit, bis die Daten geladen werden und für eine Komponente zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="90f0b-360">Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="90f0b-361">Ein Ansatz besteht darin, nachzuverfolgen, ob die Daten den Zustand `null` aufweisen. Dies bedeutet, dass die Daten noch geladen werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="90f0b-362">In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="90f0b-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="90f0b-363">[Legen Sie fest, dass `currentCount` NULL-Werte aufweisen kann](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="90f0b-364">Anstatt bedingungslos die Anzahl und die Schaltfläche **`Increment`** anzuzeigen, legen Sie fest, dass diese Elemente nur dann angezeigt werden, wenn die Daten geladen sind:</span><span class="sxs-lookup"><span data-stu-id="90f0b-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="90f0b-365">Handhabung von Prerendering</span><span class="sxs-lookup"><span data-stu-id="90f0b-365">Handle prerendering</span></span>

<span data-ttu-id="90f0b-366">Während des Prerenderings:</span><span class="sxs-lookup"><span data-stu-id="90f0b-366">During prerendering:</span></span>

* <span data-ttu-id="90f0b-367">Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="90f0b-368">Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="90f0b-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="90f0b-369">`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="90f0b-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="90f0b-370">Wenn die Komponente versucht,mit dem Speicher zu interagieren, wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="90f0b-371">Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="90f0b-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="90f0b-372">Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="90f0b-373">Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="90f0b-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="90f0b-374">Öffnen Sie die Datei `Pages/_Host.cshtml`, und ändern Sie das `render-mode`-Attribut des [Komponententag-Hilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um PreRendering zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="90f0b-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="90f0b-375">Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="90f0b-376">Damit PreRendering beibehalten wird, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="90f0b-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="90f0b-377">Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:</span><span class="sxs-lookup"><span data-stu-id="90f0b-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="90f0b-378">Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort</span><span class="sxs-lookup"><span data-stu-id="90f0b-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="90f0b-379">Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes.</span><span class="sxs-lookup"><span data-stu-id="90f0b-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="90f0b-380">Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen*, die die Zustandsanbieterlogik kapselt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="90f0b-381">Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="90f0b-382">Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten in `sessionStorage` persistent gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="90f0b-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="90f0b-383">Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="90f0b-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="90f0b-384">Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt.</span><span class="sxs-lookup"><span data-stu-id="90f0b-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="90f0b-385">Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den <xref:Microsoft.AspNetCore.Components.Routing.Router> in der `App`-Komponente (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="90f0b-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="90f0b-386">Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern.</span><span class="sxs-lookup"><span data-stu-id="90f0b-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="90f0b-387">Die folgende `Counter`-Komponente implementiert das Muster:</span><span class="sxs-lookup"><span data-stu-id="90f0b-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="90f0b-388">Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="90f0b-389">Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten.</span><span class="sxs-lookup"><span data-stu-id="90f0b-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="90f0b-390">Weitere Informationen finden Sie im Abschnitt [Verarbeiten von PreRendering](#handle-prerendering).</span><span class="sxs-lookup"><span data-stu-id="90f0b-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="90f0b-391">In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:</span><span class="sxs-lookup"><span data-stu-id="90f0b-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="90f0b-392">Um den Zustand über viele Komponenten hinweg zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="90f0b-392">To consume state across many components.</span></span>
* <span data-ttu-id="90f0b-393">Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="90f0b-394">Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das globale persistente Speichern des Zustands zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="90f0b-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="90f0b-395">Arbeitsspeicherinterner Zustandscontainer des Diensts</span><span class="sxs-lookup"><span data-stu-id="90f0b-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
