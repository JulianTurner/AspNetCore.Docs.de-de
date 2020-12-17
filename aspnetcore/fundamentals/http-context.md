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
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506993"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="2a0e8-103">Zugreifen auf HttpContext in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a0e8-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="2a0e8-104">ASP.NET Core-Apps greifen über die <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>-Schnittstelle und ihrer Standardimplementierung <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> auf `HttpContext` zu.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="2a0e8-105">`IHttpContextAccessor` muss nur verwendet werden, wenn Sie auf `HttpContext` innerhalb eines Diensts zugreifen müssen.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="2a0e8-106">Verwenden von HttpContext über Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2a0e8-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="2a0e8-107">Das <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages stellt die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext>-Eigenschaft zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="2a0e8-108">Verwenden von HttpContext über eine Razor-Ansicht</span><span class="sxs-lookup"><span data-stu-id="2a0e8-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="2a0e8-109">Razor-Ansichten machen `HttpContext` direkt über die [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context)-Eigenschaft in der Ansicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="2a0e8-110">Im folgenden Beispiel wird der aktuelle Benutzername in einer Intranet-App abgerufen, die die Windows-Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="2a0e8-111">Verwenden von HttpContext über einen Controller</span><span class="sxs-lookup"><span data-stu-id="2a0e8-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="2a0e8-112">Controller zeigen die Eigenschaft [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) an:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="2a0e8-113">Verwenden von HttpContext über Middleware</span><span class="sxs-lookup"><span data-stu-id="2a0e8-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="2a0e8-114">Bei der Verwendung benutzerdefinierter Middlewarekomponenten wird `HttpContext` an die Methode `Invoke` oder `InvokeAsync` übergeben. Wenn die Middleware konfiguriert ist, kann darauf zugegriffen werden:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="2a0e8-115">Verwenden von HttpContext über benutzerdefinierte Komponenten</span><span class="sxs-lookup"><span data-stu-id="2a0e8-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="2a0e8-116">Für andere Framework- und benutzerdefinierte Komponenten, die Zugriff auf `HttpContext` erfordern, wird empfohlen, eine Abhängigkeit mithilfe des integrierten Containers [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="2a0e8-117">Der Abhängigkeitsinjektionscontainer stellt `IHttpContextAccessor` allen Klassen zur Verfügung, die ihn in ihren Konstruktoren als Abhängigkeit deklarieren:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="2a0e8-118">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-118">In the following example:</span></span>

* <span data-ttu-id="2a0e8-119">`UserRepository` deklariert seine Abhängigkeit von `IHttpContextAccessor`.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="2a0e8-120">Die Abhängigkeit wird bereitgestellt, wenn die Abhängigkeitsinjektion die Abhängigkeitskette auflöst und eine `UserRepository`-Instanz erstellt.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="2a0e8-121">HttpContext-Zugriff von einem Hintergrundthread aus</span><span class="sxs-lookup"><span data-stu-id="2a0e8-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="2a0e8-122">`HttpContext` ist nicht threadsicher.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="2a0e8-123">Eigenschaften von `HttpContext` außerhalb der Verarbeitung einer Anforderung zu lesen oder zu schreiben kann zu einer <xref:System.NullReferenceException> führen.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="2a0e8-124">Wenn Ihre App sporadische `NullReferenceException`-Fehler erzeugt, überprüfen Sie die Teile des Codes, die die Hintergrundverarbeitung starten oder die Verarbeitung fortsetzen, nachdem eine Anforderung erfüllt wurde.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="2a0e8-125">Suchen Sie nach Fehlern, wie das Definieren einer Controllermethode als `async void`.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="2a0e8-126">So werden Hintergrundaufgaben mit `HttpContext`-Daten sicher ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="2a0e8-127">Kopieren Sie die benötigten Daten während der Verarbeitung der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="2a0e8-128">Übergeben Sie die kopierten Daten an eine Hintergrundaufgabe.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="2a0e8-129">Übergeben Sie `HttpContext` niemals an eine Methode, die Hintergrundaufgaben ausführt, um unsicheren Code zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="2a0e8-130">Übergeben Sie stattdessen die erforderlichen Daten.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-130">Pass the required data instead.</span></span> <span data-ttu-id="2a0e8-131">Im folgenden Beispiel wird `SendEmailCore` aufgerufen, um mit dem Senden einer E-Mail zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="2a0e8-132">`correlationId` wird an `SendEmailCore` übergeben, nicht an `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="2a0e8-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="2a0e8-133">Die Codeausführung wartet nicht auf den Abschluss von `SendEmailCore`:</span><span class="sxs-lookup"><span data-stu-id="2a0e8-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="2a0e8-134">Blazor und freigegebener Zustand</span><span class="sxs-lookup"><span data-stu-id="2a0e8-134">Blazor and shared state</span></span>

[!INCLUDE[](~/blazor/includes/security/blazor-shared-state.md)]
