---
title: Zuverlässige gRPC-Dienste mit Fristen und Abbrüchen
author: jamesnk
description: Hier erfahren Sie, wie Sie zuverlässige gRPC-Dienste mit Fristen und Abbrüchen in .NET erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059922"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a>Zuverlässige gRPC-Dienste mit Fristen und Abbrüchen

Von [James Newton-King](https://twitter.com/jamesnk)

Fristen und Abbrüche sind Features, die von gRPC-Clients zum Abbrechen von aktiven Aufrufen verwendet werden. In diesem Artikel werden die Bedeutung von Fristen und Abbrüchen sowie deren Verwendung in .NET-gRPC-Apps erläutert.

## <a name="deadlines"></a>Fristen

Mit einer Frist kann ein gRPC-Client angeben, wie lange er bereit ist, auf den Abschluss eines Aufrufs zu warten. Bei Überschreiten der Frist wird der Aufruf abgebrochen. Das Festlegen einer Frist ist wichtig, da sie eine Obergrenze für den Zeitraum vorgibt, in dem ein Aufruf ausgeführt werden kann. Dadurch wird verhindert, dass fehlerhafte Dienste unbegrenzt lange ausgeführt werden und die Serverressourcen belasten. Fristen stellen einen wichtigen Aspekt bei der Erstellung zuverlässiger Apps dar und sollten daher konfiguriert werden.

Konfiguration von Fristen:

* Die Konfiguration einer Frist für die Ausführung eines Aufrufs erfolgt mit `CallOptions.Deadline`.
* Es gibt keinen Standardwert für Fristen. gRPC-Aufrufe sind zeitlich nicht begrenzt, es sei denn, es wird eine Frist festgelegt.
* Fristen werden im UTC-Zeitformat angegeben. `DateTime.UtcNow.AddSeconds(5)` entspricht beispielsweise einer Frist von 5 Sekunden ab dem aktuellen Zeitpunkt.
* Wenn ein früherer oder der aktuelle Zeitpunkt angegeben wird, führt dies sofort zur Fristüberschreitung des Aufrufs.
* Die Frist wird mit dem gRPC-Aufruf an den Dienst gesendet und vom Client und Dienst unabhängig voneinander nachverfolgt. Es ist möglich, dass ein gRPC-Aufruf auf einem Computer abgeschlossen ist, die Frist jedoch überschritten ist, wenn die Antwort an den Client zurückgegeben wurde.

Bei Überschreiten der Frist verhalten sich Client und Dienst unterschiedlich:

* Der Client bricht die zugrunde liegende HTTP-Anforderung sofort ab und löst den Fehler `DeadlineExceeded` aus. Die Client-App kann den Fehler abfangen und dem Benutzer eine Meldung mit dem Hinweis auf eine Zeitüberschreitung anzeigen.
* Die ausgeführte HTTP-Anforderung wird auf dem Server abgebrochen, und der Fehler [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) wird ausgelöst. Die HTTP-Anforderung wird zwar abgebrochen, der gRPC-Aufruf wird allerdings weiterhin auf dem Server ausgeführt, bis die Methode abgeschlossen ist. Es ist wichtig, dass das Abbruchtoken an asynchrone Methoden übergeben wird, damit sie zusammen mit dem Aufruf abgebrochen werden. Beispiel: Ein Abbruchtoken wird an asynchrone Datenbankabfragen und HTTP-Anforderungen übergeben. Mit Übergabe eines Abbruchtokens können abgebrochene Aufrufe schnell auf dem Server ausgeführt und Ressourcen für andere Aufrufe freigegeben werden.

Konfigurieren Sie `CallOptions.Deadline`, um eine Frist für einen gRPC-Aufruf festzulegen:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Verwenden von `ServerCallContext.CancellationToken` in einem gRPC-Dienst:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a>Übertragen von Fristen

Wenn ein gRPC-Aufruf von einem ausgeführten gRPC-Dienst verarbeitet wird, muss die Frist übertragen werden. Zum Beispiel:

1. Client-App ruft `FrontendService.GetUser` mit einer Frist auf.
2. `FrontendService` ruft `UserService.GetUser` auf. Die vom Client angegebene Frist muss mit dem neuen gRPC-Aufruf angegeben werden.
3. `UserService.GetUser` empfängt die Frist. Wenn die Frist für die Client-App überschritten wird, kommt es zu einer Zeitüberschreitung.

Im Aufrufkontext wird die Frist mit `ServerCallContext.Deadline` bereitgestellt:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

Das manuelle Übertragen von Fristen kann mühsam sein. Die Frist muss an jeden einzelnen Aufruf übermittelt werden. Dabei kann schnell einer übersehen werden. Die gRPC-Clientfactory stellt eine automatische Lösung bereit. Durch Angeben von `EnableCallContextPropagation` wird Folgendes sichergestellt:

* Automatische Weitergabe des Frist- und Abbruchtokens an untergeordnete Aufrufe
* Zuverlässige Übertragung der Frist und des Abbruchs komplexer und verschachtelter gRPC-Szenarios

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

Weitere Informationen finden Sie unter <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.

## <a name="cancellation"></a>Abbruch

Mit Festlegen eines Abbruchs kann ein gRPC-Client nicht mehr benötigte Aufrufe mit langer Ausführungszeit abbrechen. So startet beispielsweise ein gRPC-Aufruf, mit dem Echtzeitupdates gestreamt werden, wenn der Benutzer eine Seite auf einer Website besucht. Der Streamingvorgang sollte abgebrochen werden, wenn der Benutzer von der Seite wieder verlässt.

Der gRPC-Aufruf kann im Client abgebrochen werden, indem ein Abbruchtoken mit [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) oder durch Aufrufen von `Dispose` weitergegeben wird.

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

gRPC-Dienste, die abgebrochen werden können, sollten folgende Aktionen ausführen:
* Übergeben von `ServerCallContext.CancellationToken` an asynchrone Methoden. Durch Abbrechen von asynchronen Methoden kann ein Aufruf schnell auf dem Server ausgeführt werden.
* Weitergabe des Abbruchtokens an untergeordnete Aufrufe. Mit der Weitergabe des Abbruchtokens wird sichergestellt, dass untergeordnete Aufrufe mit ihren übergeordneten Aufrufen abgebrochen werden. [gRPC-Clientfactory](xref:grpc/clientfactory) und `EnableCallContextPropagation()` geben das Abbruchtoken automatisch weiter.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/client>
* <xref:grpc/clientfactory>
