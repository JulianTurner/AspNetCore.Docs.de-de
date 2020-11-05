---
title: Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie stark typisierte Middleware mit einer factorybezogenen Aktivierung und einem Drittanbietercontainer in ASP.NET Core verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057764"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden. Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar. Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).

Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird. Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.

> [!NOTE]
> Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken. Die Verwendung von Simple Injector ist nicht verpflichtend. Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen. Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.

In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen. Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.

Middleware, die von einer `IMiddlewareFactory`-Implementierung ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ) aktiviert wurde:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Eine Erweiterung ( *Middleware/MiddlewareExtensions.cs* ) wird für die Middleware erstellt:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` muss mehrere Tasks durchführen:

* Richten Sie den Simple Injector-Container ein.
* Registrieren Sie die Factory und die Middleware.
* Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden. Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar. Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).

Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird. Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.

> [!NOTE]
> Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken. Die Verwendung von Simple Injector ist nicht verpflichtend. Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen. Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.

In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen. Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.

Middleware, die von einer `IMiddlewareFactory`-Implementierung ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ) aktiviert wurde:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Eine Erweiterung ( *Middleware/MiddlewareExtensions.cs* ) wird für die Middleware erstellt:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices` muss mehrere Tasks durchführen:

* Richten Sie den Simple Injector-Container ein.
* Registrieren Sie die Factory und die Middleware.
* Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Factorybezogene Middlewareaktivierung](xref:fundamentals/middleware/extensibility)
* [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector)
* [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html)
