---
title: Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
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
uid: migration/http-modules
ms.openlocfilehash: 213807634a2a6990e9025de7871295cf97a81faf
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865587"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware

In diesem Artikel wird gezeigt, wie vorhandene ASP.net [-HTTP-Module und-Handler von System. Webserver](/iis/configuration/system.webserver/) zu ASP.net Core [Middleware](xref:fundamentals/middleware/index)migriert werden.

## <a name="modules-and-handlers-revisited"></a>Wieder besuchte Module und Handler

Bevor Sie mit der ASP.net Core Middleware fortfahren, können Sie zuerst die Funktionsweise von HTTP-Modulen und-Handlern fortsetzen:

![Module-Handler](http-modules/_static/moduleshandlers.png)

**Handler sind:**

* Klassen, die [IHttpHandler](/dotnet/api/system.web.ihttphandler) implementieren

* Wird zum Verarbeiten von Anforderungen mit einem angegebenen Dateinamen oder einer Erweiterung verwendet, z *. b.. Report*

* [Konfiguriert](/iis/configuration/system.webserver/handlers/) in *Web.config*

**Module:**

* Klassen, die [IHttpModule](/dotnet/api/system.web.ihttpmodule) implementieren

* Wird für jede Anforderung aufgerufen.

* Kurzschluss (weitere Verarbeitung einer Anforderung wird beendet)

* Der HTTP-Antwort kann hinzugefügt werden, oder es kann ein eigener erstellt werden.

* [Konfiguriert](/iis/configuration/system.webserver/modules/) in *Web.config*

**Die Reihenfolge, in der Module eingehende Anforderungen verarbeiten, wird durch Folgendes bestimmt:**

1. Der <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> , bei dem es sich um eine Reihe von Ereignissen handelt, die von ASP.net ausgelöst werden: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)usw. Jedes Modul kann einen Handler für ein oder mehrere Ereignisse erstellen.

2. Für dasselbe Ereignis die Reihenfolge, in der Sie in *Web.config*konfiguriert sind.

Zusätzlich zu Modulen können Sie Handler für die Lebenszyklus Ereignisse ihrer *Global.asax.cs* -Datei hinzufügen. Diese Handler werden nach den Handlern in den konfigurierten Modulen ausgeführt.

## <a name="from-handlers-and-modules-to-middleware"></a>Von Handlern und Modulen bis Middleware

**Middleware ist einfacher als HTTP-Module und-Handler:**

* Module, Handler, *Global.asax.cs*, *Web.config* (außer IIS-Konfiguration) und der Lebenszyklus der Anwendung sind nicht mehr vorhanden.

* Die Rollen von Modulen und Handlern wurden von Middleware übernommen.

* Middleware wird nicht in *Web.config* sondern mithilfe von Code konfiguriert.

::: moniker range=">= aspnetcore-3.0"

* Mithilfe der [Pipeline Verzweigung](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) können Sie Anforderungen an bestimmte Middleware senden. Dies basiert nicht nur auf der URL, sondern auch auf Anforderungs Headern, Abfrage Zeichenfolgen usw.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* Mithilfe der [Pipeline Verzweigung](xref:fundamentals/middleware/index#use-run-and-map) können Sie Anforderungen an bestimmte Middleware senden. Dies basiert nicht nur auf der URL, sondern auch auf Anforderungs Headern, Abfrage Zeichenfolgen usw.

::: moniker-end

**Middleware ähnelt Modulen stark:**

* Wird im Prinzip für jede Anforderung aufgerufen.

* Eine Anforderung kann kurz gebunden werden, da [die Anforderung nicht an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware) wird.

* Ihre eigene HTTP-Antwort kann erstellt werden.

**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**

* Die Reihenfolge der Middleware basiert auf der Reihenfolge, in der Sie in die Anforderungs Pipeline eingefügt werden, während die Reihenfolge der Module hauptsächlich auf Ereignissen basiert. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>

* Die Reihenfolge der Middleware für Antworten ist das Gegenteil bei Anforderungen, während die Reihenfolge der Module für Anforderungen und Antworten gleich ist.

* Weitere Informationen finden Sie [unter Erstellen einer middlewarepipeline mit iapplicationbuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Beachten Sie, dass in der obigen Abbildung die Authentifizierungs Middleware die Anforderung kurzgeschlossen hat.

## <a name="migrating-module-code-to-middleware"></a>Migrieren von Modulcode zu Middleware

Ein vorhandenes HTTP-Modul sieht in etwa wie folgt aus:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Wie auf der Seite [Middleware](xref:fundamentals/middleware/index) gezeigt, ist eine ASP.net Core Middleware eine-Klasse, die eine-Methode verfügbar macht, die eine `Invoke` `HttpContext` -Klasse und eine zurückgibt `Task` . Die neue Middleware sieht wie folgt aus:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Die vorangehende Middleware-Vorlage stammt aus dem Abschnitt zum [Schreiben von Middleware](xref:fundamentals/middleware/write).

Die Hilfsklasse *mymiddlewareextensions* vereinfacht das Konfigurieren ihrer Middleware in der `Startup` Klasse. Die- `UseMyMiddleware` Methode fügt der Anforderungs Pipeline die Middleware-Klasse hinzu. Die von der Middleware benötigten Dienste werden in den Konstruktor der Middleware eingefügt.

<a name="http-modules-shortcircuiting-middleware"></a>

Das Modul kann eine Anforderung beenden, z. b. wenn der Benutzer nicht autorisiert ist:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Eine Middleware verarbeitet dies, indem nicht `Invoke` für die nächste Middleware in der Pipeline aufgerufen wird. Beachten Sie, dass dies die Anforderung nicht vollständig beendet, da vorherige Middlewares weiterhin aufgerufen werden, wenn die Antwort durch die Pipeline wieder zurückgegeben wird.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Wenn Sie die Funktionalität Ihres Moduls zu ihrer neuen Middleware migrieren, stellen Sie möglicherweise fest, dass der Code nicht kompiliert wird, da die `HttpContext` Klasse in ASP.net Core erheblich geändert wurde. [Später](#migrating-to-the-new-httpcontext)erfahren Sie, wie Sie zum neuen ASP.net Core HttpContext migrieren.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrieren des Moduls in die Anforderungs Pipeline

HTTP-Module werden in der Regel mit *Web.config*der Anforderungs Pipeline hinzugefügt:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Konvertieren Sie diese, indem [Sie die neue Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) der Anforderungs Pipeline in ihrer Klasse hinzufügen `Startup` :

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Die genaue Position in der Pipeline, an der Sie die neue Middleware einfügen, hängt von dem Ereignis ab, das als Modul ( `BeginRequest` , `EndRequest` usw.) behandelt wurde, und der Reihenfolge in der Liste der Module in *Web.config*.

Wie bereits erwähnt, gibt es keinen Anwendungslebenszyklus in ASP.net Core und die Reihenfolge, in der die Antworten von der Middleware verarbeitet werden, unterscheidet sich von der von Modulen verwendeten Reihenfolge. Dies könnte dazu führen, dass Ihre Bestell Entscheidung schwieriger wird.

Wenn die Reihenfolge zu einem Problem wird, können Sie das Modul in mehrere middlewarekomponenten aufteilen, die unabhängig voneinander angeordnet werden können.

## <a name="migrating-handler-code-to-middleware"></a>Migrieren von Handlercode zu Middleware

Ein HTTP-Handler sieht in etwa wie folgt aus:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

In Ihrem ASP.net Core Projekt würden Sie dies in eine Middleware übersetzen, die in etwa wie folgt aussieht:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Diese Middleware ähnelt der Middleware, die den Modulen entspricht. Der einzige wirkliche Unterschied besteht darin, dass hier kein-Rückruf erfolgt `_next.Invoke(context)` . Das ist sinnvoll, da sich der Handler am Ende der Anforderungs Pipeline befindet, sodass keine nächste Middleware aufgerufen werden kann.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrieren von handlereinfügung in die Anforderungs Pipeline

Das Konfigurieren eines HTTP-Handlers erfolgt in *Web.config* und sieht in etwa wie folgt aus:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Sie können dies konvertieren, indem Sie die neue handlermiddleware der Anforderungs Pipeline in ihrer Klasse hinzufügen `Startup` , ähnlich der von Modulen konvertierten Middleware. Das Problem bei diesem Ansatz besteht darin, dass alle Anforderungen an die neue handlermiddleware gesendet werden. Es ist jedoch nur gewünscht, dass Anforderungen mit einer bestimmten Erweiterung ihre Middleware erreichen. Dies bietet Ihnen die gleiche Funktionalität wie bei Ihrem HTTP-Handler.

Eine Lösung besteht darin, die Pipeline für Anforderungen mit einer bestimmten Erweiterung mithilfe der Erweiterungsmethode zu verzweigen `MapWhen` . Dies geschieht in derselben Methode, in der `Configure` Sie die andere Middleware hinzufügen:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` Diese Parameter werden von benötigt:

1. Ein Lambda, das den annimmt `HttpContext` und zurückgibt, `true` Wenn die Anforderung den Branch nach unten durchlaufen soll. Dies bedeutet, dass Sie Anforderungen verzweigen können, die nicht nur auf Grundlage ihrer Erweiterung basieren, sondern auch auf Anforderungs Headern, Abfrage Zeichen folgen Parametern usw.

2. Ein Lambda, das eine annimmt `IApplicationBuilder` und alle Middleware für den Branch hinzufügt. Dies bedeutet, dass Sie der Verzweigung vor ihrer handlermiddleware zusätzliche Middleware hinzufügen können.

Der Pipeline hinzugefügte Middleware, bevor die Verzweigung für alle Anforderungen aufgerufen wird. die Verzweigung wirkt sich nicht auf diese aus.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Laden von Middleware-Optionen mit dem Options Muster

Einige Module und Handler verfügen über Konfigurationsoptionen, die in *Web.config*gespeichert werden. In ASP.net Core jedoch anstelle *Web.config*ein neues Konfigurations Modell verwendet.

Das neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen folgende Optionen, um dieses Problem zu beheben:

* Fügen Sie die Optionen direkt in die Middleware ein, wie im [nächsten Abschnitt](#loading-middleware-options-through-direct-injection)gezeigt.

* Verwenden Sie das [options Muster](xref:fundamentals/configuration/options):

1. Erstellen Sie eine Klasse zum Speichern der Middleware-Optionen, z. b.:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Speichern der Optionswerte

   Mit dem Konfigurationssystem können Sie Optionswerte beliebig speichern. Bei den meisten Websites wird jedoch *appsettings.json*verwendet, daher wird dieser Ansatz verwendet:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *Mymiddlewareoptionssection* hier ist ein Abschnitts Name. Er muss nicht mit dem Namen der Options Klasse identisch sein.

3. Zuordnen der Optionswerte zur Options-Klasse

    Das Options Muster verwendet das Framework für die Abhängigkeitsinjektion von ASP.net Core, um den Options Typ (z. b. `MyMiddlewareOptions` ) einem- `MyMiddlewareOptions` Objekt zuzuordnen, das über die tatsächlichen Optionen verfügt.

    Aktualisieren Sie Ihre `Startup` Klasse:

   1. Wenn Sie *appsettings.json*verwenden, fügen Sie es dem Konfigurations-Generator im- `Startup` Konstruktor hinzu:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Konfigurieren Sie den Options Dienst:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Ordnen Sie die Optionen der Options-Klasse zu:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Fügen Sie die Optionen in ihren Middleware-Konstruktor ein. Dies ähnelt dem Einfügen von Optionen in einen Controller.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Die [usemiddleware](#http-modules-usemiddleware) -Erweiterungsmethode, mit der die Middleware hinzugefügt wird, `IApplicationBuilder` übernimmt die Abhängigkeitsinjektion.

   Dies ist nicht auf- `IOptions` Objekte beschränkt. Alle anderen Objekte, die von der Middleware benötigt werden, können auf diese Weise eingefügt werden.

## <a name="loading-middleware-options-through-direct-injection"></a>Laden von Middleware-Optionen durch direkte Injektion

Das Options Muster hat den Vorteil, dass es eine lose Kopplung zwischen Options Werten und ihren Consumern erzeugt. Nachdem Sie eine Options Klasse mit den tatsächlichen Options Werten verknüpft haben, kann jede andere Klasse über das Framework für die Abhängigkeitsinjektion Zugriff auf die Optionen erhalten. Es ist nicht erforderlich, Optionswerte zu übergeben.

Dies wird jedoch unterbrochen, wenn Sie dieselbe Middleware zweimal mit unterschiedlichen Optionen verwenden möchten. Beispielsweise eine Autorisierungs Middleware, die in verschiedenen Verzweigungen verwendet wird und verschiedene Rollen zulässt. Sie können der One Options-Klasse nicht zwei verschiedene Options Objekte zuordnen.

Die Lösung besteht darin, die Options Objekte mit den tatsächlichen Options Werten in Ihrer `Startup` Klasse zu erhalten und diese direkt an jede Instanz Ihrer Middleware zu übergeben.

1. Fügen Sie einen zweiten Schlüssel zum *appsettings.js* hinzu.

   Verwenden Sie zum Hinzufügen eines zweiten Satzes von Optionen zum *appsettings.js* Datei einen neuen Schlüssel, um ihn eindeutig zu identifizieren:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Rufen Sie Optionswerte ab, und übergeben Sie Sie an die Middleware. Die `Use...` Erweiterungsmethode (die die Middleware zur Pipeline hinzufügt) ist ein logischer Speicherort, um die Optionswerte zu übergeben: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Aktivieren Sie die Middleware, um einen Optionsparameter zu verwenden. Stellen Sie eine Überladung der `Use...` Erweiterungsmethode bereit (die den options-Parameter annimmt und Sie an übergibt `UseMiddleware` ). Wenn `UseMiddleware` mit Parametern aufgerufen wird, übergibt es die Parameter an Ihren Middleware-Konstruktor, wenn das Middleware-Objekt instanziiert wird.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Beachten Sie, dass dies das Options-Objekt in einem-Objekt umschließt `OptionsWrapper` . Dies implementiert `IOptions` , wie vom Middleware-Konstruktor erwartet.

## <a name="migrating-to-the-new-httpcontext"></a>Migrieren zum neuen HttpContext

Sie haben bereits gesehen, dass die- `Invoke` Methode in der Middleware einen Parameter des Typs annimmt `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` wurde in ASP.net Core erheblich geändert. In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften von [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) in den neuen übersetzt werden `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**HttpContext. Items** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Eindeutige Anforderungs-ID (kein System. Web. HttpContext-Pendant)**

Gibt Ihnen eine eindeutige ID für jede Anforderung. Sehr nützlich, um in Ihre Protokolle einzubeziehen.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext. Request

**HttpContext. Request. HttpMethod** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext. Request. QueryString** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

" **HttpContext. Request. URL** " und " **HttpContext. Request. RawUrl** " übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext. Request. IsSecureConnection** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext. Request. UserHostAddress** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext. Request. Cookie s** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext. Request. RequestContext. RouteData** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext. Request. Headers** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext. Request. UserAgent** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext. Request. UrlReferrer** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext. Request. ContentType** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext. Request. Form** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Liest Formular Werte nur, wenn der Sub-Inhaltstyp " *x-www-form-urlencoded* " oder " *Form-Data*" ist.

**HttpContext. Request. InputStream** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Verwenden Sie diesen Code nur in einer Handlertyp-Middleware am Ende einer Pipeline.
>
>Sie können den unformatierten Text nur einmal pro Anforderung lesen. Middleware, die versucht, den Text nach dem ersten Lesevorgang zu lesen, liest einen leeren Text.
>
>Dies gilt nicht für das Lesen eines Formulars wie zuvor gezeigt, da dies aus einem Puffer erfolgt.

### <a name="httpcontextresponse"></a>HttpContext. Response

" **HttpContext. Response. Status** " und " **HttpContext. Response. StatusDescription** " übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

" **HttpContext. Response. ContentEncoding** " und " **HttpContext. Response. ContentType** " übersetzen in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

" **HttpContext. Response. ContentType** " selbst übersetzt ebenfalls Folgendes:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext. Response. Output** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

Die Einrichtung einer Datei wird [hier](../fundamentals/request-features.md#middleware-and-request-features)erläutert.

**HttpContext. Response. Headers**

Das Senden von Antwort Headern ist kompliziert, wenn Sie Sie nach dem Schreiben in den Antworttext festlegen, werden Sie nicht gesendet.

Die Lösung besteht darin, eine Rückruf Methode festzulegen, die vor dem Schreiben in die Antwort direkt aufgerufen wird. Dies wird am Anfang der `Invoke` Methode in ihrer Middleware am besten erreicht. Diese Rückruf Methode legt die Antwortheader fest.

Der folgende Code legt eine Rückruf Methode namensfest `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetHeaders` Rückruf Methode würde wie folgt aussehen:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie Hymnen**

Cookies wechseln zum Browser in einem *Set-Response Cookie -* Header. Daher cookie erfordert das Senden von s denselben Rückruf wie zum Senden von Antwort Headern:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetCookies` Rückruf Methode würde wie folgt aussehen:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Übersicht über HTTP-Handler und HTTP-Module](/iis/configuration/system.webserver/)
* [Konfiguration](xref:fundamentals/configuration/index)
* [Anwendungsstart](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
