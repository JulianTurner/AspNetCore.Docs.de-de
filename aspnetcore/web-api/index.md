---
title: Erstellen von Web-APIs mit ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Grundlagen zum Erstellen einer Web-API in ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/20/2020
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
uid: web-api/index
ms.openlocfilehash: 51f76bc33d2da6097d26c1ffe17b1c93046a7b88
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052460"
---
# <a name="create-web-apis-with-aspnet-core"></a>Erstellen von Web-APIs mit ASP.NET Core

Von [Scott Addie](https://github.com/scottaddie) und [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core unterstützt das Erstellen von RESTful-Diensten, die auch als Web-APIs bezeichnet werden, unter Verwendung von C#. Eine Web-API verwendet Controller, um Anforderungen zu verarbeiten. *Controller* in einer Web-API sind von `ControllerBase` abgeleitete Klassen. In diesem Artikel wird beschrieben, wie Sie Controller für die Verarbeitung von Web-API-Anforderungen verwenden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="controllerbase-class"></a>ControllerBase-Klasse

Eine Web-API besteht aus mindestens einer Controllerklasse, die von <xref:Microsoft.AspNetCore.Mvc.ControllerBase> abgeleitet ist. Die Web-API-Projektvorlage stellt einen Startercontroller bereit:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Erstellen Sie einen Web-API-Controller nicht durch Ableitung aus der <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse. `Controller` wird von `ControllerBase` abgeleitet und fügt Unterstützung für Ansichten hinzu, wird also für die Verarbeitung von Webseiten verwendet und nicht für Web-API-Anforderungen. Es gibt eine Ausnahme von dieser Regel: Wenn Sie denselben Controller sowohl für Ansichten als auch für Web-APIs verwenden möchten, leiten Sie ihn von `Controller` ab.

Die `ControllerBase`-Klasse bietet viele Eigenschaften und Methoden, die für die Verarbeitung von HTTP-Anforderungen hilfreich sind. `ControllerBase.CreatedAtAction` gibt beispielsweise Statuscode 201 zurück:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Hier einige weitere Beispiele für die von `ControllerBase` bereitgestellten Methoden.

|Methode   |Hinweise    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Gibt Statuscode 400 zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Gibt Statuscode 404 zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Gibt eine Datei zurück.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Ruft die [Modellbindung](xref:mvc/models/model-binding) auf.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Ruft die [Modellvalidierung](xref:mvc/models/validation) auf.|

Eine Liste aller verfügbaren Methoden und Eigenschaften finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Attribute

Der <xref:Microsoft.AspNetCore.Mvc>-Namespace enthält Attribute, mit denen das Verhalten von Web-API-Controllern und Aktionsmethoden konfiguriert werden kann. Im folgenden Beispiel werden Attribute verwendet, um das unterstützte HTTP-Aktionsverb und alle bekannten HTTP-Statuscodes, die zurückgegeben werden können, anzugeben:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Hier einige weitere Beispiele für die verfügbaren Attribute.

|Attribut|Hinweise|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Gibt ein URL-Muster für einen Controller oder eine Aktion an.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Gibt Präfixe und Eigenschaften an, die in die Modellbindung einbezogen werden sollen.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifiziert eine Aktion, die das HTTP GET-Aktionsverb unterstützt.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Gibt die von einer Aktion akzeptierten Datentypen an.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Gibt die von einer Aktion zurückgegebenen Datentypen an.|

Eine Liste mit den verfügbaren Attributen finden Sie im <xref:Microsoft.AspNetCore.Mvc>-Namespace.

## <a name="apicontroller-attribute"></a>ApiController-Attribut

Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut kann auf eine Controllerklasse angewendet werden, um folgende API-spezifische Verhalten mit einigen vordefinierten Konfigurationen zu aktivieren:

::: moniker range=">= aspnetcore-2.2"

* [Anforderung für das Attributrouting](#attribute-routing-requirement)
* [Automatische HTTP 400-Antworten](#automatic-http-400-responses)
* [Rückschluss auf Bindungsquellparameter](#binding-source-parameter-inference)
* [Ableiten der Multipart/form-data-Anforderung](#multipartform-data-request-inference)
* [Problemdetails für Fehlerstatuscodes](#problem-details-for-error-status-codes)

Das Feature *Problemdetails für Fehlerstatuscodes* erfordert mindestens die [Kompatibilitätsversion](xref:mvc/compatibility-version) 2.2. Die anderen Features erfordern mindestens Kompatibilitätsversion 2.1.

::: moniker-end

* [Anforderung für das Attributrouting](#attribute-routing-requirement)
* [Automatische HTTP 400-Antworten](#automatic-http-400-responses)
* [Rückschluss auf Bindungsquellparameter](#binding-source-parameter-inference)
* [Ableiten der Multipart/form-data-Anforderung](#multipartform-data-request-inference)

Diese Features erfordern mindestens [Kompatibilitätsversion](xref:mvc/compatibility-version) 2.1.

### <a name="attribute-on-specific-controllers"></a>Attribut für bestimmte Controller

Das `[ApiController]`-Attribut kann, wie im folgenden Beispiel der Projektvorlage ersichtlich, auf bestimmte Controller angewendet werden:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Attribut für mehrere Controller

Eine Möglichkeit, das Attribut für mehr als einen Controller zu verwenden, besteht darin, eine benutzerdefinierte Basiscontrollerklasse zu erstellen, die mit dem `[ApiController]`-Attribut kommentiert ist. Im folgenden Beispiel wird eine benutzerdefinierte Basisklasse und ein Controller, der von ihr abgeleitet wird, gezeigt:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Attribut für eine Assembly

Wenn die [Kompatibilitätsversion](xref:mvc/compatibility-version) auf 2.2 oder höher festgelegt ist, kann das `[ApiController]`-Attribut auf eine Assembly angewendet werden. Auf diese Weise wendet die Anmerkung das Web-API-Verhalten auf alle Controller in der Assembly an. Es gibt keine Möglichkeit, einzelne Controller auszuschließen. Wenden Sie das Attribut auf Assemblyebene auf die Namespacedeklaration an, die die `Startup`-Klasse umgibt:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Anforderung für das Attributrouting

Durch das `[ApiController]`-Attribut wird das Attributrouting zu einer Anforderung. Zum Beispiel:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Sie können über [konventionelle Routen](xref:mvc/controllers/routing#conventional-routing), die durch <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> oder <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure` definiert sind, nicht auf Aktionen zugreifen.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Automatische HTTP 400-Antwort

Durch das `[ApiController]`-Attribut wird bei Modellvalidierungsfehlern automatisch eine HTTP 400-Antwort ausgelöst. Deshalb ist der folgende Code in einer Aktionsmethode überflüssig:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC verwendet den <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter>-Aktionsfilter, um die vorherige Überprüfung durchzuführen.

### <a name="default-badrequest-response"></a>Standardmäßige BadRequest-Antwort

Bei Kompatibilitätsversion 2.1 wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.SerializableError> verwendet. Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

Bei Kompatibilitätsversion 2.2 oder höher wird für eine HTTP 400-Antwort standardmäßig der Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> verwendet. Der folgende Anforderungstext ist ein Beispiel für den serialisierten Typ:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

Der `ValidationProblemDetails`-Typ:

* Bietet ein vom Computer lesbares Format zum Angeben von Fehlern in Web-API-Antworten.
* Entspricht der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807).

::: moniker-end

Rufen Sie die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A>-Methode anstelle von <xref:System.Web.Http.ApiController.BadRequest%2A> auf, um automatische und benutzerdefinierte Antworten konsistent zu machen. `ValidationProblem` gibt sowohl ein <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>-Objekt als auch die automatische Antwort zurück.

### <a name="log-automatic-400-responses"></a>Protokollieren automatischer 400-Antworten

Informationen finden Sie unter [Protokollieren automatischer 400-Antworten auf Modellvalidierungsfehler, (dotnet/AspNetCore.Docs#12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Deaktivieren automatischer 400-Antwort

Um das automatische Verhalten bei Statuscode 400 zu deaktivieren, legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter>-Eigenschaft auf `true` fest. Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Rückschluss auf Bindungsquellparameter

Ein Bindungsquellenattribut definiert den Speicherort, an dem der Wert eines Aktionsparameters vorhanden ist. Es gibt die folgenden Bindungsquellattribute:

|Attribut|Bindungsquelle |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Anforderungstext |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Formulardaten im Anforderungstext |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Anforderungsheader |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Abfragezeichenfolge-Parameter der Anforderung |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Routendaten aus aktuellen Anforderungen |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Der als Aktionsparameter eingefügte Anforderungsdienst. |

> [!WARNING]
> Verwenden Sie `[FromRoute]` nicht, wenn Werte möglicherweise `%2f` enthalten (d.h. `/`). `%2f` wird nicht durch Entfernen von Escapezeichen zu `/`. Verwenden Sie `[FromQuery]`, wenn der Wert `%2f` enthalten könnte.

Ohne das `[ApiController]`-Attribut oder Bindungsquellenattribute wie `[FromQuery]` versucht die ASP.NET Core-Runtime, die Modellbindung für komplexe Objekte zu verwenden. Bei der Modellbindung für komplexe Objekte werden Daten aus Wertanbietern in einer festgelegten Reihenfolge abgerufen.

Im folgenden Beispiel gibt das `[FromQuery]`-Attribut an, dass der Parameterwert `discontinuedOnly` in der Abfragezeichenfolge der Anforderungs-URL bereitgestellt wird:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Das `[ApiController]`-Attribut wendet Rückschlussregeln auf die Standarddatenquellen von Aktionsparametern an. Da durch diese Regeln Attribute auf die Aktionsparameter angewendet werden, müssen Sie Bindungsquellen nicht manuell identifizieren. Die Rückschlussregeln für Bindungsquellen verhalten sich wie folgt:

* `[FromBody]` wird für komplexe Typparameter abgeleitet. Jeder komplexe integrierte Typ mit spezieller Bedeutung, z. B. <xref:Microsoft.AspNetCore.Http.IFormCollection> und <xref:System.Threading.CancellationToken>, stellt eine Ausnahme von der `[FromBody]`-Rückschlussregel dar. Der Rückschlusscode der Bindungsquelle ignoriert diese Typen.
* `[FromForm]` wird für Aktionsparameter des Typs <xref:Microsoft.AspNetCore.Http.IFormFile> und <xref:Microsoft.AspNetCore.Http.IFormFileCollection> abgeleitet. Es wird für keine einfachen oder benutzerdefinierte Typen abgeleitet.
* `[FromRoute]` wird für jeden Namen von Aktionsparametern abgeleitet, der mit einem Parameter in der Routenvorlage übereinstimmt. Wenn mehr als eine Route mit einem Aktionsparameter übereinstimmt, gilt jeder Routenwert als `[FromRoute]`.
* `[FromQuery]` wird für alle anderen Aktionsparameter abgeleitet.

### <a name="frombody-inference-notes"></a>Hinweise zum FromBody-Rückschluss

`[FromBody]` wird für einfache Typen wie `string` oder `int` nicht abgeleitet. Deshalb sollte das `[FromBody]`-Attribut für einfache Typen verwendet werden, wenn diese Funktionsweise benötigt wird.

Wenn an eine Aktion mehr als ein Parameter aus dem Anforderungstext gebunden ist, wird eine Ausnahme ausgelöst. Beispielsweise lösen alle Signaturen der folgenden Aktionsmethoden eine Ausnahme aus:

* `[FromBody]` wird für beide abgeleitet, da sie komplexe Typen darstellen.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* Das `[FromBody]`-Attribut für einen Typ wird für den anderen abgeleitet, da es sich um einen komplexen Typ handelt.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* Das `[FromBody]`-Attribut für beide.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> In ASP.NET Core 2.1 werden Sammlungstypparameter wie Listen und Arrays fälschlicherweise als `[FromQuery]` abgeleitet. Für diese Parameter sollte das `[FromBody]`-Attribut verwendet werden, wenn sie aus dem Anforderungstext gebunden werden sollen. Dieses Verhalten wurde bei ASP.NET Core 2.2 oder höher behoben. Hier werden Sammlungstypparameter standardmäßig als „[FromBody]“ abgeleitet.

::: moniker-end

### <a name="disable-inference-rules"></a>Deaktivieren von Rückschlussregeln

Legen Sie <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> auf `true` fest, um den Rückschluss auf Bindungsquellen zu deaktivieren. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Ableiten der Multipart/form-data-Anforderung

Das `[ApiController]`-Attribut wendet eine Rückschlussregel an, wenn ein Aktionsparameter mit dem [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Attribut kommentiert ist. Der `multipart/form-data`-Anforderungsinhaltstyp wird abgeleitet.

Legen Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters>-Eigenschaft auf `true` in `Startup.ConfigureServices` fest, um das Standardverhalten zu deaktivieren.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Problemdetails für Fehlerstatuscodes

Bei Kompatibilitätsversion 2.2 oder höher wandelt MVC ein Fehlerergebnis (ein Ergebnis mit dem Statuscode 400 oder höher) in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> um. Die `ProblemDetails`-Typ basiert auf der [RFC 7807-Spezifikation](https://tools.ietf.org/html/rfc7807) für die Bereitstellung maschinenlesbarer Fehlerdetails in einer HTTP-Antwort.

Betrachten Sie den folgenden Code in einer Controlleraktion:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Die `NotFound`-Methode erzeugt einen HTTP-404-Statuscode mit einem `ProblemDetails`-Text. Zum Beispiel:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Deaktivieren der ProblemDetails-Antwort

Die automatische Erstellung einer `ProblemDetails`-Antwort für Fehlerstatuscodes ist deaktiviert, wenn die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A>-Eigenschaft auf `true` festgelegt ist. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definieren unterstützter Anforderungsinhaltstypen mit dem [Consumes]-Attribut

Standardmäßig unterstützt eine Aktion alle verfügbaren Anforderungsinhaltstypen. Wenn eine App beispielsweise zur Unterstützung von JSON- und XML-[Eingabeformatierern](xref:mvc/models/model-binding#input-formatters) konfiguriert ist, unterstützt eine Aktion mehrere Inhaltstypen, wie etwa `application/json` und `application/xml`.

Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken. Wenden Sie das `[Consumes]`-Attribut auf eine Aktion oder einen Controller an, und geben Sie mindestens einen Inhaltstyp an:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

Im obigen Code gibt die `CreateProduct`-Aktion den Inhaltstyp `application/xml` an. Anforderungen, die an diese Aktion weitergeleitet werden, müssen den `Content-Type`-Header `application/xml` angeben. Für Anforderungen, die den `Content-Type`-Header `application/xml` nicht angeben, wird die Antwort [415 – Nicht unterstützter Medientyp](https://developer.mozilla.org/docs/Web/HTTP/Status/415) zurückgegeben.

Das `[Consumes]`-Attribut ermöglicht es einer Aktion auch, eine Typeinschränkung anzuwenden, um die Auswahl basierend auf dem Inhaltstyp einer eingehenden Anforderung zu beeinflussen. Betrachten Sie das folgende Beispiel:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

Im obigen Code ist `ConsumesController` zur Verarbeitung von Anforderungen konfiguriert, die an die URL `https://localhost:5001/api/Consumes` gesendet werden. Beide Controlleraktionen – `PostJson` und `PostForm` – verarbeiten POST-Anforderungen mit derselben URL. Wenn das `[Consumes]`-Attribut keine Typeinschränkung anwendet, wird eine Ausnahme in Bezug auf eine mehrdeutige Übereinstimmung ausgelöst.

Das `[Consumes]`-Attribut wird auf beide Aktionen angewendet. Die `PostJson`-Aktion verarbeitet Anforderungen, die mit dem `Content-Type`-Header `application/json` gesendet werden. Die `PostForm`-Aktion verarbeitet Anforderungen, die mit dem `Content-Type`-Header `application/x-www-form-urlencoded` gesendet werden. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
