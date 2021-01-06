---
title: Das Metapaket „Microsoft.AspNetCore.All“ für ASP.NET Core 2.0
author: Rick-Anderson
description: Das Metapaket „Microsoft.AspNetCore.All“ wird für ASP.NET Core 2.1 oder höher nicht empfohlen.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/25/2018
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
uid: fundamentals/metapackage
ms.openlocfilehash: b739398c2a440f21c8bdfdc1f4d8e25412358a6a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060689"
---
# <a name="microsoftaspnetcoreall-metapackage-for-aspnet-core-20"></a>Das Metapaket „Microsoft.AspNetCore.All“ für ASP.NET Core 2.0

::: moniker range=">= aspnetcore-3.0"

Das `Microsoft.AspNetCore.All`-Metapaket ist in ASP.NET Core 3.0 und höher nicht enthalten. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Announcements/issues/314).

::: moniker-end

> [!NOTE]
> Es wird empfohlen, für Anwendungen, die ASP.NET Core 2.1 und höher anzielen, das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) statt diesem Paket zu verwenden. Weitere Informationen finden Sie in diesem Artikel unter [Migrieren von Microsoft.AspNetCore.All zu Microsoft.AspNetCore.App](#migrate).

Für dieses Feature ist ASP.NET Core 2.x für .NET Core 2.x erforderlich.

[Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) ist ein Metapaket, das auf ein freigegebenes Framework verweist. Ein *freigegebenes Framework*  besteht aus einer Reihe von Assemblys (*DLL*-Dateien), die sich nicht in den Ordnern der App befinden. Das freigegebene Framework muss zum Ausführen der App auf dem Computer installiert sein. Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).

Das freigegebene Framework, auf das `Microsoft.AspNetCore.All` verweist, enthält:

* alle unterstützten Pakete des ASP.NET Core-Teams
* alle unterstützten Pakete von Entity Framework Core
* interne und Drittanbieterabhängigkeiten, die von ASP.NET Core und Entity Framework Core verwendet werden

In dem Paket `Microsoft.AspNetCore.All` sind alle Features von ASP.NET Core 2.x und Entity Framework Core 2.x enthalten. Die Standardprojektvorlagen für ASP.NET Core 2.0 verwenden dieses Paket.

Die Versionsnummer des `Microsoft.AspNetCore.All`-Metapakets gibt die erforderlichen Mindestversionen von ASP.NET Core und Entity Framework Core an.

Die folgende *.csproj*-Datei verweist auf das Metapaket `Microsoft.AspNetCore.All` für ASP.NET Core:

[!code-xml[](metapackage/samples/Metapackage.All.Example.csproj?highlight=8)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implicit-versioning"></a>Implizite Versionsverwaltung

In ASP.NET Core 2.1 oder höher können Sie den `Microsoft.AspNetCore.All`-Paketverweis ohne Version angeben. Wenn die Version nicht angegeben wird, wird vom SDK eine implizite Version angegeben (`Microsoft.NET.Sdk.Web`). Es wird empfohlen, die vom SDK angegebene implizite Version beizubehalten, statt die Versionsnummer im Paketverweis explizit festzulegen. Wenn Sie Fragen zu dieser Vorgehensweise haben, können Sie einen GitHub-Kommentar unter [Discussion for the Microsoft.AspNetCore.App implicit version (Diskussion zur impliziten Version für Microsoft.AspNetCore.App)](https://github.com/dotnet/AspNetCore.Docs/issues/6430) verfassen.

Die implizite Version wird auf `major.minor.0` festgelegt, wenn es sich um Apps für Mobilgeräte handelt. Der Rollforwardmechanismus des freigegebenen Frameworks führt die App auf der neuesten kompatiblen Version der installierten freigegebenen Frameworks aus. Stellen Sie sicher, dass die gleiche Version des freigegebenen Frameworks in allen Umgebungen installiert ist, um zu gewährleisten, dass die gleiche Version bei der Entwicklung, beim Testen und in der Produktion verwendet wird. Bei unabhängigen Apps wird die implizite Versionsnummer auf die Versionsnummer `major.minor.patch` des freigegebenen Frameworks festgelegt, das im installierten SDK zusammengefasst ist.

Das Angeben einer Versionsnummer im `Microsoft.AspNetCore.All`-Paketverweis garantiert **nicht**, dass diese Version des freigegebenen Frameworks ausgewählt wird. Gehen Sie beispielsweise davon aus, dass „2.1.1“ angegeben, aber „2.1.3“ installiert ist. In diesem Fall verwendet die App Version 2.1.3. Sie können den Rollforward (für „patch“ und/oder „minor“) deaktivieren. Dies wird jedoch nicht empfohlen. Weitere Informationen zum Rollforward des dotnet-Hosts und der Konfiguration seines Verhaltens finden Sie unter [dotnet host roll forward (Rollforward des dotnet-Hosts)](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).

Das SDK des Projekts muss in der Projektdatei auf `Microsoft.NET.Sdk.Web` festgelegt werden, damit die implizite Versionsverwaltung von `Microsoft.AspNetCore.All` verwendet werden kann. Wenn das SDK `Microsoft.NET.Sdk` festgelegt wird (`<Project Sdk="Microsoft.NET.Sdk">` ganz oben in der Projektdatei), wird die folgende Warnung angezeigt:

*Warnung NU1604: Projektabhängigkeit Microsoft.AspNetCore.App enthält keine inklusive Untergrenze. Schließen Sie eine Untergrenze in die Abhängigkeitsversion ein, um konsistente Wiederherstellungsergebnisse zu erzielen.)*

Dies ist ein bekanntes Problem mit dem .NET Core 2.1 SDK und wird im .NET Core SDK 2.2 behoben.

::: moniker-end

<a name="migrate"></a>

## <a name="migrating-from-microsoftaspnetcoreall-to-microsoftaspnetcoreapp"></a>Migrieren von Microsoft.AspNetCore.All zu Microsoft.AspNetCore.App

Folgende Pakete sind in `Microsoft.AspNetCore.All`, aber nicht in `Microsoft.AspNetCore.App` enthalten.

* `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServices.HostingStartup`
* `Microsoft.AspNetCore.AzureAppServicesIntegration`
* `Microsoft.AspNetCore.DataProtection.AzureKeyVault`
* `Microsoft.AspNetCore.DataProtection.AzureStorage`
* `Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`
* `Microsoft.AspNetCore.SignalR.Redis`
* `Microsoft.Data.Sqlite`
* `Microsoft.Data.Sqlite.Core`
* `Microsoft.EntityFrameworkCore.Sqlite`
* `Microsoft.EntityFrameworkCore.Sqlite.Core`
* `Microsoft.Extensions.Caching.Redis`
* `Microsoft.Extensions.Configuration.AzureKeyVault`
* `Microsoft.Extensions.Logging.AzureAppServices`
* `Microsoft.VisualStudio.Web.BrowserLink`

Wenn Sie von `Microsoft.AspNetCore.All` zu `Microsoft.AspNetCore.App` migrieren möchten und Ihre App APIs aus den oben aufgeführten Paketen verwendet, fügen Sie in Ihrem Projekt Verweise auf diese Pakete hinzu.

Alle Abhängigkeiten der vorangehenden Pakete, die keine Abhängigkeiten von `Microsoft.AspNetCore.App` sind, sind nicht implizit enthalten. Zum Beispiel:

* `StackExchange.Redis` als Abhängigkeit von `Microsoft.Extensions.Caching.Redis`
* `Microsoft.ApplicationInsights` als Abhängigkeit von `Microsoft.AspNetCore.ApplicationInsights.HostingStartup`

## <a name="update-aspnet-core-21"></a>Aktualisieren von ASP.NET Core 2.1

Wir empfehlen die Migration zum Metapaket `Microsoft.AspNetCore.App` für 2.1 oder höher. Wenn Sie das `Microsoft.AspNetCore.All`-Metapaket weiterhin verwenden und sicherstellen möchten, dass die neueste Patchversion bereitgestellt wird, gehen Sie folgendermaßen vor:

* Auf Entwicklungscomputern und Buildservern: Installieren Sie das neueste [.NET Core SDK](https://dotnet.microsoft.com/download).
* Auf Bereitstellungsservern: Installieren Sie die neueste [.NET Core Runtime](https://dotnet.microsoft.com/download).
 Für Ihre App wird bei einem Neustart der Anwendungen ein Rollforward auf die neueste installierte Version ausgeführt.
