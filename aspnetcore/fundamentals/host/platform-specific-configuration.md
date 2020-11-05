---
title: Verwenden von Hostingstartassemblys in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.NET Core-App aus einer externen Assembly mithilfe einer Implementierung von IHostingStartup erweitern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: c12487875db69472ee328dfc7a611ee99974c770
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061053"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Verwenden von Hostingstartassemblys in ASP.NET Core

Von [Pavel Krymets](https://github.com/pakrym)

::: moniker range=">= aspnetcore-3.0"

Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen. Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Das Attribut HostingStartup

Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.

Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält. Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen. Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen.

Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`. `StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Ermitteln geladener Hostingstartassemblys

Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App. Fehler, die beim Laden von Assemblys auftreten, werden protokolliert. Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Deaktivieren des automatischen Ladens von Hostingstartassemblys

Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:

* Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:

  * Hostkonfigurationseinstellung „Verhindern des Hostingstarts“:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`

* Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:

  * Hostkonfigurationseinstellung „Hostingstartausschlussassemblys“:

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.

Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.

## <a name="project"></a>Projekt

Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:

* [Klassenbibliothek](#class-library)
* [Konsolen-App ohne Einstiegspunkt](#console-app-without-an-entry-point)

### <a name="class-library"></a>Klassenbibliothek

In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden. Die Bibliothek enthält ein `HostingStartup`-Attribut.

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*. Die Klassenbibliothek:

* Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert. `ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.
* Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.

Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.

*HostingStartupLibrary/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt. Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf. Das Paket:

* Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert. `ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.
* Enthält ein `HostingStartup`-Attribut.

*HostingStartupPackage/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konsolen-App ohne Einstiegspunkt

*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*

Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält. Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.

In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:

* Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen. Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.
* Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.

Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:

* Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:
  * Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.
  * Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.
* Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen. Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.
* Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.
* Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt. Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.

Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen. Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

Eine Klasse implementiert `IHostingStartup`. Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen. `IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Die Datei für die Abhängigkeiten ( *.deps.json* ) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin* -Ordner fest:

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Nur ein Teil der Datei wird angezeigt. `StartupEnhancement` ist der Name der Assembly im Beispiel.

## <a name="configuration-provided-by-the-hosting-startup"></a>Beim Hostingstart bereitgestellte Konfiguration

Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:

1. Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden. Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.
1. Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden. Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Angeben der Hostingstartassembly

Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an. Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.

Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht. Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung „Hostingstartassemblys“ festgelegt werden:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.

## <a name="activation"></a>Aktivierung

Optionen für die Hostingstartaktivierung:

* [Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich. Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern. Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.
* Kompilierzeitverweis für die Aktivierung erforderlich
  * [NuGet-Paket](#nuget-package)
  * [Ordner „Bin“ des Projekts](#project-bin-folder)

### <a name="runtime-store"></a>Laufzeitspeicher

Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt. Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.

Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

In der Beispiel-App (Projekt *RuntimeStore* ) wird der folgende Befehl verwendet:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.

**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**

Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an. `additionalDeps` ermöglicht Ihnen Folgendes:

* Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json* -Dateien, die beim Start mit der App-eigenen *.deps.json* -Datei zusammengeführt werden.
* Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann

Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:

 1. Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.
 1. Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json* -Datei.

Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Legen Sie die *.deps.json* -Datei an folgendem Speicherort ab:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.
* `{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.
* `{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.
* `{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.

In der Beispiel-App (Projekt *RuntimeStore* ) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.

In der Beispiel-App (Projekt *RuntimeStore* ) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.

Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).

**Bereitstellung**

Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment* -Ordner, der Folgendes enthält:

* Der Laufzeitspeicher des Hostingstarts.
* Die Abhängigkeitendatei des Hostingstarts.
* Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen. Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.

### <a name="nuget-package"></a>NuGet-Paket

In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden. Das Paket enthält ein `HostingStartup`-Attribut. Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:

* Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis). Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden. Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.
* Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.

Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:

* [So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools](/dotnet/core/deploying/creating-nuget-packages)
* [Veröffentlichen von Paketen](/nuget/create-packages/publish-a-package)
* [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Ordner „Bin“ des Projekts

Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden. Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:

* Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis). Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden. Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts ( *DLL* -Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:
  * Das verbrauchende Projekt.
  * Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.
* Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.
* Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:
  * Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin* , in dem die ausführbare Datei ( *.exe* ) der App gespeichert wird.
  * Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen. Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.

## <a name="sample-code"></a>Beispielcode

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:

* Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:
  * NuGet-Paket ( *HostingStartupPackage* )
  * Klassenbibliothek ( *HostingStartupLibrary* )
* Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly ( *StartupDiagnostics* ) aktiviert. Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:
  * Registrierte Dienste
  * Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)
  * Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)
  * Anforderungsheader
  * Umgebungsvariablen

So führen Sie das Beispiel aus:

**Aktivierung über ein NuGet-Paket**

1. Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.
1. Kompilieren Sie die App, und führen Sie diese aus. In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis). Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug* ) als Paketquelle an. Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.

Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache. Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivierung über eine Klassenbibliothek**

1. Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).
1. Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.
1. Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.
1. Kompilieren Sie die App, und führen Sie diese aus. Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll* ) – Kompilierzeitverweis. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.

**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**

1. Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten. PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert. Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.
1. Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus. Das Skript:
   * Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .
   * Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*. Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart. Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden. Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird. Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.
   * Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* . Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben. Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.
   * Legt die Datei *deploy.ps1* im Ordner *deployment* ab.
1. Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus. Das Skript fügt Folgendes:
   * `StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.
   * Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.
1. Führen Sie die Beispiel-App aus.
1. Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen. Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Mit einer <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung (Hostingstart) werden Verbesserungen an einer App beim Start von einer externen Assembly aus vorgenommen. Eine externe Bibliothek kann beispielsweise eine Hostingstartimplementierung verwenden, um zusätzliche Konfigurationsanbieter oder -dienste für eine App bereitzustellen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Das Attribut HostingStartup

Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut gibt an, ob eine Hostingstartassembly vorhanden ist, die zur Laufzeit aktiviert werden kann.

Bei der Einstiegsassembly oder bei der Assembly, die die Klasse `Startup` enthält, wird automatisch geprüft, ob sie das Attribut `HostingStartup` enthält. Die Liste der Assemblys für die Suche nach `HostingStartup`-Attributen wird zur Laufzeit aus der Konfiguration in [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey) geladen. Die Liste der Assemblys, die aus der Ermittlung ausgeschlossen werden sollen, wird aus [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey) geladen. Weitere Informationen finden Sie unter [Webhost: Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies)[Webhost: Auszuschließende Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

Im folgenden Beispiel lautet der Namespace der Hostingstartassembly `StartupEnhancement`. `StartupEnhancementHostingStartup` ist die Klasse mit dem Hostingstartcode:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Das Attribut `HostingStartup` befindet sich in der Regel in der `IHostingStartup`-Implementierungsklassendatei der Hostingstartassembly.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Ermitteln geladener Hostingstartassemblys

Zum Ermitteln von geladenen Hostingstartassemblys aktivieren Sie die Protokollierung und überprüfen die Protokolle der App. Fehler, die beim Laden von Assemblys auftreten, werden protokolliert. Geladene Hostingstartassemblys werden auf der Debugebene protokolliert. Außerdem werden alle Fehler protokolliert.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Deaktivieren des automatischen Ladens von Hostingstartassemblys

Um das automatische Laden von Hostingstartassemblys zu deaktivieren, verwenden Sie einen der folgenden Ansätze:

* Um zu verhindern, dass alle Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf `true` oder `1` fest:
  * Hostkonfigurationseinstellung [Verhindern des Hostingstarts](xref:fundamentals/host/web-host#prevent-hosting-startup).
  * Die Umgebungsvariable `ASPNETCORE_PREVENTHOSTINGSTARTUP`
* Um zu verhindern, dass bestimmte Hostingstartassemblys geladen werden, legen Sie eine der folgenden Einstellungen auf eine durch Semikolons getrennte Zeichenfolge mit Hostingstartassemblys fest, die beim Starten ausgeschlossen werden sollen:
  * Hostkonfigurationseinstellung [Hostingstartausschlussassemblys](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).
  * Die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`

Wenn sowohl die Konfigurationseinstellung für den Host als auch die Umgebungsvariable festgelegt werden, wird das Verhalten durch die Hosteinstellung gesteuert.

Durch das Deaktivieren von Hostingstartassemblys mithilfe der Hosteinstellung oder der Umgebungsvariable wird die Assembly global deaktiviert. Einige Eigenschaften einer App können ebenfalls deaktiviert werden.

## <a name="project"></a>Projekt

Erstellen Sie einen Hostingstart mit einem der folgenden Projekttypen:

* [Klassenbibliothek](#class-library)
* [Konsolen-App ohne Einstiegspunkt](#console-app-without-an-entry-point)

### <a name="class-library"></a>Klassenbibliothek

In einer Klassenbibliothek kann eine Hostingstarterweiterung bereitgestellt werden. Die Bibliothek enthält ein `HostingStartup`-Attribut.

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält die Razor Pages-App *HostingStartupApp* und die Klassenbibliothek *HostingStartupLibrary*. Die Klassenbibliothek:

* Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert. `ServiceKeyInjection` fügt mithilfe des Anbieters der Konfiguration im Arbeitsspeicher ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)) der App-Konfiguration zwei Dienstzeichenfolgen hinzu.
* Enthält ein `HostingStartup`-Attribut, das den Namespace und die Klasse des Hostingstarts angibt.

Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der `ServiceKeyInjection`-Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen.

*HostingStartupLibrary/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Klassenbibliothek festgelegt werden:

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) enthält darüber hinaus auch ein NuGet-Paketprojekt, das den separaten Hostingstart *HostingStartupPackage* bereitstellt. Das Paket weist dieselben Merkmale wie die bereits beschriebene Klassenbibliothek auf. Das Paket:

* Enthält die Hostingstartklasse `ServiceKeyInjection`, die `IHostingStartup` implementiert. `ServiceKeyInjection` fügt der App-Konfiguration zwei Dienstzeichenfolgen hinzu.
* Enthält ein `HostingStartup`-Attribut.

*HostingStartupPackage/ServiceKeyInjection.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

Die Indexseite der App liest und rendert die Konfigurationswerte für die beiden Schlüssel, die von der Hostingstartassembly der Bibliothek festgelegt werden:

*HostingStartupApp/Pages/Index.cshtml.cs* :

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Konsolen-App ohne Einstiegspunkt

*Dieses Verfahren ist nur für .NET Core-Apps, nicht jedoch für .NET Framework verfügbar.*

Eine dynamische Hostingstarterweiterung, die zur Aktivierung keinen Kompilierungszeitverweis erfordert, kann in einer Konsolen-App ohne Einstiegspunkt bereitgestellt werden, die ein `HostingStartup`-Attribut enthält. Durch die Veröffentlichung der Konsolen-App wird eine Hostingstartassembly erstellt, die über den Laufzeitspeicher genutzt werden kann.

In diesem Prozess wird aus folgenden Gründen eine Konsolen-App ohne Einstiegspunkt verwendet:

* Eine Abhängigkeitendatei ist erforderlich, um den Hostingstart in der Hostingstartassembly zu nutzen. Eine Abhängigkeitendatei ist eine ausführbare App-Ressource, die durch das Veröffentlichen einer App, nicht einer Bibliothek, erstellt wird.
* Eine Bibliothek kann dem [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store), der ein ausführbares Projekt benötigt, das die freigegebene Laufzeit als Ziel verwendet, nicht direkt hinzugefügt werden.

Bei der Erstellung eines dynamischen Hostingstarts geschieht Folgendes:

* Eine Hostingstartassembly wird über die Konsolen-App ohne Einstiegspunkt erstellt, für die Folgendes gilt:
  * Sie enthält eine Klasse mit der `IHostingStartup`-Implementierung.
  * Sie enthält ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut zum Identifizieren der `IHostingStartup`-Implementierungsklasse.
* Die Konsolen-App wird veröffentlicht, um die Abhängigkeiten des Hostingstarts abzurufen. Die Veröffentlichung der Konsolen-App hat unter anderem zur Folge, dass nicht verwendete Abhängigkeiten aus der Abhängigkeitendatei entfernt werden.
* Die Abhängigkeitendatei wird so geändert, dass der Laufzeitspeicherort der Hostingstartassembly festgelegt wird.
* Die Hostingstartassembly und die dazu gehörende Abhängigkeitendatei werden im Laufzeitpaketspeicher abgelegt. Damit die Hostingstartassembly und die entsprechende Abhängigkeitendatei erkannt werden, werden sie in einem Paar von Umgebungsvariablen aufgeführt.

Die Konsolen-App verweist auf das Paket [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Ein [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute)-Attribut identifiziert eine Klasse bei der Erstellung von <xref:Microsoft.AspNetCore.Hosting.IWebHost> als eine Implementierung von `IHostingStartup` zum Laden und Ausführen. Im folgenden Beispiel ist der Namespace `StartupEnhancement` und die Klasse ist `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Eine Klasse implementiert `IHostingStartup`. Die <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methode der Klasse verwendet einen <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, um einer App Erweiterungen hinzuzufügen. `IHostingStartup.Configure` wird in der Hoststartassembly von der Runtime vor `Startup.Configure` im Benutzercode aufgerufen. Dadurch kann Benutzercode jede Konfiguration der Hoststartassembly überschreiben.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Die Datei für die Abhängigkeiten ( *.deps.json* ) legt beim Erstellen eine `IHostingStartup`-Projekts den `runtime`-Speicherort der Assembly auf den *bin* -Ordner fest:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Nur ein Teil der Datei wird angezeigt. `StartupEnhancement` ist der Name der Assembly im Beispiel.

## <a name="configuration-provided-by-the-hosting-startup"></a>Beim Hostingstart bereitgestellte Konfiguration

Es gibt zwei Ansätze zur Verarbeitung der Konfiguration, je nachdem, ob die Konfiguration des Hostingstarts oder die der App Vorrang haben soll:

1. Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> zum Laden der Konfiguration, nachdem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt wurden. Bei diesem Ansatz hat die Hostingstartkonfiguration Vorrang vor der Konfiguration der App.
1. Bereitstellen der Konfiguration für die App mit <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zum Laden der Konfiguration, bevor die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>-Delegaten der App ausgeführt werden. Die Konfigurationswerte der App haben bei diesem Ansatz Vorrang vor denen des Hostingstarts.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Angeben der Hostingstartassembly

Geben Sie für einen von der Klassenbibliothek oder von der Konsolen-App bereitgestellten Hostingstart den Namen der Hostingstartassembly in der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` an. Die Umgebungsvariable ist eine durch Semikolons getrennte Liste von Assemblys.

Nur in Hostingstartassemblys wird nach dem `HostingStartup`-Attribut gesucht. Damit die Beispiel-App *HostingStartupApp* die weiter oben beschriebenen Hostingstarts erkennt, wird für die Umgebungsvariable der folgende Wert festgelegt:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Eine Hostingstartassembly kann auch mithilfe der Hostkonfigurationseinstellung [Hostingstartassemblys](xref:fundamentals/host/web-host#hosting-startup-assemblies) festgelegt werden.

Wenn mehrere Hoststartassemblys vorhanden sind, werden ihre <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>-Methoden in der Reihenfolge der aufgelisteten Assemblys ausgeführt.

## <a name="activation"></a>Aktivierung

Optionen für die Hostingstartaktivierung:

* [Laufzeitspeicher:](#runtime-store) Für die Aktivierung ist kein Kompilierzeitverweis erforderlich. Die Beispiel-App legt die Hostingstartassembly und die Abhängigkeitendateien im Ordner *deployment* ab, um die Bereitstellung des Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern. Der Ordner *deployment* enthält darüber hinaus auch ein PowerShell-Skript, das im Bereitstellungssystem Umgebungsvariablen erstellt oder ändert, um den Hostingstart zu ermöglichen.
* Kompilierzeitverweis für die Aktivierung erforderlich
  * [NuGet-Paket](#nuget-package)
  * [Ordner „Bin“ des Projekts](#project-bin-folder)

### <a name="runtime-store"></a>Laufzeitspeicher

Die Hostingstartimplementierung wird im [Laufzeitspeicher](/dotnet/core/deploying/runtime-store) abgelegt. Ein Kompilierzeitverweis auf die Assembly wird von der erweiterten App nicht benötigt.

Nach der Erstellung des Hostingstarts wird mithilfe der Manifestprojektdatei und des Befehls [dotnet store](/dotnet/core/tools/dotnet-store) ein Laufzeitspeicher generiert.

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

In der Beispiel-App (Projekt *RuntimeStore* ) wird der folgende Befehl verwendet:

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Damit die Runtime den Laufzeitspeicher ermitteln kann, wird der Speicherort des Laufzeitspeichers der Umgebungsvariablen `DOTNET_SHARED_STORE` hinzugefügt.

**Ändern und Ablegen der Abhängigkeitendatei des Hostingstarts**

Um die Erweiterung ohne einen Paketverweis auf die Erweiterung zu aktivieren, geben Sie mit `additionalDeps` zusätzliche Abhängigkeiten zur Laufzeit an. `additionalDeps` ermöglicht Ihnen Folgendes:

* Erweitern des App-Bibliotheksdiagramms durch Bereitstellen einer Reihe zusätzlicher *.deps.json* -Dateien, die beim Start mit der App-eigenen *.deps.json* -Datei zusammengeführt werden.
* Bereitstellen der Hostingstartassembly, sodass sie ermittelt und geladen werden kann

Folgender Ansatz wird zum Generieren der zusätzlichen Abhängigkeitendatei empfohlen:

 1. Führen Sie `dotnet publish` für die Manifestdatei des Laufzeitspeichers aus, auf die im vorherigen Abschnitt verwiesen wurde.
 1. Entfernen Sie den Manifestverweis aus den Bibliotheken und dem Abschnitt `runtime` der daraus resultierenden *.deps.json* -Datei.

Im Beispielprojekt wird die Eigenschaft `store.manifest/1.0.0` aus den Abschnitten `targets` und `libraries` entfernt:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Legen Sie die *.deps.json* -Datei an folgendem Speicherort ab:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: Der Speicherort wurde zur Umgebungsvariable `DOTNET_ADDITIONAL_DEPS` hinzugefügt.
* `{SHARED FRAMEWORK NAME}`: Dies ist das freigegebene Framework, das für diese zusätzliche Abhängigkeitsdatei erforderlich ist.
* `{SHARED FRAMEWORK VERSION}`: Dies ist die Mindestversion des freigegebenen Frameworks.
* `{ENHANCEMENT ASSEMBLY NAME}`: Dies ist der Name der Erweiterungsassembly.

In der Beispiel-App (Projekt *RuntimeStore* ) wird die zusätzliche Abhängigkeitendatei an folgendem Speicherort abgelegt:

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Damit die Runtime den Speicherort des Laufzeitspeichers ermitteln kann, wir die zusätzliche Abhängigkeitendatei der Umgebungsvariablen `DOTNET_ADDITIONAL_DEPS` hinzugefügt.

In der Beispiel-App (Projekt *RuntimeStore* ) wird das Erstellen des Laufzeitspeichers und das Generieren der zusätzlichen Abhängigkeitendatei durch ein [PowerShell](/powershell/scripting/powershell-scripting)-Skript durchgeführt.

Beispiele zum Festlegen von Umgebungsvariablen für verschiedene Betriebssysteme finden Sie unter [Use multiple environments (Verwenden mehrerer Umgebungen)](xref:fundamentals/environments).

**Bereitstellung**

Um die Bereitstellung eines Hostingstarts in einer Umgebung mit mehreren Computern zu erleichtern, erstellt die Beispiel-App in der veröffentlichten Ausgabe einen *deployment* -Ordner, der Folgendes enthält:

* Der Laufzeitspeicher des Hostingstarts.
* Die Abhängigkeitendatei des Hostingstarts.
* Ein PowerShell-Skript, das `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` und `DOTNET_ADDITIONAL_DEPS` erstellt oder ändert, um die Aktivierung des Hostingstarts zu unterstützen. Führen Sie das Skript über eine PowerShell-Administratoreingabeaufforderung auf dem Bereitstellungssystem aus.

### <a name="nuget-package"></a>NuGet-Paket

In einem NuGet-Paket kann eine Hostingstarterweiterung bereitgestellt werden. Das Paket enthält ein `HostingStartup`-Attribut. Die vom Paket bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:

* Die Projektdatei der erweiterten App erstellt einen Paketverweis für den Hostingstart in der Projektdatei der App (Kompilierzeitverweis). Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden. Dieses Verfahren gilt für ein in [nuget.org](https://www.nuget.org/) veröffentlichtes Hostingstartassembly-Paket.
* Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.

Weitere Informationen zu NuGet-Paketen und den Laufzeitspeicher finden Sie in den folgenden Themen:

* [So erstellen Sie ein NuGet-Paket mit plattformübergreifenden Tools](/dotnet/core/deploying/creating-nuget-packages)
* [Veröffentlichen von Paketen](/nuget/create-packages/publish-a-package)
* [Laufzeitpaketspeicher](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a>Ordner „Bin“ des Projekts

Eine Hostingstarterweiterung kann durch eine mittels *bin* bereitgestellte Assembly in der erweiterten App bereitgestellt werden. Die von der Assembly bereitgestellten Hostingstarttypen werden für die App mit einem der folgenden Verfahren verfügbar gemacht:

* Die Projektdatei der erweiterten App enthält einen Assemblyverweis auf den Hostingstart (Kompilierzeitverweis). Wenn ein Kompilierzeitverweis eingerichtet ist, werden die Hostingstartassembly und alle dazu gehörenden Abhängigkeiten in die Abhängigkeitendatei der App ( *.deps.json* ) eingebunden. Dieser Ansatz gilt, wenn das Bereitstellungsszenario erfordert, dass ein Kompilierzeitverweis auf die Assembly des Hostingstarts ( *DLL* -Datei) erstellt und die Assembly in eine der beiden Dateien verschoben wird:
  * Das verbrauchende Projekt.
  * Einen Speicherort, auf den das verbrauchende Projekt zugreifen kann.
* Die Abhängigkeitendatei des Hostingstarts wird für die erweiterte App wie im Abschnitt [Laufzeitspeicher](#runtime-store) beschrieben (ohne Kompilierzeitverweis) verfügbar gemacht.
* Wenn das Ziel .NET Framework ist, kann die Assembly in den Standardladekontext geladen werden, was bei .NET Framework bedeutet, dass sich die Assembly an einem der folgenden Speicherorte befindet:
  * Anwendungsbasispfad: Dabei handelt es sich um den Ordner *bin* , in dem die ausführbare Datei ( *.exe* ) der App gespeichert wird.
  * Globaler Assemblycache (GAC): Der GAC speichert Assemblys, die mehrere .NET Framework-Apps gemeinsam nutzen. Weitere Informationen finden Sie unter [Vorgehensweise: Installieren einer Assembly in den globalen Assemblycache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in der .NET Framework-Dokumentation.

## <a name="sample-code"></a>Beispielcode

Der [Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([Herunterladen](xref:index#how-to-download-a-sample)) zeigt Szenarios für die Hostingstartimplementierung:

* Zwei Hostingstartassemblys (Klassenbibliotheken) legen zwei Schlüssel-Wert-Paare der Konfiguration im Arbeitsspeicher fest:
  * NuGet-Paket ( *HostingStartupPackage* )
  * Klassenbibliothek ( *HostingStartupLibrary* )
* Ein Hostingstart wird über eine im Laufzeitspeicher bereitgestellte Assembly ( *StartupDiagnostics* ) aktiviert. Die Assembly fügt der App zum Start zwei Middlewares hinzu, die Diagnoseinformationen zu folgenden Komponenten bereitstellen:
  * Registrierte Dienste
  * Adresse (Schema, Host, Basispfad, Pfad, Abfragezeichenfolge)
  * Verbindung (Remote-IP, Remoteport, lokale IP, lokaler Port, Clientzertifikat)
  * Anforderungsheader
  * Umgebungsvariablen

So führen Sie das Beispiel aus:

**Aktivierung über ein NuGet-Paket**

1. Kompilieren Sie das Paket *HostingStartupPackage* mit dem Befehl [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Fügen Sie den Assemblynamen des Pakets *HostingStartupPackage* zur Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` hinzu.
1. Kompilieren Sie die App, und führen Sie diese aus. In der erweiterten App ist ein Paketverweis vorhanden (Kompilierzeitverweis). Eine `<PropertyGroup>` in der Projektdatei der App gibt die Ausgabe des Paketprojekts ( *../HostingStartupPackage/bin/Debug* ) als Paketquelle an. Dadurch kann die Anwendung das Paket verwenden, ohne das Paket in [nuget.org](https://www.nuget.org/) hochzuladen. Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode des Pakets festgelegten Werten.

Wenn Sie am Projekt *HostingStartupPackage* Änderungen vornehmen und das Projekt erneut kompilieren, bereinigen Sie die lokalen NuGet-Paketcaches, um sicherzustellen, dass die *HostingStartupApp* das aktualisierte Paket erhält und kein veraltetes Paket aus dem lokalen Cache. Um die lokalen NuGet-Caches zu bereinigen, führen Sie den folgenden [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals)-Befehl aus:

```dotnetcli
dotnet nuget locals all --clear
```

**Aktivierung über eine Klassenbibliothek**

1. Kompilieren Sie die Klassenbibliothek *HostingStartupLibrary* mit dem Befehl [dotnet build](/dotnet/core/tools/dotnet-build).
1. Fügen Sie der Umgebungsvariablen `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` den Assemblynamen *HostingStartupLibrary* der Klassenbibliothek hinzu.
1. Stellen Sie die Assembly der Klassenbibliothek mittels *bin* für die App bereit, indem Sie die Datei *HostingStartupLibrary.dll* aus der kompilierten Ausgabe der Klassenbibliothek in den Ordner *bin/Debug* der App kopieren.
1. Kompilieren Sie die App, und führen Sie diese aus. Eine `<ItemGroup>` in der Projektdatei der App verweist auf die Assembly der Klassenbibliothek ( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll* ) (Kompilierzeitverweis). Weitere Informationen finden Sie in den Hinweisen in der Projektdatei von HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Die von der Indexseite gerenderten Schlüsselwerte der Dienstkonfiguration entsprechen den durch die `ServiceKeyInjection.Configure`-Methode der Klassenbibliothek festgelegten Werten.

**Aktivierung über eine mittels Laufzeitspeicher bereitgestellten Assembly**

1. Im Projekt *StartupDiagnostics* wird [PowerShell](/powershell/scripting/powershell-scripting) verwendet, um die Datei *StartupDiagnostics.deps.json* zu bearbeiten. PowerShell ist standardmäßig auf Windows-Betriebssystemen ab Windows 7 SP1 und Windows Server 2008 R2 SP1 installiert. Im Artikel [Installieren verschiedener Versionen von PowerShell](/powershell/scripting/install/installing-powershell) erfahren Sie, wie Sie PowerShell auf anderen Plattformen erwerben können.
1. Führen Sie das Skript *build.ps1* im Ordner *RuntimeStore* aus. Das Skript:
   * Generiert das `StartupDiagnostics`-Paket im Ordner *obj\packages* .
   * Generiert den Laufzeitspeicher für `StartupDiagnostics` im Ordner *store*. Der `dotnet store`-Befehl im Skript verwendet den [Laufzeitbezeichner (RID)](/dotnet/core/rid-catalog) `win7-x64` für einen für Windows bereitgestellten Hostingstart. Wenn der Hostingstart für eine andere Laufzeit bereitgestellt wird, muss in Zeile 37 die entsprechende RID eingegeben werden. Der Runtimespeicher für `StartupDiagnostics` würde später in den Runtimespeicher des Benutzers oder Systems auf dem Computer verschoben, auf dem die Assembly verwendet wird. Der Installationsspeicherort für den Benutzerruntimespeicher für die `StartupDiagnostics`-Assembly ist *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.
   * Generiert die `additionalDeps` für `StartupDiagnostics` im Ordner *additionalDeps* . Die zusätzlichen Abhängigkeiten würden später in die zusätzlichen Abhängigkeiten des Benutzers oder Systems verschoben. Der Installationsspeicherort der zusätzlichen Abhängigkeiten des Benutzers `StartupDiagnostics` ist *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.
   * Legt die Datei *deploy.ps1* im Ordner *deployment* ab.
1. Führen Sie das Skript *deploy.ps1* im Ordner *deployment* aus. Das Skript fügt Folgendes:
   * `StartupDiagnostics` an die Umgebungsvariable `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * Der Pfad der Hostingstartabhängigkeiten (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_ADDITIONAL_DEPS`-Umgebungsvariablen.
   * Der Pfad des Runtimespeichers (im Ordner *deployment* des RuntimeStore-Projekts) zur `DOTNET_SHARED_STORE`-Umgebungsvariablen.
1. Führen Sie die Beispiel-App aus.
1. Fordern Sie den Endpunkt `/services` an, um die registrierten Dienste der App anzuzeigen. Fordern Sie den Endpunkt `/diag` an, um Diagnoseinformationen anzuzeigen.

::: moniker-end
