---
title: SignalR API-Entwurfs Überlegungen
author: anurse
description: Erfahren Sie, wie Sie SignalR APIs für die Kompatibilität zwischen verschiedenen Versionen Ihrer APP entwerfen.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059649"
---
# <a name="no-locsignalr-api-design-considerations"></a>SignalR API-Entwurfs Überlegungen

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Anleitungen zum Entwickeln von SignalR -basierten APIs.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Verwenden von benutzerdefinierten Objekt Parametern, um Abwärtskompatibilität zu gewährleisten

Das Hinzufügen von Parametern zu einer SignalR Hub-Methode (auf dem Client oder auf dem Server) ist eine *Breaking Change* . Dies bedeutet, dass ältere Clients/Server Fehler erhalten, wenn Sie versuchen, die Methode ohne die erforderliche Anzahl von Parametern aufzurufen. Das Hinzufügen von Eigenschaften zu einem benutzerdefinierten Objekt Parameter ist jedoch **keine** Breaking Change. Dies kann zum Entwerfen kompatibler APIs verwendet werden, die für Änderungen auf dem Client oder dem Server stabil sind.

Stellen Sie sich beispielsweise eine serverseitige API wie die folgende vor:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Der JavaScript-Client ruft diese Methode mit `invoke` wie folgt auf:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Wenn Sie später der Server Methode einen zweiten Parameter hinzufügen, wird dieser Parameterwert von älteren Clients nicht bereitgestellt. Zum Beispiel:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Wenn der alte Client versucht, diese Methode aufzurufen, wird eine Fehlermeldung wie die folgende angezeigt:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Auf dem Server wird eine Protokollmeldung wie die folgende angezeigt:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Der alte Client hat nur einen Parameter gesendet, aber die neuere Server-API erforderte zwei Parameter. Die Verwendung von benutzerdefinierten Objekten als Parameter bietet Ihnen mehr Flexibilität. Wir wollen die ursprüngliche API so umgestalten, dass Sie ein benutzerdefiniertes Objekt verwendet:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Nun verwendet der Client ein-Objekt, um die-Methode aufzurufen:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Fügen Sie dem-Objekt eine Eigenschaft hinzu, anstatt einen Parameter hinzuzufügen `TotalLengthRequest` :

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Wenn der alte Client einen einzelnen Parameter sendet, bleibt die zusätzliche `Param2` Eigenschaft erhalten `null` . Sie können eine Nachricht erkennen, die von einem älteren Client gesendet wurde, indem Sie den `Param2` für prüfen `null` und einen Standardwert anwenden. Ein neuer Client kann beide Parameter senden.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Dieselbe Technik funktioniert auch für Methoden, die auf dem Client definiert sind. Sie können ein benutzerdefiniertes Objekt von der Serverseite senden:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Auf der Clientseite greifen Sie auf die-Eigenschaft zu, `Message` anstatt einen Parameter zu verwenden:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Wenn Sie sich später entscheiden, den Absender der Nachricht der Nutzlast hinzuzufügen, fügen Sie dem-Objekt eine Eigenschaft hinzu:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Die älteren Clients erwarten den Wert nicht `Sender` , sodass Sie ihn ignorieren. Ein neuer Client kann ihn akzeptieren, indem er aktualisiert, um die neue Eigenschaft zu lesen:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

In diesem Fall ist der neue Client auch für einen alten Server tolerant, der den Wert nicht bereitstellt `Sender` . Da der alte Server den Wert nicht bereitstellt `Sender` , überprüft der Client, ob er vorhanden ist, bevor er darauf zugreifen kann.
