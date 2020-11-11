---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060468"
---
# <a name="handle-errors-in-aspnet-core"></a>Fehlerbehandlung in ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Von [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps. Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Die Registerkarte „Netzwerk“ in den F12-Entwicklertools im Browser ist beim Testen der Beispiel-App nützlich.

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen. Die ASP.NET Core-Vorlagen generieren den folgenden Code:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

Der vorangehende hervorgehobene Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.

Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> am Anfang der Middlewarepipeline, sodass die in der nachfolgenden Middleware ausgelösten Ausnahmen abgefangen werden.

Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler **nur** dann, wenn die App in der Entwicklungsumgebung ausgeführt wird. Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.

Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:

Stapelüberwachung
* Abfragezeichenfolgeparameter, sofern vorhanden
* Cookies, sofern vorhanden
* Header

## <a name="exception-handler-page"></a>Seite „Ausnahmehandler“

Um eine benutzerdefinierte Fehlerbehandlungsseite für die [Produktionsumgebung](xref:fundamentals/environments) zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> auf. Die Aufgaben der Middleware zur Ausnahmebehandlung:

* Dient zum Abfangen und Protokollieren von Ausnahmen.
* Führt erneut die Anforderung in einer anderen Pipeline im angegebenen Pfad aus. Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde. Der von der Vorlage generierte Code führt die Anforderung erneut mit dem `/Error`-Pfad aus.

Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml* ) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit. Die Projektvorlage einer MVC-App enthält die Aktionsmethode `Error` und die Ansicht „Error“ im Home-Controller.

Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Aktionsmethode zum Einsatz kommt. Lassen Sie den anonymen Zugriff auf die Methode zu, wenn nicht authentifizierten Benutzern die Fehleransicht angezeigt werden soll.

### <a name="access-the-exception"></a>Zugreifen auf die Ausnahme

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Fehlerhandler zuzugreifen. Der folgende Code fügt dem Standard *Pages/Error.cshtml.cs* die von den ASP.NET Core-Vorlagen generierte `ExceptionMessage` hinzu:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

So testen Sie die Ausnahme in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)

* Legen Sie die Umgebung auf Produktion fest.
* Entfernen Sie die Kommentare aus `webBuilder.UseStartup<Startup>();` in *Program.cs*.
* Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.

## <a name="exception-handler-lambda"></a>Lambda-Ausdruck für Ausnahmehandler

Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.

Der folgende Code verwendet einen Lambdaausdruck für die Ausnahmebehandlung:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

So testen Sie den Lambdaausdruck für die Ausnahmebehandlung in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)

* Legen Sie die Umgebung auf Produktion fest.
* Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.
* Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Die ASP.NET Core-App stellt für HTTP-Fehlerstatuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung. Wenn eine App eine HTTP-Fehlerbedingung zwischen 400–499 feststellt, die keinen Text enthält, werden der Statuscode und ein leerer Antworttext zurückgegeben. Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten. Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

Rufen Sie `UseStatusCodePages` vor der Middleware für die Anforderungsverarbeitung auf. Rufen Sie beispielsweise `UseStatusCodePages` vor der Middleware für statische Dateien und der Middleware für Endpunkte auf.

Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann. Navigieren Sie zum Beispiel zu `Home/Privacy2`. Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.

So testen Sie `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)

* Legen Sie die Umgebung auf Produktion fest.
* Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.
* Klicken Sie auf die Links auf der Startseite.

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages mit Formatzeichenfolge

Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

Im vorangehenden Code ist `{0}` ein Platzhalter für den Fehlercode.

`UseStatusCodePages` mit Formatzeichenfolge wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.

Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages mit Lambda-Ausdruck

Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` mit Lambdaausdruck wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.

Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Sendet den Statuscode [302 Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (Gefunden) an den Client.
* Leitet den Client an den in der URL-Vorlage angegebenen Fehlerbehandlungsendpunkt weiter. Der Fehlerbehandlungsendpunkt zeigt in der Regel Fehlerinformationen an und gibt den HTTP-Code 200 zurück.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

Die URL-Vorlage kann, wie im vorangehenden Code gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten. Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt. Wenn Sie in der App einen Endpunkt angeben, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen. Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:

* Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet. Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.

Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Gibt den ursprünglichen Statuscode an den Client zurück.
* Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Wenn in der App ein Endpunkt angegeben ist, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen. Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann. Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:

* Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten. Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.

Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten. Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.

## <a name="disable-status-code-pages"></a>Deaktivieren von Statuscodeseiten

Verwenden Sie das Attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Ausnahmebehandlungscode

Code auf Seiten zur Ausnahmebehandlung kann ebenfalls Ausnahmen auslösen. Produktionsfehlerseiten sollten gründlich getestet werden. Achten Sie darauf, dabei keine eigenen Ausnahmen auszulösen.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Antwortheader

Nachdem die Header für eine Antwort gesendet wurden:

* Die App kann den Statuscode der Antwort nicht ändern.
* Weder Ausnahmeseiten noch Handler können ausgeführt werden. Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.

## <a name="server-exception-handling"></a>Sichere Ausnahmebehandlung

Neben der Ausnahmebehandlungslogik in einer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen. Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort `500 - Internal Server Error` ohne Antworttext. Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung. Anforderungen, die nicht von der App verarbeitet werden, werden vom Server verarbeitet. Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet. Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.

## <a name="startup-exception-handling"></a>Fehlerbehandlung während des Starts

Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten. Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.

Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt. Wenn die Bindung misslingt:

* Die Hostebene protokolliert eine kritische Ausnahme.
* Der DotNet-Prozess stürzt ab.
* Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.

Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Datenbank-Fehlerseite

Der Ausnahmefilter der Datenbankentwicklerseite `AddDatabaseDeveloperPageExceptionFilter` erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework Core-Migrationen aufgelöst werden können. Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert. Diese Seite ist nur in der Entwicklungsumgebung aktiviert. Der folgende Code wurde von den ASP.NET Core Razor Pages-Vorlagen generiert, als einzelne Benutzerkonten angegeben wurden:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Ausnahmefilter

In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden. In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden. Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt. Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.

Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen ausgelöst werden. Sie sind jedoch nicht so flexibel wie die integrierte [Middleware für die Ausnahmebehandlung](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`. Es wird empfohlen, `UseExceptionHandler` zu verwenden, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie auswählen müssen.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Modellstatusfehler

Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps. Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).)

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen. Die ASP.NET Core-Vorlagen generieren den folgenden Code:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.

Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> vor jeder Middleware, sodass Ausnahmen in der nachfolgenden Middleware abgefangen werden.

Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler nur dann, wenn die App in der **Entwicklungsumgebung** ausgeführt wird. Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.

Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:

* Stapelüberwachung
* Abfragezeichenfolgeparameter, sofern vorhanden
* Cookies, sofern vorhanden
* Header

## <a name="exception-handler-page"></a>Seite „Ausnahmehandler“

Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung. Die Middleware:

* Dient zum Abfangen und Protokollieren von Ausnahmen.
* Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus. Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde. Der von der Vorlage generierte Code führt die Anforderung erneut mit `/Error` aus.

Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml* ) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit. Die Projektvorlage einer MVC-App enthält eine Fehleraktionsmethode und die Ansicht „Fehler“ im Home-Controller.

Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt. Lassen Sie den anonymen Zugriff auf die Methode zu, wenn nicht authentifizierten Benutzern die Fehleransicht angezeigt werden soll.

### <a name="access-the-exception"></a>Zugreifen auf die Ausnahme

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

Legen Sie die Umgebung auf Produktionen fest, und erzwingen Sie eine Ausnahme, um die vorherige Seite zur Ausnahmebehandlung zu aktivieren.

## <a name="exception-handler-lambda"></a>Lambda-Ausdruck für Ausnahmehandler

Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>. Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.

Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

Im vorangehenden Code wird `await context.Response.WriteAsync(new string(' ', 512));` hinzugefügt, sodass der Internet Explorer-Browser die Fehlermeldung anstelle einer IE-Fehlermeldung anzeigt. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung. Die App gibt einen Statuscode und einen leeren Antworttext zurück. Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.

Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt.

Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).

Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann. Navigieren Sie zum Beispiel zu `Home/Privacy2`. Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages mit Formatzeichenfolge

Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages mit Lambda-Ausdruck

Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:

* Sendet den Statuscode *302 Found* (Gefunden) an den Client.
* Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten. Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt. Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen. Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:

* Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet. Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:

* Gibt den ursprünglichen Statuscode an den Client zurück.
* Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen. Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann. Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:

* Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten. Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.
* Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.

Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten. Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen. Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann. Eine Razor Page für Fehler sollte z. B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:

```cshtml
@page "{code?}"
```

Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Deaktivieren von Statuscodeseiten

Verwenden Sie das Attribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Ausnahmebehandlungscode

Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen. Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.

### <a name="response-headers"></a>Antwortheader

Nachdem die Header für eine Antwort gesendet wurden:

* Die App kann den Statuscode der Antwort nicht ändern.
* Weder Ausnahmeseiten noch Handler können ausgeführt werden. Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.

## <a name="server-exception-handling"></a>Sichere Ausnahmebehandlung

Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen. Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext. Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung. Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet. Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet. Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.

## <a name="startup-exception-handling"></a>Fehlerbehandlung während des Starts

Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten. Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.

Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt. Wenn die Bindung misslingt:

* Die Hostebene protokolliert eine kritische Ausnahme.
* Der DotNet-Prozess stürzt ab.
* Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.

Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Datenbank-Fehlerseite

Die Middleware „Datenbank-Fehlerseite“ erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können. Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert. Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden. Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> benötigt das [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/)-NuGet-Paket.

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Ausnahmefilter

In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden. In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden. Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt. Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung. Es wird empfohlen, die Middleware zu verwenden. Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.

## <a name="model-state-errors"></a>Modellstatusfehler

Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
