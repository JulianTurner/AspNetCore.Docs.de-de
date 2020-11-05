---
title: ASP.NET Core :::no-loc(Blazor):::-Umgebungen
author: guardrex
description: Weitere Informationen zu Umgebungen und zum Festlegen der Umgebung einer :::no-loc(Blazor WebAssembly):::-App finden Sie unter :::no-loc(Blazor):::.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: 61d46e0bd83d8bd82bf7faaf9d8f2fecbacc2ffa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056035"
---
# <a name="aspnet-core-no-locblazor-environments"></a><span data-ttu-id="6e161-103">ASP.NET Core :::no-loc(Blazor):::-Umgebungen</span><span class="sxs-lookup"><span data-stu-id="6e161-103">ASP.NET Core :::no-loc(Blazor)::: environments</span></span>

> [!NOTE]
> <span data-ttu-id="6e161-104">Dieses Thema gilt für :::no-loc(Blazor WebAssembly):::.</span><span class="sxs-lookup"><span data-stu-id="6e161-104">This topic applies to :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="6e161-105">Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6e161-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6e161-106">Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6e161-106">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="6e161-107">Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="6e161-107">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="6e161-108">Eine gehostete :::no-loc(Blazor WebAssembly):::-App übernimmt die Umgebung vom Server mittels einer Middleware, die die Umgebung dem Browser mitteilt, indem sie den `blazor-environment`-Header hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="6e161-108">A hosted :::no-loc(Blazor WebAssembly)::: app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="6e161-109">Der Wert des Headers ist die Umgebung.</span><span class="sxs-lookup"><span data-stu-id="6e161-109">The value of the header is the environment.</span></span> <span data-ttu-id="6e161-110">Die gehostete :::no-loc(Blazor):::-App und die Server-App verwenden gemeinsam dieselbe Umgebung.</span><span class="sxs-lookup"><span data-stu-id="6e161-110">The hosted :::no-loc(Blazor)::: app and the server app share the same environment.</span></span> <span data-ttu-id="6e161-111">Weitere Informationen, einschließlich Informationen zum Konfigurieren der Umgebung, finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="6e161-111">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="6e161-112">Wenn eine eigenständige App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6e161-112">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="6e161-113">Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e161-113">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="6e161-114">Im folgenden Beispiel für IIS fügen Sie den benutzerdefinierten Header zu der veröffentlichten Datei `web.config` hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e161-114">In the following example for IIS, add the custom header to the published `web.config` file.</span></span> <span data-ttu-id="6e161-115">Die `web.config`-Datei befindet sich im Ordner `bin/Release/{TARGET FRAMEWORK}/publish`:</span><span class="sxs-lookup"><span data-stu-id="6e161-115">The `web.config` file is located in the `bin/Release/{TARGET FRAMEWORK}/publish` folder:</span></span>

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
> <span data-ttu-id="6e161-116">Informationen zum Verwenden einer benutzerdefinierten Datei `web.config` für IIS, die nicht überschrieben wird, wenn die App im Ordner `publish` veröffentlicht wird, finden Sie unter <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="6e161-116">To use a custom `web.config` file for IIS that isn't overwritten when the app is published to the `publish` folder, see <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="6e161-117">Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen:</span><span class="sxs-lookup"><span data-stu-id="6e161-117">Obtain the app's environment in a component by injecting <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> and reading the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment> property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="6e161-118">Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment>-Eigenschaft verfügbar, was es Entwicklern ermöglicht, umgebungsspezifische Logik in ihrem Code zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="6e161-118">During startup, the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> exposes the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> through the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="6e161-119">Die folgenden bequemen Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Entwicklung“, „Produktion“, „Staging“ sowie benutzerdefinierte Umgebungsnamen:</span><span class="sxs-lookup"><span data-stu-id="6e161-119">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* `IsEnvironment("{ENVIRONMENT NAME}")`

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

<span data-ttu-id="6e161-120">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="6e161-120">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> property can be used during startup when the <xref:Microsoft.AspNetCore.Components.NavigationManager> service isn't available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e161-121">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6e161-121">Additional resources</span></span>

* <xref:fundamentals/environments>
