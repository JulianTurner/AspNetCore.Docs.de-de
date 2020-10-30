---
title: Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Anforderungs Handler mithilfe von Abhängigkeitsinjektion in eine ASP.net Core-App einfügen.
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
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060260"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Abhängigkeitsinjektion in Anforderungs Handlern in ASP.net Core

<a name="security-authorization-di"></a>

[Autorisierungs Handler müssen](xref:security/authorization/policies#handler-registration) während der Konfiguration mithilfe von [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in der Dienst Auflistung registriert werden.

Angenommen, Sie hatten ein Repository von Regeln, die Sie innerhalb eines Autorisierungs Handlers auswerten wollten, und dieses Repository wurde in der Dienst Sammlung registriert. Die Autorisierung wird aufgelöst und in den Konstruktor eingefügt.

Beispielsweise zur Verwendung von ASP. Die Protokollierungs Infrastruktur von NET, eingefügt `ILoggerFactory` in den-Handler. Ein solcher Handler könnte wie der folgende Code aussehen:

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

Der vorangehende Handler kann bei jeder [Dienst Lebensdauer](/dotnet/core/extensions/dependency-injection#service-lifetimes)registriert werden. Der folgende Code verwendet `AddSingleton` , um den vorangehenden Handler zu registrieren:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Eine Instanz des-Handlers wird erstellt, wenn die APP gestartet wird, und di fügt die registrierte `ILoggerFactory` in den Konstruktor ein.

> [!NOTE]
> Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.
