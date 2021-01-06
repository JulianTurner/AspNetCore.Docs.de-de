---
title: Konfigurieren des Linkers für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: 0c99056053356133e901d6cf468fec8034dfb845
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055827"
---
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a>Konfigurieren des Linkers für ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex)

Blazor WebAssembly führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu kürzen. Der Linker ist beim Buildvorgang in der Debugkonfiguration deaktiviert. Apps müssen in der Releasekonfiguration erstellt werden, damit der Linker aktiviert wird. Sie sollten die Releasekonfiguration beim Buildvorgang anwenden, wenn Sie Blazor WebAssembly-Apps erstellen. 

Das Verknüpfen einer App optimiert die Größe, kann jedoch auch negative Auswirkungen haben. Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Linker dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind. Zur Kürzung solcher Apps muss der Linker über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.

Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung häufig die Releasebuilds der App zu testen.

Das Verknüpfen von Blazor-Apps kann mithilfe der folgenden MSBuild-Funktionen konfiguriert werden:

* Globales Konfigurieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#control-linking-with-an-msbuild-property)
* Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)

## <a name="control-linking-with-an-msbuild-property"></a>Steuern der Verknüpfung mit einer MSBuild-Eigenschaft

Die Verknüpfung wird aktiviert, wenn eine App in der `Release`-Konfiguration erstellt wird. Sie können dieses Verhalten ändern, indem Sie die MSBuild-Eigenschaft `BlazorWebAssemblyEnableLinking` in der Projektdatei konfigurieren:

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Steuern der Verknüpfung mit einer Konfigurationsdatei.

Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Weitere Informationen finden Sie unter [Data Formats (GitHub-Repository zu mono/linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>Hinzufügen einer XML-Linkerkonfigurationsdatei zu einer Bibliothek

Fügen Sie eine XML-Linkerkonfigurationsdatei als eingebettete Ressource zur Bibliothek hinzu, um den Linker für eine spezifische Bibliothek zu konfigurieren. Die eingebettete Ressource muss denselben Namen wie die Assembly aufweisen.

Im folgenden Beispiel wird die Datei `LinkerConfig.xml` als eingebettete Ressource festgelegt, die denselben Namen wie die Assembly der Bibliothek aufweist:

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>Konfigurieren des Linkers für die Internationalisierung

Die Blazor-Linkerkonfiguration für Blazor WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas. Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.

Legen Sie die MSBuild-Eigenschaft `<BlazorWebAssemblyI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| Regionswert     | Mono-Regionsassembly    |
| ---------------- | ----------------------- |
| `all`            | Alle Assemblys sind enthalten |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none` (Standardwert) | Keine                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).

Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
