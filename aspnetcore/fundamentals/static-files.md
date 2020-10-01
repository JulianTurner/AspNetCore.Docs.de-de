---
title: Statische Dateien in ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie, wie statische Dateien bereitgestellt und gesichert werden und wie das Verhalten von Middleware beim Hosting statischer Dateien in einer ASP.NET Core-Web-App konfiguriert wird.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 36eeb3532ed5ea6f5f75c20cbe14331ed840a788
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393937"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="b08b9-103">Statische Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b08b9-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b08b9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b08b9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="b08b9-105">Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App standardmäßig direkt bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="b08b9-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b08b9-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="b08b9-107">Bereitstellen statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="b08b9-107">Serve static files</span></span>

<span data-ttu-id="b08b9-108">Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="b08b9-109">Das Standardverzeichnis lautet `{content root}/wwwroot`, kann jedoch mit der Methode <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> geändert werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="b08b9-110">Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="b08b9-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="b08b9-111">Die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:</span><span class="sxs-lookup"><span data-stu-id="b08b9-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="b08b9-112">Der vorangehende Code wurde mit der Web-App-Vorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="b08b9-113">Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="b08b9-114">Die **Webanwendung**-Projektvorlagen verfügen beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="b08b9-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="b08b9-115">Erstellen Sie ggf. den *wwwroot/images*-Ordner, und fügen Sie die Datei *wwwroot/images/MyImage. pg* hinzu.</span><span class="sxs-lookup"><span data-stu-id="b08b9-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="b08b9-116">Das URI-Format für den Zugriff auf eine Datei im `images`-Ordner ist `https://<hostname>/images/<image_file_name>`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="b08b9-117">Beispiel: `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="b08b9-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="b08b9-118">Verarbeiten von Dateien im Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="b08b9-118">Serve files in web root</span></span>

<span data-ttu-id="b08b9-119">Mit den Standard-Webappvorlagen wird die <xref:Owin.StaticFileExtensions.UseStaticFiles%2A>-Methode in `Startup.Configure` aufgerufen, die die Verarbeitung statischer Dateien ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="b08b9-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="b08b9-120">Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar.</span><span class="sxs-lookup"><span data-stu-id="b08b9-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="b08b9-121">Folgendes Markup verweist auf *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="b08b9-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="b08b9-122">Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="b08b9-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="b08b9-123">Bereitstellen von Dateien außerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="b08b9-123">Serve files outside of web root</span></span>

<span data-ttu-id="b08b9-124">Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:</span><span class="sxs-lookup"><span data-stu-id="b08b9-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="b08b9-125">Über eine Anforderung kann auf die Datei `red-rose.jpg` zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="b08b9-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="b08b9-126">Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b08b9-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="b08b9-127">Eine Anforderung an `https://<hostname>/StaticFiles/images/red-rose.jpg` dient der *red-rose.jpg*-Datei.</span><span class="sxs-lookup"><span data-stu-id="b08b9-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="b08b9-128">Folgendes Markup verweist auf *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="b08b9-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="b08b9-129">Festlegen von HTTP-Antwortheadern</span><span class="sxs-lookup"><span data-stu-id="b08b9-129">Set HTTP response headers</span></span>

<span data-ttu-id="b08b9-130">Mit einem <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>-Objekt können HTTP-Antwortheader festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="b08b9-131">Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b08b9-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="b08b9-132">Statische Dateien können 600 Sekunden lang öffentlich zwischengespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="b08b9-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="b08b9-134">Autorisierung statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="b08b9-134">Static file authorization</span></span>

<span data-ttu-id="b08b9-135">Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="b08b9-136">Alle über die Middleware bereitgestellten Dateien, Dateien unter `wwwroot` inbegriffen, sind öffentlich zugänglich.</span><span class="sxs-lookup"><span data-stu-id="b08b9-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="b08b9-137">So stellen Sie Dateien basierend auf der Autorisierung bereit:</span><span class="sxs-lookup"><span data-stu-id="b08b9-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="b08b9-138">Speichern Sie sie außerhalb von `wwwroot` sowie außerhalb sämtlicher Verzeichnisse, auf die die Standardmiddleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="b08b9-138">Store them outside of `wwwroot` and any directory accessible to the default Static File Middleware.</span></span>
* <span data-ttu-id="b08b9-139">Rufen Sie `UseStaticFiles` nach `UseAuthorization` auf, und geben Sie den Pfad an:</span><span class="sxs-lookup"><span data-stu-id="b08b9-139">Call `UseStaticFiles` after `UseAuthorization` and specify the path:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  <span data-ttu-id="b08b9-140">Der Ansatz oben erfordert, dass Benutzer authentifiziert werden:</span><span class="sxs-lookup"><span data-stu-id="b08b9-140">The preceding approach requires users to be authenticated:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="b08b9-141">Eine Alternative zum Bereitstellen von Dateien basierend auf Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="b08b9-141">An alternative approach to serve files based on authorization:</span></span>

* <span data-ttu-id="b08b9-142">Speichern Sie sie außerhalb von `wwwroot` sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="b08b9-142">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="b08b9-143">Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt, und geben Sie ein <xref:Microsoft.AspNetCore.Mvc.FileResult>-Objekt zurück:</span><span class="sxs-lookup"><span data-stu-id="b08b9-143">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="b08b9-144">Verzeichnissuche</span><span class="sxs-lookup"><span data-stu-id="b08b9-144">Directory browsing</span></span>

<span data-ttu-id="b08b9-145">Die Verzeichnissuche ermöglicht die Verzeichnisauflistung in angegebenen Verzeichnissen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-145">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="b08b9-146">Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-146">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="b08b9-147">Weitere Informationen finden Sie unter [Überlegungen](#considerations).</span><span class="sxs-lookup"><span data-stu-id="b08b9-147">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="b08b9-148">Aktivieren Sie die Verzeichnissuche mit:</span><span class="sxs-lookup"><span data-stu-id="b08b9-148">Enable directory browsing with:</span></span>

* <span data-ttu-id="b08b9-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-149"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="b08b9-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-150"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="b08b9-151">Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL `https://<hostname>/MyImages` mit Links zu den einzelnen Dateien und Ordnern:</span><span class="sxs-lookup"><span data-stu-id="b08b9-151">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![Verzeichnissuche](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="b08b9-153">Bereitstellen von Standarddokumenten</span><span class="sxs-lookup"><span data-stu-id="b08b9-153">Serve default documents</span></span>

<span data-ttu-id="b08b9-154">Durch das Festlegen einer Standardseite wird Besuchern ein Ausgangspunkt auf einer Website bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-154">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="b08b9-155">Um eine Standardseite von `wwwroot` ohne voll qualifizierten URI bereitzustellen, rufen Sie die <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="b08b9-155">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="b08b9-156">`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b08b9-156">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="b08b9-157">`UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="b08b9-157">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="b08b9-158">Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner in `wwwroot` folgende Dateien durchsucht:</span><span class="sxs-lookup"><span data-stu-id="b08b9-158">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="b08b9-159">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="b08b9-159">*default.htm*</span></span>
* <span data-ttu-id="b08b9-160">*default.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-160">*default.html*</span></span>
* <span data-ttu-id="b08b9-161">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="b08b9-161">*index.htm*</span></span>
* <span data-ttu-id="b08b9-162">*index.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-162">*index.html*</span></span>

<span data-ttu-id="b08b9-163">Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI.</span><span class="sxs-lookup"><span data-stu-id="b08b9-163">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="b08b9-164">Die Browser-URL spiegelt weiterhin den angeforderten URI wider.</span><span class="sxs-lookup"><span data-stu-id="b08b9-164">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="b08b9-165">Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-165">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="b08b9-166">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="b08b9-166">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="b08b9-167">UseFileServer für Standarddokumente</span><span class="sxs-lookup"><span data-stu-id="b08b9-167">UseFileServer for default documents</span></span>

<span data-ttu-id="b08b9-168">In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-168"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="b08b9-169">Rufen Sie `app.UseFileServer` auf, um die Bereitstellung statischer Dateien und der Standarddatei zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="b08b9-169">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="b08b9-170">Die Verzeichnissuche ist nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-170">Directory browsing isn't enabled.</span></span> <span data-ttu-id="b08b9-171">Der folgende Code zeigt `Startup.Configure` mit `UseFileServer` an:</span><span class="sxs-lookup"><span data-stu-id="b08b9-171">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="b08b9-172">Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-172">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="b08b9-173">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="b08b9-173">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="b08b9-174">Betrachten Sie die folgende Verzeichnishierarchie:</span><span class="sxs-lookup"><span data-stu-id="b08b9-174">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="b08b9-175">Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche von `MyStaticFiles` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-175">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="b08b9-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet.</span><span class="sxs-lookup"><span data-stu-id="b08b9-176"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="b08b9-177">Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="b08b9-177">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="b08b9-178">URI</span><span class="sxs-lookup"><span data-stu-id="b08b9-178">URI</span></span>            |      <span data-ttu-id="b08b9-179">Antwort</span><span class="sxs-lookup"><span data-stu-id="b08b9-179">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="b08b9-180">*MyStaticFiles/images/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="b08b9-180">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="b08b9-181">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-181">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="b08b9-182">Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt `https://<hostname>/StaticFiles` die Verzeichnisliste mit klickbaren Links zurück:</span><span class="sxs-lookup"><span data-stu-id="b08b9-182">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Liste der statischen Dateien](static-files/_static/db2.png)

<span data-ttu-id="b08b9-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung vom Ziel-URI ohne nachstehenden `/` zum Ziel-URI mit nachstehendem `/` durch.</span><span class="sxs-lookup"><span data-stu-id="b08b9-184"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="b08b9-185">Beispielsweise von `https://<hostname>/StaticFiles` zu `https://<hostname>/StaticFiles/`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-185">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="b08b9-186">Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich (`/`) als ungültig.</span><span class="sxs-lookup"><span data-stu-id="b08b9-186">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="b08b9-187">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="b08b9-187">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="b08b9-188">Die Klasse <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient.</span><span class="sxs-lookup"><span data-stu-id="b08b9-188">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="b08b9-189">Im folgenden Beispiel werden mehrere Dateierweiterungen bekannten MIME-Typen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b08b9-189">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="b08b9-190">Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt:</span><span class="sxs-lookup"><span data-stu-id="b08b9-190">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="b08b9-191">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="b08b9-191">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="b08b9-192">Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="b08b9-192">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="b08b9-193">Inhaltstypen, die vom Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="b08b9-193">Non-standard content types</span></span>

<span data-ttu-id="b08b9-194">Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-194">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="b08b9-195">Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="b08b9-195">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="b08b9-196">Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b08b9-196">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="b08b9-197">Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-197">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="b08b9-198">Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-198">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="b08b9-199">Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:</span><span class="sxs-lookup"><span data-stu-id="b08b9-199">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="b08b9-200">Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b08b9-200">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-201">Die Aktivierung von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="b08b9-201">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="b08b9-202">Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="b08b9-202">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="b08b9-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-203">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="b08b9-204">Bereitstellen von Dateien aus mehreren Speicherorten</span><span class="sxs-lookup"><span data-stu-id="b08b9-204">Serve files from multiple locations</span></span>

<span data-ttu-id="b08b9-205">Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-205">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="b08b9-206">Weitere Instanzen von `UseStaticFiles` und `UseFileServer` können mit anderen Dateianbietern bereitgestellt werden, um Dateien von anderen Speicherorten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-206">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="b08b9-207">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="b08b9-207">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="b08b9-208">Sicherheitsüberlegungen für statische Dateien</span><span class="sxs-lookup"><span data-stu-id="b08b9-208">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-209">`UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken.</span><span class="sxs-lookup"><span data-stu-id="b08b9-209">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="b08b9-210">Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-210">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="b08b9-211">Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-211">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="b08b9-212">Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht.</span><span class="sxs-lookup"><span data-stu-id="b08b9-212">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="b08b9-213">Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. `<content_root>/wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="b08b9-213">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="b08b9-214">Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages, Konfigurationsdateien usw.</span><span class="sxs-lookup"><span data-stu-id="b08b9-214">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="b08b9-215">Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems.</span><span class="sxs-lookup"><span data-stu-id="b08b9-215">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="b08b9-216">Bei Windows wird die Groß-/Kleinschreibung beispielsweise nicht beachtet, jedoch bei macOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="b08b9-216">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="b08b9-217">In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="b08b9-217">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="b08b9-218">Der IIS-Handler für statische Dateien wird nicht verwendet und hat keine Möglichkeit, Anforderungen zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b08b9-218">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="b08b9-219">Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="b08b9-219">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="b08b9-220">Navigieren Sie zum Feature **Module**.</span><span class="sxs-lookup"><span data-stu-id="b08b9-220">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="b08b9-221">Wählen Sie **StaticFileModule** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="b08b9-221">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="b08b9-222">Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.</span><span class="sxs-lookup"><span data-stu-id="b08b9-222">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-223">Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-223">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="b08b9-224">Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.</span><span class="sxs-lookup"><span data-stu-id="b08b9-224">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="b08b9-225">Platzieren Sie Codedateien einschließlich *.cs* und *.cshtml* außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts.</span><span class="sxs-lookup"><span data-stu-id="b08b9-225">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="b08b9-226">Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-226">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="b08b9-227">Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.</span><span class="sxs-lookup"><span data-stu-id="b08b9-227">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b08b9-228">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b08b9-228">Additional resources</span></span>

* [<span data-ttu-id="b08b9-229">Middleware</span><span class="sxs-lookup"><span data-stu-id="b08b9-229">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="b08b9-230">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b08b9-230">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b08b9-231">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="b08b9-231">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="b08b9-232">Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App direkt bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-232">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="b08b9-233">Damit diese Dateien bereitgestellt werden können, sind einige Konfigurationsschritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b08b9-233">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="b08b9-234">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b08b9-234">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="b08b9-235">Bereitstellen statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="b08b9-235">Serve static files</span></span>

<span data-ttu-id="b08b9-236">Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-236">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="b08b9-237">Das Standardverzeichnis lautet *{content root}/wwwroot*, kann jedoch über die Methode <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> geändert werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-237">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="b08b9-238">Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="b08b9-238">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="b08b9-239">Der Web-Host der App muss über das Inhaltsstammverzeichnis informiert werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-239">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="b08b9-240">Die Methode `WebHost.CreateDefaultBuilder` legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:</span><span class="sxs-lookup"><span data-stu-id="b08b9-240">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="b08b9-241">Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-241">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="b08b9-242">Die Projektvorlage der **Webanwendung** verfügt beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:</span><span class="sxs-lookup"><span data-stu-id="b08b9-242">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="b08b9-243">Das URI-Format für den Zugriff auf eine Datei im Unterordner *images* lautet *http://\<server_address>/images/\<image_file_name>* .</span><span class="sxs-lookup"><span data-stu-id="b08b9-243">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="b08b9-244">Beispiel: *http://localhost:9189/images/banner3.svg*</span><span class="sxs-lookup"><span data-stu-id="b08b9-244">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="b08b9-245">Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="b08b9-245">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="b08b9-246">Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b08b9-246">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="b08b9-247">Konfigurieren Sie die [Middleware](xref:fundamentals/middleware/index), über die Sie statische Dateien bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="b08b9-247">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="b08b9-248">Bereitstellen von Dateien innerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="b08b9-248">Serve files inside of web root</span></span>

<span data-ttu-id="b08b9-249">Rufen Sie die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>-Methode in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="b08b9-249">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="b08b9-250">Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar.</span><span class="sxs-lookup"><span data-stu-id="b08b9-250">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="b08b9-251">Folgendes Markup verweist auf *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="b08b9-251">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="b08b9-252">Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="b08b9-252">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="b08b9-253">Bereitstellen von Dateien außerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="b08b9-253">Serve files outside of web root</span></span>

<span data-ttu-id="b08b9-254">Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:</span><span class="sxs-lookup"><span data-stu-id="b08b9-254">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="b08b9-255">Über eine Anforderung kann auf die Datei *banner1.svg* zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="b08b9-255">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="b08b9-256">Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="b08b9-256">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="b08b9-257">Über eine Anforderung an *http://\<server_address>/StaticFiles/images/banner1.svg* wird die Datei *banner1.svg* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-257">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="b08b9-258">Folgendes Markup verweist auf *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="b08b9-258">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="b08b9-259">Festlegen von HTTP-Antwortheadern</span><span class="sxs-lookup"><span data-stu-id="b08b9-259">Set HTTP response headers</span></span>

<span data-ttu-id="b08b9-260">Mit einem <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>-Objekt können HTTP-Antwortheader festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-260">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="b08b9-261">Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b08b9-261">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b08b9-262">Die <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>-Methode ist im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) vorhanden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-262">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="b08b9-263">Die Dateien wurden 10 Minuten lang (600 Sekunden) in der Entwicklungsumgebung öffentlich zwischenspeicherbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="b08b9-263">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="b08b9-265">Autorisierung statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="b08b9-265">Static file authorization</span></span>

<span data-ttu-id="b08b9-266">Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-266">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="b08b9-267">Alle über die Middleware bereitgestellten Dateien, Dateien unter *wwwroot* inbegriffen, sind öffentlich zugänglich.</span><span class="sxs-lookup"><span data-stu-id="b08b9-267">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="b08b9-268">So stellen Sie Dateien basierend auf der Autorisierung bereit:</span><span class="sxs-lookup"><span data-stu-id="b08b9-268">To serve files based on authorization:</span></span>

* <span data-ttu-id="b08b9-269">Speichern Sie sie außerhalb von *wwwroot* sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="b08b9-269">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="b08b9-270">Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-270">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="b08b9-271">Geben Sie ein <xref:Microsoft.AspNetCore.Mvc.FileResult>-Objekt zurück:</span><span class="sxs-lookup"><span data-stu-id="b08b9-271">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="b08b9-272">Aktivieren der Verzeichnissuche</span><span class="sxs-lookup"><span data-stu-id="b08b9-272">Enable directory browsing</span></span>

<span data-ttu-id="b08b9-273">Über die Verzeichnissuche können Benutzer Ihrer Web-App eine Verzeichnisliste und Dateien innerhalb eines angegebenen Verzeichnisses anzeigen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-273">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="b08b9-274">Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="b08b9-274">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="b08b9-275">Aktivieren Sie die Verzeichnissuche, indem Sie die Methode <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="b08b9-275">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="b08b9-276">Fügen Sie erforderliche Dienste hinzu, indem Sie die Methode <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> von `Startup.ConfigureServices` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="b08b9-276">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="b08b9-277">Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* mit Links zu den einzelnen Dateien und Ordnern:</span><span class="sxs-lookup"><span data-stu-id="b08b9-277">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![Verzeichnissuche](static-files/_static/dir-browse.png)

<span data-ttu-id="b08b9-279">Weitere Informationen zu den Sicherheitsrisiken bei der Aktivierung der Suche finden Sie unter [Überlegungen](#considerations).</span><span class="sxs-lookup"><span data-stu-id="b08b9-279">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="b08b9-280">Beachten Sie die beiden `UseStaticFiles`-Aufrufe im folgenden Beispiel.</span><span class="sxs-lookup"><span data-stu-id="b08b9-280">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="b08b9-281">Durch den ersten Aufruf wird die Bereitstellung statischer Dateien im Ordner *wwwroot* aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-281">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="b08b9-282">Durch den zweiten Aufruf wird die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-282">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="b08b9-283">Bereitstellen eines Standarddokuments</span><span class="sxs-lookup"><span data-stu-id="b08b9-283">Serve a default document</span></span>

<span data-ttu-id="b08b9-284">Durch das Festlegen einer Standardstartseite wird Besuchern Ihrer Website ein logischer Ausgangspunkt geboten.</span><span class="sxs-lookup"><span data-stu-id="b08b9-284">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="b08b9-285">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> aus `Startup.Configure` auf, um eine Standardseite bereitzustellen, ohne dass der Benutzer den URI vollständig qualifizieren muss:</span><span class="sxs-lookup"><span data-stu-id="b08b9-285">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="b08b9-286">`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b08b9-286">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="b08b9-287">`UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="b08b9-287">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="b08b9-288">Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles`, um die Datei bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-288">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="b08b9-289">Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner folgende Dateien durchsucht:</span><span class="sxs-lookup"><span data-stu-id="b08b9-289">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="b08b9-290">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="b08b9-290">*default.htm*</span></span>
* <span data-ttu-id="b08b9-291">*default.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-291">*default.html*</span></span>
* <span data-ttu-id="b08b9-292">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="b08b9-292">*index.htm*</span></span>
* <span data-ttu-id="b08b9-293">*index.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-293">*index.html*</span></span>

<span data-ttu-id="b08b9-294">Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI.</span><span class="sxs-lookup"><span data-stu-id="b08b9-294">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="b08b9-295">Die Browser-URL spiegelt weiterhin den angeforderten URI wider.</span><span class="sxs-lookup"><span data-stu-id="b08b9-295">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="b08b9-296">Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-296">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="b08b9-297">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="b08b9-297">UseFileServer</span></span>

<span data-ttu-id="b08b9-298">In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-298"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="b08b9-299">Mit dem folgenden Code wird die Bereitstellung statischer Dateien und der Standarddatei aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-299">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="b08b9-300">Die Verzeichnissuche ist nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-300">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="b08b9-301">Der folgende Code baut auf der parameterlosen Überladung auf, indem die Verzeichnissuche aktiviert wird:</span><span class="sxs-lookup"><span data-stu-id="b08b9-301">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="b08b9-302">Betrachten Sie die folgende Verzeichnishierarchie:</span><span class="sxs-lookup"><span data-stu-id="b08b9-302">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="b08b9-303">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="b08b9-303">**wwwroot**</span></span>
  * <span data-ttu-id="b08b9-304">**css**</span><span class="sxs-lookup"><span data-stu-id="b08b9-304">**css**</span></span>
  * <span data-ttu-id="b08b9-305">**images**</span><span class="sxs-lookup"><span data-stu-id="b08b9-305">**images**</span></span>
  * <span data-ttu-id="b08b9-306">**js**</span><span class="sxs-lookup"><span data-stu-id="b08b9-306">**js**</span></span>
* <span data-ttu-id="b08b9-307">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="b08b9-307">**MyStaticFiles**</span></span>
  * <span data-ttu-id="b08b9-308">**images**</span><span class="sxs-lookup"><span data-stu-id="b08b9-308">**images**</span></span>
    * <span data-ttu-id="b08b9-309">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="b08b9-309">*banner1.svg*</span></span>
  * <span data-ttu-id="b08b9-310">*default.html*</span><span class="sxs-lookup"><span data-stu-id="b08b9-310">*default.html*</span></span>

<span data-ttu-id="b08b9-311">Mit dem folgenden Code werden statische Dateien, Standarddateien und die Verzeichnissuche von `MyStaticFiles` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-311">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="b08b9-312">`AddDirectoryBrowser` muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet:</span><span class="sxs-lookup"><span data-stu-id="b08b9-312">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="b08b9-313">Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="b08b9-313">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="b08b9-314">URI</span><span class="sxs-lookup"><span data-stu-id="b08b9-314">URI</span></span>            |                             <span data-ttu-id="b08b9-315">Antwort</span><span class="sxs-lookup"><span data-stu-id="b08b9-315">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="b08b9-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="b08b9-316">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="b08b9-317">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="b08b9-317">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="b08b9-318">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="b08b9-318">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="b08b9-319">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="b08b9-319">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="b08b9-320">Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt *http://\<server_address>/StaticFiles* die Verzeichnisliste mit klickbaren Links zurück:</span><span class="sxs-lookup"><span data-stu-id="b08b9-320">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="b08b9-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung von `http://{SERVER ADDRESS}/StaticFiles` (ohne nachstehenden Schrägstrich) zu `http://{SERVER ADDRESS}/StaticFiles/` (mit nachstehendem Schrägstrich) durch.</span><span class="sxs-lookup"><span data-stu-id="b08b9-322"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="b08b9-323">Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich als ungültig.</span><span class="sxs-lookup"><span data-stu-id="b08b9-323">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="b08b9-324">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="b08b9-324">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="b08b9-325">Die Klasse <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient.</span><span class="sxs-lookup"><span data-stu-id="b08b9-325">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="b08b9-326">Im folgenden Beispiel werden mehrere Dateierweiterungen bei bekannten MIME-Typen registriert.</span><span class="sxs-lookup"><span data-stu-id="b08b9-326">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="b08b9-327">Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-327">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="b08b9-328">Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="b08b9-328">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="b08b9-329">Informationen zur Verwendung eines benutzerdefinierten <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> oder zum Konfigurieren anderer <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor-Server-Apps finden Sie unter <xref:blazor/fundamentals/additional-scenarios#static-files>.</span><span class="sxs-lookup"><span data-stu-id="b08b9-329">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/additional-scenarios#static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="b08b9-330">Inhaltstypen, die vom Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="b08b9-330">Non-standard content types</span></span>

<span data-ttu-id="b08b9-331">Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-331">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="b08b9-332">Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="b08b9-332">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="b08b9-333">Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b08b9-333">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="b08b9-334">Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-334">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="b08b9-335">Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:</span><span class="sxs-lookup"><span data-stu-id="b08b9-335">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="b08b9-336">Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b08b9-336">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-337">Die Aktivierung von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="b08b9-337">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="b08b9-338">Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="b08b9-338">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="b08b9-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-339">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="b08b9-340">Bereitstellen von Dateien aus mehreren Speicherorten</span><span class="sxs-lookup"><span data-stu-id="b08b9-340">Serve files from multiple locations</span></span>

<span data-ttu-id="b08b9-341">Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b08b9-341">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="b08b9-342">Sie können weitere Instanzen von `UseStaticFiles` und `UseFileServer` mit anderen Dateianbietern bereitstellen, um Dateien von anderen Speicherorten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-342">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="b08b9-343">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="b08b9-343">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="b08b9-344">Weitere Überlegungen</span><span class="sxs-lookup"><span data-stu-id="b08b9-344">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-345">`UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken.</span><span class="sxs-lookup"><span data-stu-id="b08b9-345">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="b08b9-346">Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-346">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="b08b9-347">Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-347">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="b08b9-348">Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht.</span><span class="sxs-lookup"><span data-stu-id="b08b9-348">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="b08b9-349">Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="b08b9-349">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="b08b9-350">Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages (nur 2.x), Konfigurationsdateien usw.</span><span class="sxs-lookup"><span data-stu-id="b08b9-350">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="b08b9-351">Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems.</span><span class="sxs-lookup"><span data-stu-id="b08b9-351">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="b08b9-352">Bei Windows wird die Groß-/Kleinschreibung beispielsweise beachtet, bei macOS und Linux hingegen nicht.</span><span class="sxs-lookup"><span data-stu-id="b08b9-352">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="b08b9-353">In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="b08b9-353">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="b08b9-354">Der statische IIS-Dateihandler wird nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="b08b9-354">The IIS static file handler isn't used.</span></span> <span data-ttu-id="b08b9-355">Er kann Anforderungen erst verarbeiten, nachdem sie vom Modul verarbeitet wurden.</span><span class="sxs-lookup"><span data-stu-id="b08b9-355">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="b08b9-356">Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="b08b9-356">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="b08b9-357">Navigieren Sie zum Feature **Module**.</span><span class="sxs-lookup"><span data-stu-id="b08b9-357">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="b08b9-358">Wählen Sie **StaticFileModule** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="b08b9-358">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="b08b9-359">Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.</span><span class="sxs-lookup"><span data-stu-id="b08b9-359">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="b08b9-360">Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b08b9-360">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="b08b9-361">Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.</span><span class="sxs-lookup"><span data-stu-id="b08b9-361">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="b08b9-362">Platzieren Sie Codedateien (einschließlich *.cs* und *.cshtml*) außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts.</span><span class="sxs-lookup"><span data-stu-id="b08b9-362">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="b08b9-363">Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen.</span><span class="sxs-lookup"><span data-stu-id="b08b9-363">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="b08b9-364">Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.</span><span class="sxs-lookup"><span data-stu-id="b08b9-364">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b08b9-365">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b08b9-365">Additional resources</span></span>

* [<span data-ttu-id="b08b9-366">Middleware</span><span class="sxs-lookup"><span data-stu-id="b08b9-366">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="b08b9-367">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b08b9-367">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
