---
title: Migrieren von ASP.NET Core 1.x zu 2.0
author: scottaddie
description: In diesem Artikel werden die Voraussetzungen und üblichen Schritte zum Migrieren eines ASP.NET Core 1.x-Projekts zu ASP.NET Core 2.0 behandelt.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: migration/1x-to-2x/index
ms.openlocfilehash: 6d67924d87cdbe72cb08c5305dfe45c5b22b31bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057114"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a>Migrieren von ASP.NET Core 1.x zu 2.0

Von [Scott Addie](https://github.com/scottaddie)

In diesem Artikel aktualisieren wir exemplarisch ein vorhandenes ASP.NET Core 1.x-Projekt auf ASP.NET Core 2.0. Durch Migrieren der Anwendung zu ASP.NET Core 2.0 kommen Sie in den Genuss [vieler neuer Funktionen und Leistungsverbesserungen](xref:aspnetcore-2.0).

Vorhandene ASP.NET Core 1.x-Anwendungen basieren auf versionsspezifischen Projektvorlagen. Doch das ASP.NET Core-Framework entwickelt sich weiter. Gleiches gilt für die darin enthaltenen Projektvorlagen und den Startercode. Zusätzlich zum Aktualisieren des ASP.NET Core-Frameworks müssen Sie den Code für Ihre Anwendung aktualisieren.

<a name="prerequisites"></a>

## <a name="prerequisites"></a>Voraussetzungen

Weitere Informationen finden Sie unter [Erste Schritte mit ASP.NET Core](xref:getting-started).

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a>Aktualisieren des Zielframeworkmonikers (Target Framework Moniker, TFM)

Auf .NET Core ausgelegte Projekte müssen den [TFM](/dotnet/standard/frameworks) einer Version größer gleich .NET Core 2.0 verwenden. Suchen Sie den Knoten `<TargetFramework>` in der *CSPROJ* -Datei, und ersetzen Sie dessen inneren Text durch `netcoreapp2.0`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

Auf .NET Framework ausgelegte Projekte müssen den TFM einer Version größer gleich .NET Framework 4.6.1 verwenden. Suchen Sie den Knoten `<TargetFramework>` in der *CSPROJ* -Datei, und ersetzen Sie dessen inneren Text durch `net461`:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> .NET Core 2.0 bietet eine viel größere Oberfläche als .NET Core 1.x. Wenn Sie mit .NET Framework entwickeln, nur weil APIs in .NET Core 1.x fehlen, wird das Entwickeln mit .NET Core 2.0 wahrscheinlich funktionieren.

Wenn die Projektdatei `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>` enthält, lesen Sie die Informationen zu [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a>Aktualisieren der .NET Core SDK-Version in „global.json“

Wenn Ihre Projektmappe von einer Datei [global.json](/dotnet/core/tools/global-json) abhängig ist, um eine bestimmte .NET Core SDK-Version als Ziel anzugeben, aktualisieren Sie deren `version`-Eigenschaft so, dass die auf Ihrem Computer installierte Version 2.0 verwendet wird:

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a>Aktualisieren von Paketverweisen

In der *CSPROJ* -Datei in einem Projekt der Version 1.x sind alle NuGet-Pakete aufgeführt, die vom Projekt verwendet werden.

In einem ASP.NET Core 2.0-Projekt für .NET Core 2.0 ersetzt ein einzelner Verweis des Typs [metapackage](xref:fundamentals/metapackage) in der *CSPROJ* -Datei die Paketsammlung:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

Alle Funktionen von ASP.NET Core 2.0 und Entity Framework Core 2.0 sind in „metapackage“ enthalten.

ASP.NET Core 2.0-Projekte für .NET Framework müssen weiterhin auf einzelne NuGet-Pakete verweisen. Aktualisieren Sie das `Version`-Attribut aller Knoten des Typs `<PackageReference />` auf 2.0.0.

Hier ist z.B. die Liste der `<PackageReference />`-Knoten in einem typischen ASP.NET Core 2.0-Projekt für .NET Framework:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a>Aktualisieren von .NET Core CLI-Tools

Aktualisieren Sie in der *CSPROJ* -Datei das `Version`-Attribut jedes `<DotNetCliToolReference />`-Knotens auf 2.0.0.

Hier ist z.B. die Liste der CLI-Tools in einem typischen ASP.NET Core 2.0-Projekt für .NET Core 2.0:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a>Umbenennen der „TargetFallback“-Eigenschaft des Pakets

Die *CSPROJ* -Datei eines 1.x-Projekts hat einen Knoten des Typs `PackageTargetFallback` und eine Variable verwendet:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

Benennen Sie den Knoten und die Variable in `AssetTargetFallback` um:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a>Aktualisieren der „Main“-Methode in „Program.cs“

In 1.x-Projekten sah die `Main`-Methode von *Program.cs* wie folgt aus:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

In 2.0-Projekten wurde die `Main`-Methode von *Program.cs* vereinfacht:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

Das Übernehmen dieses neuen 2.0-Musters wird dringend empfohlen und ist für Produktfunktionen wie [Entity Framework Core-Migrationen (EF)](xref:data/ef-mvc/migrations) erforderlich. Bei Ausführung von `Update-Database` im Paket-Manager-Konsolenfenster oder von `dotnet ef database update` an der Befehlszeile (für in ASP.NET Core 2.0 konvertierte Projekte) wird der folgende Fehler generiert:

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a>Hinzufügen von Konfigurationsanbietern

In 1.x-Projekten konnten Sie Konfigurationsanbieter einer App mit dem `Startup`-Konstruktor hinzufügen. Dazu mussten Sie eine Instanz von `ConfigurationBuilder` erstellen, die betreffenden Anbieter laden (Umgebungsvariablen, App-Einstellungen usw.) und einen Member von `IConfigurationRoot` initialisieren.

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

Im vorherigen Beispiel wurde der `Configuration`-Member mit Konfigurationseinstellen aus *appsettings.json* geladen sowie aus jeder anderen *appsettings.\<EnvironmentName\>.jso* n-Datei, die mit der Eigenschaft `IHostingEnvironment.EnvironmentName` übereinstimmt. Diese Dateien befinden sich am gleichen Speicherort wie *startup.cs*.

In 2.0-Projekten wird der Bausteinkonfigurationsknoten, der 1.x-Projekten eigen ist, im Hintergrund ausgeführt. Umgebungsvariablen und App-Einstellungen werden beispielsweise beim Start geladen. Der entsprechende Code *startup.cs* wird zur `IConfiguration`-Initialisierung mit der eingefügten Instanz reduziert:

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

Um die von `WebHostBuilder.CreateDefaultBuilder` hinzugefügten Standardanbieter zu entfernen, rufen Sie die `Clear`-Methode in der `IConfigurationBuilder.Sources`-Eigenschaft in `ConfigureAppConfiguration` auf. Um Anbieter wieder hinzuzufügen, verwenden Sie die `ConfigureAppConfiguration`-Methode in *program.cs* :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

Die von der `CreateDefaultBuilder`-Methode verwendete Konfiguration im vorherigen Codeausschnitt können Sie sich [hier](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152) ansehen.

Weitere Informationen finden Sie unter [Konfiguration in ASP.NET Core](xref:fundamentals/configuration/index).

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a>Verschieben des Initialisierungscodes der Datenbank

In 1.x-Projekten, die EF Core 1.x verwenden, bewirkt ein Befehl wie `dotnet ef migrations add` Folgendes:

1. Instanziiert eine `Startup`-Instanz
1. Ruft die `ConfigureServices`-Methode auf, um alle Dienste mit Abhängigkeitsinjektion (einschließlich `DbContext`-Typen zu registrieren
1. Führt die erforderlichen Aufgaben aus

In 2.0-Projekten, die EF Core 2.0 verwenden, wird `Program.BuildWebHost` aufgerufen, um die Anwendungsdienste abzurufen. Im Gegensatz zu 1.x hat dies den zusätzlichen Nebeneffekt, dass `Startup.Configure` aufgerufen wird. Wenn Ihre 1.x-App den Datenbankinitialisierungscode in der `Configure`-Methode aufgerufen hat, können unerwartete Probleme auftreten. Wenn die Datenbank beispielsweise noch nicht existiert, wird der Seedingcode vor der Befehlsausführung der EF Core-Migration ausgeführt. Dieses Problem führt dazu, dass ein `dotnet ef migrations list`-Befehl fehlschlägt, da die Datenbank noch nicht vorhanden ist.

Erwägen Sie den folgenden 1.x-Startcode der Initialisierung in der `Configure`-Methode von *Startup.cs* :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

Verschieben Sie in 2.0-Projekten den Aufruf `SeedData.Initialize` zur `Main`-Methode von *Program.cs* :

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

Ab 2.0 ist es keine gute Idee, etwas in `BuildWebHost` zu tun, außer den Webhost zu erstellen und zu konfigurieren. Alles, was in der Anwendung ausgeführt werden soll, sollte außerhalb von `BuildWebHost` &mdash; behandelt werden, in der Regel in der `Main`-Methode von *Program.cs*.

<a name="view-compilation"></a>

## <a name="review-no-locrazor-view-compilation-setting"></a>Überprüfen der Einstellungen für die Kompilierung der Razor-Ansicht

Eine schnellere Anwendungsstartzeit und kleinere veröffentlichte Pakete sind für Sie von höchster Wichtigkeit. Aus diesen Gründen ist [Razor-Ansichtskompilierung](xref:mvc/views/view-compilation) in ASP.NET Core 2.0 standardmäßig aktiviert.

Das Festlegen der `MvcRazorCompileOnPublish`-Eigenschaft auf „true“ ist nicht mehr erforderlich. Außer wenn Sie die Ansichtskompilierung deaktivieren, kann die Eigenschaft aus der *CSPROJ* -Datei entfernt werden.

Bei Entwicklung für .NET Framework müssen Sie weiter explizit auf das NuGet-Paket [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) in Ihrer *.csproj* -Datei verweisen:

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a>Arbeiten mit den Startfeatures von Application Insights

Die mühelose Einrichtung der Instrumentierung der Anwendungsleistung ist wichtig. Hierfür können Sie nun mit den neuen Startfeatures von [Application Insights](/azure/application-insights/app-insights-overview) in den Visual Studio 2017-Tools arbeiten.

Bei in Visual Studio 2017 erstellten ASP.NET Core 1.1-Projekten wurde Application Insights standardmäßig hinzugefügt. Wenn Sie das Application Insights SDK nicht direkt verwenden, gehen Sie außerhalb von *Program.cs* und *Startup.cs* folgendermaßen vor:

1. Wenn .NET Core Ihr Ziel ist, entfernen Sie den folgenden `<PackageReference />`-Knoten aus der *CSPROJ* -Datei:

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. Wenn .NET Core Ihr Ziel ist, entfernen Sie den Aufruf der Erweiterungsmethode `UseApplicationInsights` aus *Program.cs* :

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. Entfernen Sie aus *_Layout.cshtml* den clientseitigen API-Aufruf von Application Insights. Dieser umfasst die beiden folgenden Codezeilen:

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

Sie können das Application Insights SDK weiterhin direkt verwenden. [metapackage](xref:fundamentals/metapackage) der Version 2.0 enthält die neueste Version von Application Insights, weshalb ein Fehler zum Downgrade eines Pakets angezeigt wird, wenn Sie auf eine ältere Version verweisen.

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationno-locidentity-improvements"></a>Übernehmen der Authentifizierung bzw. Verbesserungen an Identity

ASP.NET Core 2.0 verfügt über ein neues Authentifizierungsmodell und bietet eine Reihe wichtiger Änderungen an ASP.NET Core Identity. Wenn Sie Ihr Projekt mit aktivierter Option „Einzelne Benutzerkonten“ erstellt oder Authentifizierungs- oder Identitätsfunktionen manuell hinzugefügt haben, finden Sie unter [Migrieren von Authentifizierungs- und Identitätseinstellungen nach ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x) weitere Informationen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Wichtige Änderungen in ASP.NET Core 2.0](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
