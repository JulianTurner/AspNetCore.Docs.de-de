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
ms.openlocfilehash: 4fec0fa750b9209849030d0d6b7de8f4e163d62f
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570132"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a>ASP.NET Core Blazor-CSS-Isolation

Von [Dave Brock](https://twitter.com/daveabrock)

Die CSS-Isolation verringert den CSS-Speicherbedarf einer App, indem Abhängigkeiten von globalen Stilen verhindert werden. Außerdem werden damit Stilkonflikte zwischen Komponenten und Bibliotheken vermieden.

## <a name="enable-css-isolation"></a>Aktivieren der CSS-Isolation 

Damit Sie komponentenspezifische Stile definieren können, erstellen Sie eine Datei `.razor.css`, die mit dem Namen der `.razor`-Datei für die Komponente übereinstimmt. Eine solche Datei `.razor.css` ist eine *bereichsbezogene CSS-Datei*. 

Erstellen Sie für eine `MyComponent`-Komponente, die eine Datei `MyComponent.razor` enthält, eine Datei mit dem Namen `MyComponent.razor.css`. Beim `MyComponent`-Wert im Dateinamen von `.razor.css` wird **nicht** zwischen Groß- und Kleinschreibung unterschieden.

Wenn Sie beispielsweise der `Counter`-Komponente in der Standardprojektvorlage Blazor CSS-Isolation hinzufügen möchten, fügen Sie neben der Datei `Counter.razor` eine neue Datei mit dem Namen `Counter.razor.css` und dann folgenden CSS-Code hinzu:

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

Die in `Counter.razor.css` definierten Stile werden nur auf die gerenderte Ausgabe der `Counter`-Komponente angewandt. `h1`-CSS-Deklarationen, die an anderer Stelle in der App definiert sind, verursachen keine Konflikte mit `Counter`-Stilen.

> [!NOTE]
> Damit die Stilisolation auch bei der Bündelung gewährleistet ist, werden `@import` Razor-Blöcke in bereichsbezogenen CSS-Dateien nicht unterstützt.

## <a name="css-isolation-bundling"></a>Bündelung mit CSS-Isolation

Die CSS-Isolation erfolgt zum Zeitpunkt der Erstellung. Während dieses Vorgangs schreibt Blazor CSS-Selektoren um, damit diese mit dem von der Komponente gerenderten Markup übereinstimmen. Diese umgeschriebenen CSS-Stile werden gebündelt und als statisches Objekt in `{PROJECT NAME}.styles.css` erstellt, wobei der Platzhalter `{PROJECT NAME}` das referenzierte Paket oder der Produktname ist.

Auf diese statischen Dateien wird standardmäßig aus dem Stammpfad der App verwiesen. Die Referenzierung auf die gebündelte Datei in der App erfolgt durch Überprüfen des Verweises im `<head>`-Tag des generierten HTML-Codes:

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

Innerhalb der gebündelten Datei ist jede Komponente einem Bereichsbezeichner zugeordnet. Für jede formatierte Komponente wird ein HTML-Attribut im Format `b-<10-character-string>` angehängt. Der Bezeichner ist für jede App eindeutig. In der gerenderten `Counter`-Komponente fügt Blazor einen Bereichsbezeichner an das `h1`-Element an:

```html
<h1 b-3xxtam6d07>
```

Die Datei `MyProjectName.styles.css` verwendet den Bereichsbezeichner, um eine Stildeklaration mit Ihrer Komponente zu gruppieren. Im folgenden Beispiel wird der Stil für das vorherige `<h1>`-Element bereitstellt:

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

Zum Zeitpunkt der Erstellung wird ein Projektbündel mit der Konvention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` erstellt, wobei der Platzhalter `{STATIC WEB ASSETS BASE PATH}` der statische Basispfad für Webressourcen ist.

Wenn andere Projekte verwendet werden, z. B. NuGet-Pakete oder [Razor-Klassenbibliotheken](xref:blazor/components/class-libraries), gilt für die gebündelte Datei Folgendes:

* Verweise auf Stile erfolgen mithilfe von CSS-Importen.
* Sie wird nicht als statische Webressource der App veröffentlicht, die die Stile verwendet.

## <a name="child-component-support"></a>Unterstützung für untergeordnete Komponenten

Standardmäßig gilt die CSS-Isolation nur für die Komponente, die Sie dem Format `{COMPONENT NAME}.razor.css` zuordnen, wobei der Platzhalter `{COMPONENT NAME}` in der Regel der Komponentenname ist. Verwenden Sie zum Anwenden von Änderungen auf eine untergeordnete Komponente den `::deep`-Kombinator für alle Nachfolgerelemente in der Datei `.razor.css` der übergeordneten Komponente. Der `::deep`-Kombinator wählt Elemente aus, die *Nachfolger* des generierten Bereichsbezeichners eines Elements sind. 

Das folgende Beispiel zeigt die übergeordnete Komponente `Parent`, die eine untergeordnete Komponente namens `Child` hat.

`Parent.razor`:

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

`Child.razor`:

```razor
<h1>Child Component</h1>
```

Aktualisieren Sie die `h1`-Deklaration in `Parent.razor.css` mit dem `::deep`-Kombinator, um anzugeben, dass die Stildeklaration `h1` auf die übergeordnete Komponente und deren untergeordnete Komponenten angewandt werden muss:

```css
::deep h1 { 
    color: red;
}
```

Der `h1`-Stil gilt jetzt für die Komponenten `Parent` und `Child`, ohne dass eine separate CSS-Datei für die untergeordnete Komponente erstellt werden muss.

> [!NOTE]
> Der `::deep`-Kombinator funktioniert nur mit Nachfolgerelementen. Die folgende HTML-Struktur wendet die `h1`-Stile wie erwartet auf Komponenten an:
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> In diesem Szenario wendet ASP.NET Core den Bereichsbezeichner der übergeordneten Komponente auf das `div`-Element an, damit der Browser weiß, dass Stile von der übergeordneten Komponente geerbt werden.
>
> Wenn Sie jedoch das `div`-Element ausschließen, wird die Nachfolgerbeziehung aufgehoben, und der Stil wird **nicht** auf die untergeordnete Komponente angewandt:
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a>Unterstützung für CSS-Präprozessoren

CSS-Präprozessoren tragen zur Verbesserung der CSS-Entwicklung bei, indem sie Features wie Variablen, Schachtelung, Module, Mischung und Vererbung nutzen. CSS-Isolation unterstützt CSS-Präprozessoren wie Sass oder Less zwar nicht nativ, dennoch können CSS-Präprozessoren nahtlos integriert werden, sofern die Kompilierung des Präprozessors erfolgt, bevor Blazor die CSS-Selektoren während des Buildprozesses umschreibt. Konfigurieren Sie z. B. mithilfe von Visual Studio die vorhandene Präprozessorkompilierung als Aufgabe **Vor Build** im Aufgabenausführungs-Explorer von Visual Studio.

Viele NuGet-Pakete von Drittanbietern (z. B. [Delegat.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)) können die SASS-/SCSS-Dateien am Anfang des Buildprozesses – noch vor der CSS-Isolation – kompilieren, ohne dass eine zusätzliche Konfiguration erforderlich ist.

## <a name="css-isolation-configuration"></a>Konfiguration der CSS-Isolation

Die CSS-Isolation ist so konzipiert, dass sie sofort einsatzbereit ist. Es ist aber eine Konfiguration für einige erweiterte Szenarien möglich, wenn z. B. Abhängigkeiten von vorhandenen Tools oder Workflows vorhanden sind.

### <a name="customize-scope-identifier-format"></a>Anpassen des Formats von Bereichsbezeichnern

Standardmäßig wird für Bereichsbezeichner das Format `b-<10-character-string>` verwendet. Um das Format der Bereichsbezeichner anzupassen, aktualisieren Sie die Projektdatei mit dem gewünschten Muster:

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

Im vorherigen Beispiel ändert der für `MyComponent.Razor.css` generierte CSS-Code seinen Bereichsbezeichner von `b-<10-character-string>` in `my-custom-scope-identifier`.

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

## <a name="no-locrazor-class-library-rcl-support"></a>Unterstützung für Razor-Klassenbibliothek (RCL)

Wenn eine [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) isolierte Stile bereitstellt, zeigt das `href`-Attribut des `<link>`-Tags auf `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, wobei die Platzhalter für Folgendes stehen:

* `{STATIC WEB ASSET BASE PATH}`: Basispfad der statischen Webressource
* `{ASSEMBLY NAME}`: Assemblyname der Klassenbibliothek

Im folgenden Beispiel:

* Der Basispfad der statischen Webressource lautet `_content/ClassLib`.
* Der Assemblyname der Klassenbibliothek lautet `ClassLib`.

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

Weitere Informationen zu RCLs und Komponentenbibliotheken finden Sie unter folgendem Link:

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>.
