---
title: ASP.NET Core Blazor-CSS-Isolation
author: daveabrock
description: Erfahren Sie, wie Sie mit der CSS-Isolation den Bereich von CSS auf Ihre Komponenten beschränken können, um Ihr CSS zu vereinfachen und Konflikte mit anderen Komponenten oder Bibliotheken zu vermeiden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529981"
---
# <a name="aspnet-core-blazor-css-isolation"></a>ASP.NET Core Blazor-CSS-Isolation

Von [Dave Brock](https://twitter.com/daveabrock)

Die CSS-Isolation verringert den CSS-Speicherbedarf einer App, indem Abhängigkeiten von globalen Stilen verhindert werden. Außerdem werden damit Stilkonflikte zwischen Komponenten und Bibliotheken vermieden.

## <a name="enable-css-isolation"></a>Aktivieren der CSS-Isolation 

Damit Sie komponentenspezifische Stile definieren können, erstellen Sie eine Datei `.razor.css`, die mit dem Namen der `.razor`-Datei für die Komponente im selben Ordner übereinstimmt. Die Datei `.razor.css` ist eine *bereichsbezogene CSS-Datei*. 

Erstellen Sie für eine `Example`-Komponente in einer `Example.razor`-Datei eine Datei mit dem Namen `Example.razor.css`. Die `Example.razor.css`-Datei muss sich im gleichen Ordner befinden wie die `Example`-Komponente (`Example.razor`). Beim `Example`-Basisnamen der Datei wird die Groß-/Kleinschreibung **nicht** beachtet.

`Pages/Example.razor`:

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

`Pages/Example.razor.css`:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

**Die in `Example.razor.css` definierten Stile werden nur auf die gerenderte Ausgabe der `Example`-Komponente angewandt.** Die CSS-Isolation wird auf HTML-Elemente in der entsprechenden Razor-Datei angewandt. `h1`-CSS-Deklarationen, die an anderer Stelle in der App definiert sind, verursachen keine Konflikte mit den Stilen der `Example`-Komponente.

> [!NOTE]
> Damit die Stilisolation auch bei der Bündelung gewährleistet ist, wird das Importieren von CSS in Razor-Codeblöcken nicht unterstützt.

## <a name="css-isolation-bundling"></a>Bündelung mit CSS-Isolation

Die CSS-Isolation erfolgt zum Zeitpunkt der Erstellung. Während dieses Vorgangs schreibt Blazor CSS-Selektoren um, damit diese mit dem von der Komponente gerenderten Markup übereinstimmen. Diese umgeschriebenen CSS-Stile werden gebündelt und als statisches Objekt in `{PROJECT NAME}.styles.css` erstellt, wobei der Platzhalter `{PROJECT NAME}` das referenzierte Paket oder der Produktname ist.

Auf diese statischen Dateien wird standardmäßig aus dem Stammpfad der App verwiesen. Die Referenzierung auf die gebündelte Datei in der App erfolgt durch Überprüfen des Verweises im `<head>`-Tag des generierten HTML-Codes:

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

Innerhalb der gebündelten Datei ist jede Komponente einem Bereichsbezeichner zugeordnet. Für jede formatierte Komponente wird ein HTML-Attribut im Format `b-<10-character-string>` angehängt. Der Bezeichner ist für jede App eindeutig. In der gerenderten `Counter`-Komponente fügt Blazor einen Bereichsbezeichner an das `h1`-Element an:

```html
<h1 b-3xxtam6d07>
```

Die Datei `ProjectName.styles.css` verwendet den Bereichsbezeichner, um eine Stildeklaration mit Ihrer Komponente zu gruppieren. Im folgenden Beispiel wird der Stil für das vorherige `<h1>`-Element bereitstellt:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

Zum Zeitpunkt der Erstellung wird ein Projektbündel mit der Konvention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` erstellt, wobei der Platzhalter `{STATIC WEB ASSETS BASE PATH}` der statische Basispfad für Webressourcen ist.

Wenn andere Projekte verwendet werden, z. B. NuGet-Pakete oder [Razor-Klassenbibliotheken](xref:blazor/components/class-libraries), gilt für die gebündelte Datei Folgendes:

* Verweise auf Stile erfolgen mithilfe von CSS-Importen.
* Sie wird nicht als statische Webressource der App veröffentlicht, die die Stile verwendet.

## <a name="child-component-support"></a>Unterstützung für untergeordnete Komponenten

Standardmäßig gilt die CSS-Isolation nur für die Komponente, die Sie dem Format `{COMPONENT NAME}.razor.css` zuordnen, wobei der Platzhalter `{COMPONENT NAME}` in der Regel der Komponentenname ist. Verwenden Sie zum Anwenden von Änderungen auf eine untergeordnete Komponente den `::deep`-Kombinator für alle Nachfolgerelemente in der Datei `.razor.css` der übergeordneten Komponente. Der `::deep`-Kombinator wählt Elemente aus, die *Nachfolger* des generierten Bereichsbezeichners eines Elements sind. 

Das folgende Beispiel zeigt die übergeordnete Komponente `Parent`, die eine untergeordnete Komponente namens `Child` hat.

`Pages/Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Shared/Child.razor`:

```razor
<h1>Child Component</h1>
```

Aktualisieren Sie die `h1`-Deklaration in `Parent.razor.css` mit dem `::deep`-Kombinator, um anzugeben, dass die Stildeklaration `h1` auf die übergeordnete Komponente und deren untergeordnete Komponenten angewandt werden muss.

`Pages/Parent.razor.css`:

```css
::deep h1 { 
    color: red;
}
```

Der `h1`-Stil gilt jetzt für die Komponenten `Parent` und `Child`, ohne dass eine separate CSS-Datei für die untergeordnete Komponente erstellt werden muss.

Der `::deep`-Kombinator funktioniert nur mit Nachfolgerelementen. Das folgende Markup wendet die `h1`-Stile wie erwartet auf Komponenten an. Der Bereichsbezeichner der übergeordneten Komponente wird auf das `div`-Element angewendet, damit der Browser weiß, dass Stile von der übergeordneten Komponente geerbt werden.

`Pages/Parent.razor`:

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

Durch Ausschließen des `div`-Elements wird jedoch die Nachfolgerbeziehung entfernt. Im folgenden Beispiel wird der Stil **nicht** auf die untergeordnete Komponente angewendet.

`Pages/Parent.razor`:

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a>Unterstützung für CSS-Präprozessoren

CSS-Präprozessoren tragen zur Verbesserung der CSS-Entwicklung bei, indem sie Features wie Variablen, Schachtelung, Module, Mischung und Vererbung nutzen. CSS-Isolation unterstützt CSS-Präprozessoren wie Sass oder Less zwar nicht nativ, dennoch können CSS-Präprozessoren nahtlos integriert werden, sofern die Kompilierung des Präprozessors erfolgt, bevor Blazor die CSS-Selektoren während des Buildprozesses umschreibt. Konfigurieren Sie z. B. mithilfe von Visual Studio die vorhandene Präprozessorkompilierung als Aufgabe **Vor Build** im Aufgabenausführungs-Explorer von Visual Studio.

Viele NuGet-Pakete von Drittanbietern (z. B. [Delegat.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)) können die SASS-/SCSS-Dateien am Anfang des Buildprozesses – noch vor der CSS-Isolation – kompilieren, ohne dass eine Konfiguration erforderlich ist.

## <a name="css-isolation-configuration"></a>Konfiguration der CSS-Isolation

Die CSS-Isolation ist so konzipiert, dass sie sofort einsatzbereit ist. Es ist aber eine Konfiguration für einige erweiterte Szenarien möglich, wenn z. B. Abhängigkeiten von vorhandenen Tools oder Workflows vorhanden sind.

### <a name="customize-scope-identifier-format"></a>Anpassen des Formats von Bereichsbezeichnern

Standardmäßig wird für Bereichsbezeichner das Format `b-<10-character-string>` verwendet. Um das Format der Bereichsbezeichner anzupassen, aktualisieren Sie die Projektdatei mit dem gewünschten Muster:

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Im vorherigen Beispiel ändert der für `Example.Razor.css` generierte CSS-Code seinen Bereichsbezeichner von `b-<10-character-string>` in `my-custom-scope-identifier`.

Verwenden Sie Bereichsbezeichner, um eine Vererbung mit bereichsbezogenen CSS-Dateien zu erzielen. Im folgenden Beispiel für eine Projektdatei enthält eine `BaseComponent.razor.css`-Datei allgemeine Stile in Komponenten. Eine `DerivedComponent.razor.css`-Datei erbt diese Stile.

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Verwenden Sie den Platzhalteroperator (`*`), um Bereichsbezeichner für mehrere Dateien freizugeben:

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a>Ändern des Basispfads für statische Webressourcen

Die Datei `scoped.styles.css` wird im Stammverzeichnis der App generiert. Verwenden Sie in der Projektdatei die `StaticWebAssetBasePath`-Eigenschaft, um den Standardpfad zu ändern. Im folgenden Beispiel werden die Datei `scoped.styles.css` und die restlichen Ressourcen der App unter dem Pfad `_content` gespeichert:

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a>Deaktivieren der automatischen Bündelung

Wenn Sie das Verhalten von Blazor beim Veröffentlichen und Laden bereichsbezogener Dateien zur Laufzeit ändern möchten, verwenden Sie die `DisableScopedCssBundling`-Eigenschaft. Die Verwendung dieser Eigenschaft bedeutet, dass die isolierten CSS-Dateien durch andere Tools oder Prozesse aus dem Verzeichnis `obj` übernommen und zur Laufzeit veröffentlicht und geladen werden:

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a>Unterstützung für Razor-Klassenbibliothek (RCL)

Wenn eine [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) isolierte Stile bereitstellt, zeigt das `href`-Attribut des `<link>`-Tags auf `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, wobei die Platzhalter für Folgendes stehen:

* `{STATIC WEB ASSET BASE PATH}`: Basispfad der statischen Webressource
* `{ASSEMBLY NAME}`: Assemblyname der Klassenbibliothek

Im folgenden Beispiel:

* Der Basispfad der statischen Webressource lautet `_content/ClassLib`.
* Der Assemblyname der Klassenbibliothek lautet `ClassLib`.

`wwwroot/index.html` (Blazor WebAssembly) oder `Pages_Host.cshtml` (Blazor Server):

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Weitere Informationen zu RCLs und Komponentenbibliotheken finden Sie unter folgendem Link:

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
