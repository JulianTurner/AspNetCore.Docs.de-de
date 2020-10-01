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
# <a name="static-files-in-aspnet-core"></a>Statische Dateien in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)

Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App standardmäßig direkt bereitgestellt werden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Bereitstellen statischer Dateien

Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert. Das Standardverzeichnis lautet `{content root}/wwwroot`, kann jedoch mit der Methode <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> geändert werden. Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).

Die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

Der vorangehende Code wurde mit der Web-App-Vorlage erstellt.

Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden. Die **Webanwendung**-Projektvorlagen verfügen beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:

* `wwwroot`
  * `css`
  * `js`
  * `lib`

Erstellen Sie ggf. den *wwwroot/images*-Ordner, und fügen Sie die Datei *wwwroot/images/MyImage. pg* hinzu. Das URI-Format für den Zugriff auf eine Datei im `images`-Ordner ist `https://<hostname>/images/<image_file_name>`. Beispiel: `https://localhost:5001/images/MyImage.jpg`

### <a name="serve-files-in-web-root"></a>Verarbeiten von Dateien im Webstammverzeichnis

Mit den Standard-Webappvorlagen wird die <xref:Owin.StaticFileExtensions.UseStaticFiles%2A>-Methode in `Startup.Configure` aufgerufen, die die Verarbeitung statischer Dateien ermöglicht:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar. Folgendes Markup verweist auf *wwwroot/images/MyImage.jpg*:

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Bereitstellen von Dateien außerhalb des Webstammverzeichnisses

Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

Über eine Anforderung kann auf die Datei `red-rose.jpg` zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht. Eine Anforderung an `https://<hostname>/StaticFiles/images/red-rose.jpg` dient der *red-rose.jpg*-Datei.

Folgendes Markup verweist auf *MyStaticFiles/images/red-rose.jpg*:

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a>Festlegen von HTTP-Antwortheadern

Mit einem <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>-Objekt können HTTP-Antwortheader festgelegt werden. Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

Statische Dateien können 600 Sekunden lang öffentlich zwischengespeichert werden:

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisierung statischer Dateien

Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen. Alle über die Middleware bereitgestellten Dateien, Dateien unter `wwwroot` inbegriffen, sind öffentlich zugänglich. So stellen Sie Dateien basierend auf der Autorisierung bereit:

* Speichern Sie sie außerhalb von `wwwroot` sowie außerhalb sämtlicher Verzeichnisse, auf die die Standardmiddleware für statische Dateien zugreifen kann.
* Rufen Sie `UseStaticFiles` nach `UseAuthorization` auf, und geben Sie den Pfad an:

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2)]
  
  Der Ansatz oben erfordert, dass Benutzer authentifiziert werden:

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-99)]

   [!INCLUDE[](~/includes/requireAuth.md)]

Eine Alternative zum Bereitstellen von Dateien basierend auf Autorisierung:

* Speichern Sie sie außerhalb von `wwwroot` sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.
* Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt, und geben Sie ein <xref:Microsoft.AspNetCore.Mvc.FileResult>-Objekt zurück:

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a>Verzeichnissuche

Die Verzeichnissuche ermöglicht die Verzeichnisauflistung in angegebenen Verzeichnissen.

Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert. Weitere Informationen finden Sie unter [Überlegungen](#considerations).

Aktivieren Sie die Verzeichnissuche mit:

* <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.
* <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL `https://<hostname>/MyImages` mit Links zu den einzelnen Dateien und Ordnern:

![Verzeichnissuche](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a>Bereitstellen von Standarddokumenten

Durch das Festlegen einer Standardseite wird Besuchern ein Ausgangspunkt auf einer Website bereitgestellt. Um eine Standardseite von `wwwroot` ohne voll qualifizierten URI bereitzustellen, rufen Sie die <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A>-Methode auf:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird. `UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.

Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner in `wwwroot` folgende Dateien durchsucht:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI. Die Browser-URL spiegelt weiterhin den angeforderten URI wider.

Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a>UseFileServer für Standarddokumente

In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.

Rufen Sie `app.UseFileServer` auf, um die Bereitstellung statischer Dateien und der Standarddatei zu aktivieren. Die Verzeichnissuche ist nicht aktiviert. Der folgende Code zeigt `Startup.Configure` mit `UseFileServer` an:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche aktiviert:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

Betrachten Sie die folgende Verzeichnishierarchie:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

Mit dem folgenden Code wird die Bereitstellung statischer Dateien, der Standarddatei und der Verzeichnissuche von `MyStaticFiles` aktiviert:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet.

Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:

| URI            |      Antwort  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | *MyStaticFiles/images/MyImage.jpg* |
| `https://<hostname>/StaticFiles` | *MyStaticFiles/default.html* |

Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt `https://<hostname>/StaticFiles` die Verzeichnisliste mit klickbaren Links zurück:

![Liste der statischen Dateien](static-files/_static/db2.png)

<xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung vom Ziel-URI ohne nachstehenden `/` zum Ziel-URI mit nachstehendem `/` durch. Beispielsweise von `https://<hostname>/StaticFiles` zu `https://<hostname>/StaticFiles/`. Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich (`/`) als ungültig.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Die Klasse <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient. Im folgenden Beispiel werden mehrere Dateierweiterungen bekannten MIME-Typen zugeordnet. Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Inhaltstypen, die vom Standard abweichen

Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen. Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline. Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben. Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.

Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

Der folgende Code zeigt `Startup.Configure` mit dem vorherigen Code:

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.

> [!WARNING]
> Die Aktivierung von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stellt ein Sicherheitsrisiko dar. Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.

## <a name="serve-files-from-multiple-locations"></a>Bereitstellen von Dateien aus mehreren Speicherorten

Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf `wwwroot`. Weitere Instanzen von `UseStaticFiles` und `UseFileServer` können mit anderen Dateianbietern bereitgestellt werden, um Dateien von anderen Speicherorten bereitzustellen. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a>Sicherheitsüberlegungen für statische Dateien

> [!WARNING]
> `UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken. Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen. Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden. Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht. Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. `<content_root>/wwwroot`. Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages, Konfigurationsdateien usw.

* Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems. Bei Windows wird die Groß-/Kleinschreibung beispielsweise nicht beachtet, jedoch bei macOS und Linux.

* In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien. Der IIS-Handler für statische Dateien wird nicht verwendet und hat keine Möglichkeit, Anforderungen zu verarbeiten.

* Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:
    1. Navigieren Sie zum Feature **Module**.
    1. Wählen Sie **StaticFileModule** aus der Liste aus.
    1. Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.

> [!WARNING]
> Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt. Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.

* Platzieren Sie Codedateien einschließlich *.cs* und *.cshtml* außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts. Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen. Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Einführung in ASP.NET Core](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)

Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App direkt bereitgestellt werden. Damit diese Dateien bereitgestellt werden können, sind einige Konfigurationsschritte erforderlich.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Bereitstellen statischer Dateien

Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert. Das Standardverzeichnis lautet *{content root}/wwwroot*, kann jedoch über die Methode <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> geändert werden. Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).

Der Web-Host der App muss über das Inhaltsstammverzeichnis informiert werden.

Die Methode `WebHost.CreateDefaultBuilder` legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden. Die Projektvorlage der **Webanwendung** verfügt beispielsweise über mehrere Ordner innerhalb des Ordners `wwwroot`:

* `wwwroot`
  * `css`
  * `images`
  * `js`

Das URI-Format für den Zugriff auf eine Datei im Unterordner *images* lautet *http://\<server_address>/images/\<image_file_name>* . Beispiel: *http://localhost:9189/images/banner3.svg*

Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu. Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

Konfigurieren Sie die [Middleware](xref:fundamentals/middleware/index), über die Sie statische Dateien bereitstellen können.

### <a name="serve-files-inside-of-web-root"></a>Bereitstellen von Dateien innerhalb des Webstammverzeichnisses

Rufen Sie die <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>-Methode in `Startup.Configure` auf:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar. Folgendes Markup verweist auf *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Bereitstellen von Dateien außerhalb des Webstammverzeichnisses

Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

Über eine Anforderung kann auf die Datei *banner1.svg* zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht. Über eine Anforderung an *http://\<server_address>/StaticFiles/images/banner1.svg* wird die Datei *banner1.svg* bereitgestellt.

Folgendes Markup verweist auf *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Festlegen von HTTP-Antwortheadern

Mit einem <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>-Objekt können HTTP-Antwortheader festgelegt werden. Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Die <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>-Methode ist im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) vorhanden.

Die Dateien wurden 10 Minuten lang (600 Sekunden) in der Entwicklungsumgebung öffentlich zwischenspeicherbar gemacht:

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisierung statischer Dateien

Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen. Alle über die Middleware bereitgestellten Dateien, Dateien unter *wwwroot* inbegriffen, sind öffentlich zugänglich. So stellen Sie Dateien basierend auf der Autorisierung bereit:

* Speichern Sie sie außerhalb von *wwwroot* sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.
* Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt. Geben Sie ein <xref:Microsoft.AspNetCore.Mvc.FileResult>-Objekt zurück:

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Aktivieren der Verzeichnissuche

Über die Verzeichnissuche können Benutzer Ihrer Web-App eine Verzeichnisliste und Dateien innerhalb eines angegebenen Verzeichnisses anzeigen. Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#considerations)). Aktivieren Sie die Verzeichnissuche, indem Sie die Methode <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure` aufrufen:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Fügen Sie erforderliche Dienste hinzu, indem Sie die Methode <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> von `Startup.ConfigureServices` aufrufen:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* mit Links zu den einzelnen Dateien und Ordnern:

![Verzeichnissuche](static-files/_static/dir-browse.png)

Weitere Informationen zu den Sicherheitsrisiken bei der Aktivierung der Suche finden Sie unter [Überlegungen](#considerations).

Beachten Sie die beiden `UseStaticFiles`-Aufrufe im folgenden Beispiel. Durch den ersten Aufruf wird die Bereitstellung statischer Dateien im Ordner *wwwroot* aktiviert. Durch den zweiten Aufruf wird die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* aktiviert:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Bereitstellen eines Standarddokuments

Durch das Festlegen einer Standardstartseite wird Besuchern Ihrer Website ein logischer Ausgangspunkt geboten. Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> aus `Startup.Configure` auf, um eine Standardseite bereitzustellen, ohne dass der Benutzer den URI vollständig qualifizieren muss:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird. `UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann. Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles`, um die Datei bereitzustellen.

Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner folgende Dateien durchsucht:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI. Die Browser-URL spiegelt weiterhin den angeforderten URI wider.

Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.

Mit dem folgenden Code wird die Bereitstellung statischer Dateien und der Standarddatei aktiviert. Die Verzeichnissuche ist nicht aktiviert.

```csharp
app.UseFileServer();
```

Der folgende Code baut auf der parameterlosen Überladung auf, indem die Verzeichnissuche aktiviert wird:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Betrachten Sie die folgende Verzeichnishierarchie:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Mit dem folgenden Code werden statische Dateien, Standarddateien und die Verzeichnissuche von `MyStaticFiles` aktiviert:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:

| URI            |                             Antwort  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt *http://\<server_address>/StaticFiles* die Verzeichnisliste mit klickbaren Links zurück:

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung von `http://{SERVER ADDRESS}/StaticFiles` (ohne nachstehenden Schrägstrich) zu `http://{SERVER ADDRESS}/StaticFiles/` (mit nachstehendem Schrägstrich) durch. Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich als ungültig.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Die Klasse <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient. Im folgenden Beispiel werden mehrere Dateierweiterungen bei bekannten MIME-Typen registriert. Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt.

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).

Informationen zur Verwendung eines benutzerdefinierten <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> oder zum Konfigurieren anderer <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor-Server-Apps finden Sie unter <xref:blazor/fundamentals/additional-scenarios#static-files>.

## <a name="non-standard-content-types"></a>Inhaltstypen, die vom Standard abweichen

Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen. Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline. Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben. Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.

Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.

> [!WARNING]
> Die Aktivierung von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stellt ein Sicherheitsrisiko dar. Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.

## <a name="serve-files-from-multiple-locations"></a>Bereitstellen von Dateien aus mehreren Speicherorten

Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf *wwwroot*. Sie können weitere Instanzen von `UseStaticFiles` und `UseFileServer` mit anderen Dateianbietern bereitstellen, um Dateien von anderen Speicherorten bereitzustellen. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Weitere Überlegungen

> [!WARNING]
> `UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken. Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen. Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden. Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht. Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. *\<content_root>/wwwroot*. Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages (nur 2.x), Konfigurationsdateien usw.

* Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems. Bei Windows wird die Groß-/Kleinschreibung beispielsweise beachtet, bei macOS und Linux hingegen nicht.

* In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien. Der statische IIS-Dateihandler wird nicht verwendet. Er kann Anforderungen erst verarbeiten, nachdem sie vom Modul verarbeitet wurden.

* Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:
    1. Navigieren Sie zum Feature **Module**.
    1. Wählen Sie **StaticFileModule** aus der Liste aus.
    1. Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.

> [!WARNING]
> Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt. Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.

* Platzieren Sie Codedateien (einschließlich *.cs* und *.cshtml*) außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts. Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen. Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Einführung in ASP.NET Core](xref:index)

::: moniker-end
