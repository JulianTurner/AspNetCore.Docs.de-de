---
title: Verwalten von Protobuf-Verweisen mit dotnet-grpc
author: juntaoluo
description: Erfahren Sie mehr über das Hinzufügen, Aktualisieren, Entfernen und Auflisten von Protobuf-Verweisen mit dem globalen Tool „dotnet-grpc“.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059909"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Verwalten von Protobuf-Verweisen mit dotnet-grpc

Von [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` ist ein globales .NET Core-Tool zum Verwalten von [Protobuf ( *.proto*)](xref:grpc/basics#proto-file)-Verweisen in einem .NET gRPC-Projekt. Das Tool kann zum Hinzufügen, Aktualisieren, Entfernen und Auflisten von Protobuf-Verweisen verwendet werden.

## <a name="installation"></a>Installation

Zur Installation des `dotnet-grpc` [globales .NET Core-Tool](/dotnet/core/tools/global-tools), führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Hinzufügen von Verweisen

`dotnet-grpc` kann verwendet werden, um Protobuf-Verweise als `<Protobuf />`-Elemente zur *CSPROJ*-Datei hinzuzufügen:

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Die Protobuf-Verweise werden zum Generieren der C#-Client- und/oder -Serverobjekte verwendet. Das `dotnet-grpc`-Tool kann Folgendes:

* Protobuf-Verweise aus lokalen Dateien auf dem Datenträger erstellen.
* Einen Protobuf-Verweis aus einer Remotedatei erstellen, die durch eine URL angegeben ist.
* Sicherstellen, dass die richtigen gRPC-Paketabhängigkeiten zum Projekt hinzugefügt werden.

Das `Grpc.AspNetCore`-Paket wird z. B. zu einer Web-App hinzugefügt. `Grpc.AspNetCore` enthält gRPC-Server- und -Clientbibliotheken sowie Toolunterstützung. Alternativ werden die Pakete `Grpc.Net.Client`, `Grpc.Tools` und `Google.Protobuf`, die nur die gRPC-Clientbibliotheken und die Toolunterstützung enthalten, zu einer Konsolen-App hinzugefügt.

### <a name="add-file"></a>Datei hinzufügen

Der Befehl `add-file` wird verwendet, um lokale Dateien auf dem Datenträger als Protobuf-Verweise hinzuzufügen. Die bereitgestellten Dateipfade:

* Kann relativ zum aktuellen Verzeichnis oder zu absoluten Pfaden sein.
* Kann Platzhalter für musterbasiertes Datei-[Globbing](https://wikipedia.org/wiki/Glob_(programming)) enthalten.

Wenn sich Dateien außerhalb des Projektverzeichnisses befinden, wird ein `Link`-Element hinzugefügt, um die Datei unter dem Ordner `Protos` in Visual Studio anzuzeigen.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Dateien | Die Protobuf-Dateiverweise. Dies kann ein Pfad zu „Glob“ für lokale Protobuf-Dateien sein. |

#### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --project | Der Pfad zu der Projektdatei, mit der gearbeitet werden soll. Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.
| -S | --services | Der Typ der gRPC-Dienste, die generiert werden sollen. Wenn `Default` angegeben ist, wird `Both` für Webprojekte und `Client` für Nicht-Webprojekte verwendet. Zulässige Werte sind `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --additional-import-dirs | Zusätzliche Verzeichnisse, die bei der Auflösung von Importen für die Protobuf-Dateien verwendet werden. Dies ist eine durch Semikolons getrennte Liste von Pfaden.
| | --access | Der Zugriffsmodifizierer, der für die generierten C#-Klassen verwendet werden soll. Der Standardwert ist `Public`. Zulässige Werte sind `Internal` und `Public`.

### <a name="add-url"></a>URL hinzufügen

Der Befehl `add-url` wird verwendet, um eine Remotedatei hinzuzufügen, die durch eine Quell-URL als Protobuf-Verweis angegeben wird. Es muss ein Dateipfad angegeben werden, um anzugeben, wo die Remotedatei heruntergeladen werden soll. Der Dateipfad kann relativ zum aktuellen Verzeichnis oder ein absoluter Pfad sein. Wenn der Dateipfad außerhalb des Projektverzeichnisses liegt, wird ein `Link`-Element hinzugefügt, um die Datei unter dem virtuellen Ordner `Protos` in Visual Studio anzuzeigen.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| url | Die URL zu einer Protobuf-Remotedatei. |

#### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -o | --output | Gibt den Downloadpfad für die Protobuf-Remotedatei an. Diese Option muss angegeben werden.
| -p | --project | Der Pfad zu der Projektdatei, mit der gearbeitet werden soll. Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.
| -S | --services | Der Typ der gRPC-Dienste, die generiert werden sollen. Wenn `Default` angegeben ist, wird `Both` für Webprojekte und `Client` für Nicht-Webprojekte verwendet. Zulässige Werte sind `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --additional-import-dirs | Zusätzliche Verzeichnisse, die bei der Auflösung von Importen für die Protobuf-Dateien verwendet werden. Dies ist eine durch Semikolons getrennte Liste von Pfaden.
| | --access | Der Zugriffsmodifizierer, der für die generierten C#-Klassen verwendet werden soll. Der Standardwert ist `Public`sein. Zulässige Werte sind `Internal` und `Public`.

## <a name="remove"></a>Remove

Der Befehl `remove` wird verwendet, um Protobuf-Verweise aus der *CSPROJ*-Datei zu entfernen. Der Befehl akzeptiert Pfadargumente und Quell-URLs als Argumente. Folgendes gilt für das Tool:

* Es entfernt nur den Protobuf-Verweis.
* Es löscht nicht die *PROTO*-Datei, auch wenn sie ursprünglich von einer Remote-URL heruntergeladen wurde.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Referenzen | Die URLs oder Dateipfade der zu entfernenden Protobuf-Verweise. |

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --project | Der Pfad zu der Projektdatei, mit der gearbeitet werden soll. Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.

## <a name="refresh"></a>Aktualisieren

Der Befehl `refresh` wird verwendet, um einen Remoteverweis mit dem neuesten Inhalt von der Quell-URL zu aktualisieren. Sowohl der Pfad der Downloaddatei als auch die Quell-URL können zur Angabe des zu aktualisierenden Verweises verwendet werden. Hinweis:

* Die Hashes des Dateiinhalts werden verglichen, um festzustellen, ob die lokale Datei aktualisiert werden sollte.
* Es werden keine Zeitstempelinformationen verglichen.

Das Tool ersetzt immer die lokale Datei durch die Remotedatei, wenn eine Aktualisierung erforderlich ist.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Referenzen | Die URLs oder Dateipfade zu Protobuf-Remoteverweisen, die aktualisiert werden sollten. Lassen Sie dieses Argument leer, um alle Remoteverweise zu aktualisieren. |

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --project | Der Pfad zu der Projektdatei, mit der gearbeitet werden soll. Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.
| | --dry-run | Gibt eine Liste von Dateien aus, die ohne Herunterladen neuer Inhalte aktualisiert werden würden.

## <a name="list"></a>Liste

Der Befehl `list` wird verwendet, um alle Protobuf-Verweise in der Projektdatei anzuzeigen. Wenn alle Werte einer Spalte Standardwerte sind, kann die Spalte ausgelassen werden.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --project | Der Pfad zu der Projektdatei, mit der gearbeitet werden soll. Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
