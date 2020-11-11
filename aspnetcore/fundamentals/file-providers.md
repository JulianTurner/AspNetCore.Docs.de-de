---
title: Dateianbieter in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dateisystemzugriff durch die Verwendung von Dateianbietern abstrahiert.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 16e5ead9898125c804da4d60322510474201d897
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059441"
---
# <a name="file-providers-in-aspnet-core"></a>Dateianbieter in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern. Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet. Zum Beispiel:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.
* Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.
* [Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.
* .NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dateianbieterschnittstellen

Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` stellt Methoden zur Verfügung, zum:

* Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum

Sie können die Datei mithilfe der <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>-Methode lesen.

Die Beispiel-App *FileProviderSample* demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Dependency Injection](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Dateianbieterimplementierungen

In der folgenden Tabelle werden Implementierungen von `IFileProvider` aufgeführt.

| Implementierung | Beschreibung |
| -------------- | ----------- |
| [CompositeFileProvider](#compositefileprovider) | Diese Implementierung wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von mindestens einem anderen Anbieter bereitzustellen. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Diese Implementierung wird für den Zugriff auf in Assemblys eingebettete Dateien verwendet. |
| [PhysicalFileProvider](#physicalfileprovider) | Diese Implementierung wird für den Zugriff auf die physischen Dateien des Systems verwendet. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem. `PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet. Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen. Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

Beim direkten Instanziieren dieses Anbieters ist ein absoluter Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient. Globmuster werden im Verzeichnispfad nicht unterstützt.

Im folgenden Code wird die Verwendung von `PhysicalFileProvider` zum Abrufen von Verzeichnisinhalten und Dateiinformationen veranschaulicht:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Typen im vorherigen Beispiel:

* `provider` ist ein `IFileProvider`.
* `contents` ist ein `IDirectoryContents`.
* `fileInfo` ist ein `IFileInfo`.

Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen. Globmuster können nicht an die `GetFileInfo`-Methode übergeben werden. Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.

Die Beispiel-App *FileProviderSample* erstellt den Anbieter in der `Startup.ConfigureServices`-Methode mithilfe von <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind. Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.

So generieren Sie ein Manifest der eingebetteten Dateien:

1. Fügen Sie das NuGet-Paket [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) zu Ihrem Projekt hinzu.
1. Legen Sie die `<GenerateEmbeddedFilesManifest>` -Eigenschaft auf `true`fest. Geben Sie die einzubettenden Dateien mit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.

Die Beispiel-App *FileProviderSample* erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.

*Startup.cs* :

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Mit zusätzlichen Überladungen können Sie:

* Einen relativen Dateipfad angeben.
* Dateien einem zuletzt geänderten Datum zuordnen.
* Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.

| Überladung | Beschreibung |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter. Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>). Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter. `manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält. |

### <a name="compositefileprovider"></a>CompositeFileProvider

Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen. Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.

In der Beispiel-App *FileProviderSample* stellen die Anbieter `PhysicalFileProvider` und `ManifestEmbeddedFileProvider` Dateien für einen `CompositeFileProvider`-Anbieter bereit, der im Dienstcontainer der App registriert ist. Der folgende Code befindet sich in der `Startup.ConfigureServices`-Methode des Projekts:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Überwachen auf Änderungen

Die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>-Methode bietet ein Szenario zum Überwachen von Änderungen an Dateien oder Verzeichnissen. Die `Watch`-Methode:

* akzeptiert eine Dateipfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.
* Gibt einen <xref:Microsoft.Extensions.Primitives.IChangeToken> zurück.

Das resultierende Änderungstoken stellt Folgendes zur Verfügung:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden. Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf. Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.

Die Beispiel-App *WatchConsole* schreibt eine Nachricht, wenn eine *TXT* -Datei im Verzeichnis *TextFiles* geändert wird:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig . Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).

### <a name="glob-patterns"></a>Globmuster

Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*. Geben Sie Gruppen von Dateien mit diesen Mustern an. Die zwei Platzhalterzeichen sind `*` und `**`:

**`*`**  
Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung. Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.

**`**`**  
Überprüft alles auf mehrere Verzeichnisebenen. Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.

Die folgende Tabelle enthält gängige Beispiele für Globmuster.

|Muster  |Beschreibung  |
|---------|---------|
|`directory/file.txt`|Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.|
|`directory/*.txt`|Entspricht allen Dateien mit *.txt* -Erweiterung in einem bestimmten Verzeichnis.|
|`directory/*/appsettings.json`|Dieses Muster entspricht allen *appsettings.json* -Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory* -Ordners.|
|`directory/**/*.txt`|Vergleicht alle Dateien mit der Erweiterung *.txt* , die innerhalb des Ordners *directory* ermittelt werden.|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern. Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.
* Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.
* [Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.
* .NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dateianbieterschnittstellen

Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` stellt Methoden zur Verfügung, zum:

* Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum

Mithilfe der [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)-Methode können Sie die Datei auslesen.

Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Dateianbieterimplementierungen

Es sind drei Implementierungen von `IFileProvider` verfügbar.

| Implementierung | Beschreibung |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind. |
| [CompositeFileProvider](#compositefileprovider) | Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem. `PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet. Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen. Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

Beim direkten Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.

Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typen im vorherigen Beispiel:

* `provider` ist ein `IFileProvider`.
* `contents` ist ein `IDirectoryContents`.
* `fileInfo` ist ein `IFileInfo`.

Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen. Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.

Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind. Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.

Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest. Geben Sie die einzubettenden Dateien mit [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.

Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.

*Startup.cs* :

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Mit zusätzlichen Überladungen können Sie:

* Einen relativen Dateipfad angeben.
* Dateien einem zuletzt geänderten Datum zuordnen.
* Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.

| Überladung | Beschreibung |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter. Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>). Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter. `manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält. |

### <a name="compositefileprovider"></a>CompositeFileProvider

Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen. Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.

In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Überwachen auf Änderungen

Die [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen. `Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben. <xref:Microsoft.Extensions.Primitives.IChangeToken> gibt `Watch` zurück. Das Änderungstoken macht folgendes verfügbar:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden. Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf. Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.

In der Beispiel-App wird die *WatchConsole* -Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig . Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).

## <a name="glob-patterns"></a>Globmuster

Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*. Geben Sie Gruppen von Dateien mit diesen Mustern an. Die zwei Platzhalterzeichen sind `*` und `**`:

**`*`**  
Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung. Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.

**`**`**  
Überprüft alles auf mehrere Verzeichnisebenen. Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.

**Beispiele für Globmuster**

**`directory/file.txt`**  
Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.

**`directory/*.txt`**  
Entspricht allen Dateien mit *.txt* -Erweiterung in einem bestimmten Verzeichnis.

**`directory/*/appsettings.json`**  
Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory* -Ordners.

**`directory/**/*.txt`**  
Entspricht allen Dateien mit *.txt* -Erweiterung, die an einer beliebigen Stelle unter dem *directory* -Ordner gefunden werden.

::: moniker-end
