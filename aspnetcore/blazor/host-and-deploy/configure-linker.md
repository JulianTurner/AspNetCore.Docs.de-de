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
# <a name="configure-the-linker-for-aspnet-core-no-locblazor"></a><span data-ttu-id="88146-103">Konfigurieren des Linkers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="88146-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="88146-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="88146-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="88146-105">Blazor WebAssembly führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="88146-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="88146-106">Der Linker ist beim Buildvorgang in der Debugkonfiguration deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="88146-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="88146-107">Apps müssen in der Releasekonfiguration erstellt werden, damit der Linker aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="88146-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="88146-108">Sie sollten die Releasekonfiguration beim Buildvorgang anwenden, wenn Sie Blazor WebAssembly-Apps erstellen.</span><span class="sxs-lookup"><span data-stu-id="88146-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="88146-109">Das Verknüpfen einer App optimiert die Größe, kann jedoch auch negative Auswirkungen haben.</span><span class="sxs-lookup"><span data-stu-id="88146-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="88146-110">Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Linker dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="88146-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="88146-111">Zur Kürzung solcher Apps muss der Linker über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="88146-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="88146-112">Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung häufig die Releasebuilds der App zu testen.</span><span class="sxs-lookup"><span data-stu-id="88146-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="88146-113">Das Verknüpfen von Blazor-Apps kann mithilfe der folgenden MSBuild-Funktionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="88146-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="88146-114">Globales Konfigurieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#control-linking-with-an-msbuild-property)</span><span class="sxs-lookup"><span data-stu-id="88146-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="88146-115">Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="88146-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="88146-116">Steuern der Verknüpfung mit einer MSBuild-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="88146-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="88146-117">Die Verknüpfung wird aktiviert, wenn eine App in der `Release`-Konfiguration erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="88146-117">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="88146-118">Sie können dieses Verhalten ändern, indem Sie die MSBuild-Eigenschaft `BlazorWebAssemblyEnableLinking` in der Projektdatei konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="88146-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="88146-119">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="88146-119">Control linking with a configuration file</span></span>

<span data-ttu-id="88146-120">Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:</span><span class="sxs-lookup"><span data-stu-id="88146-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="88146-121">`LinkerConfig.xml`:</span><span class="sxs-lookup"><span data-stu-id="88146-121">`LinkerConfig.xml`:</span></span>

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

<span data-ttu-id="88146-122">Weitere Informationen finden Sie unter [Data Formats (GitHub-Repository zu mono/linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="88146-122">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="88146-123">Hinzufügen einer XML-Linkerkonfigurationsdatei zu einer Bibliothek</span><span class="sxs-lookup"><span data-stu-id="88146-123">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="88146-124">Fügen Sie eine XML-Linkerkonfigurationsdatei als eingebettete Ressource zur Bibliothek hinzu, um den Linker für eine spezifische Bibliothek zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="88146-124">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="88146-125">Die eingebettete Ressource muss denselben Namen wie die Assembly aufweisen.</span><span class="sxs-lookup"><span data-stu-id="88146-125">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="88146-126">Im folgenden Beispiel wird die Datei `LinkerConfig.xml` als eingebettete Ressource festgelegt, die denselben Namen wie die Assembly der Bibliothek aufweist:</span><span class="sxs-lookup"><span data-stu-id="88146-126">In the following example, the `LinkerConfig.xml` file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="88146-127">Konfigurieren des Linkers für die Internationalisierung</span><span class="sxs-lookup"><span data-stu-id="88146-127">Configure the linker for internationalization</span></span>

<span data-ttu-id="88146-128">Die Blazor-Linkerkonfiguration für Blazor WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="88146-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="88146-129">Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.</span><span class="sxs-lookup"><span data-stu-id="88146-129">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="88146-130">Legen Sie die MSBuild-Eigenschaft `<BlazorWebAssemblyI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="88146-130">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="88146-131">Regionswert</span><span class="sxs-lookup"><span data-stu-id="88146-131">Region Value</span></span>     | <span data-ttu-id="88146-132">Mono-Regionsassembly</span><span class="sxs-lookup"><span data-stu-id="88146-132">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="88146-133">Alle Assemblys sind enthalten</span><span class="sxs-lookup"><span data-stu-id="88146-133">All assemblies included</span></span> |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| <span data-ttu-id="88146-134">`none` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="88146-134">`none` (default)</span></span> | <span data-ttu-id="88146-135">Keine</span><span class="sxs-lookup"><span data-stu-id="88146-135">None</span></span>                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

<span data-ttu-id="88146-136">Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="88146-136">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="88146-137">Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="88146-137">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88146-138">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="88146-138">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
