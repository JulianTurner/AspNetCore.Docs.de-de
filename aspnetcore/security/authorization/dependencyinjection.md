---
title: Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Anforderungs Handler mithilfe von Abhängigkeitsinjektion in eine ASP.net Core-App einfügen.
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060260"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="04bc8-103">Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="04bc8-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="04bc8-104">[Autorisierungs Handler müssen](xref:security/authorization/policies#handler-registration) während der Konfiguration mithilfe von [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in der Dienst Auflistung registriert werden.</span><span class="sxs-lookup"><span data-stu-id="04bc8-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="04bc8-105">Angenommen, Sie hatten ein Repository von Regeln, die Sie innerhalb eines Autorisierungs Handlers auswerten wollten, und dieses Repository wurde in der Dienst Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="04bc8-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="04bc8-106">Die Autorisierung wird aufgelöst und in den Konstruktor eingefügt.</span><span class="sxs-lookup"><span data-stu-id="04bc8-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="04bc8-107">Beispielsweise zur Verwendung von ASP. Die Protokollierungs Infrastruktur von NET, eingefügt `ILoggerFactory` in den-Handler.</span><span class="sxs-lookup"><span data-stu-id="04bc8-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="04bc8-108">Ein solcher Handler könnte wie der folgende Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="04bc8-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="04bc8-109">Der vorangehende Handler kann bei jeder [Dienst Lebensdauer](/dotnet/core/extensions/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="04bc8-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="04bc8-110">Der folgende Code verwendet `AddSingleton` , um den vorangehenden Handler zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="04bc8-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="04bc8-111">Eine Instanz des-Handlers wird erstellt, wenn die APP gestartet wird, und di fügt die registrierte `ILoggerFactory` in den Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="04bc8-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="04bc8-112">Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.</span><span class="sxs-lookup"><span data-stu-id="04bc8-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
