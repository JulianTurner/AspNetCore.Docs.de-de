---
title: Formatieren von Antwortdaten in Web-APIs in ASP.NET Core
author: ardalis
description: Informationen zum Formatieren von Antwortdaten in Web-APIS in ASP.NET Core
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 89e3e51373db5f7cff974b7a8c69d06bedf856ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052512"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a>Formatieren von Antwortdaten in Web-APIs in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC unterstützt das Formatieren von Antwortdaten. Antwortdaten können mithilfe bestimmter Formate oder durch Übernahme des vom Client angeforderten Formats formatiert werden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="format-specific-action-results"></a>Formatspezifische Aktionsergebnisse

Einige Aktionsergebnistypen sind für ein bestimmtes Format spezifisch, wie z.B. <xref:Microsoft.AspNetCore.Mvc.JsonResult> und <xref:Microsoft.AspNetCore.Mvc.ContentResult>. Aktionen können Ergebnisse zurückgeben, die in einem bestimmten Format formatiert sind, unabhängig von den Clienteinstellungen. Mit `JsonResult` beispielsweise werden JSON-formatierte Daten zurückgegeben. Mit `ContentResult` oder einer Zeichenfolge werden Zeichenfolgendaten in Nur-Text-Format zurückgegeben.

Zum Zurückgeben eines bestimmten Typs ist keine Aktion erforderlich. ASP.NET Core unterstützt jeden Objektrückgabewert.  Ergebnisse von Aktionen, die Objekte zurückgeben, deren Typ nicht <xref:Microsoft.AspNetCore.Mvc.IActionResult> ist, werden mit der entsprechenden <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>-Implementierung serialisiert. Weitere Informationen finden Sie unter <xref:web-api/action-return-types>.

Die integrierte Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> gibt JSON-formatierte Daten zurück: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]

Der Beispieldownload gibt die Liste der Autoren zurück. Bei Verwendung der F12-Entwicklertools im Browser oder von [Postman](https://www.getpostman.com/tools) mit dem obigen Code gilt Folgendes:

* Der Antwortheader mit **content-type:** `application/json; charset=utf-8` wird angezeigt.
* Die Anforderungsheader werden angezeigt. Beispiel: Der Header `Accept`. Der `Accept`-Header wird vom vorangehenden Code ignoriert.

Wenn Sie Daten im Textformat zurückgeben möchten, verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ContentResult> und das <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>-Hilfsprogramm:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

Im obigen Code wird `text/plain` als `Content-Type` zurückgegeben. Das Zurückgeben einer Zeichenfolge liefert `text/plain` als `Content-Type`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

Bei Aktionen mit mehreren Rückgabetypen wird `IActionResult` zurückgegeben. Beispiel: Die Rückgabe von verschiedenen HTTP-Statuscodes basierend auf dem Ergebnis der ausgeführten Vorgänge.

## <a name="content-negotiation"></a>Inhaltsaushandlung

Eine Inhaltsaushandlung tritt auf, wenn der Client einen [Accept-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) angibt. Das von ASP.NET Core verwendete Standardformat ist [JSON](https://json.org/). Für die Inhaltsaushandlung gilt:

* Sie wird durch <xref:Microsoft.AspNetCore.Mvc.ObjectResult> implementiert.
* Sie ist in die Statuscode-spezifischen Aktionsergebnisse integriert, die von den Hilfsmethoden zurückgegeben werden. Die Hilfsmethoden für Aktionsergebnisse basieren auf `ObjectResult`.

Wenn ein Modelltyp zurückgegeben wird, lautet der Rückgabetyp `ObjectResult`.

Die folgende Aktionsmethode verwendet die Hilfsmethoden `Ok` und `NotFound`:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

Standardmäßig unterstützt ASP.NET Core `application/json`-, `text/json`- und `text/plain`-Medientypen. Tools wie [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/tools) können den `Accept`-Anforderungsheader festlegen, um das Rückgabeformat anzugeben. Wenn der `Accept`-Header einen vom Server unterstützten Typ enthält, wird dieser Typ zurückgegeben. Der nächste Abschnitt zeigt, wie zusätzliche Formatierer hinzugefügt werden.

Controlleraktionen können POCOs (Plain Old CLR Objects) zurückgeben. Wenn ein POCO zurückgegeben wird, erstellt die Runtime automatisch ein `ObjectResult`, das das Objekt umschließt. Der Client empfängt das formatierte serialisierte Objekt. Wenn das zurückgegebene Objekt `null` ist, wird eine `204 No Content`-Antwort zurückgegeben.

Zurückgeben eines Objekttyps:

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

Im oben stehenden Code gibt die Anforderung eines gültigen Autoralias eine `200 OK`-Antwort mit den Daten des Autors zurück. Die Anforderung eines ungültigen Alias gibt eine `204 No Content`-Antwort zurück.

### <a name="the-accept-header"></a>Der Accept-Header

Eine *Aushandlung* des Inhalts findet statt, wenn in der Anforderung ein `Accept`-Header angezeigt wird. Wenn eine Anforderung einen Accept-Header enthält, führt ASP.NET Core Folgendes aus:

* Die Medientypen im Accept-Header werden in der bevorzugten Reihenfolge aufgelistet.
* Es wird versucht, einen Formatierer zu finden, der eine Antwort in einem der angegebenen Formate erzeugen kann.

Wenn kein Formatierer gefunden wird, der die Clientanforderung erfüllen kann, führt ASP.NET Core Folgendes aus:

* Es wird `406 Not Acceptable` zurückgegeben, wenn <xref:Microsoft.AspNetCore.Mvc.MvcOptions> festgelegt wurde, oder
* Es wird versucht, den ersten Formatierer zu finden, der eine Antwort erzeugen kann.

Wenn kein Formatierer für das angeforderte Format konfiguriert wurde, wird der erste Formatierer verwendet, der das Objekt formatieren kann. Wenn in der Anforderung kein `Accept`-Header vorhanden ist, gilt Folgendes:

* Der erste Formatierer, der das Objekt verarbeiten kann, wird zum Serialisieren der Antwort verwendet.
* Es findet keine Aushandlung statt. Der Server bestimmt, welches Format zurückgegeben werden soll.

Enthält der Accept-Header `*/*`, wird der Header ignoriert, es sei denn, `RespectBrowserAcceptHeader` ist in <xref:Microsoft.AspNetCore.Mvc.MvcOptions> auf TRUE festgelegt.

### <a name="browsers-and-content-negotiation"></a>Browser und Inhaltsaushandlung

Im Gegensatz zu typischen API-Clients, stellen Webbrowser `Accept`-Header bereit. Webbrowser geben viele Formate an, einschließlich Platzhaltern. Wenn das Framework erkennt, dass eine Anforderung von einem Browser stammt, wird standardmäßig Folgendes ausgeführt:

* Der `Accept`-Header wird ignoriert.
* Der Inhalt wird im JSON-Format zurückgegeben, sofern nicht anders konfiguriert.

Dies sorgt bei der Verwendung von APIs für ein konsistenteres browserübergreifendes Benutzererlebnis.

Um eine App so zu konfigurieren, dass Accept-Header in Browsern berücksichtigt werden, legen Sie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> auf `true` fest:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a>Konfigurieren von Formatierern

Apps, die zusätzliche Formate unterstützen müssen, können die geeigneten NuGet-Pakete hinzufügen und die Unterstützung konfigurieren. Es gibt unterschiedliche Formatierungsprogramme für Ein- und für Ausgaben. Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet. Ausgabeformatierer werden zum Formatieren von Antworten verwendet. Informationen zum Erstellen eines benutzerdefinierten Formatierers finden Sie unter [Benutzerdefinierte Formatierer](xref:web-api/advanced/custom-formatters).

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a>Hinzufügen von Unterstützung für das XML-Format

XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.

Bei Verwendung dieses Codes geben Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurück.

### <a name="configure-systemtextjson-based-formatters"></a>Konfigurieren von System.Text.Json-basierten Formatierern

Funktionen für die `System.Text.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` konfiguriert werden.

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden. Zum Beispiel:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a>Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format

Vor ASP.NET Core 3.0 wurden standardmäßig die JSON-Formatierer verwendet, die mithilfe des `Newtonsoft.Json`-Pakets implementiert wurden. In ASP.NET Core 3.0 oder höher basieren die Standardformatierer für JSON auf `System.Text.Json`. Unterstützung für `Newtonsoft.Json` basierte Formatierer und Features ist verfügbar, indem [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) Sie das nuget-Paket installieren und in konfigurieren `Startup.ConfigureServices` .

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

Im vorangehenden Code werden mit dem-Befehl `AddNewtonsoftJson` die folgenden Features für Web-API, MVC und Pages konfiguriert, die verwendet werden können Razor `Newtonsoft.Json` :

* Eingabe-und Ausgabe Formatierer, die JSON lesen und schreiben
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [JSON Patch](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [TempData](xref:fundamentals/app-state#tempdata)

Einige Features funktionieren mit `System.Text.Json`-basierten Formatierern möglicherweise nicht gut und erfordern einen Verweis auf die `Newtonsoft.Json`-basierten Formatierer. Verwenden Sie weiterhin `Newtonsoft.Json`-basierte Formatierer, wenn für die App Folgendes gilt:

* Sie verwendet `Newtonsoft.Json`-Attribute. Zum Beispiel: `[JsonProperty]` oder `[JsonIgnore]`.
* Sie passt die Serialisierungseinstellungen an.
* Sie nutzt Features, die von `Newtonsoft.Json` bereitgestellt werden.
* `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` konfiguriert. Vor ASP.NET Core 3.0 akzeptiert `JsonResult.SerializerSettings` eine Instanz von `JsonSerializerSettings`, die für `Newtonsoft.Json` spezifisch ist.
* die [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>)-Dokumentation generiert.

Funktionen für die `Newtonsoft.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` konfiguriert werden:

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden. Zum Beispiel:

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a>Hinzufügen von Unterstützung für das XML-Format

Die XML-Formatierung erfordert das NuGet-Paket [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).

XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.

Bei Verwendung dieses Codes sollten Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurückgeben.

::: moniker-end

### <a name="specify-a-format"></a>Angeben eines Formats

Um die Antwortformate einzuschränken, wenden Sie den [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter an. Wie die [Filters](xref:mvc/controllers/filters)meisten Filter `[Produces]` können auch auf Aktion, Controller oder globaler Bereich angewendet werden:

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

Der vorangehende [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter:

* Er erzwingt die Rückgabe von JSON-formatierten Antworten von allen Aktionen im Controller.
* Wenn andere Formatierer konfiguriert sind und der Client ein anderes Format angibt, wird JSON zurückgegeben.

Weitere Informationen finden Sie unter [Filter](xref:mvc/controllers/filters).

### <a name="special-case-formatters"></a>Formatierer für besondere Fälle

Einige besondere Fälle werden mithilfe von integrierten Formatierungsprogrammen implementiert. Standardmäßig werden `string`-Rückgabetypen als *text/plain* formatiert (bzw. als *text/html* , wenn sie über den `Accept`-Header angefordert werden). Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> gelöscht werden. Formatierer werden in der `ConfigureServices`-Methode entfernt. Aktionen mit einem Modellobjekt-Rückgabetyp geben `null` zurück, wenn der Rückgabewert `204 No Content` lautet. Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> gelöscht werden. Der folgende Code entfernt `StringOutputFormatter` und `HttpNoContentOutputFormatter`.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

Ohne den `StringOutputFormatter` formatiert der integrierte JSON-Formatierer `string`-Rückgabetypen. Wenn der integrierte JSON-Formatierer entfernt wird und ein XML-Formatierer verfügbar ist, formatiert der XML-Formatierer `string`-Rückgabetypen. Andernfalls geben `string`-Rückgabetypen `406 Not Acceptable` zurück.

Ohne `HttpNoContentOutputFormatter` werden NULL-Objekte mithilfe des konfigurierten Formatierungsprogramms formatiert. Zum Beispiel:

* Der JSON-Formatierer gibt eine Antwort mit dem Text `null` zurück.
* Der XML-Formatierer gibt ein leeres XML-Element mit festgelegtem Attribut `xsi:nil="true"` zurück.

## <a name="response-format-url-mappings"></a>Zuordnung des Antwortformats durch URLs

Clients können in der URL ein bestimmtes Format anfordern, beispielsweise folgendermaßen:

* In der Abfragezeichenfolge oder als Teil des Pfads.
* Durch Verwendung einer formatspezifischen Dateierweiterung wie „.xml“ oder „.json“.

Die Zuordnung des Anforderungspfads sollte in der Route angegeben werden, die die API verwendet. Zum Beispiel:

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

Mit der oben genannten Route kann das angeforderte Format als optionale Dateierweiterung angegeben werden. Das [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) -Attribut überprüft das vorhanden sein des Format Werts im `RouteData` und ordnet das Antwortformat dem entsprechenden Formatierer zu, wenn die Antwort erstellt wird.

|           Route        |             Formatierungsprogramm              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    Standard-Ausgabeformatierungsprogramm    |
| `/api/products/5.json` | JSON-Formatierungsprogramm (falls konfiguriert) |
| `/api/products/5.xml`  | XML-Formatierungsprogramm (falls konfiguriert)  |
