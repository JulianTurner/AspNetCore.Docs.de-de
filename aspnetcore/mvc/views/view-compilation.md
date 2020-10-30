---
title: Razor Datei Kompilierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie die Kompilierung von Razor Dateien in einer ASP.net Core-App erfolgt.
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
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
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059077"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a>Razor Datei Kompilierung in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

RazorDateien mit der Erweiterung *. cshtml* werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert. Die Runtime-Kompilierung kann optional durch Konfigurieren des Projekts aktiviert werden.

## <a name="no-locrazor-compilation"></a>Razor Neuauflage

Kompilierzeit-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor . Wenn diese Option aktiviert ist, ergänzt die Lauf Zeit Kompilierung die Kompilierungs Kompilierung, sodass Razor Dateien aktualisiert werden können, wenn Sie bearbeitet werden.

## <a name="enable-runtime-compilation-at-project-creation"></a>Aktivieren der Lauf Zeit Kompilierung bei der Projekt Erstellung

Die Razor Seiten und MVC-Projektvorlagen enthalten eine Option zum Aktivieren der Lauf Zeit Kompilierung, wenn das Projekt erstellt wird. Diese Option wird in ASP.net Core 3,1 und höher unterstützt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie im Dialogfeld **neue ASP.net Core Webanwendung erstellen** Folgendes:

1. Wählen Sie entweder die Projektvorlage **Webanwendung** oder **Webanwendung (Model-View-Controller)** aus.
1. Aktivieren Sie das Kontrollkästchen **Razor Lauf Zeit Kompilierung aktivieren** .

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Verwenden Sie `-rrc` die `--razor-runtime-compilation` Vorlagen Option oder. Der folgende Befehl erstellt z. b. ein neues Razor pages-Projekt mit aktivierter Runtime-Kompilierung:

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>Aktivieren der Lauf Zeit Kompilierung in einem vorhandenen Projekt

So aktivieren Sie die Lauf Zeit Kompilierung für alle Umgebungen in einem vorhandenen Projekt:

1. Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.
1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält. Zum Beispiel:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>Bedingte Aktivierung der Lauf Zeit Kompilierung in einem vorhandenen Projekt

Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist. Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:

* Es werden kompilierte Ansichten verwendet.
* Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.

So aktivieren Sie die Lauf Zeit Kompilierung nur in der Entwicklungsumgebung:

1. Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.
1. Ändern Sie den Abschnitt Start Profil `environmentVariables` in *launchSettings.js* :
    * Überprüfen `ASPNETCORE_ENVIRONMENT` ist auf festgelegt `"Development"` .
    * Legen Sie `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` auf `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"` fest.

Im folgenden Beispiel ist die Lauf Zeit Kompilierung in der Entwicklungsumgebung für die `IIS Express` `RazorPagesApp` Start Profile und aktiviert:

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

In der-Klasse des Projekts sind keine Codeänderungen erforderlich `Startup` . Zur Laufzeit sucht ASP.net Core nach einem [hostingstartup-Attribut](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) auf Assemblyebene in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` . Das- `HostingStartup` Attribut gibt den auszuführenden App-Startcode an. Dieser Startcode ermöglicht die Lauf Zeit Kompilierung.

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a>Aktivieren der Lauf Zeit Kompilierung für eine Razor Klassenbibliothek

Stellen Sie sich ein Szenario vor, in dem ein Razor Seiten Projekt auf eine [ Razor Klassenbibliothek (RCL)](xref:razor-pages/ui-class) mit dem Namen *myclasslib* verweist. Die RCL enthält eine *_Layout. cshtml* -Datei, die von allen MVC-und Pages-Projekten Ihres Teams genutzt wird Razor . Sie möchten die Runtime-Kompilierung für die Datei *_Layout. cshtml* in dieser RCL aktivieren. Nehmen Sie die folgenden Änderungen im Razor pages-Projekt vor:

1. Aktivieren Sie die Lauf Zeit Kompilierung mit den Anweisungen unter [bedingt Aktivieren der Lauf Zeit Kompilierung in einem vorhandenen Projekt](#conditionally-enable-runtime-compilation-in-an-existing-project).
1. Konfigurieren Sie die Optionen für die Lauf Zeit Kompilierung in `Startup.ConfigureServices` :

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    Im vorangehenden Code wird ein absoluter Pfad zur *myclasslib* -RCL erstellt. Die [physicalfileprovider-API](xref:fundamentals/file-providers#physicalfileprovider) wird verwendet, um Verzeichnisse und Dateien in diesem absoluten Pfad zu suchen. Schließlich wird die `PhysicalFileProvider` Instanz einer Datei Anbieter Auflistung hinzugefügt, die den Zugriff auf die *cshtml* -Dateien der RCL ermöglicht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [ Razor Compileonbuild-und Razor compileonpublish-](xref:razor-pages/sdk#properties) Eigenschaften.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

RazorDateien mit der Erweiterung *. cshtml* werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert. Die Laufzeitkompilierung kann optional aktiviert werden, indem Ihre Anwendung konfiguriert wird.

## <a name="no-locrazor-compilation"></a>Razor Neuauflage

Kompilierzeit-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor . Wenn diese Option aktiviert ist, ergänzt die Lauf Zeit Kompilierung die Kompilierungs Kompilierung, sodass Razor Dateien aktualisiert werden können, wenn Sie bearbeitet werden.

## <a name="runtime-compilation"></a>Laufzeitkompilierung

So aktivieren Sie die Laufzeitkompilierung für alle Umgebungen und Konfigurationsmodi:

1. Installieren Sie [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -nuget-Paket.

1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> enthält. Zum Beispiel:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>Bedingte Aktivierung der Laufzeitkompilierung

Die Laufzeitkompilierung kann so aktiviert werden, dass sie nur für die lokale Entwicklung verfügbar ist. Eine bedingte Aktivierung auf diese Weise stellt für die veröffentlichte Ausgabe Folgendes sicher:

* Es werden kompilierte Ansichten verwendet.
* Die Größe ist geringer.
* Dateiüberwachungselemente in der Produktionsumgebung werden nicht aktiviert.

So aktivieren Sie die Laufzeitkompilierung basierend auf Umgebung und Konfigurationsmodus:

1. Verweisen Sie bedingt auf [Microsoft. aspnetcore. MVC. Razor . Runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) -Paket basierend auf dem aktiven `Configuration` Wert:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. Aktualisieren Sie die `Startup.ConfigureServices`-Methode des Projekts so, dass diese einen Aufruf von `AddRazorRuntimeCompilation` enthält. Führen Sie `AddRazorRuntimeCompilation` bedingt so aus, dass die Ausführung nur dann im Debugmodus erfolgt, wenn die `ASPNETCORE_ENVIRONMENT`-Variable auf `Development` festgelegt ist:

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [ Razor Compileonbuild-und Razor compileonpublish-](xref:razor-pages/sdk#properties) Eigenschaften.
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* Im [Beispiel zur Lauf Zeit Kompilierung auf GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) finden Sie ein Beispiel, das zeigt, wie die Lauf Zeit Kompilierung in Projekten funktioniert.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine Razor Datei wird zur Laufzeit kompiliert, wenn die zugehörige Razor Seite oder MVC-Ansicht aufgerufen wird. RazorDateien werden zum Zeitpunkt der Erstellung und Veröffentlichung mit dem [ Razor SDK](xref:razor-pages/sdk)kompiliert.

## <a name="no-locrazor-compilation"></a>Razor Neuauflage

Build-und Veröffentlichungszeit Kompilierung von Razor Dateien wird vom SDK standardmäßig aktiviert Razor . Das Bearbeiten Razor von Dateien nach ihrer Aktualisierung wird zur Buildzeit unterstützt. Standardmäßig werden nur die Dateien für kompilierte *Views.dll* und keine *cshtml* -Dateien oder Verweise, die zum Kompilieren von Razor Dateien erforderlich sind, mit Ihrer APP bereitgestellt.

> [!IMPORTANT]
> Das Vorkompilierungstool ist veraltet und wird in ASP.NET Core 3.0 entfernt. Es wird empfohlen, zu [ Razor SDK](xref:razor-pages/sdk)zu migrieren.
>
> Das Razor SDK ist nur wirksam, wenn keine vorkompilierungs spezifischen Eigenschaften in der Projektdatei festgelegt sind. Wenn Sie z. b. die-Eigenschaft der *csproj* -Datei festlegen, wird `MvcRazorCompileOnPublish` `true` das Razor SDK deaktiviert.

## <a name="runtime-compilation"></a>Laufzeitkompilierung

Die Kompilierung der Kompilierzeit wird durch die Lauf Zeit Kompilierung von Razor Dateien ergänzt. ASP.net Core MVC kompiliert Dateien neu, Razor Wenn sich der Inhalt einer *cshtml* -Datei ändert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
