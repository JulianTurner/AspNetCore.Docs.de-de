---
title: Klassenbibliotheken für ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten aus einer externen Komponentenbibliothek in Blazor-Apps einbezogen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 24a5b93a18cfe36c50d9739ba56d12aca41615c0
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570158"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a>Klassenbibliotheken für ASP.NET Core-Razor-Komponenten

Von [Simon Timms](https://github.com/stimms)

Komponenten können in einer [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) projektübergreifend gemeinsam genutzt werden. Eine *Klassenbibliothek für Razor-Komponenten* kann wie folgt einbezogen werden:

* Über ein anderes Projekt in der Projektmappe.
* Über ein NuGet-Paket.
* Über eine referenzierte .NET-Bibliothek.

Genauso wie Komponenten normale .NET-Typen sind, sind von der RCL bereitgestellte Komponenten normale .NET-Assemblys.

## <a name="create-an-rcl"></a>Erstellen einer RCL

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Klicken Sie auf **Razor-Klassenbibliothek**. Klicken Sie auf **Weiter**.
1. Klicken Sie im Dialogfeld **Neue Razor-Klassenbibliothek erstellen** auf **Erstellen**.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Die Beispiele in diesem Thema verwenden den Projektnamen `ComponentLibrary`. Wählen Sie **Erstellen** aus.
1. Fügen Sie die RCL zu einer Projektmappe hinzu:
   1. Klicken Sie mit der rechten Maustaste auf die Projektmappe. Wählen Sie **Hinzufügen** > **Vorhandenes Projekt** aus.
   1. Navigieren Sie zur Projektdatei der RCL.
   1. Wählen Sie die Projektdatei (`.csproj`) der RCL aus.
1. Fügen Sie einen Verweis auf die RCL aus der App hinzu:
   1. Klicken Sie mit der rechten Maustaste auf das App-Projekt. Wählen Sie **Hinzufügen** > **Verweis** aus.
   1. Wählen Sie das RCL-Projekt aus. Klicken Sie auf **OK**.

> [!NOTE]
> Wenn das Kontrollkästchen **Seiten und Ansichten unterstützen** beim Erstellen der RCL aus der Vorlage aktiviert ist, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

1. Verwenden Sie die Vorlage **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) in einer Befehlsshell. Im folgenden Beispiel wird eine RCL mit dem Namen `ComponentLibrary` erstellt. Der Ordner, der `ComponentLibrary` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > Wenn der Switch `-s|--support-pages-and-views` beim Erstellen der RCL aus der Vorlage verwendet wird, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.

1. Verwenden Sie den Befehl [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell, um die Bibliothek zu einem bestehenden Projekt hinzuzufügen. Im folgenden Beispiel wird die RCL der App hinzugefügt. Führen Sie den folgenden Befehl aus dem Projektordner der App mit dem Pfad zur Bibliothek aus:

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>Verwenden einer Bibliothekskomponente

Um Komponenten, die in einer Bibliothek definiert sind, in einem anderen Projekt zu verwenden, verwenden Sie einen der folgenden Ansätze:

* Verwenden Sie den vollständigen Typnamen mit dem Namespace.
* Verwenden Sie die Razor-Anweisung [`@using`](xref:mvc/views/razor#using). Einzelne Komponenten können über den Namen hinzugefügt werden.

In den folgenden Beispielen ist `ComponentLibrary` eine Komponentenbibliothek, die die `Component1`-Komponente (`Component1.razor`) enthält. Die `Component1`-Komponente ist eine Beispielkomponente, die bei der Erstellung der Bibliothek automatisch durch die RCL-Projektvorlage hinzugefügt wird.

Verweisen Sie mit dem zugehörigen Namespace auf die `Component1`-Komponente:

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

Alternativ können Sie die Bibliothek mit einer [`@using`](xref:mvc/views/razor#using)-Anweisung in den Gültigkeitsbereich einbeziehen und die Komponente ohne ihren Namespace verwenden:

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

Fügen Sie die `@using ComponentLibrary`-Anweisung optional in die `_Import.razor`-Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein ganzes Projekt zur Verfügung zu stellen. Fügen Sie die Anweisung auf beliebiger Ebene zu einer `_Import.razor`-Datei hinzu, um den Namespace auf eine einzelne Komponente oder eine Reihe von Komponenten innerhalb eines Ordners anzuwenden.

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

Um die `my-component`-CSS-Klasse von `Component1`bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) der App her:

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Erstellen einer Klassenbibliothek für Razor-Komponenten mit statischen Objekten

Eine RCL kann statische Objekte enthalten. Die statischen Objekte sind für jede App verfügbar, die die Bibliothek nutzt. Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Bereitstellen von Komponenten und statischen Ressourcen für mehrere gehostete Blazor-Apps

Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a>Browserkompatibilitäts-Analysetool für Blazor WebAssembly

Blazor WebAssembly-Apps zielen auf die vollständige .NET-API-Oberfläche ab, aber aufgrund von Browsersandboxeinschränkungen werden nicht alle .NET-APIs in WebAssembly unterstützt. Nicht unterstützte APIs lösen <xref:System.PlatformNotSupportedException> bei der Ausführung in Webassembly aus. Ein Plattformkompatibilitäts-Analysetool warnt den Entwickler, wenn die API APIs verwendet, die nicht von den Zielplattformen der App unterstützt werden. Bei Blazor WebAssembly-Apps bedeutet dies eine Überprüfung, dass die APIs in Browsern unterstützt werden. Das Kommentieren von .NET Framework-APIs für das Kompatibilitätsanalysetool Analyzer ist ein fortlaufenden Prozess, sodass nicht alle .NET Framework-APIs derzeit mit Anmerkungen versehen sind.

Blazor WebAssembly- und Razor-Klassenbibliotheksprojekte aktivieren *automatisch* Browserkompatibilitätsprüfungen, indem `browser` als unterstützte Plattform mit dem MSBuild-Element `SupportedPlatform` hinzugefügt wird. Bibliotheksentwickler können das `SupportedPlatform`-Element manuell zur Projektdatei einer Bibliothek hinzufügen, um das Feature zu aktivieren:

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

Geben Sie beim Erstellen einer Bibliothek an, dass eine bestimmte API in Browsern nicht unterstützt wird, indem Sie `browser` für <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>angeben:

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

Weitere Informationen finden Sie unter [Kommentieren von APIs als nicht unterstützt auf bestimmten Plattformen (dotnet/designs-GitHub-Repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).

## <a name="no-locblazor-javascript-isolation-and-object-references"></a>Blazor-JavaScript-Isolierung und Objektverweise

Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). JavaScript-Isolierung bietet die folgenden Vorteile:

* Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.
* Consumer der Bibliothek und Komponenten müssen das zugehörige JavaScript nicht manuell importieren.

Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a>Erstellen, Verpacken und Liefern an NuGet

Da es sich bei Komponentenbibliotheken um standardmäßige .NET-Bibliotheken handelt, unterscheidet sich das Verpacken und Liefern an NuGet nicht vom Verpacken und Liefern einer beliebigen Bibliothek an NuGet. Das Verpacken wird mit dem Befehl [`dotnet pack`](/dotnet/core/tools/dotnet-pack) in einer Befehlsshell durchgeführt:

```dotnetcli
dotnet pack
```

Laden Sie das Paket mit dem Befehl [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) in einer Befehlsshell in NuGet hoch.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Hinzufügen eines XML-IL-Trimmer-Konfigurationsdatei (Intermediate Language, Zwischensprache) zu einer Bibliothek](xref:blazor/host-and-deploy/configure-trimmer)
* [CSS-Isolationsunterstützung mit Razor-Klassenbibliotheken](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Hinzufügen eines XML-IL-Linker-Konfigurationsdatei (Intermediate Language, Zwischensprache) zu einer Bibliothek](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
