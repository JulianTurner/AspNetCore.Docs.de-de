---
title: Neuigkeiten in ASP.NET Core 2.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.0
ms.openlocfilehash: f9fad5a63c76a3b21341a12fd40baafcdf2c9dac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059727"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="ab2e5-103">Neuigkeiten in ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="ab2e5-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 2.0 hervorgehoben und Links zu relevanter Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="no-locrazor-pages"></a><span data-ttu-id="ab2e5-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ab2e5-105">Razor Pages</span></span>

<span data-ttu-id="ab2e5-106">Razor Pages ist ein neues Feature von ASP.NET Core MVC, mit dem codierungsseitige Szenarien einfacher und produktiver werden.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="ab2e5-107">Weitere Informationen finden Sie in der Einführung und im Tutorial:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="ab2e5-108">Einführung in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ab2e5-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="ab2e5-109">Erste Schritte mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ab2e5-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="ab2e5-110">ASP.NET Core-Metapaket</span><span class="sxs-lookup"><span data-stu-id="ab2e5-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="ab2e5-111">Ein neues ASP.NET Core-Metapaket umfasst alle Pakete, die von den ASP.NET Core- und Entity Framework Core-Teams erstellt und unterstützt werden, sowie deren interne Abhängigkeiten und Abhängigkeiten von Drittanbietern.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="ab2e5-112">Sie müssen nicht länger einzelne Microsoft ASP.NET Core-Funktionen nach Paket auswählen.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="ab2e5-113">Alle Funktionen sind im Paket [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) enthalten.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="ab2e5-114">Die Standardvorlagen verwenden dieses Paket.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-114">The default templates use this package.</span></span>

<span data-ttu-id="ab2e5-115">Weitere Informationen finden Sie unter [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0 (Microsoft.AspNetCore.All-Metapaket für ASP.NET Core 2.0)](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="ab2e5-116">Laufzeitspeicher</span><span class="sxs-lookup"><span data-stu-id="ab2e5-116">Runtime Store</span></span>

<span data-ttu-id="ab2e5-117">Anwendungen, die das `Microsoft.AspNetCore.All`-Metapaket verwenden, profitieren automatisch vom neuen .NET Core-Laufzeitspeicher.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="ab2e5-118">Der Speicher enthält alle Runtime-Objekte, die zum Ausführen von ASP.NET Core 2.0-Anwendungen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="ab2e5-119">Durch die Verwendung des `Microsoft.AspNetCore.All`-Metapakets werden keine Objekte von referenzierten ASP.NET Core NuGet-Paketen mit der Anwendung bereitgestellt, da sich diese bereits im Zielsystem befinden.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="ab2e5-120">Die Objekte im Laufzeitspeicher sind ebenso zur Verbesserung der Startzeit der Anwendung vorkompiliert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="ab2e5-121">Weitere Informationen finden Sie unter [Runtime store (Laufzeitspeicher)](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="ab2e5-122">.NET-Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-122">.NET Standard 2.0</span></span>

<span data-ttu-id="ab2e5-123">Die ASP.NET Core 2.0-Pakete sind auf .NET Standard 2.0 ausgelegt.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="ab2e5-124">Auf die Pakete kann von anderen .NET Standard 2.0-Bibliotheken verwiesen werden. Außerdem können sie unter Implementierungen von .NET ausgeführt werden, die mit .NET Standard 2.0 konform sind, darunter .NET Core 2.0 und .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="ab2e5-125">Das `Microsoft.AspNetCore.All`-Metapaket ist ausschließlich auf .NET Core 2.0 ausgelegt, da es dafür vorgesehen ist, mit dem .NET Core 2.0-Laufzeitspeicher verwendet zu werden.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="ab2e5-126">Aktualisierung der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="ab2e5-126">Configuration update</span></span>

<span data-ttu-id="ab2e5-127">Standardmäßig wird in ASP.NET Core 2.0 eine `IConfiguration`-Instanz zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="ab2e5-128">Die `IConfiguration`-Instanz im Dienstcontainer macht es Anwendungen leichter, Konfigurationswerte aus dem Container abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="ab2e5-129">Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="ab2e5-130">Aktualisierung der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="ab2e5-130">Logging update</span></span>

<span data-ttu-id="ab2e5-131">In ASP.NET Core 2.0 wird die Protokollierung standardmäßig in das System der Abhängigkeitsinjektion (dependency injection, DI) integriert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="ab2e5-132">Sie können Anbieter hinzufügen und Filter in der Datei *Program.cs* statt in der Datei *Startup.cs* konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="ab2e5-133">Die standardmäßige `ILoggerFactory` unterstützt die Filterung so, dass Sie einen flexiblen Ansatz für die anbieterübergreifende Filterung und die Filterung nach bestimmten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="ab2e5-134">Weitere Informationen finden Sie unter [Introduction to Logging (Einführung in die Protokollierung)](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="ab2e5-135">Aktualisierung der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ab2e5-135">Authentication update</span></span>

<span data-ttu-id="ab2e5-136">Ein neues Authentifizierungsmodell erleichtert das Konfigurieren der Authentifizierung für eine Anwendung mithilfe der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="ab2e5-137">Es sind neue Vorlagen für die Konfiguration der Authentifizierung für Web-Apps und Web-APIs mithilfe von [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="ab2e5-138">Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="no-locidentity-update"></a><span data-ttu-id="ab2e5-139">Update von Identity</span><span class="sxs-lookup"><span data-stu-id="ab2e5-139">Identity update</span></span>

<span data-ttu-id="ab2e5-140">Wir haben die Erstellung sicherer Web-APIs mithilfe von Identity in ASP.NET Core 2.0 vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="ab2e5-141">Sie erhalten Zugriffstoken für den Zugriff auf Ihre Web-APIs über die [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="ab2e5-142">Weitere Informationen zu Änderungen bei der Authentifizierung in 2.0 finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="ab2e5-143">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="ab2e5-144">Enable QR Code generation for authenticator apps in ASP.NET Core (Aktivieren der QR-Code-Generierung für Authentifikator-Apps in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="ab2e5-145">Migrieren von Authentifizierung und Identity zu ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="ab2e5-146">SPA-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="ab2e5-146">SPA templates</span></span>

<span data-ttu-id="ab2e5-147">Es sind Projektvorlagen der Einzelseitenanwendung (Single Page Application, SPA) für Angular, Aurelia, Knockout.js, React.js und React.js mit Redux verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="ab2e5-148">Die Angular-Vorlage wurde auf Angular 4 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="ab2e5-149">Die Vorlagen für Angular und React sind standardmäßig verfügbar. Weitere Informationen zum Abrufen der anderen Vorlagen finden Sie unter [Create a new SPA project (Erstellen eines neuen SPA-Projekts)](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="ab2e5-150">Unter <xref:client-side/spa-services> finden Sie Informationen zum Erstellen einer Single-Page-Webanwendung in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="ab2e5-151">Kestrel-Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="ab2e5-151">Kestrel improvements</span></span>

<span data-ttu-id="ab2e5-152">Der Kestrel-Webserver verfügt über neue Funktionen, die ihn geeigneter als mit dem Internet verbundenen Server machen.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="ab2e5-153">Zu der neuen Eigenschaft `Limits` der `KestrelServerOptions`-Klasse wurden eine Reihe von Optionen für die Servereinschränkungskonfiguration hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="ab2e5-154">Fügen Sie Grenzwerte für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-154">Add limits for the following:</span></span>

* <span data-ttu-id="ab2e5-155">Maximale Anzahl der Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="ab2e5-155">Maximum client connections</span></span>
* <span data-ttu-id="ab2e5-156">Maximale Größe des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ab2e5-156">Maximum request body size</span></span>
* <span data-ttu-id="ab2e5-157">Minimale Datenrate des Anforderungstexts</span><span class="sxs-lookup"><span data-stu-id="ab2e5-157">Minimum request body data rate</span></span>

<span data-ttu-id="ab2e5-158">Weitere Informationen finden Sie unter [Kestrel web server implementation in ASP.NET Core (Kestrel-Webserverimplementierung in ASP.NET Core)](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="ab2e5-159">Umbenennung von WebListener in HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ab2e5-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="ab2e5-160">Die Pakete `Microsoft.AspNetCore.Server.WebListener` und `Microsoft.Net.Http.Server` wurden in einem neuen Paket zusammengeführt: `Microsoft.AspNetCore.Server.HttpSys`.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="ab2e5-161">Die Namespaces wurden entsprechend aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="ab2e5-162">Weitere Informationen finden Sie unter [HTTP.sys web server implementation in ASP.NET Core (HTTP.sys-Webserverimplementierung in ASP.NET Core)](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="ab2e5-163">Verbesserte Unterstützung von HTTP-Headern</span><span class="sxs-lookup"><span data-stu-id="ab2e5-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="ab2e5-164">Wenn Sie MVC zum Übertragen von `FileStreamResult` oder `FileContentResult` verwenden, können Sie jetzt ein `ETag`- oder `LastModified`-Datum für den Inhalt festlegen, den Sie übertragen.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="ab2e5-165">Sie können diese Werte auf die zurückgegebenen Inhalte durch Code festlegen, ähnlich wie hier gezeigt:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="ab2e5-166">Die Datei, die an Ihre Besucher zurückgegeben wird, weist die entsprechenden HTTP-Header für die Werte `ETag` und `LastModified` auf.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="ab2e5-167">Wenn ein Besucher der Anwendung Inhalt mit einem Range-Anforderungsheader anfordert, erkennt ASP.NET Core diese Anforderung und verarbeitet den Header.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="ab2e5-168">Wenn der angeforderte Inhalt nur teilweise geliefert werden kann, überspringt ASP.NET Core diese Teile entsprechend und gibt nur den angeforderte Bytesatz zurück.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="ab2e5-169">Sie müssen keine bestimmten Handler in Ihren Methoden schreiben, um dieses Feature anzupassen oder zu verarbeiten, da dies automatisch für Sie erledigt wird.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="ab2e5-170">Hosting – Start und Application Insights</span><span class="sxs-lookup"><span data-stu-id="ab2e5-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="ab2e5-171">Hostingumgebungen können nun während des Starts der Anwendung zusätzliche Paketabhängigkeiten injizieren und Code ausführen. Die Anwendung muss nicht explizit eine Abhängigkeit übernehmen oder Methoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="ab2e5-172">Mit dieser Funktion kann bestimmten Umgebungen ermöglicht werden, Funktionen zu „starten“, die für diese Umgebung spezifisch sind, ohne dass die Anwendung vorher informiert sein muss.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="ab2e5-173">In ASP.NET Core 2.0 wird diese Funktion verwendet, um automatisch die Application Insights-Diagnose beim Debuggen in Visual Studio oder (nach der Anmeldung) bei der Ausführung in Azure App Services zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="ab2e5-174">Aus diesem Grund fügen die Projektvorlagen nicht mehr standardmäßig Application Insights-Pakete und Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="ab2e5-175">Weitere Informationen zum Status der geplanten Dokumentation finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="ab2e5-176">Automatische Verwendung von Fälschungssicherheitstoken</span><span class="sxs-lookup"><span data-stu-id="ab2e5-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="ab2e5-177">ASP.NET Core diente standardmäßig bei der HTML-Codierung von Inhalten immer als Unterstützung. Mit der neuen Version wird jedoch ein weiterer Schritt gemacht, um websiteübergreifende Anforderungsfälschungen (XSRF) zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="ab2e5-178">ASP.NET Core gibt nun standardmäßig Fälschungssicherheitstoken aus und validiert diese ohne zusätzliche Konfiguration auf POST-Aktionsformularen und -Seiten.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="ab2e5-179">Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-179">For more information, see <xref:security/anti-request-forgery>.</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="ab2e5-180">Automatisches Vorkompilieren</span><span class="sxs-lookup"><span data-stu-id="ab2e5-180">Automatic precompilation</span></span>

<span data-ttu-id="ab2e5-181">Die Razor-Ansichtenvorkompilierung ist standardmäßig während der Veröffentlichung aktiviert. Hierdurch wird die Größe der Ausgabe reduziert und die Startzeit der Anwendung verringert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="ab2e5-182">Weitere Informationen finden Sie unter [Kompilierung und Vorkompilierung einer Razor-Ansicht in ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="no-locrazor-support-for-c-71"></a><span data-ttu-id="ab2e5-183">Razor-Unterstützung für C# 7.1</span><span class="sxs-lookup"><span data-stu-id="ab2e5-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="ab2e5-184">Die Razor-Ansichts-Engine wurde aktualisiert, damit sie mit dem neuen Roslyn-Compiler funktioniert.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="ab2e5-185">Dies umfasst Unterstützung für C# 7.1-Funktionen wie Standardausdrücke, abgeleitete Tupelnamen und Mustervergleich mit Generics.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="ab2e5-186">Fügen Sie Ihrer Projektdatei die folgende Eigenschaft hinzu, um C# 7.1 in Ihrem Projekt zu verwenden, und laden Sie die Projektmappe dann erneut:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="ab2e5-187">Informationen zum Status der C# 7.1-Funktionen finden Sie im [Roslyn GitHub-Repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="ab2e5-188">Andere Aktualisierungen der Dokumentation für 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="ab2e5-189">Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="ab2e5-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="ab2e5-190">Schlüsselverwaltung</span><span class="sxs-lookup"><span data-stu-id="ab2e5-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="ab2e5-191">Configure Facebook authentication (Konfigurieren der Facebook-Authentifizierung)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="ab2e5-192">Configure Twitter authentication (Konfigurieren der Twitter-Authentifizierung)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="ab2e5-193">Configure Google authentication (Konfigurieren der Google-Authentifizierung)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="ab2e5-194">Configure Microsoft Account authentication (Konfigurieren der Microsoft-Kontoauthentifizierung)</span><span class="sxs-lookup"><span data-stu-id="ab2e5-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="ab2e5-195">Migrationsanleitung</span><span class="sxs-lookup"><span data-stu-id="ab2e5-195">Migration guidance</span></span>

<span data-ttu-id="ab2e5-196">Anleitungen zum Migrieren von ASP.NET Core 1.x-Anwendungen zu ASP.NET Core 2.0 finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="ab2e5-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="ab2e5-197">Migrieren von ASP.NET Core 1.x zu ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="ab2e5-198">Migrieren von Authentifizierung und Identity zu ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="ab2e5-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="ab2e5-199">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="ab2e5-199">Additional Information</span></span>

<span data-ttu-id="ab2e5-200">Die vollständige Liste aller Änderungen finden Sie unter [ASP.NET Core 2.0 Release Notes (ASP.NET Core 2.0 – Anmerkungen zu dieser Version)](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="ab2e5-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="ab2e5-201">Wenn Sie über den Fortschritt und die Pläne des ASP.NET Core-Entwicklungsteams auf dem Laufenden bleiben möchten, sehen Sie sich das wöchentliche [ASP.NET Community Standup](https://live.asp.net/) an.</span><span class="sxs-lookup"><span data-stu-id="ab2e5-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
