---
title: Zugreifen auf HttpContext in ASP.NET Core
author: coderandhiker
description: Anleitung zum Zugreifen auf HttpContext in ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
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
uid: fundamentals/httpcontext
ms.openlocfilehash: d5ada72bb03cd74cf77c31450e5eafb9c2841049
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506993"
---
# <a name="access-httpcontext-in-aspnet-core"></a>Zugreifen auf HttpContext in ASP.NET Core

ASP.NET Core-Apps greifen über die <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>-Schnittstelle und ihrer Standardimplementierung <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> auf `HttpContext` zu. `IHttpContextAccessor` muss nur verwendet werden, wenn Sie auf `HttpContext` innerhalb eines Diensts zugreifen müssen.

## <a name="use-httpcontext-from-no-locrazor-pages"></a>Verwenden von HttpContext über Razor Pages

Das <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages stellt die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>-Eigenschaft zur Verfügung:

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-no-locrazor-view"></a>Verwenden von HttpContext über eine Razor-Ansicht

Razor-Ansichten machen `HttpContext` direkt über die [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context)-Eigenschaft in der Ansicht verfügbar. Im folgenden Beispiel wird der aktuelle Benutzername in einer Intranet-App abgerufen, die die Windows-Authentifizierung verwendet:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Verwenden von HttpContext über einen Controller

Controller zeigen die Eigenschaft [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) an:

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Verwenden von HttpContext über Middleware

Bei der Verwendung benutzerdefinierter Middlewarekomponenten wird `HttpContext` an die Methode `Invoke` oder `InvokeAsync` übergeben. Wenn die Middleware konfiguriert ist, kann darauf zugegriffen werden:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Verwenden von HttpContext über benutzerdefinierte Komponenten

Für andere Framework- und benutzerdefinierte Komponenten, die Zugriff auf `HttpContext` erfordern, wird empfohlen, eine Abhängigkeit mithilfe des integrierten Containers [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zu registrieren. Der Abhängigkeitsinjektionscontainer stellt `IHttpContextAccessor` allen Klassen zur Verfügung, die ihn in ihren Konstruktoren als Abhängigkeit deklarieren:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

Im folgenden Beispiel:

* `UserRepository` deklariert seine Abhängigkeit von `IHttpContextAccessor`.
* Die Abhängigkeit wird bereitgestellt, wenn die Abhängigkeitsinjektion die Abhängigkeitskette auflöst und eine `UserRepository`-Instanz erstellt.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>HttpContext-Zugriff von einem Hintergrundthread aus

`HttpContext` ist nicht threadsicher. Eigenschaften von `HttpContext` außerhalb der Verarbeitung einer Anforderung zu lesen oder zu schreiben kann zu einer <xref:System.NullReferenceException> führen.

> [!NOTE]
> Wenn Ihre App sporadische `NullReferenceException`-Fehler erzeugt, überprüfen Sie die Teile des Codes, die die Hintergrundverarbeitung starten oder die Verarbeitung fortsetzen, nachdem eine Anforderung erfüllt wurde. Suchen Sie nach Fehlern, wie das Definieren einer Controllermethode als `async void`.

So werden Hintergrundaufgaben mit `HttpContext`-Daten sicher ausgeführt:

* Kopieren Sie die benötigten Daten während der Verarbeitung der Anforderung.
* Übergeben Sie die kopierten Daten an eine Hintergrundaufgabe.

Übergeben Sie `HttpContext` niemals an eine Methode, die Hintergrundaufgaben ausführt, um unsicheren Code zu vermeiden. Übergeben Sie stattdessen die erforderlichen Daten. Im folgenden Beispiel wird `SendEmailCore` aufgerufen, um mit dem Senden einer E-Mail zu beginnen. `correlationId` wird an `SendEmailCore` übergeben, nicht an `HttpContext`. Die Codeausführung wartet nicht auf den Abschluss von `SendEmailCore`:

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="no-locblazor-and-shared-state"></a>Blazor und freigegebener Zustand

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
