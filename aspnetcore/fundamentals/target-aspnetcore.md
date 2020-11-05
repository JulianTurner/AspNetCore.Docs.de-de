---
title: Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek
author: scottaddie
description: Erfahren Sie, wie ASP.NET Core-APIs in einer Klassenbibliothek verwendet werden.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061508"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a>Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek

Von [Scott Addie](https://github.com/scottaddie)

Dieses Dokument enthält Anleitungen zum Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek. Weitere Informationen zur Bibliothek finden Sie unter [Open-Source-Bibliotheksleitfaden](/dotnet/standard/library-guidance/).

## <a name="determine-which-aspnet-core-versions-to-support"></a>Bestimmen der zu unterstützenden ASP.NET Core-Versionen

ASP.NET Core entspricht der [.NET Core-Unterstützungsrichtlinie](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Überprüfen Sie beim Bestimmen der in einer Bibliothek zu unterstützenden ASP.NET Core-Versionen die Supportrichtlinie. Eine Bibliothek sollte diese Merkmale aufweisen:

* Alle als *Long-Term Support* (LTS) klassifizierten ASP.NET Core-Versionen zu unterstützen versuchen.
* Sich nicht der Unterstützung von ASP.NET Core-Versionen verschreiben, die als *End of Life* (EOL) klassifiziert sind.

Wenn Vorschauversionen von ASP.NET Core verfügbar gemacht werden, werden nicht abwärtskompatible Änderungen im GitHub-Repository [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) veröffentlicht. Kompatibilitätstests von Bibliotheken können im Rahmen der Entwicklung von Frameworkfeatures durchgeführt werden.

## <a name="use-the-aspnet-core-shared-framework"></a>Verwenden des freigegebenen ASP.NET Core-Frameworks

Mit der Veröffentlichung von .NET Core 3.0 werden viele ASP.NET Core-Assemblys nicht mehr als Pakete in NuGet veröffentlicht. Stattdessen sind die Assemblys im freigegebenen Framework `Microsoft.AspNetCore.App` enthalten, das mit dem .NET Core SDK und Runtimeinstallationsprogrammen installiert wird. Eine Liste der Pakete, die nicht mehr veröffentlicht werden, finden Sie unter [Entfernen von Verweisen auf veraltete Pakete](xref:migration/22-to-30#remove-obsolete-package-references).

Seit .NET Core 3.0 verweisen Projekte, die das `Microsoft.NET.Sdk.Web` MSBuild SDK verwenden, implizit auf das freigegebene Framework. Projekte, die das `Microsoft.NET.Sdk` oder `Microsoft.NET.Sdk.Razor` SDK verwenden, müssen auf ASP.NET Core verweisen, um ASP.NET Core-APIs im freigegebenen Framework zu verwenden.

Um auf ASP.NET Core zu verweisen, fügen Sie Ihrer Projektdatei das folgende `<FrameworkReference>`-Element hinzu:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

Der Verweis auf ASP.NET Core in dieser Weise wird nur für Projekte mit der Zielplattform .NET Core 3.x unterstützt.

## <a name="include-no-locblazor-extensibility"></a>Einschließen von Blazor-Erweiterbarkeit

Blazor unterstützt die [Hostingmodelle](xref:blazor/hosting-models) WebAssembly (WASM) und Server. Wenn es keinen bestimmten Grund gibt, der dagegen spricht, sollte eine [Razor-Komponenten](xref:blazor/components/index)-Bibliothek beide Hostingmodelle unterstützen. Eine Razor-Komponentenbibliothek muss das [Microsoft.NET.Sdk.Razor-SDK ](xref:razor-pages/sdk) verwenden.

### <a name="support-both-hosting-models"></a>Unterstützen beider Hostingmodelle

Verwenden Sie zur Unterstützung der Nutzung von Razor-Komponenten sowohl in [Blazor Server](xref:blazor/hosting-models#blazor-server)- als auch in [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly)-Projekten die folgenden Anweisungen für Ihren Editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**. Das Kontrollkästchen **Seiten und Ansichten unterstützen** sollte deaktiviert sein.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie im integrierten Terminal den folgenden Befehl aus:

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**.

---

Das aus der Vorlage generierte Projekt weist diese Eigenschaften auf:

* Zielplattform .NET Standard 2.0.
* Legt die `RazorLangVersion`-Eigenschaft auf `3.0` fest. `3.0` ist der Standardwert für .NET Core 3.x.
* Fügt die folgenden Paketverweise hinzu:
  * [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [Microsoft.AspNetCore.Components.Web](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a>Unterstützen eines bestimmten Hostingmodells

Es ist weitaus weniger üblich, ein einzelnes Blazor-Hostingmodell zu unterstützen. So wird beispielsweise die Nutzung von Razor-Komponenten nur in [Blazor Server](xref:blazor/hosting-models#blazor-server)-Projekten unterstützt:

* Legen Sie .NET Core 3.x als Ziel fest.
* Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.

Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

Weitere Informationen zu Bibliotheken, die Razor-Komponenten enthalten, finden Sie unter [Klassenbibliotheken für ASP.NET Core-Razor-Komponenten](xref:blazor/components/class-libraries).

## <a name="include-mvc-extensibility"></a>Einschließen von MVC-Erweiterbarkeit

In diesem Abschnitt werden Empfehlungen für Bibliotheken erläutert, die Folgendes umfassen:

* [Razor-Ansichten oder Razor-Seiten](#razor-views-or-razor-pages)
* [Taghilfsprogramme](#tag-helpers)
* [Ansichtskomponenten](#view-components)

In diesem Abschnitt wird nicht die Unterstützung mehrerer Zielplattformen erörtert, um mehrere Versionen von MVC zu unterstützen. Anleitungen zum Unterstützen mehrerer ASP.NET Core-Versionen finden Sie unter [Unterstützung mehrerer ASP.NET Core-Versionen](#support-multiple-aspnet-core-versions).

### <a name="no-locrazor-views-or-no-locrazor-pages"></a>Razor-Ansichten oder Razor-Seiten

Ein Projekt, das [Razor-Ansichten](xref:mvc/views/overview) oder [Razor-Seiten](xref:razor-pages/index) enthält, muss das [Microsoft.NET.Sdk.Razor-SDK ](xref:razor-pages/sdk) verwenden.

Wenn für das Projekt .NET Core 3.x als Ziel festgelegt ist, wird Folgendes benötigt:

* Eine `AddRazorSupportForMvc`-MSBuild-Eigenschaft, die auf `true` festgelegt ist.
* Ein `<FrameworkReference>`-Element für das freigegebene Framework.

Die Projektvorlage **Razor-Klassenbibliothek** erfüllt die obigen Voraussetzungen für Projekte für .NET Core 3.x. Verwenden Sie die folgenden Anweisungen für Ihren Editor.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**. Das Kontrollkästchen **Seiten und Ansichten unterstützen** sollte aktiviert sein.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie im integrierten Terminal den folgenden Befehl aus:

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Zurzeit ist keine Unterstützung durch Projektvorlagen verfügbar.

---

Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

Wenn das Projekt stattdessen .NET Standard als Ziel hat, ist ein Paketverweis auf [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) erforderlich. Das `Microsoft.AspNetCore.Mvc`-Paket wurde in ASP.NET Core 3.0 in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht. Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a>Taghilfsprogramme

Ein Projekt, das [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) umfasst, sollte das `Microsoft.NET.Sdk` SDK verwenden. Fügen Sie für .NET Core 3.x als Ziel ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu. Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Bei .NET Standard als Ziel (um Versionen vor ASP.NET Core 3.x zu unterstützen), fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) hinzu. Das `Microsoft.AspNetCore.Mvc.Razor`-Paket wurde in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht. Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a>Ansichtskomponenten

Ein Projekt, das [Ansichtskomponenten](xref:mvc/views/view-components) umfasst, sollte das `Microsoft.NET.Sdk` SDK verwenden. Fügen Sie für .NET Core 3.x als Ziel ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu. Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

Bei .NET Standard als Ziel (um Versionen vor ASP.NET Core 3.x zu unterstützen), fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) hinzu. Das `Microsoft.AspNetCore.Mvc.ViewFeatures`-Paket wurde in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht. Zum Beispiel:

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a>Unterstützung mehrerer ASP.NET Core-Versionen

Multi-Targeting ist erforderlich, um eine Bibliothek zu erstellen, die mehrere Varianten von ASP.NET Core unterstützt. Stellen Sie sich ein Szenario vor, in dem eine Taghilfsprogramm-Bibliothek die folgenden ASP.NET Core-Varianten unterstützen muss:

* ASP.NET Core 2.1 mit dem Ziel .NET Framework 4.6.1
* ASP.NET Core 2.x mit dem Ziel .NET Core 2.x
* ASP.NET Core 3.x mit dem Ziel .NET Core 3.x

Die folgende Projektdatei unterstützt diese Varianten über die `TargetFrameworks`-Eigenschaft:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

Dies erfolgt mit der vorstehenden Projektdatei:

* Das `Markdig`-Paket wird für alle Consumer hinzugefügt.
* Ein Verweis auf [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) wird für Consumer mit dem Ziel .NET Framework 4.6.1 oder höher oder .NET Core 2.x hinzugefügt. Die Version 2.1.0 des Pakets funktioniert aufgrund der Abwärtskompatibilität mit ASP.NET Core 2.2.
* Auf das freigegebene Framework wird für Consumer mit dem Ziel .NET Core 3.x verwiesen. Das `Microsoft.AspNetCore.Mvc.Razor`-Paket ist im freigegebenen Framework enthalten.

Alternativ könnte .NET Standard 2.0 als Ziel verwendet werden, statt sowohl .NET Core 2.1 als auch .NET Framework 4.6.1 als Ziel festzulegen:

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

Für die vorstehende Projektdatei gelten die folgenden Vorbehalte:

* Da die Bibliothek nur Taghilfsprogramme enthält, ist es einfacher, auf die spezifischen Plattformen zu abzuzielen, auf denen ASP.NET Core ausgeführt wird: .NET Core und .NET Framework. Taghilfsprogramme können von anderen mit .NET Standard 2.0 kompatiblen Zielframeworks wie Unity, UWP und Xamarin nicht verwendet werden.
* Bei der Verwendung von .NET Standard 2.0 aus .NET Framework gibt es einige Probleme, die in .NET Framework 4.7.2 behoben wurden. Sie können die Erfahrung für Benutzer verbessern, die .NET Framework 4.6.1 über 4.7.1 verwenden, indem Sie .NET Framework 4.6.1 als Ziel festlegen.

Wenn Ihre Bibliothek plattformspezifische APIs aufrufen muss, legen Sie spezifische .NET-Implementierungen anstelle von .NET Standard als Ziel fest. Weitere Informationen finden Sie unter [Multi-Targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).

## <a name="use-an-api-that-hasnt-changed"></a>Verwenden einer API, die nicht geändert wurde

Stellen Sie sich ein Szenario vor, in dem Sie ein Upgrade einer Middleware-Bibliothek von .NET Core 2.2 auf 3.0 durchführen. Die Middleware-APIs von ASP.NET Core, die in der Bibliothek verwendet werden, haben sich zwischen ASP.NET Core 2.2 und 3.0 nicht geändert. Um die Middleware-Bibliothek in .NET Core 3.0 weiterhin zu unterstützen, führen Sie die folgenden Schritte aus:

* Befolgen Sie den [Standardbibliotheksleitfaden](/dotnet/standard/library-guidance/).
* Fügen Sie einen Paketverweis für das NuGet-Paket jeder API hinzu, wenn die entsprechende Assembly im freigegebenen Framework nicht vorhanden ist.

## <a name="use-an-api-that-changed"></a>Verwenden einer API, die geändert wurde

Stellen Sie sich ein Szenario vor, in dem Sie ein Upgrade einer Bibliothek von .NET Core 2.2 auf .NET Core 3.0 durchführen. Eine ASP.NET Core-API, die in der Bibliothek verwendet wird, weist einen [Breaking Change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0 auf. Überprüfen Sie, ob die Bibliothek so umgeschrieben werden kann, dass sie die nicht funktionale API in allen Versionen nicht verwendet.

Wenn Sie die Bibliothek umschreiben können, tun Sie dies, und legen Sie weiterhin ein früheres Zielframework mit Paketverweisen fest (z. B. .NET Standard 2.0 oder .NET Framework 4.6.1).

Wenn die Bibliothek nicht umgeschrieben werden kann, führen Sie die folgenden Schritte aus:

* Fügen Sie ein Ziel für .NET Core 3.0 hinzu.
* Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.
* Verwenden Sie die [#if-Präprozessordirektive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) mit dem entsprechenden Zielframeworksymbol, um Code bedingt zu kompilieren.

Beispielsweise sind synchrone Lese- und Schreibvorgänge in HTTP-Anforderungs- und-Antwortdatenströmen standardmäßig ab ASP.NET Core 3.0 deaktiviert. ASP.NET Core 2.2 unterstützt das synchrone Verhalten standardmäßig. Stellen Sie sich eine Middleware-Bibliothek vor, in der synchrone Lese- und Schreibvorgänge aktiviert werden sollen, wenn E/A auftritt. Die Bibliothek sollte den Code einschließen, um in der entsprechenden Präprozessordirektive synchrone Funktionen zu aktivieren. Zum Beispiel:

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a>Verwenden einer in 3.0 eingeführten API

Stellen Sie sich vor, dass Sie eine ASP.NET Core-API verwenden möchten, die in ASP.NET Core 3.0 eingeführt wurde. Stellen Sie sich die folgenden Fragen:

1. Erfordert die Bibliothek funktionell die neue API?
1. Kann dieses Feature von der Bibliothek auf andere Weise implementiert werden?

Wenn die Bibliothek funktionell die API erfordert und es keine Möglichkeit gibt, sie auf einer Unterebene zu implementieren:

* Legen Sie .NET Core 3.x als alleiniges Ziel fest.
* Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.

Falls dieses Feature von der Bibliothek auf andere Weise implementiert werden kann:

* Fügen Sie .NET Core 3.x als Zielframework hinzu.
* Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.
* Verwenden Sie die [#if-Präprozessordirektive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) mit dem entsprechenden Zielframeworksymbol, um Code bedingt zu kompilieren.

Das folgende Taghilfsprogramm verwendet beispielsweise die in ASP.NET Core 3.0 eingeführte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>-Schnittstelle. Consumer mit .NET Core 3.0 als Ziel führen den durch das `NETCOREAPP3_0`-Zielframeworksymbol definierten Codepfad aus. Der Konstruktorparametertyp des Taghilfsprogramms ändert sich für .NET Core 2.1- und .NET Framework 4.6.1-Consumer in <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>. Diese Änderung war erforderlich, weil in ASP.NET Core 3.0 `IHostingEnvironment` als veraltet markiert und `IWebHostEnvironment` als Ersatz empfohlen wurde.

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

Die folgende Projektdatei für mehrere Ziele unterstützt dieses Szenario eines Taghilfsprogramms:

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a>Verwenden einer aus dem freigegebenen Framework entfernten API

Wenn Sie eine ASP.NET Core-Assembly verwenden möchten, die aus dem freigegebenen Framework entfernt wurde, fügen Sie den entsprechenden Paketverweis hinzu. Eine Liste der Pakete, die in ASP.NET Core 3.0 aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Entfernen von Verweisen auf veraltete Pakete](xref:migration/22-to-30#remove-obsolete-package-references).

So fügen Sie z. B. den Web-API-Client hinzu:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [Unterstützung der .NET-Implementierung](/dotnet/standard/net-standard#net-implementation-support)
* [.NET-Unterstützungsrichtlinien](https://dotnet.microsoft.com/platform/support/policy)
