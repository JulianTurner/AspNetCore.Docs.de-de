---
title: Migrieren von ASP.net-Web-API zu ASP.net Core
author: ardalis
description: Erfahren Sie, wie Sie eine Web-API-Implementierung von der ASP.NET 4. x-Web-API zu ASP.net Core MVC migrieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061469"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrieren von ASP.net-Web-API zu ASP.net Core

Von [Scott Adder](https://twitter.com/scott_addie) und [Steve Smith](https://ardalis.com/)

Eine ASP.NET 4. x-Web-API ist ein HTTP-Dienst, der eine breite Palette von Clients, einschließlich Browser und mobiler Geräte, erreicht. ASP.net Core kombiniert die MVC-und Web-API-APP-Modelle von ASP.NET 4. x in einem einzelnen Programmiermodell, das als ASP.net Core MVC bezeichnet wird. In diesem Artikel werden die erforderlichen Schritte zum Migrieren von der ASP.NET 4. x-Web-API zu ASP.net Core MVC erläutert.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x-Web-API-Projekt überprüfen

In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben. In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.

In *Global.asax.cs* wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

Die vorherige-Klasse:

* Konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl es nicht tatsächlich verwendet wird.
* Konfiguriert die Routing Tabelle.
Im Beispielcode wird erwartet, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` optional ist.

In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.

## <a name="create-the-destination-project"></a>Erstellen des Ziel Projekts

Erstellen Sie eine neue leere Projekt Mappe in Visual Studio, und fügen Sie der Migration das ASP.NET 4. x-Web-API-Projekt hinzu:

1. Klicken Sie im Menü **Datei** auf **Neu** > **Projekt** .
1. Wählen Sie die Vorlage **leere** Vorlage aus, und klicken Sie auf **weiter** .
1. Nennen Sie die Projekt Mappe *webapimigration* . Klicken Sie auf **Erstellen** .
1. Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.

Fügen Sie ein neues API-Projekt für die Migration hinzu:

1. Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu.
1. Benennen Sie im Dialogfeld **Neues Projekt konfigurieren** das Projekt *productscore* , und wählen Sie dann **Erstellen** aus.
1. Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** ausgewählt sind. Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen** .
1. Entfernen Sie die Beispieldateien *WeatherForecast.cs* und *Controllers/weatherforecastcontroller. cs* aus dem neuen *productscore* -Projekt.

Die Projekt Mappe enthält jetzt zwei Projekte. In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.

## <a name="migrate-configuration"></a>Migrating Configuration (Migrieren der Konfiguration)

ASP.net Core verwendet nicht den Ordner *App_Start* oder die Datei *Global. asax* . Außerdem wird die *web.config* -Datei zum Zeitpunkt der Veröffentlichung hinzugefügt.

Die `Startup`-Klasse:

* Ersetzt *Global. asax* .
* Behandelt alle App-Start Tasks.

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="migrate-models-and-controllers"></a>Migrieren von Modellen und Controllern

Der folgende Code zeigt `ProductsController` , dass für ASP.net Core aktualisiert werden soll:

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

Aktualisieren Sie das- `ProductsController` ASP.net Core:

1. Kopieren Sie " *Controllers/productioncontroller. cs* " und den Ordner " *Models* " aus dem ursprünglichen Projekt in das neue Projekt.
1. Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .
1. Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .

Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:

* `ApiController`-Klasse
* `System.Web.Http`-Namespace
* `IHttpActionResult`-Schnittstelle

Nehmen Sie die folgenden Änderungen vor:

1. Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.
1. Löschen Sie `using System.Web.Http;`.
1. Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .
1. Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Konfigurieren des Routings

Die ASP.net Core- *API* -Projektvorlage enthält die Konfiguration des Endpunkt Routings im generierten Code.

Die folgenden <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> -und- <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Aufrufe:

* Registrieren Sie den Routen Abgleich und die [middleware](xref:fundamentals/middleware/index) Endpunkt Ausführung in der middlewarepipeline.
* Ersetzen Sie die *App_Start/webapiconfig.cs* -Datei des Projekts " *producungapp* ".

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

Konfigurieren Sie das Routing wie folgt:

1. Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers. Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.

    Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und. Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde. Die Token:
    * Reduzieren Sie die Anzahl der magischen Zeichen folgen im Projekt.
    * Stellen Sie sicher, dass die Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn Sie refacktoren für automatisches umbenennen anwenden.
1. HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:
    * Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.
    * Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.

Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` . Es wird eine vollständige Liste der drei Produkte angezeigt. Navigieren Sie zu `/api/products/1`. Das erste Produkt wird angezeigt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>ASP.NET 4. x-Web-API-Projekt überprüfen

In diesem Artikel wird das Projekt *producungapp* verwendet, das Sie unter [Getting Started with ASP.net-Web-API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)erstellt haben. In diesem Projekt wird ein einfaches ASP.NET 4. x-Web-API-Projekt wie folgt konfiguriert.

In *Global.asax.cs* wird ein-Rückruf für Folgendes durchgeführt `WebApiConfig.Register` :

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

Die `WebApiConfig` -Klasse befindet sich im Ordner " *App_Start* " und verfügt über eine statische- `Register` Methode:

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

Diese Klasse konfiguriert das [Attribut Routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), obwohl Sie nicht tatsächlich im Projekt verwendet wird. Außerdem wird die Routing Tabelle konfiguriert, die von ASP.net-Web-API verwendet wird. In diesem Fall erwartet die ASP.NET 4. x-Web-API, dass URLs dem Format entsprechen `/api/{controller}/{id}` , wobei `{id}` Sie optional ist.

In den folgenden Abschnitten wird die Migration des Web-API-Projekts zu ASP.net Core MVC veranschaulicht.

## <a name="create-the-destination-project"></a>Erstellen des Ziel Projekts

Führen Sie in Visual Studio die folgenden Schritte aus:

* Wechseln Sie zu **Datei**  >  **neu**  >  **Projekt**  >  **andere Projekttypen**  >  **Visual Studio** -Projektmappen. Wählen Sie **leere** Projekt Mappe aus, und nennen Sie die Projekt Mappe *webapimigration* . Klicken Sie auf die Schaltfläche **OK** .
* Fügen Sie der Projekt Mappe das vorhandene *producungapp* -Projekt hinzu.
* Fügen Sie der Projekt Mappe ein neues **ASP.net Core-Webanwendungs** Projekt hinzu. Wählen Sie in der Dropdown Liste das Ziel Framework **.net Core** aus, und wählen Sie die Vorlage **API** -Projekt aus. Nennen Sie das Projekt *productscore* , und klicken Sie auf die Schaltfläche **OK** .

Die Projekt Mappe enthält jetzt zwei Projekte. In den folgenden Abschnitten wird erläutert, wie der Inhalt des *productsapp* -Projekts in das *productscore* -Projekt migriert wird.

## <a name="migrate-configuration"></a>Migrating Configuration (Migrieren der Konfiguration)

ASP.net Core nicht verwendet:

* *App_Start* Ordner oder die Datei " *Global. asax* "
* *web.config* Datei wird zum Zeitpunkt der Veröffentlichung hinzugefügt.

Die `Startup`-Klasse:

* Ersetzt *Global. asax* .
* Behandelt alle App-Start Tasks.

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

In ASP.net Core MVC ist das Attribut Routing standardmäßig enthalten, wenn <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> in aufgerufen wird `Startup.Configure` . Der folgende `UseMvc` -Befehl ersetzt die *App_Start/webapiconfig.cs* -Datei des Projekts *producungapp* :

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a>Migrieren von Modellen und Controllern

Der folgende Code zeigt das `ProductsController` Update für ASP.net Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]

Aktualisieren Sie das- `ProductsController` ASP.net Core:

1. Kopieren Sie " *Controllers/productioncontroller. cs* " aus dem ursprünglichen Projekt in das neue Projekt.
1. Kopieren Sie den Ordner *Models* aus dem ursprünglichen Projekt in das neue Projekt.
1. Ändern Sie den Stamm Namespace der kopierten Dateien in `ProductsCore` .
1. Aktualisieren Sie die- `using ProductsApp.Models;` Anweisung in `using ProductsCore.Models;` .

Die folgenden Komponenten sind nicht in ASP.net Core vorhanden:

* `ApiController`-Klasse
* `System.Web.Http`-Namespace
* `IHttpActionResult`-Schnittstelle

Nehmen Sie die folgenden Änderungen vor:

1. Ändern Sie `ApiController` in <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Fügen Sie hinzu `using Microsoft.AspNetCore.Mvc;` , um den `ControllerBase` Verweis aufzulösen.
1. Löschen Sie `using System.Web.Http;`.
1. Ändern `GetProduct` Sie den Rückgabetyp der Aktion von `IHttpActionResult` in `ActionResult<Product>` .
1. Vereinfachen `GetProduct` Sie die-Anweisung der Aktion wie `return` folgt:

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a>Konfigurieren des Routings

Konfigurieren Sie das Routing wie folgt:

1. Markieren Sie die `ProductsController` Klasse mit den folgenden Attributen:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Das vorangehende [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) Attribut konfiguriert das Attribut Routing Muster des Controllers. Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut bewirkt, dass das Attribut Routing für alle Aktionen in diesem Controller erforderlich ist.

    Das Attribut Routing unterstützt Token, wie z `[controller]` `[action]` . b. und. Zur Laufzeit wird jedes Token durch den Namen des Controllers bzw. der Aktion ersetzt, auf das das Attribut angewendet wurde. Die Token reduzieren die Anzahl der magischen Zeichen folgen im Projekt. Die Token stellen außerdem sicher, dass Routen mit den entsprechenden Controllern und Aktionen synchronisiert bleiben, wenn die refactoren für automatisches umbenennen angewendet werden.
1. Legen Sie den Kompatibilitätsmodus des Projekts auf ASP.net Core 2,2 fest:

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Die vorherige Änderung:

    * Ist erforderlich, um das- `[ApiController]` Attribut auf Controller Ebene zu verwenden.
    * Gibt an, dass potenziell wichtige Verhaltensweisen in ASP.net Core 2,2 eingeführt werden.
1. HTTP GET-Anforderungen für die `ProductController` Aktionen aktivieren:
    * Wenden Sie das- [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) Attribut auf die `GetAllProducts` Aktion an.
    * Wenden Sie das- `[HttpGet("{id}")]` Attribut auf die `GetProduct` Aktion an.

Führen Sie das migrierte Projekt aus, und navigieren Sie zu `/api/products` . Es wird eine vollständige Liste der drei Produkte angezeigt. Navigieren Sie zu `/api/products/1`. Das erste Produkt wird angezeigt.

## <a name="compatibility-shim"></a>Kompatibilitäts-Shim

Die [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) -Bibliothek stellt einen Kompatibilitäts-Shim bereit, um ASP.NET 4. x-Web-API-Projekte in ASP.net Core zu verschieben. Der Kompatibilitäts-Shim erweitert ASP.net Core, um eine Reihe von Konventionen von ASP.NET 4. x Web-API 2 zu unterstützen. Das zuvor in diesem Dokument portierte Beispiel ist so einfach, dass das Kompatibilitäts-Shim unnötig ist. Bei größeren Projekten kann die Verwendung des Kompatibilitäts-Shims nützlich sein, um die API-Lücke zwischen ASP.net Core und ASP.NET 4. x-Web-API 2 vorübergehend zu überbrücken.

Das Web-API-Kompatibilitäts-Shim ist als temporäre Maßnahme zur Unterstützung der Migration von großen ASP.NET 4. x-Web-API-Projekten zu ASP.net Core gedacht. Im Laufe der Zeit sollten Projekte aktualisiert werden, um ASP.net Core Muster zu verwenden, anstatt sich auf den Kompatibilitäts-Shim zu verlassen.

Zu den in enthaltenen Kompatibilitäts Features `Microsoft.AspNetCore.Mvc.WebApiCompatShim` gehören:

* Fügt einen `ApiController` Typ hinzu, sodass die Basis Typen der Controller nicht aktualisiert werden müssen.
* Ermöglicht die Modell Bindung im Web-API-Stil. ASP.net Core die MVC-Modell Bindung ähnlich der von ASP.NET 4. x MVC 5 standardmäßig. Mit dem Kompatibilitäts-Shim wird die Modell Bindung so geändert, dass Sie den Modell Bindungs Konventionen von ASP.NET 4. x Web API 2 ähnelt. Komplexe Typen werden z. b. automatisch vom Anforderungs Text gebunden.
* Erweitert die Modell Bindung, sodass Controller Aktionen Parameter des Typs annehmen können `HttpRequestMessage` .
* Fügt nachrichtenformatierer hinzu, sodass Aktionen Ergebnisse vom Typ zurückgeben können `HttpResponseMessage` .
* Fügt zusätzliche Antwort Methoden hinzu, die von Web-API 2-Aktionen verwendet werden können, um Antworten zu erfüllen:
  * `HttpResponseMessage` ger
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Aktions Ergebnis Methoden:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Fügt `IContentNegotiator` dem Container für die Abhängigkeitsinjektion der App eine Instanz von hinzu und stellt die inhaltsaushandlungs bezogenen Typen von [Microsoft. Aspnet. WebAPI. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)zur Verfügung. Beispiele für solche Typen sind `DefaultContentNegotiator` und `MediaTypeFormatter` .

So verwenden Sie den Kompatibilitäts-Shim:

1. Installieren Sie das nuget-Paket [Microsoft. aspnetcore. MVC. webapicompatshim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Registrieren Sie die Dienste des Kompatibilitäts-Shims mit dem di-Container der APP, indem Sie `services.AddMvc().AddWebApiConventions()` in Aufrufen `Startup.ConfigureServices` .
1. Definieren Sie Web-API-spezifische Routen mithilfe `MapWebApiRoute` von für den `IRouteBuilder` im App-Befehl `IApplicationBuilder.UseMvc` .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
