---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060871"
---
# <a name="aspnet-core-no-locrazor-sdk"></a>ASP.NET Core Razor SDK

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Übersicht

Das [!INCLUDE[](~/includes/2.1-SDK.md)] enthält das MSBuild SDK `Microsoft.NET.Sdk.Razor` (Razor SDK). Das Razor SDK...

::: moniker range=">= aspnetcore-3.0"

* ist zum Erstellen, Packen und Veröffentlichen von Paketen erforderlich, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte oder [Blazor](xref:blazor/index)-Projekte enthalten.
* enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien (*CSHTML* oder *RAZOR*) ermöglichen.

Das Razor SDK enthält `Content`-Elemente mit `Include`-Attributen, die auf die `**\*.cshtml`- und `**\*.razor`-Globmuster festgelegt sind. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardisiert die Vorgänge zum Erstellen, Packen und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte Projekte enthalten.
* Enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien ermöglichen.

Das Razor SDK enthält ein `Content`-Element mit einem `Include`-Attribut, das auf das `**\*.cshtml`-Globmuster festgelegt ist. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a>Verwenden des Razor SDK

Die meisten Web-Apps müssen nicht explizit auf das Razor SDK verweisen.

::: moniker range=">= aspnetcore-3.0"

Damit Sie das Razor SDK zur Erstellung von Klassenbibliotheken mit Razor Views oder Razor Pages verwenden können, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (Razor Class Library, RCL) zu beginnen. Eine RCL, die zum Erstellen von Blazor-Dateien (*RAZOR*) verwendet wird, erfordert mindestens einen Verweis auf das Paket [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components). Eine RCL, die zum Erstellen von Razor Views oder Razor Pages (*CSHTML-Dateien*) verwendet wird, erfordert mindestens die Ausrichtung auf `netcoreapp3.0` oder höher und verfügt über ein `FrameworkReference`-Element für das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor Views oder Razor Pages enthalten:

* Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In der Regel wird ein Paketverweis auf `Microsoft.AspNetCore.Mvc` benötigt, um zusätzliche Abhängigkeiten zum Erstellen und Kompilieren von Razor Pages und Razor Views zu empfangen. Ihr Projekt sollte mindestens Paketverweise zu Folgendem hinzufügen:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Das `Microsoft.AspNetCore.Razor.Design`-Paket enthält die Aufgaben und Ziele der Razor-Kompilierung für das Projekt.

  Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten. Das folgende Markup zeigt eine Projektdatei, die das Razor SDK verwendet, um Razor-Dateien für eine Razor Pages-App in ASP.NET Core zu erstellen:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Die Pakete `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten. Der versionslose Paketverweis `Microsoft.AspNetCore.App` bietet jedoch ein Metapaket für die App, das nicht die neueste Version von `Microsoft.AspNetCore.Razor.Design` enthält. Projekte müssen auf eine konsistente Version von `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) verweisen, sodass die neuesten Buildzeitfehlerbehebungen für Razor enthalten sind. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

* `RazorCompileOnBuild`: Kompiliert die Razor-Assembly im Rahmen der Projekterstellung und gibt sie aus, wenn `true` festgelegt wird. Wird standardmäßig auf `true` festgelegt.
* `RazorCompileOnPublish`: Kompiliert die Razor-Assembly im Rahmen der Projektveröffentlichung und gibt sie aus, wenn `true` festgelegt wird. Wird standardmäßig auf `true` festgelegt.

Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren der Eingabe und Ausgabe an das Razor SDK verwendet.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Ab ASP.NET Core 3.0 werden MVC Views oder Razor Pages standardmäßig nicht mehr verarbeitet, wenn die MSBuild-Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` in der Projektdatei deaktiviert sind. Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die App auf die Laufzeitkompilierung angewiesen ist, um *CSHTML-Dateien* zu verarbeiten.

::: moniker-end

| Elemente | Beschreibung |
| ----- | ----------- |
| `RazorGenerate` | Item-Elemente (*CSHTML*-Dateien), die Eingaben für die Codegenerierung sind. |
| `RazorComponent` | Item-Elemente (*RAZOR*-Dateien), die Eingaben für die Codegenerierung für Razor-Komponenten sind. |
| `RazorCompile` | Item-Elemente (*CS*-Dateien), die Eingaben für Razor-Kompilierungsziele sind. Verwenden Sie dieses `ItemGroup`-Element, um zusätzliche Dateien für die Kompilierung in die Razor-Assembly anzugeben. |
| `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Programmieren von Generate-Attributen für die Razor-Assembly verwendet werden. Zum Beispiel:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Item-Elemente, die als eingebettete Ressourcen in die generierte Razor-Assembly eingefügt werden. |

::: moniker range=">= aspnetcore-3.0"

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. |
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Bei `true` werden *web.config*-, *JSON*- und *CSHTML*-Dateien als Inhalt in das Projekt einbezogen. Bei einem Verweis über `Microsoft.NET.Sdk.Web` sind auch Dateien unter *wwwroot* und Konfigurationsdateien enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Generiert eine *CS*-Datei, die von `RazorAssemblyAttribute` angegebene Attribute enthält, und schließt die Datei in der Kompilierungsausgabe ein, wenn `true` festgelegt ist. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Kopiert `RazorGenerate`-Elemente (*CSHTML*-Dateien) in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Razor-Dateien nicht für eine veröffentlichte App erforderlich, wenn sie an der Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung beteiligt sind. Wird standardmäßig auf `false` festgelegt. |
| `PreserveCompilationReferences` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Referenzassemblys nicht für eine veröffentlichte App erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung stattfindet. Legen Sie den Wert `true` fest, wenn Ihre veröffentlichte App eine Laufzeitkompilierung erfordert. Legen Sie z. B. den Wert auf `true` fest, wenn die App zur Laufzeit *CSHTML*-Dateien ändert oder eingebettete Ansichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Alle Razor-Inhaltselemente ( *.cshtml* L-Dateien) werden für die Aufnahme in das generierte NuGet-Paket markiert, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Wenn `true` festgelegt ist, fügt RazorGenerate-Elemente ( *.cshtml*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Das SDK generiert zusätzliche Attribute, die von MVC zur Laufzeit zur Durchführung der Anwendungsteilerkennung verwendet werden, wenn `true` festgelegt ist. |
| `DefaultWebContentItemExcludes` | Ein Globmuster für Item-Elemente, die in Projekten, die auf das Web oder das Razor SDK ausgerichtet sind, aus der `Content`-Elementgruppe ausgeschlossen werden sollen. |
| `ExcludeConfigFilesFromBuildOutput` | Bei `true` werden *CONFIG*- und *JSON*-Dateien nicht in das Buildausgabeverzeichnis kopiert. |
| `AddRazorSupportForMvc` | Bei `true` konfiguriert das Razor SDK die Unterstützung für die MVC-Konfiguration, die beim Erstellen von Anwendungen mit MVC Views oder Razor Pages erforderlich ist. Diese Eigenschaft wird implizit für Projekte mit .NET Core 3.0 oder höher festgelegt, die auf das Web-SDK ausgerichtet sind. |
| `RazorLangVersion` | Die Version der zu verwendenden Razor-Sprache. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. |
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Bei `true` werden *web.config*-, *JSON*- und *CSHTML*-Dateien als Inhalt in das Projekt einbezogen. Bei einem Verweis über `Microsoft.NET.Sdk.Web` sind auch Dateien unter *wwwroot* und Konfigurationsdateien enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Generiert eine *CS*-Datei, die von `RazorAssemblyAttribute` angegebene Attribute enthält, und schließt die Datei in der Kompilierungsausgabe ein, wenn `true` festgelegt ist. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Kopiert `RazorGenerate`-Elemente (*CSHTML*-Dateien) in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Razor-Dateien nicht für eine veröffentlichte App erforderlich, wenn sie an der Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung beteiligt sind. Wird standardmäßig auf `false` festgelegt. |
| `CopyRefAssembliesToPublishDirectory` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Referenzassemblys nicht für eine veröffentlichte App erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung stattfindet. Legen Sie den Wert `true` fest, wenn Ihre veröffentlichte App eine Laufzeitkompilierung erfordert. Legen Sie z. B. den Wert auf `true` fest, wenn die App zur Laufzeit *CSHTML*-Dateien ändert oder eingebettete Ansichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Alle Razor-Inhaltselemente ( *.cshtml* L-Dateien) werden für die Aufnahme in das generierte NuGet-Paket markiert, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Wenn `true` festgelegt ist, fügt RazorGenerate-Elemente ( *.cshtml*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Das SDK generiert zusätzliche Attribute, die von MVC zur Laufzeit zur Durchführung der Anwendungsteilerkennung verwendet werden, wenn `true` festgelegt ist. |
| `DefaultWebContentItemExcludes` | Ein Globmuster für Item-Elemente, die in Projekten, die auf das Web oder das Razor SDK ausgerichtet sind, aus der `Content`-Elementgruppe ausgeschlossen werden sollen. |
| `ExcludeConfigFilesFromBuildOutput` | Bei `true` werden *CONFIG*- und *JSON*-Dateien nicht in das Buildausgabeverzeichnis kopiert. |
| `AddRazorSupportForMvc` | Bei `true` konfiguriert das Razor SDK die Unterstützung für die MVC-Konfiguration, die beim Erstellen von Anwendungen mit MVC Views oder Razor Pages erforderlich ist. Diese Eigenschaft wird implizit für Projekte mit .NET Core 3.0 oder höher festgelegt, die auf das Web-SDK ausgerichtet sind. |
| `RazorLangVersion` | Die Version der zu verwendenden Razor-Sprache. |

::: moniker-end

Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Ziele

Das Razor SDK definiert zwei Hauptziele:

* `RazorGenerate`: Der Code generiert *CS-Dateien* aus `RazorGenerate`-Item-Elementen. Verwenden Sie die Eigenschaft `RazorGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorCompile`: Kompiliert generierte *CS-Dateien* in eine Razor-Assembly. Verwenden Sie `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorComponentGenerate`: Der Code generiert *CS-Dateien* für `RazorComponent`-Item-Elemente. Verwenden Sie die Eigenschaft `RazorComponentGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.

### <a name="runtime-compilation-of-no-locrazor-views"></a>Kompilierung von Razor-Ansichten zur Laufzeit

* Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind. Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden). Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.

* Stellen Sie bei einer Web-App sicher, dass Ihre App auf das `Microsoft.NET.Sdk.Web` SDK ausgerichtet ist.

## <a name="no-locrazor-language-version"></a>Razor-Sprachversion

Bei Abzielen auf das `Microsoft.NET.Sdk.Web` SDK wird die Razor-Sprachversion von der Zielframeworkversion der App abgeleitet. Für Projekte, die auf das `Microsoft.NET.Sdk.Razor` SDK abzielen, oder in dem seltenen Fall, dass die App eine andere Razor-Sprachversion als der abgeleitete Wert erfordert, kann eine Version durch Festlegen der `<RazorLangVersion>`-Eigenschaft in der Projektdatei der App konfiguriert werden:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Die Sprachversion von Razor ist eng mit der Version der Laufzeitumgebung integriert, für die sie erstellt wurde. Die Ausrichtung auf eine Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und führt wahrscheinlich zu Buildfehlern.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erweiterungen des CSPROJ-Formats für .NET Core](/dotnet/core/tools/csproj)
* [Gemeinsame MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items)
