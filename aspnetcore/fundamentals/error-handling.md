---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: a1f40bdcdd4f2472aa86b311bfd9302e6aa8adc0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635098"
---
# <a name="handle-errors-in-aspnet-core"></a>Fehlerbehandlung in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps. Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Der Artikel enthält Anweisungen zum Festlegen von Präprozessordirektiven (`#if`, `#endif`, `#define`) in der Beispiel-App, um verschiedene Szenarien zu ermöglichen.

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen. Die Seite wird von der `Microsoft.AspNetCore.Diagnostics`-Assembly bereitgestellt, die sich im [`Microsoft.AspNetCore.App` gemeinsam verwendeten Framework](xref:fundamentals/metapackage-app) befindet. Fügen Sie der `Startup.Configure`-Methode Code zum Aktivieren der Seite hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> vor Middleware an der Stelle ein, an der Sie Ausnahmen abfangen möchten.

> [!WARNING]
> Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**. Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.

Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:

* Stapelüberwachung
* Abfragezeichenfolgeparameter (sofern vorhanden)
* Cookies (ggf.)
* Header

Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite mit Ausnahmen für Entwickler anzuzeigen, verwenden Sie die Präprozessordirektive `DevEnvironment`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.

## <a name="exception-handler-page"></a>Seite „Ausnahmehandler“

Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung. Die Middleware:

* Dient zum Abfangen und Protokollieren von Ausnahmen.
* Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus. Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.

Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit. Für eine MVC-App enthält die Projektvorlage die Aktionsmethode „Error“ und die Ansicht „Error“. Es folgt die Aktionsmethode:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt. Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.

### <a name="access-the-exception"></a>Zugreifen auf die Ausnahme

Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit. Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.

Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerPage`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.

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

Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.

Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).

Hier ist ein Beispiel des von den Standardhandlern angezeigten Texts:

```
Status Code: 404; Not Found
```

Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) eines der verschiedenen Formate für Statuscodeseiten anzuzeigen, verwenden Sie eine der Präprozessordirektiven, die mit `StatusCodePages` beginnen, und wählen Sie auf der Startseite **404 auslösen** aus.

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

Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten. Wenn die URL-Vorlage mit einer Tilde (~) beginnt, wird die Tilde durch die Angabe für `PathBase` der App ersetzt. Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen. Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

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
