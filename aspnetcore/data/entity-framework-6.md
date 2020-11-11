---
title: ASP.NET Core und Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 und höher funktioniert mit ASP.Net Core 3.1 und höher.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059454"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core und Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

Von [Patrick Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>Verwenden von Entity Framework 6 mit ASP.NET Core

[Entity Framework Core](/ef/) sollte für neue Entwicklungsvorhaben verwendet werden. Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) verwendet [Entity Framework 6 (EF6)](/ef/ef6), das zum Migrieren von vorhandenen Apps zu ASP.NET Core verwendet werden kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Entity Framework – Code-Based Configuration (Codebasierte Konfiguration von Entity Framework)](/ef/ef6/fundamentals/configuring/code-based)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) und [Tom Dykstra](https://github.com/tdykstra)

Dieser Artikel veranschaulicht die Verwendung von Entity Framework 6 in einer ASP.NET Core-Anwendung.    

## <a name="overview"></a>Übersicht 

Damit Sie Entity Framework 6 verwenden können, muss Ihr Projekt mit .NET Framework kompiliert werden, da .NET Core von Entity Framework 6 nicht unterstützt wird. Wenn Sie plattformübergreifende Features benötigen, müssen Sie ein Upgrade auf [Entity Framework Core](/ef/) durchführen.  

Die empfohlene Methode zum Verwenden von Entity Framework 6 in einer ASP.NET Core-Anwendung besteht darin, den Kontext von Entity Framework 6 und die Modellklassen in einem Klassenbibliotheksprojekt einzufügen, das das .NET-Framework anzielt. Fügen Sie einen Verweis vom ASP.NET Core-Projekt zur Klassenbibliothek hinzu. Weitere Informationen finden Sie im Beispiel [Visual Studio-Projektmappe mit Entity Framework 6 und ASP.NET Core-Projekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).  

Sie können den Kontext von Entity Framework 6 nicht in ASP.NET Core-Projekte einfügen, da diese nicht alle Funktionalitäten unterstützen, die für Entity Framework 6-Befehle (wie *Enable-Migrations* ) erforderlich sind.    

Unabhängig vom Projekttyp, in dem Sie den Kontext von Entity Framework 6 platzieren, funktionieren nur Entity Framework 6-Befehlszeilentools mit diesem Kontext. `Scaffold-DbContext` ist beispielsweise nur in Entity Framework Core verfügbar. Wenn Sie bei einer Datenbank das Reverse Engineering (Zurückentwicklung) in ein Entity Framework 6-Modell durchführen müssen, finden Sie weitere Informationen unter <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Verweisen auf das vollständige Framework und auf Entity Framework 6 in einem ASP.NET Core-Projekt 

Das ASP.NET Core-Projekt muss .NET-Framework anzielen und auf das Entity Framework 6 verweisen. Die *CSPROJ* -Datei Ihres ASP.NET Core-Projekts ähnelt beispielsweise folgendem Beispiel (nur die relevanten Teile der Datei werden dargestellt).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

Verwenden Sie die Vorlage **ASP.NET Core-Webanwendung (.NET Framework)** , wenn Sie ein neues Projekt erstellen.    

## <a name="handle-connection-strings"></a>Verarbeiten von Verbindungszeichenfolgen    

Die Entity Framework 6-Befehlszeilentools, die Sie im Entity Framework 6-Klassenbibliotheksprojekt verwenden, erfordern einen Standardkonstruktor, um den Kontext zu instanziieren. Wenn Sie die Verbindungszeichenfolge angeben möchten, die im ASP.NET Core-Projekt verwendet werden soll, muss der Kontextkonstruktor über einen Parameter verfügen, der das Übergeben der Verbindungszeichenfolge ermöglicht. Hier finden Sie ein Beispiel dafür.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

Da in Ihrem Entity Framework 6-Kontext kein Konstruktor ohne Parameter vorhanden ist, muss Ihr Entity Framework 6-Projekt eine Implementierung von <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> bereitstellen. Das Entity Framework 6-Befehlszeilentool sucht und verwendet diese Implementierung, damit der Kontext instanziiert werden kann. Hier finden Sie ein Beispiel dafür.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

In diesem Beispielcode übergibt die `IDbContextFactory`-Implementierung eine hart codierte Verbindungszeichenfolge. Dabei handelt es sich um die Verbindungszeichenfolge, die von den Befehlszeilentools verwendet wird. Sie sollten eine Strategie implementieren, um sicherzustellen, dass die Klassenbibliothek die gleiche Verbindungszeichenfolge wie die aufrufende Anwendung verwendet. Sie könnten den Wert in beiden Projekten beispielsweise aus einer Umgebungsvariable abrufen.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Einrichten von Dependency Injection im ASP.NET Core-Projekt  

Richten Sie den Entity Framework 6-Kontext in der Datei *Startup.cs* des Core-Projekts für Dependency Injection (DI) in `ConfigureServices` ein. Die Lebensdauer der Entity Framework-Kontextobjekte sollte auf den Zeitraum vor der Anforderung begrenzt werden.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

Sie können dann mithilfe von DI eine Instanz des Kontext in Ihre Controller abrufen. Der Code ähnelt dem, den Sie für einen Entity Framework Core-Kontext verwenden würden:    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>Beispielanwendung   

Eine funktionierende Beispielanwendung finden Sie in der [Visual Studio-Beispielprojektmappe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/), die in diesem Artikel verwendet wird.  

Dieses Beispiel kann von Grund auf neu erstellt werden, indem Sie folgende Schritte in Visual Studio befolgen:    

* Erstellen Sie eine Projektmappe.    

* **Hinzufügen** > **Neues Projekt** > **Web** > **ASP.NET Core-Webanwendung**    
  * Wählen Sie im Dialogfeld zur Auswahl der Projektvorlage „API und .NET Framework“ in der Dropdown-Liste aus. 

* **Hinzufügen** > **Neues Projekt** > **Windows-Desktop** > **Klassenbibliothek (.NET Framework)**  

* Führen Sie in der **Paket-Manager-Konsole** für beide Projekte den Befehl `Install-Package Entityframework` aus.    

* Erstellen Sie Datenmodellklassen, eine Kontextklasse und eine Implementierung von `IDbContextFactory` im Klassenbibliotheksprojekt.    

* Führen Sie in der Paket-Manager-Konsole des Klassenbibliotheksprojekts die Befehle `Enable-Migrations` und `Add-Migration Initial` aus. Wenn Sie das ASP.NET Core-Projekt als Startprojekt festgelegt haben, fügen Sie `-StartupProjectName EF6` zu diesen Befehlen hinzu. 

* Fügen Sie im Core-Projekt einen Projektverweis zum Klassenbibliotheksprojekt hinzu.    

* Registrieren Sie im Core-Projekt in der Datei *Startup.cs* den Kontext für DI.    

* Fügen Sie im Core-Projekt in *appsettings.json* die Verbindungszeichenfolge hinzu.  

* Fügen Sie im Core-Projekt einen Controller und Ansichten hinzu, um zu überprüfen, dass Daten gelesen und geschrieben werden können. (Beachten Sie, dass der ASP.NET Core MVC-Gerüstbau nicht mit dem Entity Framework 6-Kontext funktioniert, auf den durch die Klassenbibliothek verwiesen wird.)

::: moniker-end
