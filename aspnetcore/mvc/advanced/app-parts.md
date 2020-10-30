---
title: Freigeben von Controllern, Ansichten, Razor Seiten und mehr mit Anwendungs Teilen in ASP.net Core
author: rick-anderson
description: Freigeben von Controllern, anzeigen, Razor Seiten und mehr mit Anwendungs Teilen in ASP.net Core
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061183"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>Freigeben von Controllern, Ansichten, Razor Seiten und mehr mit Anwendungs teilen

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App. Anwendungsteile ermöglichen ASP.net Core das Ermitteln von Controllern, Ansichts Komponenten, taghilfsprogramme, Razor Seiten, Razor-Kompilierungs Quellen und vieles mehr. <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> ist ein Anwendungspart. `AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.

[Featureanbieter](#fp) arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen. Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet). Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen. Mithilfe von Anwendungs teilen können Sie eine Assembly (dll) mit Controllern, Ansichten, Razor Seiten, Razor-Kompilierungs Quellen, taghilfsprogrammen und mehr für mehrere apps freigeben. Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.

ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>. Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.

## <a name="load-aspnet-core-features"></a>Laden von ASP.NET Core-Features

Verwenden Sie die Klassen <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> und <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden. Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach. Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly. Der `SharedController` befindet sich nicht im Projekt der App. Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts).

### <a name="include-views"></a>Einschließen von Ansichten

Verwenden Sie eine [ Razor Klassenbibliothek](xref:razor-pages/ui-class) , um Sichten in die Assembly einzuschließen.

### <a name="prevent-loading-resources"></a>Vermeiden des Ladens von Ressourcen

Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden* . Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen. Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig. Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird. Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren. Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.

Der `ApplicationPartManager` enthält Parts für Folgendes:

* Die Assembly der App und die abhängigen Assemblys.
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>Featureanbieter

Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese. Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor Compileditemfeatureprovider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist. Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden. Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken. Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.

### <a name="display-available-features"></a>Anzeigen verfügbarer Features

Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Ermittlung in Anwendungsparts

Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich. Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist. Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:

* Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird. Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.
* Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten. Der Verweis kann direkt oder transitiv sein.
* Die Stammassembly muss auf das Web-SDK verweisen. Das Framework verfügt über eine Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App. Anwendungsteile ermöglichen ASP.net Core das Ermitteln von Controllern, Ansichts Komponenten, taghilfsprogramme, Razor Seiten, Razor-Kompilierungs Quellen und vieles mehr. [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) ist ein Anwendungspart. `AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.

*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen. Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet). Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen. Mithilfe von Anwendungs teilen können Sie eine Assembly (dll) mit Controllern, Ansichten, Razor Seiten, Razor-Kompilierungs Quellen, taghilfsprogrammen und mehr für mehrere apps freigeben. Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.

ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>. Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.

## <a name="load-aspnet-core-features"></a>Laden von ASP.NET Core-Features

Verwenden Sie die Klassen `ApplicationPart` und `AssemblyPart`, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden. Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach. Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly. Der `SharedController` befindet sich nicht im Projekt der Anwendung. Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).

### <a name="include-views"></a>Einschließen von Ansichten

Verwenden Sie eine [ Razor Klassenbibliothek](xref:razor-pages/ui-class) , um Sichten in die Assembly einzuschließen.

### <a name="prevent-loading-resources"></a>Vermeiden des Ladens von Ressourcen

Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden* . Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen. Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig. Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird. Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren. Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.

Der folgende Code verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, um `MyDependentLibrary` aus der App zu entfernen: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

Der `ApplicationPartManager` enthält Parts für Folgendes:

* Die Assembly der App und die abhängigen Assemblys.
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>Anwendungsfeatureanbieter

Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese. Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:

* [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [Taghilfsprogramme](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [Komponenten anzeigen](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist. Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden. Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken. Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.

### <a name="display-available-features"></a>Anzeigen verfügbarer Features

Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>Ermittlung in Anwendungsparts

Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich. Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist. Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:

* Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird. Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.
* Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten. Der Verweis kann direkt oder transitiv sein.
* Die Stammassembly muss auf das Web-SDK verweisen.
  * Das ASP.NET Core-Framework verfügt über eine benutzerdefinierte Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.

::: moniker-end
