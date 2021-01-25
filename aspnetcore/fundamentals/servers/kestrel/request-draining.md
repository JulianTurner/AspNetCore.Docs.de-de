---
title: Anforderungsausgleich mit ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zum Anforderungsausgleich mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/request-draining
ms.openlocfilehash: 41d517dae939ad0a83a3402e72eefc4e9db7b32e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253845"
---
# <a name="request-draining-with-aspnet-core-kestrel-web-server"></a>Anforderungsausgleich mit ASP.NET Core-Kestrel-Webserver

Das Öffnen von HTTP-Verbindungen ist zeitaufwendig. Bei HTTPS ist es zudem ressourcenintensiv. Daher versucht Kestrel, Verbindungen gemäß dem HTTP/1.1-Protokoll wiederzuverwenden. Ein Anforderungstext muss vollständig genutzt worden sein, damit die Verbindung wiederverwendet werden kann. Die App nutzt nicht immer den Anforderungstext, z. B. bei POST-HTTP-Anforderungen, bei der der Server eine Umleitung oder 404-Antwort zurückgibt. Im Fall einer HTTP-POST-Weiterleitung gilt Folgendes:

* Der Client hat möglicherweise bereits einen Teil der POST-Daten gesendet.
* Der Server schreibt die 301-Antwort.
* Die Verbindung kann erst dann für eine neue Anforderung verwendet werden, wenn die POST-Daten im vorherigen Anforderungstext vollständig gelesen wurden.
* Kestrel versucht, den Anforderungstext zu leeren. Leeren des Anforderungstexts bedeutet, die Daten zu lesen und zu verwerfen, ohne sie zu verarbeiten.

Beim Ausgleichsprozess wird ein Kompromiss zwischen der Möglichkeit der Wiederverwendung der Verbindung und der Dauer gefunden, die zum Entfernen der verbleibenden Daten benötigt wird:

* Für das Leeren gilt ein Zeitlimit von fünf Sekunden, das nicht konfigurierbar ist.
* Wenn vor dem Zeitlimit nicht alle durch den Header `Content-Length` oder `Transfer-Encoding` angegebenen Daten gelesen wurden, wird die Verbindung geschlossen.

Mitunter möchten Sie die Anforderung sofort beenden, entweder bevor oder nachdem die Antwort geschrieben wurde. Für Clients bestehen möglicherweise beschränkende Obergrenzen für Daten. Das Einschränken hochgeladener Daten kann zur Priorität werden. Um in solchen Fällen eine Anforderung zu beenden, rufen Sie [HttpContext.abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) in einem Controller, eine Razor Page oder Middleware auf.

Gegen den Aufruf von `Abort` gibt es Vorbehalte:

* Das Erstellen neuer Verbindungen kann langsam und aufwendig sein.
* Es gibt keine Garantie, dass der Client die Antwort gelesen hat, bevor die Verbindung geschlossen wird.
* Das Aufrufen von `Abort` sollte selten erfolgen und schweren Fehlerfällen und nicht gewöhnlichen Fehlern vorbehalten sein.
  * Rufen Sie `Abort` nur dann auf, wenn ein konkretes Problem gelöst werden muss. Rufen Sie beispielsweise `Abort` auf, wenn böswillige Clients versuchen, Daten per POST abzurufen, oder wenn es einen Fehler im Clientcode gibt, der umfangreiche oder zahlreiche Anforderungen verursacht.
  * Rufen Sie `Abort` nicht für gewöhnliche Fehlersituationen auf, wie z. B. HTTP 404 (Nicht gefunden).

Durch den Aufruf von [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) vor dem Aufruf von `Abort` wird sichergestellt, dass der Server das Schreiben der Antwort abgeschlossen hat. Das Clientverhalten ist jedoch nicht vorhersagbar, und es kann sein, dass die Antwort nicht gelesen wird, bevor die Verbindung abgebrochen wird.

Dieser Prozess bei HTTP/2 ist anders, da das Protokoll den Abbruch einzelner Anforderungsströme ohne Schließen der Verbindung unterstützt. Das fünfsekündige Zeitlimit für Ausgleiche gilt nicht. Wenn nach dem Fertigstellen einer Antwort ungelesene Anforderungstextdaten vorhanden sind, sendet der Server einen HTTP/2-RST-Datenrahmen. Zusätzliche Datenrahmen im Anforderungstext werden ignoriert.

Wenn möglich, ist es für Clients besser, den Anforderungsheader [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) zu verwenden und auf die Antwort des Servers zu warten, bevor mit dem Senden des Anforderungstexts begonnen wird. Das gibt dem Client die Gelegenheit, die Antwort zu prüfen und abzubrechen, bevor nicht benötigte Daten gesendet werden.
