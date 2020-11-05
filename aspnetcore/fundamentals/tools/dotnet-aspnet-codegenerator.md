---
title: Befehl „dotnet aspnet-codegenerator“
author: rick-anderson
description: Der Befehl „dotnet aspnet-codegenerator“ erstellt das Gerüst für ASP.NET Core-Projekte.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 88d761b09833a14de5af9f9610753174867aa09a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059987"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator`: Führt die ASP.NET Core-Gerüstbauengine aus. `dotnet aspnet-codegenerator` wird nur für den Gerüstbau über die Befehlszeile benötigt, nicht zum Gerüstbau mit Visual Studio.

Dieser Artikel gilt für [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) und höher.

## <a name="installing-aspnet-codegenerator"></a>Installieren von „aspnet-codegenerator“

`dotnet-aspnet-codegenerator` ist ein [globales Tool](/dotnet/core/tools/global-tools), das installiert werden muss. Über den folgenden Befehl wird die neueste stabile Version des `dotnet-aspnet-codegenerator`-Tools installiert:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Mit dem folgenden Befehl wird `dotnet-aspnet-codegenerator` auf die neueste stabile Version aktualisiert, die in den installierten .NET Core SDKs verfügbar ist:

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>Übersicht

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>Beschreibung

Der globale Befehl `dotnet aspnet-codegenerator` führt den ASP.NET Code-Codegenerator und die Gerüstbauengine aus.

## <a name="arguments"></a>Argumente

`generator`

Der auszuführende Codegenerator. Folgende Generatoren sind verfügbar:

| Generator  | Vorgang                                                            |
| ---------- | -------------------------------------------------------------------- |
| area       | [Gerüstbau für einen Bereich](xref:mvc/controllers/areas)                      |
| Controller | [Gerüstbau für einen Controller](xref:tutorials/first-mvc-app/adding-model)  |
| Identität   | [Gerüstbau Identity](xref:security/authentication/scaffold-identity) |
| razorpage  | [Gerüstbau für Razor Pages](xref:tutorials/razor-pages/model)            |
| Sicht       | [Gerüstbau für eine Ansicht](xref:mvc/views/overview)                          |

## <a name="options"></a>Optionen

`-n|--nuget-package-dir`

Gibt das NuGet-Paketverzeichnis an.

`-c|--configuration {Debug|Release}`

Legt die Buildkonfiguration fest. Der Standardwert ist `Debug`.

`-tfm|--target-framework`

Das zu verwendende [Zielframework](/dotnet/standard/frameworks). Beispielsweise `net46`.

`-b|--build-base-path`

Der Basispfad für den Build.

`-h|--help`

Druckt eine kurze Hilfe für den Befehl.

`--no-build`

Erstellt das Projekt nicht vor der Ausführung. Zudem wird das Flag `--no-restore` implizit festgelegt.

`-p|--project <PATH>`

Gibt den Pfad der auszuführenden Projektdatei an (Ordnername oder vollständiger Pfad). Wenn nicht angegeben, wird standardmäßig das aktuelle Verzeichnis gewählt.

## <a name="generator-options"></a>Generatoroptionen

In den folgenden Abschnitte werden die verfügbaren Optionen für die unterstützten Generatoren vorgestellt:

* Bereich
* Controller
* Identity  
* Razorpage
* Ansicht

<a name="area"></a>

### <a name="area-options"></a>Bereichsoptionen

Dieses Tool ist für ASP.NET Core-Webprojekte mit Controllern und Ansichten vorgesehen. Es ist nicht für Razor Pages-Apps gedacht.

Verwendung: `dotnet aspnet-codegenerator area AreaNameToGenerate`

Der oben gezeigte Befehl generiert die folgenden Ordner:

* *Bereiche*
  * *AreaNameToGenerate*
    * *Controller*
    * *Data*
    * *Models*
    * *Ansichten*

<a name="ctl"></a>

### <a name="controller-options"></a>Controlleroptionen

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `controller` und `razorpage`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator controller`:

| Option                         | Beschreibung                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| --controllerName oder -name      | Der Name des Controllers.                                                                                   |
| --useAsyncActions oder -async    | Generiert asynchrone Controlleraktionen.                                                                        |
| --noViews oder -nv               | Generiert **keine** Ansichten.                                                                                    |
| --restWithNoViews oder -api      | Generiert einen Controller mit einer API im REST-Stil. `noViews` wird vorausgesetzt, und ansichtsbezogene Optionen werden ignoriert. |
| --readWriteActions oder -actions | Generiert einen Controller mit Lese-/Schreibaktionen ohne Modell.                                              |

Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator controller`-Befehl zu erhalten:

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

Unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/first-mvc-app/adding-model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator controller`.

### <a name="no-locrazorpage"></a>Razorpage

<a name="rp"></a>

Für Razor Pages können jeweils einzelne Gerüste erstellt werden, indem der Name der neuen Seite und der zu verwendenden Vorlage angegeben wird. Folgende Vorlagen werden unterstützt:

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Beispielsweise verwendet der folgende Befehl die Bearbeitungsvorlage, um *MyEdit.cshtml* und *MyEdit.cshtml.cs* zu generieren:

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

Typischerweise werden die Vorlage und der Name der generierten Datei nicht angegeben, und es werden die folgenden Vorlagen erstellt:

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator` `razorpage` und `controller`:

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

Die folgende Tabelle zeigt die Optionen für `aspnet-codegenerator razorpage`:

| Option                        | Beschreibung                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| --namespaceName oder -namespace | Der Name des Namespace, der für das generierte PageModel verwendet wird.                          |
| --partialView oder -partial     | Generiert eine Teilansicht. Die Layoutoptionen -l und -udl werden ignoriert, wenn diese Option angegeben wird. |
| --noPageModel oder -npm         | Schalter, um keine PageModel-Klasse für eine leere Vorlage zu generieren.                           |

Verwenden Sie den Schalter `-h`, um Hilfe zum `aspnet-codegenerator razorpage`-Befehl zu erhalten:

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

Unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model) finden Sie ein Beispiel für `dotnet aspnet-codegenerator razorpage`.

### Identity

Siehe [Gerüst Identity](xref:security/authentication/scaffold-identity)
