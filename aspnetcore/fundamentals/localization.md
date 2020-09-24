---
title: Globalisierung und Lokalisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dienste und Middleware für das Lokalisieren von Inhalten in verschiedene Sprachen und Kulturen anbietet.
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 478e9e58f265a8c372dd048f9ba8593424050e01
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722669"
---
# <a name="globalization-and-localization-in-aspnet-core"></a>Globalisierung und Lokalisierung in ASP.NET Core

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen. ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.

Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization). Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen. Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.

Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst. Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.

Die Lokalisierung von Apps umfasst die folgenden Aufgaben:

1. Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.
1. Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.
1. Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.

<xref:Microsoft.Extensions.Localization.IStringLocalizer> und <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> wurden zur Förderung der Produktivität bei der Entwicklung von lokalisierten Apps entwickelt. `IStringLocalizer` verwendet <xref:System.Resources.ResourceManager> und <xref:System.Resources.ResourceReader>, um kulturspezifische Ressourcen zur Laufzeit bereitzustellen. Die Schnittstelle besitzt einen Indexer und `IEnumerable` für die Rückgabe von lokalisierten Zeichenfolgen. `IStringLocalizer` erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden. Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen. Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md). Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“. Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können. Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen. Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben. Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren. Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.

Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten. Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert. Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.

Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden. In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind. Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalisierung der Ansicht

Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an. Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht. Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht. Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei. `ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert. Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen. Sehen Sie sich das folgende Razor-Markup an:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Eine französische Ressourcendatei könnte Folgendes beinhalten:

| Key | Wert |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalisierung von DataAnnotations

Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert. Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen. ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Verwenden einer Ressourcenzeichenfolge für mehrere Klassen

Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind. DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures und SupportedUICultures

ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`. Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen. `SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen. Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture). `SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden. Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden. Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`. ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen. Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.

## <a name="resource-files"></a>Ressourcendateien

Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code. Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert. Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält. „es“ ist der Sprachcode für Spanisch. Erstellen dieser Ressourcendatei in Visual Studio:

1. Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.

   ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet. Ein zweites Kontextmenü ist unter „Hinzufügen“ geöffnet, der Befehl „Neues Element“ wird angezeigt und hervorgehoben.](localization/_static/newi.png)

1. Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.

   ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

1. Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.

   ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

   Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.

   ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a>Benennung von Ressourcendateien

Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt. Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten. Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen. Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.

Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*. Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden. Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*. Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden. Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.

| Ressourcenname | Punkt- oder Pfadbenennung |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punkt  |
| Resources/Controllers/HomeController.fr.resx  | Pfad |

Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster. Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden. Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei. Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet. 

> [!WARNING]
> Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist. Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt. 

Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:

* Die Lokalisierung funktioniert standardmäßig nicht.
* Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl. `RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist. 

Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.

## <a name="culture-fallback-behavior"></a>Kulturfallbackverhalten

Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“. Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück. Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar. Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird. Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“. „es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.

Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält. Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)

Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert. Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird. Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.

### <a name="generate-resource-files-with-visual-studio"></a>Erstellen von Ressourcendateien mit Visual Studio

Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge. Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen. In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen). Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*). Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.

### <a name="add-other-cultures"></a>Hinzufügen von anderen Kulturen

Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei. Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**). Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

### <a name="configure-localization"></a>Konfigurieren der Lokalisierung

Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer hinzu. Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.

* `AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien hinzu. In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei. Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.

### <a name="localization-middleware"></a>Lokalisierungsmiddleware

Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt. Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert. Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt. Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet. Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten. Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen. Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Einige Apps verwenden eine Abfragezeichenfolge, um die <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> festzulegen. Bei Apps, die die Ansätze cookie oder Accept-Language-Header verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich. Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert. Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`. Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest. Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies. Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines cookies.

`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, mit dem ermittelt wird, welche Kulturinformationen der Benutzer bevorzugt. Der Standardcookiename lautet `.AspNetCore.Culture`.

Das cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:

```
c=en-UK|uic=en-US
```

Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.

### <a name="the-accept-language-http-header"></a>Der Accept-Language-HTTP-Header

Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben. Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde. Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.

### <a name="set-the-accept-language-http-header-in-ie"></a>Festlegen des Accept-Language-HTTP-Headers in Internet Explorer

1. Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.

1. Klicken Sie auf **Sprachen**.

   ![Internetoptionen](localization/_static/lang.png)

1. Klicken Sie auf **Spracheinstellungen festlegen**.

1. Klicken Sie auf **Sprache hinzufügen**.

1. Fügen Sie die Sprache hinzu.

1. Klicken Sie auf die Sprache und dann auf **Nach oben**.

### <a name="use-a-custom-provider"></a>Verwenden eines benutzerdefinierten Anbieters

Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen. In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.

### <a name="set-the-culture-programmatically"></a>Programmgesteuertes Festlegen der Kultur

Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`. Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Die Methode `SetLanguage` legt das Kulturcookie fest.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen. Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.

## <a name="model-binding-route-data-and-query-strings"></a>Routendaten und Abfragezeichenfolgen für die Modellbindung

Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Begriffe für die Globalisierung und Lokalisierung

Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen. Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen. Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.

[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.

Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode. Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist. Siehe <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt. Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“. Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).

Begriffe:

* Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.
* Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.
* Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.
* Kultur: Beschreibt eine Sprache und optional auch eine Region.
* Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region. (Zum Beispiel „en“, „es“)
* Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt. (Zum Beispiel „en-US“, „en-GB“, „es-CL“)
* Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält. („en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)
* Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.
* [Globalisieren und Lokalisieren von .NET-Anwendungen](/dotnet/standard/globalization-localization/index)
* [Ressourcen in RESX-Dateien](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft Multilingual App Toolkit](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalisierung und Generics](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen. ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.

Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization). Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen. Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.

Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst. Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.

Die Lokalisierung von Apps umfasst die folgenden Aufgaben:

1. Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.
1. Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.
1. Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.

<xref:Microsoft.Extensions.Localization.IStringLocalizer> und <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> wurden zur Förderung der Produktivität bei der Entwicklung von lokalisierten Apps entwickelt. `IStringLocalizer` verwendet <xref:System.Resources.ResourceManager> und <xref:System.Resources.ResourceReader>, um kulturspezifische Ressourcen zur Laufzeit bereitzustellen. Die Schnittstelle besitzt einen Indexer und `IEnumerable` für die Rückgabe von lokalisierten Zeichenfolgen. `IStringLocalizer` erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden. Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen. Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md). Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“. Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können. Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen. Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben. Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren. Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.

Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten. Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert. Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.

Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden. In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind. Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalisierung der Ansicht

Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an. Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht. Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht. Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei. `ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert. Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen. Sehen Sie sich das folgende Razor-Markup an:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Eine französische Ressourcendatei könnte Folgendes beinhalten:

| Key | Wert |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalisierung von DataAnnotations

Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert. Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen. ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Verwenden einer Ressourcenzeichenfolge für mehrere Klassen

Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind. DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures und SupportedUICultures

ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`. Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen. `SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen. Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture). `SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden. Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden. Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`. ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen. Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.

## <a name="resource-files"></a>Ressourcendateien

Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code. Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert. Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält. „es“ ist der Sprachcode für Spanisch. Erstellen dieser Ressourcendatei in Visual Studio:

1. Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.

   ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet. Ein zweites Kontextmenü ist unter „Hinzufügen“ geöffnet, der Befehl „Neues Element“ wird angezeigt und hervorgehoben.](localization/_static/newi.png)

1. Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.

   ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

1. Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.

   ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

   Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.

   ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a>Benennung von Ressourcendateien

Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt. Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten. Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen. Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.

Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*. Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden. Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*. Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden. Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.

| Ressourcenname | Punkt- oder Pfadbenennung |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punkt  |
| Resources/Controllers/HomeController.fr.resx  | Pfad |

Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster. Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden. Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei. Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet. 

> [!WARNING]
> Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist. Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt. 

Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:

* Die Lokalisierung funktioniert standardmäßig nicht.
* Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl. `RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist. 

Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.

## <a name="culture-fallback-behavior"></a>Kulturfallbackverhalten

Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“. Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück. Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar. Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird. Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“. „es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.

Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält. Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)

Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert. Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird. Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.

### <a name="generate-resource-files-with-visual-studio"></a>Erstellen von Ressourcendateien mit Visual Studio

Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge. Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen. In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen). Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*). Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.

### <a name="add-other-cultures"></a>Hinzufügen von anderen Kulturen

Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei. Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**). Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

### <a name="configure-localization"></a>Konfigurieren der Lokalisierung

Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer hinzu. Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.

* `AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien hinzu. In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei. Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.

### <a name="localization-middleware"></a>Lokalisierungsmiddleware

Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt. Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert. Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt. Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet. Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten. Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen. Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Einige Apps verwenden eine Abfragezeichenfolge, um die <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> festzulegen. Bei Apps, die die Ansätze cookie oder Accept-Language-Header verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich. Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert. Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`. Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest. Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies. Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines cookies.

`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, mit dem ermittelt wird, welche Kulturinformationen der Benutzer bevorzugt. Der Standardcookiename lautet `.AspNetCore.Culture`.

Das cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:

```
c=en-UK|uic=en-US
```

Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.

### <a name="the-accept-language-http-header"></a>Der Accept-Language-HTTP-Header

Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben. Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde. Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.

### <a name="set-the-accept-language-http-header-in-ie"></a>Festlegen des Accept-Language-HTTP-Headers in Internet Explorer

1. Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.

1. Klicken Sie auf **Sprachen**.

   ![Internetoptionen](localization/_static/lang.png)

1. Klicken Sie auf **Spracheinstellungen festlegen**.

1. Klicken Sie auf **Sprache hinzufügen**.

1. Fügen Sie die Sprache hinzu.

1. Klicken Sie auf die Sprache und dann auf **Nach oben**.

### <a name="use-a-custom-provider"></a>Verwenden eines benutzerdefinierten Anbieters

Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen. In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.

### <a name="set-the-culture-programmatically"></a>Programmgesteuertes Festlegen der Kultur

Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`. Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Die Methode `SetLanguage` legt das Kulturcookie fest.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen. Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.

## <a name="model-binding-route-data-and-query-strings"></a>Routendaten und Abfragezeichenfolgen für die Modellbindung

Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Begriffe für die Globalisierung und Lokalisierung

Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen. Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen. Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.

[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.

Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode. Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist. Siehe <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt. Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“. Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).

Begriffe:

* Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.
* Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.
* Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.
* Kultur: Beschreibt eine Sprache und optional auch eine Region.
* Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region. (Zum Beispiel „en“, „es“)
* Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt. (Zum Beispiel „en-US“, „en-GB“, „es-CL“)
* Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält. („en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)
* Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.
* [Globalisieren und Lokalisieren von .NET-Anwendungen](/dotnet/standard/globalization-localization/index)
* [Ressourcen in RESX-Dateien](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft Multilingual App Toolkit](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalisierung und Generics](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), und [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Eine mehrsprachige Website ermöglicht es, eine größere Zielgruppe zu erreichen. ASP.NET Core bietet Dienste und Middleware zur Lokalisierung in verschiedene Sprachen und Kulturen.

Die Internationalisierung umfasst die[Globalisierung](/dotnet/api/system.globalization) und die [Lokalisierung](/dotnet/standard/globalization-localization/localization). Globalisierung bezeichnet das Entwerfen von Anwendungen, die verschiedene Kulturen unterstützen. Durch die Globalisierung wird die Unterstützung von Eingabe, Anzeige und Ausgabe mehrerer definierter Sprachskripts hinzugefügt, die zu bestimmten geografischen Bereichen gehören.

Durch die Lokalisierung wird eine globalisierte App, die bereits auf Lokalisierbarkeit vorbereitet wurde, auf eine bestimmte Kultur bzw. ein bestimmtes Gebietsschema angepasst. Weitere Informationen finden Sie unter **Begriffe für die Globalisierung und Lokalisierung** am Ende dieses Dokuments.

Die Lokalisierung von Apps umfasst die folgenden Aufgaben:

1. Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.
1. Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.
1. Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="make-the-apps-content-localizable"></a>Stellen Sie sicher, dass der Inhalt der App lokalisierbar ist.

<xref:Microsoft.Extensions.Localization.IStringLocalizer> und <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> wurden zur Förderung der Produktivität bei der Entwicklung von lokalisierten Apps entwickelt. `IStringLocalizer` verwendet <xref:System.Resources.ResourceManager> und <xref:System.Resources.ResourceReader>, um kulturspezifische Ressourcen zur Laufzeit bereitzustellen. Die Schnittstelle besitzt einen Indexer und `IEnumerable` für die Rückgabe von lokalisierten Zeichenfolgen. `IStringLocalizer` erfordert nicht, dass die Zeichenfolgen der Standardsprache in einer Ressourcendatei gespeichert werden. Sie können eine App entwickeln, die für die Lokalisierung ausgelegt ist, und müssen in den frühen Entwicklungsphasen keine Ressourcendateien erstellen. Im folgenden Codebeispiel wird dargestellt, wie die Zeichenfolge „About Title“ für die Lokalisierung umschlossen wird.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

Im vorangehenden Codebeispiel stammt die Implementierung von `IStringLocalizer<T>` aus [Dependency Injection](dependency-injection.md). Wenn kein lokalisierter Wert von „About Title“ gefunden wird, wird der Indexerschlüssel zurückgegeben, d.h. die Zeichenfolge „About Title“. Sie können die Literalzeichenfolgen der App in der Standardsprache beibehalten und diese in der Lokalisierung umschließen, damit Sie sich auf die Entwicklung der App konzentrieren können. Entwickeln Sie Ihre App mit Ihrer Standardsprache, und bereiten Sie sie auf die Lokalisierung vor, ohne zuerst eine Standardressourcendatei zu erstellen. Alternativ können Sie das herkömmliche Verfahren verwenden und einen Schlüssel zum Abrufen der Zeichenfolge in der Standardsprache angeben. Der neue Workflow, der keine Standardsprache in der *RESX*-Datei verwendet und die Literalzeichenfolgen einfach umschließt, kann für viele Entwickler den Aufwand beim Lokalisieren einer App reduzieren. Andere Entwickler bevorzugen weiterhin den herkömmlichen Workflow, weil es dabei einfacher ist, mit längeren Literalzeichenfolgen zu arbeiten und lokalisierte Zeichenfolgen zu aktualisieren.

Verwenden Sie die Implementierung von `IHtmlLocalizer<T>` für Ressourcen, die HTML enthalten. Mit `IHtmlLocalizer` werden Argumente HTML-codiert, die in der Ressourcenzeichenfolge formatiert sind. Die Ressourcenzeichenfolgen werden jedoch nicht HTML-codiert. Im folgenden Beispiel wird hervorgehoben, dass nur der Wert des Parameters `name` HTML-codiert ist.

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Auf der untersten Ebene können Sie `IStringLocalizerFactory` aus [Dependency Injection](dependency-injection.md) abrufen:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

Im obigen Codebeispiel werden beide factory.Create-Methoden veranschaulicht.

Sie können Ihre lokalisierten Zeichenfolgen in Steuerelemente und Bereiche aufteilen oder nur einen Container verwenden. In der Beispiel-App wird eine Dummyklasse namens `SharedResource` für freigegebene Ressourcen verwendet.

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

Einige Entwickler verwenden die Klasse `Startup`, damit globale oder freigegebene Zeichenfolgen enthalten sind. Im folgenden Beispiel werden die Lokalisierer `InfoController` und `SharedResource` verwendet:

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a>Lokalisierung der Ansicht

Der Dienst `IViewLocalizer` gibt lokalisierte Zeichenfolgen für eine [Ansicht](xref:mvc/views/overview) an. Die Klasse `ViewLocalizer` implementiert diese Schnittstelle und sucht den Speicherort der Ressource über den Dateipfad der Ansicht. Im folgenden Codebeispiel wird die Verwendung der Standardimplementierung von `IViewLocalizer` veranschaulicht:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

Die Standardimplementierung von `IViewLocalizer` sucht die Ressourcendatei über den Dateinamen der Ansicht. Es gibt keine Option zur Nutzung einer globalen freigegebenen Ressourcendatei. `ViewLocalizer` implementiert den Lokalisierer mithilfe von `IHtmlLocalizer`, damit Razor die lokalisierte Zeichenfolge nicht HTML-codiert. Sie können Ressourcenzeichenfolgen parametrisieren, und `IViewLocalizer` codiert die Parameter mit HTML, aber nicht die Ressourcenzeichenfolgen. Sehen Sie sich das folgende Razor-Markup an:

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

Eine französische Ressourcendatei könnte Folgendes beinhalten:

| Key | Wert |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

Die gerenderte Ansicht würde das HTML-Markup der Ressourcendatei enthalten.

> [!NOTE]
> Im Allgemeinen wird nur Text lokalisiert, nicht HTML.

Fügen Sie `IHtmlLocalizer<T>` ein, um eine freigegebene Ressourcendatei in einer Ansicht zu verwenden:

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a>Lokalisierung von DataAnnotations

Fehlermeldungen über DataAnnotations werden mit `IStringLocalizer<T>` lokalisiert. Durch Verwendung der Option `ResourcesPath = "Resources"` können die Fehlermeldungen in `RegisterViewModel` unter einem der folgenden Pfade gespeichert werden:

* *Resources/ViewModels.Account.RegisterViewModel.fr.resx*
* *Resources/ViewModels/Account/RegisterViewModel.fr.resx*

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

In ASP.NET Core MVC 1.1.0 und höher werden Attribute lokalisiert, die keine Validierungsattribute darstellen. ASP.NET Core MVC 1.0 sucht **nicht** nach lokalisierten Zeichenfolgen für Attribute, die keine Validierungsattribute darstellen.

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a>Verwenden einer Ressourcenzeichenfolge für mehrere Klassen

Das folgende Codebeispiel zeigt, wie eine Ressourcenzeichenfolge für Validierungsattribute mit mehreren Klassen verwendet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

Im obigen Codebeispiel bezeichnet `SharedResource` die Klasse, die der RESX-Datei entspricht, in der Ihre Validierungsmeldungen gespeichert sind. DataAnnotations verwendet bei diesem Ansatz nur `SharedResource` anstelle der Ressource für jede Klasse.

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a>Stellen Sie die lokalisierten Ressourcen für die unterstützten Sprachen und Kulturen bereit.

### <a name="supportedcultures-and-supporteduicultures"></a>SupportedCultures und SupportedUICultures

ASP.NET Core ermöglicht Ihnen, zwei Werte für die Kultur anzugeben: `SupportedCultures` und `SupportedUICultures`. Das Objekt [CultureInfo](/dotnet/api/system.globalization.cultureinfo) für `SupportedCultures` bestimmt die Ergebnisse von kulturabhängigen Funktionen, wie z.B. das Format von Datumswerten, Uhrzeiten, Zahlen und Währungen. `SupportedCultures` bestimmt auch die Sortierreihenfolge von Texten, Groß-/Kleinschreibungskonventionen und Zeichenfolgenvergleichen. Weitere Informationen darüber, wie der Server die Kultur abruft, finden Sie unter [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture). `SupportedUICultures` bestimmt, welche übersetzten Zeichenfolgen (aus den *RESX*-Dateien) von [ResourceManager](/dotnet/api/system.resources.resourcemanager) abgerufen werden. Die `ResourceManager`-Klasse sucht nach kulturspezifischen Zeichenfolgen, die durch `CurrentUICulture` bestimmt werden. Jeder Thread in .NET enthält die Objekte `CurrentCulture` und `CurrentUICulture`. ASP.NET Core überprüft diese Werte beim Rendern von kulturspezifischen Funktionen. Wenn die Kultur des aktuellen Threads zum Beispiel auf „en-US“ (Englisch, USA) festgelegt ist, gibt `DateTime.Now.ToLongDateString()` „Thursday, February 18, 2016“ aus, wenn `CurrentCulture` jedoch auf „es-ES“ (Spanisch, Spanien) festgelegt ist, wird „jueves, 18 de febrero de 2016“ ausgegeben.

## <a name="resource-files"></a>Ressourcendateien

Eine Ressourcendatei ist ein nützlicher Mechanismus für das Trennen von lokalisierbaren Zeichenfolgen von Code. Übersetzte Zeichenfolgen für die Sprache, die nicht die Standardsprache darstellt, werden in *RESX*-Ressourcendateien isoliert. Möglicherweise möchten Sie z.B. eine spanische Ressourcendatei namens *Welcome.es.resx* erstellen, die übersetzte Zeichenfolgen enthält. „es“ ist der Sprachcode für Spanisch. Erstellen dieser Ressourcendatei in Visual Studio:

1. Führen Sie im **Projektmappen-Explorer** einen Rechtsklick auf den Ordner aus, der die Ressourcendatei enthalten soll, und klicken Sie dann auf **Hinzufügen** > **Neues Element**.

   ![Geschachteltes Kontextmenü: Im Projektmappen-Explorer ist ein Kontextmenü für Ressourcen geöffnet. Ein zweites Kontextmenü ist unter „Hinzufügen“ geöffnet, der Befehl „Neues Element“ wird angezeigt und hervorgehoben.](localization/_static/newi.png)

1. Geben Sie „resource“ (Ressource) im Feld **Search installed templates** (Installierte Vorlagen durchsuchen) ein, und benennen Sie die Datei.

   ![Dialogfeld „Neues Element hinzufügen“](localization/_static/res.png)

1. Geben Sie den Schlüsselwert (native Zeichenfolge) in der Spalte **Name** und die übersetzte Zeichenfolge in der Spalte **Wert** ein.

   ![Welcome.es.resx-Datei (die Willkommensressourcendatei für Spanisch) mit dem Wort „Hello“ in der Spalte „Name“ und dem Wort „Hola“ (Hallo in Spanisch) in der Spalte „Wert“](localization/_static/hola.png)

   Die Datei *Welcome.es.resx* wird in Visual Studio angezeigt.

   ![Die Ressourcendatei „Welcome Spanish (es)“ im Projektmappen-Explorer](localization/_static/se.png)

## <a name="resource-file-naming"></a>Benennung von Ressourcendateien

Ressourcen werden nach dem vollständigen Typnamen ihrer Klasse, abzüglich des Assemblynamens, benannt. Eine französische Ressourcendatei, deren Hauptassembly für die Klasse `LocalizationWebsite.Web.Startup``LocalizationWebsite.Web.dll` ist, würde zum Beispiel den Namen *Startup.fr.resx* erhalten. Eine Ressource für die Klasse `LocalizationWebsite.Web.Controllers.HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Wenn der Namespace Ihrer Zielklasse nicht dem Assemblynamen entspricht, benötigen Sie den vollständigen Typnamen. Eine Ressource für den Typ `ExtraNamespace.Tools` im Beispielprojekt würde z.B. den Namen *ExtraNamespace.Tools.fr.resx* erhalten.

Im Beispielprojekt legt die Methode `ConfigureServices` die `ResourcesPath`-Eigenschaft auf „Resources“ fest. Der relative Projektpfad für den Controller „Home“ der französischen Ressourcendatei ist also *Resources/Controllers.HomeController.fr.resx*. Alternativ können Sie Ordner zum Organisieren von Ressourcendateien verwenden. Für den Controller „Home“ wäre der Pfad *Resources/Controllers/HomeController.fr.resx*. Wenn Sie die Option `ResourcesPath` nicht verwenden, würde sich die *RESX*-Datei im Basisprojektverzeichnis befinden. Die Ressourcendatei für `HomeController` würde den Namen *Controllers.HomeController.fr.resx* erhalten. Ob Sie die Benennungskonventionen mit Punkten oder wie Pfade verwenden, hängt davon ab, wie Sie Ihre Ressourcendateien organisieren möchten.

| Ressourcenname | Punkt- oder Pfadbenennung |
| ------------   | ------------- |
| Resources/Controllers.HomeController.fr.resx | Punkt  |
| Resources/Controllers/HomeController.fr.resx  | Pfad |

Ressourcendateien, die `@inject IViewLocalizer` in Razor-Ansichten verwenden, folgen einem ähnlichen Muster. Die Ressourcendatei für eine Ansicht kann mit der Punkt- oder Pfadbenennung benannt werden. Ressourcendateien der Razor-Ansicht imitieren den Pfad ihrer zugehörigen Ansichtsdatei. Wenn `ResourcesPath` zum Beispiel auf „Resources“ festgelegt wird, ist die französische Ressourcendatei, die der Ansicht *Views/Home/About.cshtml* zugeordnet ist, eine der folgenden zwei:

* Resources/Views/Home/About.fr.resx

* Resources/Views.Home.About.fr.resx

Wenn Sie nicht die Option `ResourcesPath` verwenden, befindet sich die *RESX*-Datei für eine Ansicht im selben Ordner wie die Ansicht.

### <a name="rootnamespaceattribute"></a>RootNamespaceAttribute 

Das [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1)-Attribut stellt den Stammnamespace einer Assembly bereit, wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet. 

> [!WARNING]
> Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist. Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt. 

Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, dann geschieht Folgendes:

* Die Lokalisierung funktioniert standardmäßig nicht.
* Die Lokalisierung schlägt aufgrund der Art und Weise, wie nach Ressourcen innerhalb der Assembly gesucht wird, fehl. `RootNamespace` ist ein Buildzeitwert, der für den ausgeführten Prozess nicht verfügbar ist. 

Wenn sich `RootNamespace` vom `AssemblyName` unterscheidet, schließen Sie Folgendes in *AssemblyInfo.cs* ein (mit den durch die aktuellen Werte ersetzten Parameterwerten):

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

Der vorangehende Code ermöglicht die erfolgreiche Auflösung von RESX-Dateien.

## <a name="culture-fallback-behavior"></a>Kulturfallbackverhalten

Bei der Suche nach einer Ressource initiiert die Lokalisierung „Kulturfallbackverhalten“. Wenn die angeforderte Kultur nicht gefunden wird, setzt sie diese Kultur auf die übergeordnete Kultur zurück. Die Eigenschaft [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) stellt übrigens die übergeordnete Kultur dar. Das bedeutet in der Regel (aber nicht immer), dass der nationale Bezeichner aus der ISO entfernt wird. Beispielsweise ist der in Mexiko gesprochene spanische Sprache „es-MX“. „es“&mdash;Spanisch ist das übergeordnete Element und bezieht sich nicht auf ein einzelnes Land.

Nehmen Sie an, dass Ihre Website eine Anforderung für eine Willkommensressource mit der Kultur „fr-CA“ erhält. Das Lokalisierungssystem sucht der Reihenfolge nach nach der folgenden Ressource und wählt die erste Übereinstimmung aus:

* *Welcome.fr-CA.resx*
* *Welcome.fr.resx*
* *Welcome.resx* (wenn `NeutralResourcesLanguage` „fr-CA“ ist)

Wenn Sie beispielsweise den Kulturkennzeichner „.fr“ entfernen und die Kultur auf „Französisch“ festgelegt ist, wird die Standardressourcendatei gelesen, und Zeichenfolgen werden lokalisiert. Der Ressourcen-Manager kennzeichnet eine Standard- oder Fallbackressource, wenn keine Entsprechung für die angeforderte Kultur gefunden wird. Wenn Sie nur den Schlüssel zurückgeben möchten, während eine Ressource für die angefragte Kultur fehlt, darf keine Standardressourcendatei festgelegt sein.

### <a name="generate-resource-files-with-visual-studio"></a>Erstellen von Ressourcendateien mit Visual Studio

Wenn Sie eine Ressourcendatei in Visual Studio erstellen, ohne eine Kultur im Dateinamen (z.B. *Welcome.resx*) festzulegen, erstellt Visual Studio eine C#-Klasse mit einer Eigenschaft für jede Zeichenfolge. Das ist in der Regel nicht das, was Sie mit ASP.NET Core erreichen wollen. In der Regel gibt es keine Standard-*RESX*-Ressourcendatei (eine *RESX*-Datei ohne den Kulturnamen). Es wird empfohlen, dass Sie eine *RESX*-Datei mit einem Kulturnamen erstellen (z.B. *Welcome.fr.resx*). Wenn Sie eine *RESX*-Datei mit einem Kulturnamen erstellen, erstellt Visual Studio keine Klassendatei.

### <a name="add-other-cultures"></a>Hinzufügen von anderen Kulturen

Jede Kombination von Sprache und Kultur (mit Ausnahme der Standardsprache) erfordert eine eindeutige Ressourcendatei. Sie erstellen Ressourcendateien für verschiedene Kulturen und Gebietsschemas, indem Sie neue Ressourcendateien erstellen, in denen ISO-Sprachcodes im Dateinamen enthalten sind (z.B. **en-us** **fr-ca**, und **en-gb**). Diese ISO-Codes werden zwischen dem Dateinamen und der Erweiterung *.resx* platziert, z.B. *Welcome.es-MX.resx* (Spanisch/Mexiko).

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a>Implementieren Sie eine Strategie zum Auswählen der Sprache bzw. Kultur für jede Anforderung.

### <a name="configure-localization"></a>Konfigurieren der Lokalisierung

Die Lokalisierung wird über die Methode `Startup.ConfigureServices` konfiguriert:

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* `AddLocalization` fügt die Lokalisierungsdienste dem Dienstcontainer hinzu. Im obigen Codebeispiel wird der Ressourcenpfad auf „Resources“ festgelegt.

* `AddViewLocalization` fügt Unterstützung für lokalisierte Ansichtsdateien hinzu. In diesem Beispiel basiert die Lokalisierung der Ansicht auf dem Suffix der Ansichtsdatei. Zum Beispiel „fr“ in der Datei *Index.fr.cshtml*.

* `AddDataAnnotationsLocalization` fügt Unterstützung für lokalisierte `DataAnnotations`-Validierungsmeldungen durch Abstraktionen von `IStringLocalizer` hinzu.

### <a name="localization-middleware"></a>Lokalisierungsmiddleware

Die aktuell angefragte Kultur wird in der [Middleware](xref:fundamentals/middleware/index) für die Lokalisierung festgelegt. Die Middleware für die Lokalisierung wird in der `Startup.Configure`-Methode aktiviert. Die Lokalisierungsmiddleware muss vor Middleware konfiguriert werden, die möglicherweise die Anforderungskultur prüft (z.B. `app.UseMvcWithDefaultRoute()`).

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

`UseRequestLocalization` initialisiert ein `RequestLocalizationOptions`-Objekt. Bei jeder Anforderung wird die Liste von `RequestCultureProvider` in `RequestLocalizationOptions` aufgelistet und der erste Anbieter, der erfolgreich die Anforderungskultur bestimmen kann, wird verwendet. Die Standardanbieter stammen aus der Klasse `RequestLocalizationOptions`:

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

Die Reihenfolge der Standardliste fängt bei den spezifischsten Anbietern an und endet mit den allgemeinsten. Im Verlauf des Artikels erfahren Sie, wie Sie die Reihenfolge ändern und einen benutzerdefinierten Kulturanbieter hinzufügen. Wenn kein Anbieter die Anforderungskultur bestimmen kann, wird `DefaultRequestCulture` verwendet.

### <a name="querystringrequestcultureprovider"></a>QueryStringRequestCultureProvider

Einige Apps verwenden eine Abfragezeichenfolge, um die <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> festzulegen. Bei Apps, die die Ansätze cookie oder Accept-Language-Header verwenden, ist das Hinzufügen einer Abfragezeichenfolge zur URL für das Debuggen und Testen von Code nützlich. Standardmäßig ist `QueryStringRequestCultureProvider` als erster Lokalisierungsanbieter in der Liste `RequestCultureProvider` registriert. Sie übergeben die Abfragezeichenfolge-Parameter `culture` und `ui-culture`. Im folgenden Beispiel ist die spezifische Kultur (Sprache und Region) auf Spanisch/Mexiko festgelegt:

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

Wenn Sie nur eine der beiden Abfragezeichenfolgen (`culture` oder `ui-culture`) übergeben, setzt der Anbieter für Abfragezeichenfolgen beide Werte entsprechend der übergebenen Abfrage fest. Wenn beispielsweise nur die Kultur festgelegt wird, werden sowohl `Culture` als auch `UICulture` wie folgt festgelegt:

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a>CookieRequestCultureProvider

Produktions-Apps bieten oft einen Mechanismus zum Festlegen der Kultur mithilfe des ASP.NET Core-Kulturcookies. Verwenden Sie die Methode `MakeCookieValue` zum Erstellen eines cookies.

`CookieRequestCultureProvider` `DefaultCookieName` gibt den Standardcookienamen zurück, mit dem ermittelt wird, welche Kulturinformationen der Benutzer bevorzugt. Der Standardcookiename lautet `.AspNetCore.Culture`.

Das cookieformat ist `c=%LANGCODE%|uic=%LANGCODE%`, wobei `c` für `Culture` steht und `uic` für `UICulture`, zum Beispiel:

```
c=en-UK|uic=en-US
```

Wenn Sie nur eine Kulturinformation und eine Benutzeroberflächenkultur angeben, wird die angegebene Kultur sowohl für die Kulturinformation als auch die Benutzeroberflächenkultur verwendet.

### <a name="the-accept-language-http-header"></a>Der Accept-Language-HTTP-Header

Der [Accept-Language-Header](https://www.w3.org/International/questions/qa-accept-lang-locales) ist in den meisten Browsern konfigurierbar und war ursprünglich dafür gedacht, die Sprache des Benutzers anzugeben. Diese Einstellung gibt an, was im Browser zum Senden festgelegt ist oder vom zugrunde liegenden Betriebssystem geerbt wurde. Der Accept-Language-HTTP-Header einer Browseranfrage ist keine unfehlbare Methode zum Erkennen der bevorzugten Sprache des Benutzers (siehe [Setting language preferences in a browser (Festlegen der bevorzugten Sprache in einem Browser)](https://www.w3.org/International/questions/qa-lang-priorities.en.php)). In einer Produktions-App sollten Benutzer die Möglichkeit haben, ihre bevorzugte Kultur anzupassen.

### <a name="set-the-accept-language-http-header-in-ie"></a>Festlegen des Accept-Language-HTTP-Headers in Internet Explorer

1. Klicken Sie auf das Zahnradsymbol und dann auf **Internetoptionen**.

1. Klicken Sie auf **Sprachen**.

   ![Internetoptionen](localization/_static/lang.png)

1. Klicken Sie auf **Spracheinstellungen festlegen**.

1. Klicken Sie auf **Sprache hinzufügen**.

1. Fügen Sie die Sprache hinzu.

1. Klicken Sie auf die Sprache und dann auf **Nach oben**.

### <a name="the-content-language-http-header"></a>Der Content-Language-HTTP-Header

Der [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language)-Entitätsheader:

* Wird verwendet, um die für die Zielgruppe vorgesehenen Sprachen zu beschreiben.
* Ermöglicht einem Benutzer, gemäß seiner bevorzugten Sprache zu differenzieren.

Entitätsheader werden in HTTP-Anforderungen und -Antworten verwendet.

Der `Content-Language`-Header kann durch Festlegen der Eigenschaft `ApplyCurrentCultureToResponseHeaders` hinzugefügt werden.

Hinzufügen des `Content-Language`-Headers:

* Ermöglicht RequestLocalizationMiddleware, den `Content-Language`-Header mit der `CurrentUICulture` festzulegen.
* Macht das explizite Festlegen des `Content-Language`-Antwortheaders überflüssig.

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a>Verwenden eines benutzerdefinierten Anbieters

Angenommen, Sie möchten Ihren Kunden das Speichern ihrer Sprache und Kultur in Ihren Datenbanken ermöglichen. In diesem Fall können Sie einen Anbieter codieren, der diese Werte für den Benutzer abruft. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen benutzerdefinierten Anbieter hinzufügen:

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

Verwenden Sie `RequestLocalizationOptions`, um Lokalisierungsanbieter hinzuzufügen oder zu entfernen.

### <a name="set-the-culture-programmatically"></a>Programmgesteuertes Festlegen der Kultur

Das Beispielprojekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält eine Benutzeroberfläche zum Festlegen von `Culture`. Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* ermöglicht Ihnen das Auswählen der Kultur aus der Liste von unterstützten Kulturen:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

Die Datei *Views/Shared/_SelectLanguagePartial.cshtml* wird dem Abschnitt `footer` der Layoutdatei hinzugefügt, damit sie für alle Ansichten verfügbar ist:

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

Die Methode `SetLanguage` legt das Kulturcookie fest.

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

Sie können *_SelectLanguagePartial.cshtml* dem Beispielcode für dieses Projekt nicht hinzufügen. Das Projekt **Localization.StarterWeb** auf [GitHub](https://github.com/aspnet/entropy) enthält Code, der `RequestLocalizationOptions` durch den Container von [Dependency Injection](dependency-injection.md) an eineRazor-Teilansicht übermittelt.

## <a name="model-binding-route-data-and-query-strings"></a>Routendaten und Abfragezeichenfolgen für die Modellbindung

Weitere Informationen finden Sie unter [Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung](xref:mvc/models/model-binding#glob).

## <a name="globalization-and-localization-terms"></a>Begriffe für die Globalisierung und Lokalisierung

Der Lokalisierungsprozess für Ihre App erfordert ein grundlegendes Verständnis von relevanten Zeichensätzen, die häufig in der modernen Softwareentwicklung verwendet werden, und von den Problemen, die mit ihnen zusammenhängen. Obwohl alle Computer Text als Zahlen (Codes) speichern, speichern verschiedene Systeme denselben Text mit anderen Zahlen. Der Lokalisierungsprozess bezieht sich auf das Übersetzen der Benutzeroberfläche (UI) der App für eine spezifische Kultur bzw. ein bestimmtes Gebietsschema.

[Lokalisierbarkeit](/dotnet/standard/globalization-localization/localizability-review) ist ein Zwischenschritt zum Überprüfen, ob eine globalisierte App bereit für die Lokalisierung ist.

Das Format [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) für den Kulturnamen ist `<languagecode2>-<country/regioncode2>`, wobei `<languagecode2>` der Sprachcode und `<country/regioncode2>` der Unterkulturcode. Zum Beispiel steht `es-CL` für Spanisch (Chile), `en-US` für Englisch (USA) und `en-AU` für Englisch (Australien). [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) ist eine Kombination der ISO 639, bei der zwei Kleinbuchstaben den Kulturcode beschreiben, der einer Sprache zugeordnet ist, und der ISO 3166, bei der zwei Großbuchstaben den Unterkulturcode beschreiben, der einem Land oder einer Region zugeordnet ist. Siehe <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.

Die Internationalisierung (Internationalization) wird oft mit „I18N“ abgekürzt. Diese Abkürzung verwendet den ersten und den letzten Buchstaben und die Anzahl der dazwischen liegenden Buchstaben, 18 steht also für die Menge der Buchstaben zwischen dem ersten „I“ und dem letzten „N“. Das gleiche gilt für Globalisierung (Globalization, G11N) und Lokalisierung (Localization, L10N).

Begriffe:

* Globalisierung (G11N): Der Prozess, durch den eine App mehrere Sprachen und Regionen unterstützen soll.
* Lokalisierung (L10N): Der Prozess, durch den eine App auf eine Sprache und Region angepasst wird.
* Internationalisierung (I18N): Beschreibt sowohl Globalisierung als auch Lokalisierung.
* Kultur: Beschreibt eine Sprache und optional auch eine Region.
* Neutrale Kultur: Eine Kultur, die eine bestimmte Sprache beschreibt, aber keine Region. (Zum Beispiel „en“, „es“)
* Spezifische Kultur: Eine Kultur, die eine bestimmte Sprache und Region beschreibt. (Zum Beispiel „en-US“, „en-GB“, „es-CL“)
* Übergeordnete Kultur: Eine neutrale Kultur, die eine spezifische Kultur enthält. („en“ ist z.B. die übergeordnete Kultur von „en-US“ und „en-GB“)
* Gebietsschema: Ein Gebietsschema ist identisch mit einer Kultur.

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* [Localization.StarterWeb-Projekt](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) wird im Artikel verwendet.
* [Globalisieren und Lokalisieren von .NET-Anwendungen](/dotnet/standard/globalization-localization/index)
* [Ressourcen in RESX-Dateien](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [Microsoft Multilingual App Toolkit](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [Lokalisierung und Generics](http://hishambinateya.com/localization-and-generics)

::: moniker-end
