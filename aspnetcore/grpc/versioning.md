---
title: Versionsverwaltung für gRPC-Dienste
author: jamesnk
description: Erfahren Sie, wie Sie die Versionsverwaltung für gRPC-Dienste durchführen können.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058700"
---
# <a name="versioning-grpc-services"></a>Versionsverwaltung für gRPC-Dienste

Von [James Newton-King](https://twitter.com/jamesnk)

Neue Features, die zu einer App hinzugefügt werden, können es erforderlich machen, dass sich die gRPC-Dienste, die den Kunden zur Verfügung gestellt werden, ändern, manchmal auf unerwartete und entscheidende Weise. Wenn sich die gRPC-Dienste ändern:

* Es sollten Überlegungen dahingehend angestellt werden, wie sich Veränderungen auf die Clients auswirken.
* Es sollte eine Strategie der Versionsverwaltung zur Unterstützung von Änderungen implementiert werden.

## <a name="backwards-compatibility"></a>Abwärtskompatibilität

Das gRPC-Protokoll wurde entwickelt, um Dienste zu unterstützen, die sich im Laufe der Zeit ändern. Im Allgemeinen sind die Ergänzungen zu den gRPC-Diensten und -Methoden keine Breaking Changes. Non-Breaking Changes ermöglichen es bestehenden Clients, die Arbeit ohne Änderungen fortzusetzen. Das Ändern oder Löschen von gRPC-Diensten sind Breaking Changes. Wenn gRPC-Dienste Breaking Changes aufweisen, müssen die Clients, die diesen Dienst in Anspruch nehmen, aktualisiert und erneut bereitgestellt werden.

Die Durchführung von Non-Breaking Changes an einem Dienst hat eine Reihe von Vorteilen:

* Vorhandene Clients werden weiterhin ausgeführt.
* Vermeidet die Arbeit, die damit verbunden ist, Clients über Breaking Changes zu benachrichtigen und diese zu aktualisieren.
* Es muss nur eine Version des Diensts dokumentiert und verwaltet werden.

### <a name="non-breaking-changes"></a>Geringfügige Änderungen

Diese Änderungen sind auf der gRPC-Protokollebene und der .NET-Binärebene keine Breaking Changes.

* **Hinzufügen eines neuen Diensts**
* **Hinzufügen einer neuen Methode zu einem Dienst**
* **Hinzufügen eines Felds zu einer Anforderungsnachricht**: Zu einer Anforderungsnachricht hinzugefügte Felder werden auf dem Server mit dem [Standardwert](https://developers.google.com/protocol-buffers/docs/proto3#default) deserialisiert, wenn sie nicht festgelegt sind. Damit es sich um Non-Breaking Changes handelt, muss der Dienst erfolgreich sein, wenn das neue Feld nicht von älteren Clients festgelegt wird.
* **Hinzufügen eines Felds zu einer Antwortnachricht**: Zu einer Antwortnachricht hinzugefügte Felder werden in die Sammlung [Unbekannte Felder](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) der Nachricht auf dem Client deserialisiert.
* **Hinzufügen eines Werts zu einer Enumeration**: Enumerationen werden als numerischer Wert serialisiert. Neue Enumerationswerte werden auf dem Client in den Enumerationswert ohne Enumerationsnamen deserialisiert. Damit es sich um Non-Breaking Changes handelt, müssen ältere Clients ordnungsgemäß ausgeführt werden, wenn sie den neuen Enumerationswert erhalten.

### <a name="binary-breaking-changes"></a>Binäre Breaking Changes

Die folgenden Änderungen stellen auf gRPC-Protokollebene Non-Breaking Changes dar, aber der Client muss aktualisiert werden, wenn er ein Upgrade auf den aktuellsten *.proto*-Vertrag oder die aktuellste .NET-Clientassembly durchführt. Binärkompatibilität ist wichtig, wenn Sie planen, eine gRPC-Bibliothek in NuGet zu veröffentlichen.

* **Entfernen eines Felds**: Werte aus einem entfernten Feld werden in die [unbekannten Felder](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) einer Nachricht deserialisiert. Dies sind keine Breaking Changes des gRPC-Protokolls, aber der Client muss aktualisiert werden, wenn er ein Upgrade auf den aktuellsten Vertrag erhält. Es ist wichtig, dass eine entfernte Feldnummer in Zukunft nicht versehentlich wiederverwendet wird. Um sicherzustellen, dass dies nicht passiert, geben Sie gelöschte Feldnummern und Namen in der Nachricht mit dem [reservierten](https://developers.google.com/protocol-buffers/docs/proto3#reserved) Schlüsselwort von Protobuf an.
* **Umbenennen einer Nachricht**: Nachrichtennamen werden normalerweise nicht über das Netzwerk gesendet, sodass es sich nicht um Breaking Changes des gRPC-Protokolls handelt. Der Client muss aktualisiert werden, wenn er ein Upgrade auf den aktuellsten Vertrag erhält. Eine Situation, in der Nachrichtennamen im Netzwerk **gesendet werden**, ist mit [Any](https://developers.google.com/protocol-buffers/docs/proto3#any)-Feldern, wenn der Nachrichtenname zur Identifizierung des Nachrichtentyps verwendet wird.
* **Ändern von csharp_namespace**: Durch die Änderung von `csharp_namespace` wird der Namespace von generierten .NET-Typen geändert. Dies sind keine Breaking Changes des gRPC-Protokolls, aber der Client muss aktualisiert werden, wenn er ein Upgrade auf den aktuellsten Vertrag erhält.

### <a name="protocol-breaking-changes"></a>Breaking Changes bei Protokollen

Bei den folgenden Punkten handelt es sich um Breaking Changes von Protokollen und um binäre Breaking Changes:

* **Umbenennen eines Felds**: Bei Protobuf-Inhalten werden die Feldnamen nur im generierten Code verwendet. Die Feldnummer wird verwendet, um Felder im Netzwerk zu identifizieren. Die Umbenennung eines Felds ist für Protobuf keine Breaking Change für Protokolle. Wenn ein Server jedoch JSON-Inhalte verwendet, ist die Umbenennung eines Felds eine Breaking Change.
* **Ändern eines Felddatentyps**: Das Ändern des Datentyps eines Felds in einen [inkompatiblen Typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) führt zu Fehlern bei der Deserialisierung der Nachricht. Selbst wenn der neue Datentyp kompatibel ist, ist es wahrscheinlich, dass der Client aktualisiert werden muss, um den neuen Typ zu unterstützen, wenn er ein Upgrade auf den aktuellsten Vertrag erhält.
* **Ändern einer Feldnummer**: Bei Protobuf-Nutzlasten wird die Feldnummer zur Identifizierung von Feldern im Netzwerk verwendet.
* **Umbenennen eines Pakets, Diensts oder einer Methode**: gRPC verwendet den Paket-, Dienst- und Methodennamen zum Erstellen der URL. Der Client erhält vom Server den Status *NICHT IMPLEMENTIERT*.
* **Entfernen eines Diensts oder einer Methode**: Der Client erhält beim Aufruf der entfernten Methode vom Server den Status *NICHT IMPLEMENTIERT*.

### <a name="behavior-breaking-changes"></a>Breaking Changes für Verhalten

Bei Non-Breaking Changes müssen Sie auch berücksichtigen, ob ältere Clients weiterhin mit dem neuen Dienstverhalten arbeiten können. Beispiel: Hinzufügen eines neuen Felds zu einer Anforderungsnachricht:

* Dies sind keine Breaking Changes für Protokolle.
* Die Rückgabe eines Fehlerstatus auf dem Server, wenn das neue Feld nicht festgelegt ist, macht es für alte Clients zu einer Breaking Change.

Die Verhaltenskompatibilität wird durch Ihren App-spezifischen Code bestimmt.

## <a name="version-number-services"></a>Versionsnummerndienste

Die Dienste sollten anstreben, die Abwärtskompatibilität mit alten Clients zu erhalten. Möglicherweise erfordern Änderungen an Ihrer App entsprechende Breaking Changes. Alte Clients zu unterbrechen und zu zwingen, sie zusammen mit Ihrem Dienst zu aktualisieren, ist keine gute Benutzererfahrung. Eine Möglichkeit, die Abwärtskompatibilität aufrechtzuerhalten und gleichzeitig Breaking Changes vorzunehmen, besteht darin, mehrere Versionen eines Diensts zu veröffentlichen.

gRPC unterstützt einen optionalen [Paket](https://developers.google.com/protocol-buffers/docs/proto3#packages)-Spezifizierer, der ähnlich wie ein .NET-Namespace funktioniert. Tatsächlich wird der `package` als .NET-Namespace für generierte .NET-Typen verwendet, wenn `option csharp_namespace` in der *PROTO*-Datei nicht festgelegt ist. Das Paket kann verwendet werden, um eine Versionsnummer für Ihren Dienst und seine Nachrichten anzugeben:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Der Paketname wird mit dem Dienstnamen kombiniert, um eine Dienstadresse zu identifizieren. Eine Dienstadresse ermöglicht es, mehrere Versionen eines Diensts nebeneinander zu hosten:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementierungen des Diensts mit Versionsangabe werden in *Startup.cs* registriert:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Das Einbeziehen einer Versionsnummer in den Paketnamen gibt Ihnen die Möglichkeit, eine *v2*-Version Ihres Diensts mit Breaking Changes zu veröffentlichen, während ältere Clients, die die *v1*-Version aufrufen, weiterhin unterstützt werden. Nachdem die Clients aktualisiert wurden, um den *v2*-Dienst zu nutzen, können Sie die alte Version entfernen. Wenn Sie planen, mehrere Versionen eines Diensts zu veröffentlichen:

* Vermeiden Sie Breaking Changes, wenn dies sinnvoll ist.
* Aktualisieren Sie die Versionsnummer nur dann, wenn Sie Breaking Changes vornehmen.
* Aktualisieren Sie die Versionsnummer, wenn Sie Breaking Changes vornehmen.

Durch die Veröffentlichung mehrerer Versionen eines Diensts wird dieser dupliziert. Um die Duplizierung zu verringern, sollten Sie in Erwägung ziehen, die Geschäftslogik von den Dienstimplementierungen an einen zentralen Ort zu verlagern, der von den alten und neuen Implementierungen wiederverwendet werden kann:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Dienste und Nachrichten, die mit unterschiedlichen Paketnamen generiert werden, sind **verschiedene .NET-Typen**. Das Verschieben der Geschäftslogik an einen zentralen Ort erfordert die Zuordnung von Nachrichten zu allgemeinen Typen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/protobuf>
