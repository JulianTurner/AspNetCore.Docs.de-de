---
title: Klassenbibliotheken für ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten aus einer externen Komponentenbibliothek in Blazor-Apps einbezogen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 82969bf92965bfdeb1d1474ab47ca74ecbe6dd97
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080302"
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

::: moniker range=">= aspnetcore-5.0"

Um die `my-component`-CSS-Klasse von `Component1` für die Komponente bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek unter Verwendung der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks in `Component1.razor` her:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

Um das Stylesheet in der gesamten App bereitzustellen, können Sie alternativ eine Verknüpfung mit dem Stylesheet der Bibliothek in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) der App erstellen:

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

Wenn die `Link`-Komponente in einer untergeordneten Komponente verwendet wird, ist die verknüpfte Ressource auch für jede andere untergeordnete Komponente der übergeordneten Komponente verfügbar, solange die untergeordnete Komponente mit der `Link`-Komponente gerendert wird. Der Unterschied zwischen der Verwendung der `Link`-Komponente in einer untergeordneten Komponente und dem Platzieren eines `<link>`-HTML-Tags in `wwwroot/index.html` oder `Pages/_Host.cshtml` besteht darin, dass das gerenderte HTML-Tag einer Rahmenkomponente:

* Nach Anwendungszustand geändert werden kann. Ein hartcodiertes `<link>`-HTML-Tag kann nicht nach Anwendungszustand geändert werden.
* Aus dem HTML-`<head>` entfernt wird, wenn die übergeordnete Komponente nicht mehr gerendert wird.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Um die `my-component`-CSS-Klasse von `Component1`bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) der App her:

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a>Erstellen einer Klassenbibliothek für Razor-Komponenten mit statischen Objekten

Eine RCL kann statische Objekte enthalten. Die statischen Objekte sind für jede App verfügbar, die die Bibliothek nutzt. Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a>Bereitstellen von Komponenten und statischen Ressourcen für mehrere gehostete Blazor-Apps

Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.

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

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [Hinzufügen eines XML-IL-Linker-Konfigurationsdatei (Intermediate Language, Zwischensprache) zu einer Bibliothek](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
