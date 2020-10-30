---
title: Arbeiten mit dem Anwendungsmodell in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie das Anwendungsmodell lesen und bearbeiten, um das Verhalten von MVC-Elementen in ASP.NET Core zu ändern.
ms.author: riande
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
uid: mvc/controllers/application-model
ms.openlocfilehash: a7a654eb43c0dbf375af911d8d5353ac4c04a825
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060936"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Arbeiten mit dem Anwendungsmodell in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC definiert ein *Anwendungsmodell* , in dem die Komponenten einer MVC-App dargestellt werden. Sie können dieses Modell lesen und bearbeiten, um die Verhaltensweise von MVC-Elementen zu ändern. MVC folgt standardmäßig bestimmten Konventionen, um zu ermitteln, welche Klassen als Controller betrachtet werden, welche Methoden in diesen Klassen Aktionen darstellen und wie sich Parameter und das Routing verhalten. Sie können dieses Verhalten an die Anforderungen Ihrer App anpassen, indem Sie Ihre eigenen Konventionen schaffen und diese global oder in Form von Attributen anwenden.

## <a name="models-and-providers"></a>Modelle und Anbieter

Das ASP.NET Core-MVC-Anwendungsmodell enthält abstrakte Schnittstellen und konkrete Implementierungsklassen, die eine MVC-Anwendung beschreiben. Dieses Modell resultiert aus der MVC-Ermittlung von Controllern, Aktionen, Aktionsparametern, Routen und Filtern der App gemäß den Standardkonventionen. Wenn Sie mit dem Anwendungsmodell arbeiten, können Sie Ihre App so ändern, dass sie verschiedenen Konventionen des MVC-Standardverhaltens folgt. Die Parameter, Namen, Routen und Filter werden alle als Konfigurationsdaten für Aktionen und Controller verwendet.

Das ASP.NET Core-MVC-Anwendungsmodell weist folgende Struktur auf:

* ApplicationModel
  * Controller (ControllerModel)
    * Aktionen (ActionModel)
      * Parameter (ParameterModel)

Jede Modellebenen kann auf eine allgemeine `Properties`-Sammlung zugreifen. Zudem ist auf niedrigeren Ebenen der Zugriff auf Eigenschaftswerte sowie die Überschreibung von Eigenschaftswerten möglich, die auf höheren Ebenen in der Hierarchie festgelegt wurden. Die Eigenschaften werden bei der Erstellung der Aktionen in der Datei `ActionDescriptor.Properties` permanent gespeichert. Wenn eine Anforderung dann verarbeitet wird, kann über `ActionContext.ActionDescriptor.Properties` auf sämtliche Eigenschaften zugegriffen werden, die über eine Konvention hinzugefügt oder geändert wurden. Die Verwendung von Eigenschaften stellt eine gute Möglichkeit zur Konfiguration Ihrer Filter, Modellbindungen usw. auf Grundlage einer Aktion dar.

> [!NOTE]
> Nachdem die App gestartet wurde, ist die `ActionDescriptor.Properties`-Sammlung nicht threadsicher (bei Schreibvorgängen). Konventionen stellen die beste Möglichkeit zum sicheren Hinzufügen von Daten zu dieser Sammlung dar.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC lädt das Anwendungsmodell über ein durch die Schnittstelle [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) definiertes Anbietermuster. Dieser Abschnitt enthält einige interne Implementierungsdetails zur Funktionsweise dieses Anbieters. Dies ist allerdings ein Thema für Fortgeschrittene. In den meisten Apps, die das Anwendungsmodell nutzen, sollte mit Konventionen gearbeitet werden.

Implementierungen der Schnittstelle `IApplicationModelProvider` umschließen sich gegenseitig. Dabei wird bei den einzelnen Implementierungen die Methode `OnProvidersExecuting` in aufsteigender Reihenfolge basierend auf der zugehörigen Eigenschaft `Order` aufgerufen. Anschließend wird die Methode `OnProvidersExecuted` in umgekehrter Reihenfolge aufgerufen. Das Framework definiert verschiedene Anbieter:

Erst (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Then (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> Die Reihenfolge, in der zwei Anbieter mit dem gleichen Wert für `Order` aufgerufen werden, ist nicht definiert und sollte daher nicht als zuverlässig betrachtet werden.

> [!NOTE]
> `IApplicationModelProvider` ist ein erweitertes, auszuweitendes Konzept für Autoren von Frameworks. Im Allgemeinen sollten in Apps Konventionen und in Frameworks Anbieter verwendet werden. Der Hauptunterschied besteht darin, dass Anbieter immer vor Konventionen ausgeführt werden.

Das Konzept `DefaultApplicationModelProvider` etabliert viele der von ASP.NET Core MVC verwendeten Standardverhaltensweisen. Zu den Zuständigkeiten des Konzepts zählen die folgenden:

* Hinzufügen globaler Filter zum Kontext
* Hinzufügen von Controllern zum Kontext
* Hinzufügen öffentlicher Controllermethoden in Form von Aktionen
* Hinzufügen von Aktionsmethodenparametern zum Kontext
* Anwenden einer Route und anderer Attribute

Einige integrierte Verhaltensweisen werden vom `DefaultApplicationModelProvider` implementiert. Dieser Anbieter ist für das Erstellen von zuständig [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , das wiederum auf [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) -,-und-Instanzen verweist [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) . Die `DefaultApplicationModelProvider`-Klasse stellt ein Detail zur Implementierung des internen Frameworks dar und wird sich in Zukunft ändern. 

`AuthorizationApplicationModelProvider` ist für die Anwendung des Verhaltens zuständig, das den Attributen `AuthorizeFilter` und `AllowAnonymousFilter` zugeordnet ist. [Weitere Informationen zu diesen Attributen](xref:security/authorization/simple).

`CorsApplicationModelProvider` implementiert Verhalten, das dem `IEnableCorsAttribute` und `IDisableCorsAttribute` sowie dem `DisableCorsAuthorizationFilter` zugeordnet ist. [Weitere Informationen zu CORS](xref:security/cors).

## <a name="conventions"></a>Konventionen

Das Anwendungsmodell definiert Konventionsabstraktionen, die eine einfachere Möglichkeit zur Anpassung des Verhaltens der Modelle bieten, wodurch auf eine Überschreibung des gesamten Modells oder Anbieters verzichtet werden kann. Diese Abstraktionen werden als Methode für eine Änderung des Verhaltens Ihrer App empfohlen. Mithilfe von Konventionen können Sie Code schreiben, der Anpassungen dynamisch anwendet. Während [Filter](xref:mvc/controllers/filters) eine Möglichkeit zum Ändern des Frameworkverhaltens bieten, können Sie mithilfe von Anpassungen steuern, wie die einzelnen Komponenten der App zusammenarbeiten.

Folgende Konventionen sind verfügbar:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Konventionen werden angewendet, indem Sie den MVC-Optionen hinzugefügt werden, oder indem Sie `Attribute` s implementieren und auf Controller, Aktionen oder Aktionsparameter anwenden (ähnlich wie [`Filters`](xref:mvc/controllers/filters) ). Im Gegenteil zu Filtern werden Konventionen nur beim Starten der App ausgeführt, nicht im Rahmen einzelner Anforderungen.

### <a name="sample-modifying-the-applicationmodel"></a>Beispiel: Ändern von ApplicationModel

Die folgende Konvention wird zum Hinzufügen einer Eigenschaft zum Anwendungsmodell verwendet. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Konventionen von Anwendungsmodellen werden als Optionen angewendet, wenn MVC in `ConfigureServices` zu `Startup` hinzugefügt wird.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Auf Eigenschaften kann innerhalb von Controlleraktionen über die `ActionDescriptor`-Eigenschaftensammlung zugegriffen werden:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Beispiel: Ändern der ControllerModel-Beschreibung

Wie im vorherigen Beispiel kann auch das Controllermodell so geändert werden, dass es benutzerdefinierte Eigenschaften enthält. Diese Eigenschaften überschreiben vorhandene Eigenschaften mit dem gleichen Namen, die im Anwendungsmodell angegeben sind. Das folgende Konventionsattribut fügt eine Beschreibung auf Controllerebene hinzu:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Diese Konvention wird als Attribut für einen Controller angewendet.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Auf die Eigenschaft „Beschreibung“ wird auf die gleiche Weise wie in vorherigen Beispielen zugegriffen.

### <a name="sample-modifying-the-actionmodel-description"></a>Beispiel: Ändern der ActionModel-Beschreibung

Auf einzelne Aktionen kann eine separate Attributkonvention angewendet werden, mit der das bereits auf Anwendungs- oder Controllerebene angewendete Verhalten überschrieben wird.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Eine Anwendung dieser Konvention auf eine Aktion innerhalb des Controllers aus dem vorherigen Beispiel veranschaulicht, wie die Konvention auf Controllerebene überschrieben wird:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Beispiel: Ändern von ParameterModel

Die folgende Konvention kann auf Aktionsparameter zur Änderung ihrer `BindingInfo` angewendet werden. Für folgende Konvention ist erforderlich, dass es sich bei dem Parameter um einen Routenparameter handelt; andere mögliche Bindungsquellen (z.B. Abfragezeichenfolgewerte) werden ignoriert.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

Das Attribut kann möglicherweise auf sämtliche Aktionsparameter angewendet werden:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Beispiel: Ändern des ActionModel-Namens

Folgende Konvention ändert das `ActionModel`, um den *Namen* der Aktion zu aktualisieren, auf die sie angewendet wird. Der neue Name wird als Parameter für das Attribut bereitgestellt. Er wird beim Routing verwendet und beeinflusst die Route, die zum Erreichen dieser Aktionsmethode verwendet wird.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Dieses Attribut wird auf eine Aktionsmethode im `HomeController` angewendet:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Obwohl der Methodenname `SomeName` lautet, überschreibt das Attribut die MVC-Konvention für die Verwendung des Methodennamens und ersetzt den Aktionsnamen durch `MyCoolAction`. Daher lautet die Route zum Erreichen dieser Aktion `/Home/MyCoolAction`.

> [!NOTE]
> Dieses Beispiel ist im Wesentlichen mit der Verwendung des integrierten Attributs [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) identisch.

### <a name="sample-custom-routing-convention"></a>Beispiel: Benutzerdefinierte Routingkonvention

Sie können die Funktionsweise von Routing mithilfe einer `IApplicationModelConvention` anpassen. Die folgende Konvention bezieht beispielsweise Namespaces von Controllern in ihre Routen ein und ersetzt dabei in der Route `.` im Namespace durch `/`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

Die Konvention wird als Option unter „Start“ hinzugefügt.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Sie können Konventionen zu Ihrer [Middleware](xref:fundamentals/middleware/index) hinzufügen, indem Sie über `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));` auf `MvcOptions` zugreifen.

Im nachfolgenden Beispiel wird diese Konvention auf Routen angewendet, die kein Attributrouting verwenden, bei dem der Name des Controllers „Namespace“ enthält. Der folgende Controller veranschaulicht diese Konvention:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Verwendung eines Anwendungsmodells in WebApiCompatShim

ASP.NET Core MVC verwendet andere Konventionen aus ASP.NET-Web-API 2. Mithilfe von benutzerdefinierten Konventionen können Sie das Verhalten einer ASP.NET Core-MVC-App so ändern, dass es mit dem einer Web-API-App konsistent ist. Microsoft liefert die [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speziell für diesen Zweck aus.

> [!NOTE]
> Hier finden Sie weitere Informationen zum [Migrieren der ASP.NET-Web-API](xref:migration/webapi).

Für die Verwendung der Web-API-Kompatibilitätsshim müssen Sie das Paket zu Ihrem Projekt und anschließend die Konventionen zu MVC hinzufügen, indem Sie `AddWebApiConventions`unter `Startup` aufrufen:

```csharp
services.AddMvc().AddWebApiConventions();
```

Die von der Shim bereitgestellten Konventionen gelten nur für Teile der App, auf die bestimmte Attribute angewendet worden sind. Die folgenden vier Attribute steuern, bei welchen Controllern die zugehörigen Konventionen durch die Konventionen der Shim geändert werden sollten:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Aktionskonventionen

Das `UseWebApiActionConventionsAttribute` wird für die Zuordnung der HTTP-Methode zu Aktionen basierend auf ihrem Namen verwendet (beispielsweise würde `Get` der Aktion `HttpGet` zugeordnet). Es gilt nur für Aktionen, die kein Attributrouting verwenden.

### <a name="overloading"></a>Überladen

Das `UseWebApiOverloadingAttribute` wird für die Anwendung der Konvention `WebApiOverloadingApplicationModelConvention` verwendet. Diese Konvention fügt eine `OverloadActionConstraint` zum Aktionsauswahlprozess hinzu, wodurch mögliche Aktionen auf die Aktionen begrenzt werden, bei denen die Anforderung alle nicht optionalen Parameter erfüllt.

### <a name="parameter-conventions"></a>Parameterkonventionen

Das `UseWebApiParameterConventionsAttribute` wird für die Anwendung der Aktionskonvention `WebApiParameterConventionsApplicationModelConvention` verwendet. Diese Konvention gibt an, dass einfache Typen, die als Aktionsparameter verwendet werden, standardmäßig vom URI abhängen, während komplexe Typen vom Anforderungstext abhängen.

### <a name="routes"></a>Routen

Das `UseWebApiRoutesAttribute` steuert, ob die Controllerkonvention `WebApiApplicationModelConvention` angewendet wird. Bei Aktivierung wird diese Konvention verwendet, um Unterstützung für [Bereiche](xref:mvc/controllers/areas) zur Route hinzuzufügen.

Neben einer Reihe von Konventionen enthält das Kompatibilitätspaket eine `System.Web.Http.ApiController`-Basisklasse, welche die von der Web-API bereitgestellte Klasse ersetzt. Dadurch funktionieren Ihre Controller, die für die Web-API geschrieben wurden und von dem zugehörigen `ApiController` übernommen werden, bei der Ausführung im ASP.NET Core-MVC so, wie sie entwickelt wurden. Alle oben aufgeführten `UseWebApi*`-Attribute werden auf die Basiscontrollerklasse angewendet. Der `ApiController` macht Eigenschaften, Methoden und Ergebnistypen verfügbar, die mit denen in der Web-API kompatibel sind.

## <a name="using-apiexplorer-to-document-your-app"></a>Verwenden von ApiExplorer zum Dokumentieren Ihrer App

Das Anwendungsmodell macht eine [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) -Eigenschaft auf jeder Ebene verfügbar, die zum Durchlaufen der Struktur der APP verwendet werden kann. Diese kann zum [Generieren von Hilfeseiten für Ihre Web-APIs mithilfe von Tools wie Swagger](xref:tutorials/web-api-help-pages-using-swagger) verwendet werden. Die Eigenschaft `ApiExplorer` macht eine Eigenschaft vom Typ `IsVisible` verfügbar, die festgelegt werden kann, um anzugeben, welche Teile des Modells Ihrer App verfügbar gemacht werden sollten. Sie können diese Einstellung mithilfe einer Konvention konfigurieren:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Mit diesem Ansatz (und ggf. zusätzlichen Konventionen) können Sie die API-Sichtbarkeit auf einer beliebigen Ebene innerhalb Ihrer App aktivieren oder deaktivieren. 
