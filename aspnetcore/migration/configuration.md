---
title: Konfiguration zu ASP.net Core migrieren
author: ardalis
description: Erfahren Sie, wie Sie die Konfiguration von einem ASP.NET-MVC-Projekt zu einem ASP.net Core MVC-Projekt migrieren.
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: d84204c8c791bfaf36432462cde3a42c294c7966
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059792"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Konfiguration zu ASP.net Core migrieren

Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)

Im vorherigen Artikel haben wir begonnen, [ein ASP.NET MVC-Projekt zu ASP.net Core MVC zu migrieren](xref:migration/mvc). In diesem Artikel wird die Konfiguration migriert.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Einrichtungskonfiguration

ASP.net Core verwendet nicht mehr die Dateien " *Global. asax* " und " *web.config* ", die in früheren Versionen von ASP.NET verwendet wurden. In früheren Versionen von ASP.net wurde die Anwendungsstart Logik in eine- `Application_StartUp` Methode in " *Global. asax* " eingefügt. Später wurde in ASP.NET MVC eine *Startup.cs* -Datei in das Stammverzeichnis des Projekts eingefügt. und es wurde aufgerufen, als die Anwendung gestartet wurde. ASP.net Core hat diesen Ansatz vollständig übernommen, indem er die gesamte Start Logik in der *Startup.cs* -Datei platziert hat.

Die *web.config* Datei wurde auch in ASP.net Core ersetzt. Die Konfiguration selbst kann nun im Rahmen des in *Startup.cs* beschriebenen Verfahrens zum Starten der Anwendung konfiguriert werden. Die Konfiguration kann weiterhin XML-Dateien verwenden, aber in der Regel ASP.net Core-Projekte Konfigurationswerte in eine JSON-formatierte Datei, z *appsettings.json* . b.. ASP.net Core Konfigurationssystem kann auch problemlos auf Umgebungsvariablen zugreifen, die einen sichereren [und stabileren Speicherort](xref:security/app-secrets) für Umgebungs spezifische Werte bereitstellen können. Dies gilt insbesondere für Geheimnisse wie Verbindungs Zeichenfolgen und API-Schlüssel, die nicht in die Quell Code Verwaltung eingecheckt werden sollen. Weitere Informationen zur Konfiguration in ASP.net Core finden Sie unter [Konfiguration](xref:fundamentals/configuration/index) .

In diesem Artikel beginnen wir mit dem teilweise migrierten ASP.net Core Projekt aus [dem vorherigen Artikel](xref:migration/mvc). Fügen Sie der *Startup.cs* -Datei, die sich im Stammverzeichnis des Projekts befindet, den folgenden Konstruktor und die folgende Eigenschaft hinzu, um die Konfiguration einzurichten:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Beachten Sie, dass die *Startup.cs* -Datei zu diesem Zeitpunkt nicht kompiliert werden kann, da wir weiterhin die folgende Anweisung hinzufügen müssen `using` :

```csharp
using Microsoft.Extensions.Configuration;
```

Fügen Sie dem Stamm *appsettings.json* des Projekts mithilfe der entsprechenden Element Vorlage eine Datei hinzu:

![AppSettings-JSON hinzufügen](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Konfigurationseinstellungen von web.config migrieren

Das ASP.NET-MVC-Projekt enthielt die erforderliche Daten bankverbindungs Zeichenfolge in *web.config* im- `<connectionStrings>` Element. In unserem ASP.net Core Projekt werden diese Informationen in der *appsettings.json* Datei gespeichert. Öffnen *appsettings.json* Sie, und beachten Sie, dass Sie bereits Folgendes umfasst:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Ändern Sie in der oben gezeigten markierten Zeile den Namen der Datenbank von **_CHANGE_ME** in den Namen der Datenbank.

## <a name="summary"></a>Zusammenfassung

ASP.net Core platziert alle Start Logik für die Anwendung in einer einzelnen Datei, in der die erforderlichen Dienste und Abhängigkeiten definiert und konfiguriert werden können. Sie ersetzt die *web.config* Datei durch ein flexibles Konfigurations Feature, das eine Vielzahl von Dateiformaten, z. b. JSON, und Umgebungsvariablen nutzen kann.
