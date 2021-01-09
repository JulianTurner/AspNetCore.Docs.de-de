---
title: Fehlerbehandlung in ASP.NET Core-Web-APIs
author: pranavkm
description: Erfahren Sie, wie Sie Fehler in ASP.NET Core-Web-APIs beheben können.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058375"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>Fehlerbehandlung in ASP.NET Core-Web-APIs

Dieser Artikel beschreibt die Verarbeitung und Anpassung der Fehlerbehandlung bei ASP.NET Core-Web-APIs.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) (Vorgehens[Weise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Seite mit Ausnahmen für Entwickler

Die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) ist ein nützliches Tool, um detaillierte Stapelüberwachungen für Serverfehler zu erhalten. Es verwendet <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware>, um synchrone und asynchrone Ausnahmen aus der HTTP-Pipeline zu erfassen und um Fehlerantworten zu generieren. Betrachten Sie zur Darstellung die folgende Controlleraktion:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Führen Sie den folgenden `curl`-Befehl aus, um die vorherige Aktion zu testen:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

Die Seite mit Ausnahmen für Entwickler zeigt in ASP.NET Core 3.0 und später eine Nur-Text-Antwort an, wenn der Client keine HTML-formatierte Ausgabe anfordert. Die folgende Ausgabe wird angezeigt:

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

Legen Sie den HTTP-Anforderungsheader `Accept` auf den Medientyp `text/html` fest, um stattdessen eine HTML-formatierte Antwort anzuzeigen. Beispiel:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

Beachten Sie den folgenden Auszug aus der HTTP-Antwort:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

In ASP.NET Core 2.2 und früher wird auf der Seite mit Ausnahmen für Entwickler eine HTML-formatierte Antwort angezeigt. Beachten Sie beispielsweise den folgenden Auszug aus der HTTP-Antwort:

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

Die HTML-formatierte Antwort wird beim Durchführen von Tests in Tools wie etwa Postman nützlich. Auf der folgenden Bildschirmaufnahme sind jeweils die in Klartext verfassten und die HTML-formatierte Antworten in Postman dargestellt:

![Testen auf der Seite mit Ausnahmen für Entwickler in Postman](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**. Geben Sie ausführliche Ausnahme Informationen nicht öffentlich frei, wenn die app in der Produktionsumgebung ausgeführt wird. Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.
>
> Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`. Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Aktionsmethode zum Einsatz kommt. Hiermit wird der anonyme Zugriff auf die-Methode zugelassen, wenn nicht authentifizierte Benutzer den Fehler sehen sollen.

## <a name="exception-handler"></a>Ausnahmehandler

In nicht zur Entwicklung dienenden Umgebungen kann [Middleware zur Ausnahmebehandlung](xref:fundamentals/error-handling) verwendet werden, um eine Fehlernutzlast zu erzeugen:

1. Rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> in `Startup.Configure` auf, um die Middleware zu verwenden:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Konfigurieren Sie eine Controlleraktion, um auf die `/error`-Route zu antworten:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Die oben stehende `Error`-Aktion sendet eine mit [RFC 7807](https://tools.ietf.org/html/rfc7807) konforme Nutzlast an den Client.

Middleware zur Ausnahmebehandlung kann auch eine detailliertere Ausgabe der Inhaltsaushandlung in der lokalen Entwicklungsumgebung bieten. Führen Sie die folgenden Schritte aus, um für ein konsistentes Nutzlastformat in Entwicklungs- und Produktionsumgebungen zu sorgen:

1. Registrieren Sie umgebungsspezifische Middleware zur Ausnahmebehandlung in `Startup.Configure`:

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    Im oben stehenden Code ist die Middleware mit Folgendem registriert:

    * Route `/error-local-development` in der Entwicklungsumgebung.
    * Route `/error` in einer nicht zur Entwicklung dienenden Umgebung.
    
1. Wenden Sie das Attributrouting auf Controlleraktionen an:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    Der vorangehende Code ruft [controllerbase. Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) auf, um eine Antwort zu erstellen <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .

## <a name="use-exceptions-to-modify-the-response"></a>Verwenden von Ausnahmen zum Ändern der Antwort

Der Inhalt der Antwort kann außerhalb des Controllers geändert werden. In der ASP.NET 4.x-Web-API war die einzige Möglichkeit hierfür die Verwendung des Typs <xref:System.Web.Http.HttpResponseException>. ASP.NET Core enthält keinen entsprechenden Typ. Die Unterstützung für `HttpResponseException` kann mit folgenden Schritten hinzugefügt werden:

1. Erstellen Sie einen bekannten Ausnahmetyp namens `HttpResponseException`:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Erstellen Sie einen Aktionsfilter namens `HttpResponseExceptionFilter`:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    Der vorangehende Filter gibt einen `Order` des maximalen ganzzahligen Werts minus 10 an. Dies ermöglicht das Ausführen anderer Filter am Ende der Pipeline.

1. Fügen Sie den Aktionsfilter in `Startup.ConfigureServices` der Filtersammlung hinzu:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Antwort auf Überprüfungsfehler

Bei Web-API-Controllern antwortet MVC mit dem Antworttyp <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>, wenn bei der Modellüberprüfung ein Fehler auftritt. MVC verwendet die Ergebnisse von <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>, um die Fehlerantwort auf einen Überprüfungsfehler zu erstellen. Das folgende Beispiel verwendet die Factory, um den Standardantworttyp von <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices` zu ändern:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>Fehlerantwort des Clients

Ein *Fehlerergebnis* ist als Ergebnis mit dem HTTP-Statuscode 400 oder höher definiert. Bei Web-API-Controllern transformiert MVC ein Fehlerergebnis in ein Ergebnis mit <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2.1 generiert eine Antwort mit Details zum Problem, die nahezu mit RFC 7807 konform ist. Wenn eine 100-prozentige Konformität wichtig ist, führen Sie ein Upgrade des Projekts auf ASP.NET Core 2.2 oder höher durch.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Die Fehlerantwort kann auf eine der folgenden Arten konfiguriert werden:

1. [Implementieren der ProblemDetailsFactory](#implement-problemdetailsfactory)
1. [Verwenden von ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Implementieren Sie `ProblemDetailsFactory`.

MVC verwendet die <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName>, um alle Instanzen von <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> und <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zu generieren. Hierzu gehören Clientfehlerantworten, Antworten auf Überprüfungsfehler sowie die Hilfsmethoden <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType>.

Um die Antwort mit Problemdetails anzupassen, registrieren Sie eine benutzerdefinierte Implementierung von <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Die Fehlerantwort kann so konfiguriert werden wie im Abschnitt [Verwenden von ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) beschrieben.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>Verwenden von ApiBehaviorOptions.ClientErrorMapping

Verwenden Sie die <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>-Eigenschaft zum Konfigurieren des Inhalts der `ProblemDetails`-Antwort. Der folgende Code in `Startup.ConfigureServices` aktualisiert beispielsweise die `type`-Eigenschaft für 404-Antworten:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a>Benutzerdefinierte Middleware zum Behandeln von Ausnahmen

Die Standardwerte in der Middleware für die Ausnahmebehandlung funktionieren für die meisten apps gut. Bei apps, für die eine spezielle Ausnahmebehandlung erforderlich ist, sollten Sie [die Middleware für die Ausnahmebehandlung anpassen](xref:fundamentals/error-handling#exception-handler-lambda).
