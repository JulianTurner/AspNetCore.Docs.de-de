---
title: Schlüsselverwaltung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails der APIs für die ASP.net Core Datenschutz-Schlüsselverwaltung.
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
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: f7e0fa5e80208469188a11239a9453ecafdcf5f3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060195"
---
# <a name="key-management-in-aspnet-core"></a>Schlüsselverwaltung in ASP.net Core

<a name="data-protection-implementation-key-management"></a>

Das Datenschutzsystem verwaltet die Lebensdauer von Haupt Schlüsseln, die zum Schutz und zum Aufheben des Schutzes von Nutzlasten verwendet werden, automatisch. Jeder Schlüssel kann in einer von vier Phasen vorhanden sein:

* Erstellt: der Schlüssel ist im Schlüsselbund vorhanden, wurde jedoch noch nicht aktiviert. Der Schlüssel sollte nicht für neue Schutz Vorgänge verwendet werden, bis ausreichend Zeit verstrichen ist, dass der Schlüssel an alle Computer weitergegeben werden konnte, die diesen Schlüsselbund verbrauchen.

* Aktiv: der Schlüssel ist im Schlüsselbund vorhanden und sollte für alle neuen Schutz Vorgänge verwendet werden.

* Abgelaufen: der Schlüssel hat seine natürliche Lebensdauer ausgeführt und sollte nicht mehr für neue Schutz Vorgänge verwendet werden.

* Widerrufen: der Schlüssel ist kompromittiert und darf nicht für neue Schutz Vorgänge verwendet werden.

Die Schlüssel "erstellt", "aktiv" und "abgelaufen" können alle zum Aufheben des Schutzes eingehender Nutzlasten verwendet werden. Widerrufene Schlüssel können standardmäßig nicht zum Aufheben des Schutzes von Nutzlasten verwendet werden, aber der Anwendungsentwickler kann [dieses Verhalten](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) bei Bedarf überschreiben.

>[!WARNING]
> Der Entwickler kann einen Schlüssel aus dem Schlüsselbund löschen (z. b. durch Löschen der entsprechenden Datei aus dem Dateisystem). An diesem Punkt sind alle Daten, die durch den Schlüssel geschützt sind, unwiderruflich unverschlüsselt, und es gibt keine notfallaußerkraftsetzung, wie es bei widerrufenen Schlüsseln der Fall ist. Das Löschen eines Schlüssels ist wirklich destruktives Verhalten, und folglich stellt das Datenschutzsystem keine erstklassige API zum Ausführen dieses Vorgangs zur Verfügung.

## <a name="default-key-selection"></a>Standardschlüssel Auswahl

Wenn das Datenschutzsystem den Schlüsselbund aus dem sicherungsrepository liest, wird versucht, einen "Default"-Schlüssel aus dem Schlüsselbund zu suchen. Der Standardschlüssel wird für neue Protect-Vorgänge verwendet.

Die allgemeine Heuristik besteht darin, dass das Datenschutzsystem den Schlüssel mit dem aktuellsten Aktivierungsdatum als Standardschlüssel auswählt. (Es gibt einen kleinen Mogel-Faktor, der die Server-zu-Server-Takt Neigung zulässt.) Wenn der Schlüssel abgelaufen ist oder widerrufen wird und die automatische Schlüsselgenerierung von der Anwendung nicht deaktiviert wurde, wird ein neuer Schlüssel mit sofortiger Aktivierung gemäß dem [Schlüssel Ablauf und](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) der aktuellen rollenrichtlinie generiert.

Der Grund, warum das Datenschutzsystem einen neuen Schlüssel direkt generiert, anstatt auf einen anderen Schlüssel zurückzukehren, besteht darin, dass die neue Schlüsselgenerierung als impliziter Ablauf aller Schlüssel behandelt werden soll, die vor dem neuen Schlüssel aktiviert wurden. Die allgemeine Idee ist, dass neue Schlüssel möglicherweise mit verschiedenen Algorithmen oder Verschlüsselungs-at-Rest-Mechanismen konfiguriert wurden, als alte Schlüssel. das System sollte die aktuelle Konfiguration vor dem Fallback bevorzugen.

Es gibt eine Ausnahme. Wenn der Anwendungsentwickler die [automatische Schlüsselgenerierung deaktiviert](xref:security/data-protection/configuration/overview#disableautomatickeygeneration)hat, muss das Datenschutzsystem etwas als Standardschlüssel auswählen. In diesem Fall Back Szenario wählt das System den nicht widerrufenen Schlüssel mit dem aktuellen Aktivierungsdatum aus, wobei Schlüssel für Schlüssel, die Zeit für die Weitergabe an andere Computer im Cluster hatten, Vorrang haben. Das Fall Back System wählt möglicherweise einen abgelaufenen Standardschlüssel aus. Das Fall Back System wählt niemals einen gesperrten Schlüssel als Standardschlüssel aus, und wenn der Schlüsselbund leer ist oder jeder Schlüssel widerrufen wurde, erzeugt das System bei der Initialisierung einen Fehler.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Schlüssel Ablauf und-parallele

Wenn ein Schlüssel erstellt wird, wird automatisch ein Aktivierungsdatum von {Now + 2 Days} und ein Ablaufdatum von {Now + 90 Tage} angegeben. Die 2-tägige Verzögerung vor der Aktivierung gibt die Schlüsselzeit für die Weitergabe über das System an. Dies bedeutet, dass andere Anwendungen, die auf den Sicherungs Speicher zeigen, den Schlüssel beim nächsten automatischen Aktualisierungs Zeitraum beobachten können. Dadurch wird die Wahrscheinlichkeit maximiert, dass der Schlüsselbund aktiv wird, wenn er an alle Anwendungen weitergegeben wird, die ihn möglicherweise verwenden müssen.

Wenn der Standardschlüssel innerhalb von 2 Tagen abläuft und der Schlüsselbund nicht bereits über einen Schlüssel verfügt, der nach Ablauf des Standard Schlüssels aktiv sein wird, speichert das Datenschutzsystem automatisch einen neuen Schlüssel für den Schlüsselbund. Dieser neue Schlüssel weist das Aktivierungsdatum "{Standard Key es Ablaufdatum}" und das Ablaufdatum "{now + 90 Days}" auf. Dadurch kann das System regelmäßig automatisch ein Rollback für Schlüssel ausführen, ohne Dienst Unterbrechung auszuführen.

Möglicherweise gibt es Situationen, in denen ein Schlüssel mit sofortiger Aktivierung erstellt wird. Ein Beispiel wäre, wenn die Anwendung nicht für eine Zeit ausgeführt wurde und alle Schlüssel im Schlüsselbund abgelaufen sind. Wenn dies der Fall ist, erhält der Schlüssel ein Aktivierungsdatum von {Now} ohne die normale zweitägige Aktivierungs Verzögerung.

Der Standardwert für die Schlüssel Lebensdauer beträgt 90 Tage. Dies kann jedoch wie im folgenden Beispiel konfiguriert werden.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Ein Administrator kann auch die standardmäßige systemweite ändern, obwohl ein expliziter-Vorgang eine `SetDefaultKeyLifetime` beliebige systemweite Richtlinie außer Kraft setzt. Die Standard Gültigkeitsdauer des Schlüssels darf nicht kürzer als 7 Tage sein.

## <a name="automatic-key-ring-refresh"></a>Automatische Aktualisierung des Schlüsselrings

Beim Initialisieren des Datenschutzsystems liest es den Schlüsselring aus dem zugrunde liegenden Repository und speichert ihn im Arbeitsspeicher zwischen. Mit diesem Cache können Vorgänge zum Schutz und zum Aufheben des Schutzes fortgesetzt werden, ohne den Sicherungs Speicher zu erreichen. Das System überprüft den Sicherungs Speicher automatisch auf Änderungen ungefähr alle 24 Stunden oder, wenn der aktuelle Standardschlüssel abläuft, je nachdem, welcher Fall zuerst eintritt.

>[!WARNING]
> Entwickler sollten die Schlüsselverwaltungs-APIs in sehr seltenen Fällen (Falls immer) direkt verwenden müssen. Das Datenschutzsystem führt die automatische Schlüsselverwaltung wie oben beschrieben aus.

Das Datenschutzsystem macht eine Schnittstelle verfügbar `IKeyManager` , die verwendet werden kann, um den Schlüsselring zu überprüfen und zu ändern. Das di-System, das die Instanz von bereitstellt, `IDataProtectionProvider` kann auch eine Instanz von `IKeyManager` für den Verbrauch bereitstellen. Alternativ können Sie den `IKeyManager` direkt von der abrufen, `IServiceProvider` wie im folgenden Beispiel gezeigt.

Jeder Vorgang, bei dem der Schlüsselbund geändert wird (durch explizites Erstellen eines neuen Schlüssels oder Durchführen einer Sperre) wird der in-Memory-Cache ungültig. Beim nächsten Aufrufen von `Protect` oder `Unprotect` wird das Datenschutzsystem zum erneuten Einlesen des Schlüsselrings und zum erneuten Erstellen des Caches.

Das folgende Beispiel veranschaulicht die Verwendung der `IKeyManager` -Schnittstelle, um den Schlüsselring zu überprüfen und zu bearbeiten, einschließlich der Aufhebung vorhandener Schlüssel und der manuellen Erstellung eines neuen Schlüssels.

[!code-csharp[](key-management/samples/key-management.cs)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="key-storage"></a>Schlüsselspeicher

Das Datenschutzsystem hat eine Heuristik, bei der versucht wird, einen geeigneten Schlüssel Speicherort und den Verschlüsselungs-at-Rest-Mechanismus automatisch herzuleiten. Der Schlüssel Persistenzmechanismus kann auch vom App-Entwickler konfiguriert werden. In den folgenden Dokumenten werden die in-Box-Implementierungen dieser Mechanismen erläutert:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
