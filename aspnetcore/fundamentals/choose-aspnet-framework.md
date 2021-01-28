---
title: Wählen zwischen ASP.NET 4.x und ASP.NET Core
author: rick-anderson
description: Erklärt ASP.NET Core vs. ASP.NET 4.x und wie man sich für eines von beiden entscheidet.
ms.author: riande
ms.custom: mvc, seodec18
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 192784b4f2cb3b80511814de4f777c4a49fc594b
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710736"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a>Wählen zwischen ASP.NET 4.x und ASP.NET Core

ASP.NET Core ist eine Neugestaltung von ASP.NET 4.x. Dieser Artikel listet die Unterschiede auf.

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core ist ein plattformübergreifendes Open-Source-Framework zum Erstellen moderner, cloudbasierter Web-Apps unter Windows, macOS oder Linux.

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a>ASP.NET 4.x

ASP.NET 4.x ist ein ausgereiftes Framework, das sämtliche Dienste bietet, die zum Erstellen erstklassiger serverbasierter Web-Apps unter Windows für Unternehmen erforderlich sind.

## <a name="framework-selection"></a>Auswahl des Frameworks

Die folgende Tabelle vergleicht ASP.NET Core mit ASP.NET 4.x.

| ASP.NET Core | ASP.NET 4.x |
|---|---|
|Entwickeln für Windows, macOS oder Linux|Entwickeln für Windows|
|[Razor Pages](xref:razor-pages/index) werden für das Erstellen einer Webbenutzeroberfläche mit ASP.NET Core 2.x empfohlen. Weitere Informationen finden Sie unter [MVC](xref:mvc/overview), [Web-API](xref:tutorials/first-web-api) und [SignalR](xref:signalr/introduction).|Verwenden von [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web-API](/aspnet/web-api/), [Webhooks](/aspnet/webhooks/) oder [Web Pages](/aspnet/web-pages)|
|Mehrere Versionen pro Computer|Eine Version pro Computer|
|Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) oder [Visual Studio Code](https://code.visualstudio.com/) unter Verwendung von C# oder F#|Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/) unter Verwendung von C#, VB oder F#|
|Höhere Leistung als ASP.NET 4.x|Gute Leistung|
|[Verwenden der .NET Core Runtime](/dotnet/standard/choosing-core-framework-server)|Verwenden der .NET Framework-Laufzeit|

Weitere Informationen über Unterstützung für ASP.NET Core 2.x in .NET Framework finden Sie unter [ASP.NET Core targeting .NET Framework](xref:index#target-framework).

## <a name="aspnet-core-scenarios"></a>ASP.NET Core-Szenarien

* [Websites](xref:tutorials/first-mvc-app/start-mvc)
* [APIs](xref:tutorials/first-web-api)
* [Echtzeit](xref:signalr/introduction)
* [Bereitstellen einer ASP.NET Core-App in Azure](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a>ASP.NET 4.x-Szenarios

* [Websites](/aspnet/mvc)
* [APIs](/aspnet/web-api)
* [Echtzeit](/aspnet/signalr)
* [Erstellen einer ASP.NET 4.x-Web-App in Azure](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Einführung in ASP.NET](/aspnet/overview)
* [Einführung in ASP.NET Core](xref:index)
* <xref:host-and-deploy/azure-apps/index>
