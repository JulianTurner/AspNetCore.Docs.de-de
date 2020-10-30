---
title: Kompatibilitätsversion für ASP.NET Core MVC
author: rick-anderson
description: Informationen zur Vorgehensweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: 9123bd70dfee1578912f682faf0520735fd55776
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051290"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Kompatibilitätsversion für ASP.NET Core MVC

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Die <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>-Methode ist für ASP.NET Core 3.0-Apps keine Option. Das bedeutet, dass der Aufruf von `SetCompatibilityVersion` mit einem beliebigen Wert von <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> keine Auswirkungen auf die Anwendung hat.

* Die nächste Nebenversion von ASP.NET Core stellt möglicherweise einen neuen `CompatibilityVersion`-Wert bereit.
* `CompatibilityVersion`-Werte von `Version_2_0` bis `Version_2_2` (jeweils einschließlich) sind als `[Obsolete(...)]` gekennzeichnet.
* Weitere Informationen finden Sie unter [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387) (Breaking Changes bei APIs für Fälschungsschutz, CORS, Diagnosen, MVC und Routing). Diese Liste enthält Breaking Changes für Kompatibilitätsoptionen.

Um zu erfahren, wie `SetCompatibilityVersion` mit ASP.NET Core 2.x-Apps funktioniert, wählen Sie die [ASP.NET Core 2.2-Version dieses Artikels](?view=aspnetcore-2.2) aus.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Mit der Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine ASP.NET Core 2.x-App Änderungen im Verhalten aktivieren oder deaktivieren, die in ASP.NET Core MVC 2.1 oder 2.2 eingeführt wurden und Fehler verursachen können. Diese potentiell Fehler verursachenden Änderungen im Verhalten betreffen generell das Verhalten des MVC-Subsystems und die Art, wie **Ihr Code** von der Runtime aufgerufen wird. Wenn Sie sich für die Änderungen entscheiden, erhalten Sie das aktuelle Verhalten und das langfristige Verhalten von ASP.NET Core.

Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Es wird empfohlen, Ihre App mit der aktuellen Version zu testen (`CompatibilityVersion.Latest`). Wir erwarten, dass bei den meisten Apps mit der aktuellen Version keine Fehler verursachenden Verhaltensänderungen auftreten werden.

Apps, die `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` aufrufen, sind vor potenziell Fehler verursachenden Änderungen im Verhalten geschützt, die in den ASP.NET Core MVC-Versionen 2.1 bzw. 2.2 eingeführt wurden. Dieser Schutz:

* Gilt nicht für alle Änderungen in 2.1 und höher. Das Ziel sind potentiell Fehler verursachende Änderungen im Verhalten der ASP.NET Core-Runtime im MVC-Subsystem.
* Der Schutz gilt nicht für ASP.NET Core 3.0.

Standardmäßig gilt für ASP.NET Core 2.1- und 2.2-Apps, die **nicht**`SetCompatibilityVersion` aufrufen, die Kompatibilität mit Version 2.0. Das bedeutet, `SetCompatibilityVersion` nicht aufzurufen entspricht dem Aufrufen von `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest, außer für die folgenden Verhaltensweisen:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

Bei Apps, bei denen Fehler verursachende Änderungen auftreten, können Sie die geeigneten Kompatibilitätsoptionen verwenden, um Folgendes zu erreichen:

* Sie können das aktuelle Release verwenden und spezifische Fehler verursachende Änderungen im Verhalten vermeiden.
* Sie bekommen die Gelegenheit, Ihre App zu aktualisieren, damit Sie mit den neuesten Änderungen funktioniert.

In der <xref:Microsoft.AspNetCore.Mvc.MvcOptions>-Dokumentation finden Sie eine gute Erklärung, was sich geändert hat und warum die Änderungen eine Verbesserung für die meisten Benutzer darstellen.

Alte Verhaltensweisen, die von Kompatibilitätsoptionen unterstützt werden, wurden aus ASP.NET Core 3.0 entfernt. Beinahe alle Benutzer werden von diesen positiven Änderungen profitieren. Diese Änderungen in 2.1 und 2.2 bieten Vorteile für die meisten Apps, bei anderen bleibt genügend Zeit für ein Update.
::: moniker-end