---
title: 'ASP.net Core 2,1 MVC SameSite- :::no-loc(cookie)::: Beispiel'
author: rick-anderson
description: 'ASP.net Core 2,1 MVC SameSite- :::no-loc(cookie)::: Beispiel'
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051550"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a><span data-ttu-id="fa15c-103">ASP.net Core 2,1 MVC SameSite- :::no-loc(cookie)::: Beispiel</span><span class="sxs-lookup"><span data-stu-id="fa15c-103">ASP.NET Core 2.1 MVC SameSite :::no-loc(cookie)::: sample</span></span>

<span data-ttu-id="fa15c-104">ASP.net Core 2,1 verfügt über eine integrierte Unterstützung für das [SameSite](https://www.owasp.org/index.php/SameSite) -Attribut, wurde aber in den ursprünglichen Standard geschrieben.</span><span class="sxs-lookup"><span data-stu-id="fa15c-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="fa15c-105">Das [gepatchte Verhalten](https://github.com/dotnet/aspnetcore/issues/8212) hat die Bedeutung von so geändert, dass `SameSite.None` das SameSite-Attribut mit dem Wert ausgegeben `None` wird, anstatt den Wert überhaupt auszugeben.</span><span class="sxs-lookup"><span data-stu-id="fa15c-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="fa15c-106">Wenn Sie den Wert nicht ausgeben möchten, können Sie die `SameSite` -Eigenschaft auf :::no-loc(cookie)::: -1 festlegen.</span><span class="sxs-lookup"><span data-stu-id="fa15c-106">If you want to not emit the value you can set the `SameSite` property on a :::no-loc(cookie)::: to -1.</span></span>

[!INCLUDE[](~/includes/SameSite:::no-loc(Identity):::.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="fa15c-107">Schreiben des SameSite-Attributs</span><span class="sxs-lookup"><span data-stu-id="fa15c-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="fa15c-108">Im folgenden finden Sie ein Beispiel für das Schreiben eines SameSite-Attributs für einen :::no-loc(cookie)::: :</span><span class="sxs-lookup"><span data-stu-id="fa15c-108">Following is an example of how to write a SameSite attribute on a :::no-loc(cookie)::::</span></span>

```c#
var :::no-loc(cookie):::Options = new :::no-loc(Cookie):::Options
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the :::no-loc(cookie)::: to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the :::no-loc(cookie)::: to the response :::no-loc(cookie)::: collection
Response.:::no-loc(Cookie):::s.Append(:::no-loc(Cookie):::Name, ":::no-loc(cookie):::Value", :::no-loc(cookie):::Options);
```

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a><span data-ttu-id="fa15c-109">Festlegen der :::no-loc(Cookie)::: Authentifizierung und des Sitzungs Zustands :::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="fa15c-109">Setting :::no-loc(Cookie)::: Authentication and Session State :::no-loc(cookie):::s</span></span>

<span data-ttu-id="fa15c-110">:::no-loc(Cookie)::: Authentifizierung, Sitzungs Status und [verschiedene andere Komponenten](../samesite.md?view=aspnetcore-2.1) legen ihre SameSite-Optionen über :::no-loc(Cookie)::: Optionen fest, z. b.</span><span class="sxs-lookup"><span data-stu-id="fa15c-110">:::no-loc(Cookie)::: authentication, session state and [various other components](../samesite.md?view=aspnetcore-2.1) set their sameSite options via :::no-loc(Cookie)::: options, for example</span></span>

```c#
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
    .Add:::no-loc(Cookie):::(options =>
    {
        options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
        options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
        options.:::no-loc(Cookie):::.IsEssential = true;
    });

services.AddSession(options =>
{
    options.:::no-loc(Cookie):::.SameSite = SameSiteMode.None;
    options.:::no-loc(Cookie):::.SecurePolicy = :::no-loc(Cookie):::SecurePolicy.Always;
    options.:::no-loc(Cookie):::.IsEssential = true;
});
```

<span data-ttu-id="fa15c-111">Im vorangehenden Code legen sowohl die :::no-loc(cookie)::: Authentifizierung als auch der Sitzungs Status Ihr SameSite-Attribut auf fest `None` , wobei das Attribut mit einem `None` Wert ausgegeben wird, und legen auch das Secure-Attribut auf "true" fest.</span><span class="sxs-lookup"><span data-stu-id="fa15c-111">In the preceding code, both :::no-loc(cookie)::: authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="fa15c-112">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="fa15c-112">Run the sample</span></span>

<span data-ttu-id="fa15c-113">Wenn Sie das [Beispiel Projekt](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)ausführen, laden Sie den Browser Debugger auf der Startseite, und verwenden Sie ihn, um die :::no-loc(cookie)::: Sammlung für den Standort anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="fa15c-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the :::no-loc(cookie)::: collection for the site.</span></span> <span data-ttu-id="fa15c-114">Wenn Sie dies in Edge und Chrome tun möchten, `F12` Wählen Sie die `Application` Registerkarte aus, und klicken Sie unter der `:::no-loc(Cookie):::s` Option im Abschnitt auf die URL der Website `Storage` .</span><span class="sxs-lookup"><span data-stu-id="fa15c-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `:::no-loc(Cookie):::s` option in the `Storage` section.</span></span>

![Browser Debugger::: NO-LOC (Cookie)::: List](BrowserDebugger.png)

<span data-ttu-id="fa15c-116">Sie können in der obigen Abbildung sehen, dass das :::no-loc(cookie)::: , das durch das Beispiel erstellt wird, wenn Sie auf die Schaltfläche "Create SameSite :::no-loc(Cookie)::: " klicken, über einen SameSite-Attribut Wert verfügt, der mit `Lax` dem im [Beispielcode](#sampleCode)festgelegten Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="fa15c-116">You can see from the image above that the :::no-loc(cookie)::: created by the sample when you click the "Create SameSite :::no-loc(Cookie):::" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-no-loccookies"></a><a name="interception"></a><span data-ttu-id="fa15c-117">Abfangen von :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="fa15c-117">Intercepting :::no-loc(cookie):::s</span></span>

<span data-ttu-id="fa15c-118">Um s abzufangen :::no-loc(cookie)::: , muss die Middleware verwendet werden, um den Wert None gemäß der Unterstützung im Browser-Agent des Benutzers anzupassen `:::no-loc(Cookie):::Policy` .</span><span class="sxs-lookup"><span data-stu-id="fa15c-118">In order to intercept :::no-loc(cookie):::s, to adjust the none value according to its support in the user's browser agent you must use the `:::no-loc(Cookie):::Policy` middleware.</span></span> <span data-ttu-id="fa15c-119">Diese muss in der HTTP-Anforderungs Pipeline **vor** allen Komponenten platziert werden, die :::no-loc(cookie)::: in schreiben und in konfiguriert sind `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="fa15c-119">This must be placed into the http request pipeline **before** any components that write :::no-loc(cookie):::s and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="fa15c-120">Um Sie in die Pipeline einzufügen, verwenden Sie in `app.Use:::no-loc(Cookie):::Policy()` der- `Configure(IApplicationBuilder, IHostingEnvironment)` Methode in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span><span class="sxs-lookup"><span data-stu-id="fa15c-120">To insert it into the pipeline use `app.Use:::no-loc(Cookie):::Policy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="fa15c-121">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fa15c-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.Use:::no-loc(Cookie):::Policy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="fa15c-122">Anschließend können Sie in der `ConfigureServices(IServiceCollection services)` :::no-loc(cookie)::: Richtlinie konfigurieren, um eine Hilfsklasse aufzurufen, wenn e angefügt :::no-loc(cookie)::: oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="fa15c-122">Then in the `ConfigureServices(IServiceCollection services)` configure the :::no-loc(cookie)::: policy to call out to a helper class when :::no-loc(cookie):::s are appended or deleted.</span></span> <span data-ttu-id="fa15c-123">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fa15c-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<:::no-loc(Cookie):::PolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppend:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
        options.OnDelete:::no-loc(Cookie)::: = :::no-loc(cookie):::Context =>
            CheckSameSite(:::no-loc(cookie):::Context.Context, :::no-loc(cookie):::Context.:::no-loc(Cookie):::Options);
    });
}

private void CheckSameSite(HttpContext httpContext, :::no-loc(Cookie):::Options options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="fa15c-124">Die Hilfsfunktion `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)` :</span><span class="sxs-lookup"><span data-stu-id="fa15c-124">The helper function `CheckSameSite(HttpContext, :::no-loc(Cookie):::Options)`:</span></span>

* <span data-ttu-id="fa15c-125">Wird aufgerufen :::no-loc(cookie)::: , wenn e an die Anforderung angefügt oder aus der Anforderung gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="fa15c-125">Is called when :::no-loc(cookie):::s are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="fa15c-126">Prüft, ob die- `SameSite` Eigenschaft auf festgelegt ist `None` .</span><span class="sxs-lookup"><span data-stu-id="fa15c-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="fa15c-127">Wenn `SameSite` auf festgelegt ist `None` und der aktuelle Benutzer-Agent bekanntermaßen den None-Attribut Wert nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fa15c-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="fa15c-128">Die Überprüfung erfolgt mithilfe der [samesitesupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) -Klasse:</span><span class="sxs-lookup"><span data-stu-id="fa15c-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="fa15c-129">Legt `SameSite` fest, dass der Wert nicht durch Festlegen der-Eigenschaft auf ausgegeben wird. `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="fa15c-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="fa15c-130">Ziel .NET Framework</span><span class="sxs-lookup"><span data-stu-id="fa15c-130">Targeting .NET Framework</span></span>

<span data-ttu-id="fa15c-131">ASP.net Core und System. Web (ASP.net Classic) verfügen über unabhängige Implementierungen von "SameSite".</span><span class="sxs-lookup"><span data-stu-id="fa15c-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="fa15c-132">Die SameSite-KB-Patches für .NET Framework sind nicht erforderlich, wenn Sie ASP.net Core oder die System. Web SameSite-Mindestanforderungen (.NET 4.7.2) auf ASP.net Core anwenden.</span><span class="sxs-lookup"><span data-stu-id="fa15c-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="fa15c-133">ASP.net Core auf .net erfordert das Aktualisieren von nuget-Paketabhängigkeiten, um die entsprechenden Korrekturen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="fa15c-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="fa15c-134">Um die ASP.net Core Änderungen für .NET Framework zu erhalten, stellen Sie sicher, dass Sie über einen direkten Verweis auf die gepatchten Pakete und Versionen (2.1.14 oder höher 2,1 Versionen) verfügen.</span><span class="sxs-lookup"><span data-stu-id="fa15c-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.:::no-loc(Cookie):::Policy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="fa15c-135">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="fa15c-135">More Information</span></span>
 
<span data-ttu-id="fa15c-136">[Chrome-Updates](https://www.chromium.org/updates/same-site) 
 Dokumentation zu ASP.net Core [SameSite](../samesite.md?view=aspnetcore-2.1) 
 [ASP.net Core 2,1 SameSite-Änderungs Ankündigung](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="fa15c-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](../samesite.md?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>