---
title: ASP.NET Core Blazor-Umgebungen
author: guardrex
description: Weitere Informationen zu Umgebungen und zum Festlegen der Umgebung einer Blazor WebAssembly-App finden Sie unter Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 9ba23753df1726ee4c8a9802e1a1260ef7cf6fa5
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506954"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="b6d72-103">ASP.NET Core Blazor-Umgebungen</span><span class="sxs-lookup"><span data-stu-id="b6d72-103">ASP.NET Core Blazor environments</span></span>

> [!NOTE]
> <span data-ttu-id="b6d72-104">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="b6d72-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="b6d72-105">Eine allgemeine Anleitung zur Konfiguration von ASP.NET Core-Apps, in der die Ansätze für Blazor Server-Apps beschrieben werden, finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b6d72-105">For general guidance on ASP.NET Core app configuration, which describes the approaches to use for Blazor Server apps, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b6d72-106">Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b6d72-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="b6d72-107">Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="b6d72-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="b6d72-108">Die clientseitige Blazor-App ( *`Client`* ) einer gehosteten Blazor WebAssembly-Lösung bestimmt die Umgebung aus der *`Server`* -App der Lösung über eine Middleware, die dem Browser die Umgebung mitteilt.</span><span class="sxs-lookup"><span data-stu-id="b6d72-108">The client-side Blazor app (*`Client`*) of a hosted Blazor WebAssembly solution determines the environment from the *`Server`* app of the solution via a middleware that communicates the environment to the browser.</span></span> <span data-ttu-id="b6d72-109">Die *`Server`* -App fügt einen Header namens `blazor-environment` hinzu, wobei die Umgebung den Wert des Headers darstellt.</span><span class="sxs-lookup"><span data-stu-id="b6d72-109">The *`Server`* app adds a header named `blazor-environment` with the environment as the value of the header.</span></span> <span data-ttu-id="b6d72-110">Der *`Client`* -App liest den Header.</span><span class="sxs-lookup"><span data-stu-id="b6d72-110">The *`Client`* app reads the header.</span></span> <span data-ttu-id="b6d72-111">Die *`Server`* -App der Lösung ist eine ASP.NET Core-App. Weitere Informationen zum Konfigurieren der Umgebung finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b6d72-111">The the *`Server`* app of the solution is an ASP.NET Core app, so more information on how to configure the environment is found in <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b6d72-112">Wenn eine eigenständige Blazor WebAssembly-App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="b6d72-112">For a standalone Blazor WebAssembly app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="b6d72-113">Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="b6d72-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="b6d72-114">Im folgenden Beispiel für IIS wird der benutzerdefinierte Header (`blazor-environment`) zu der veröffentlichten `web.config`-Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b6d72-114">In the following example for IIS, the custom header (`blazor-environment`) is added to the published `web.config` file.</span></span> <span data-ttu-id="b6d72-115">Die `web.config`-Datei befindet sich im Ordner `bin/Release/{TARGET FRAMEWORK}/publish`, wobei der Platzhalter `{TARGET FRAMEWORK}` für das Zielframework steht:</span><span class="sxs-lookup"><span data-stu-id="b6d72-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder, where the placeholder `{TARGET FRAMEWORK}` is the target framework:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="b6d72-116">Informationen zum Verwenden einer benutzerdefinierten Datei `web.config` für IIS, die nicht überschrieben wird, wenn die App im Ordner `publish` veröffentlicht wird, finden Sie unter <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="b6d72-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="b6d72-117">Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen.</span><span class="sxs-lookup"><span data-stu-id="b6d72-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property.</span></span>

<span data-ttu-id="b6d72-118">`Pages/ReadEnvironment.razor`:</span><span class="sxs-lookup"><span data-stu-id="b6d72-118">`Pages/ReadEnvironment.razor`:</span></span>

[!code-razor[](environments/samples_snapshot/ReadEnvironment.razor?highlight=3,7)]

<span data-ttu-id="b6d72-119">Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die Schnittstelle <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> -Eigenschaft verfügbar, die umgebungsspezifische Logik im Code des Host-Generators ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="b6d72-119">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables environment-specific logic in host builder code.</span></span>

<span data-ttu-id="b6d72-120">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6d72-120">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="b6d72-121">Die folgenden bequemen, von <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> bereitgestellten Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Development“, „Production“, „Staging“ sowie auf benutzerdefinierte Umgebungsnamen:</span><span class="sxs-lookup"><span data-stu-id="b6d72-121">The following convenience extension methods provided through <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

<span data-ttu-id="b6d72-122">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="b6d72-122">In `Program.Main` of `Program.cs`:</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="b6d72-123">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="b6d72-123">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b6d72-124">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b6d72-124">Additional resources</span></span>

* <xref:fundamentals/environments>
