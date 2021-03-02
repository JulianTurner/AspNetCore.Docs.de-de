---
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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552869"
---
Blazor-Apps befinden sich im Arbeitsspeicher des Servers. Das bedeutet, dass innerhalb desselben Prozesses mehrere Apps gehostet werden. Für jede App-Sitzung richtet Blazor eine Verbindung mit seinem eigenen Containerbereich für Abhängigkeitsinjektionen ein. Dies bedeutet, dass bereichsbezogene Dienste pro Blazor-Sitzung eindeutig sind.

> [!WARNING]
> Es wird nicht empfohlen, dass Apps auf demselben Server sich einen Zustand mithilfe von Singletondiensten teilen, es sei denn, es wird mit äußerster Vorsicht vorgegangen, da dies zu Sicherheitsrisiken führen kann, wie z. B. verbindungsübergreifende Preisgabe des Benutzerzustands.

Sie können zustandsbehaftete Singletondienste in Blazor-Apps verwenden, sofern sie speziell dafür konzipiert sind. Beispielsweise ist es in Ordnung, einen Arbeitsspeichercache als Singleton zu verwenden, da ein Schlüssel für den Zugriff auf einen bestimmten Eintrag erforderlich ist, vorausgesetzt, Benutzer haben keine Kontrolle darüber, welche Cacheschlüssel verwendet werden.

**Außerdem dürfen Sie wiederum aus Sicherheitsgründen <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> nicht in Blazor-Apps verwenden.** Blazor-Apps werden außerhalb des Kontexts der ASP.NET Core-Pipeline ausgeführt. Die Verfügbarkeit von <xref:Microsoft.AspNetCore.Http.HttpContext> in <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> ist nicht sichergestellt, und es ist auch nicht gewährleistet, dass die Klasse den Kontext zum Starten der Blazor-App enthält.

Der empfohlene Weg der Übergabe des Anforderungszustands an die Blazor-App ist über Parameter an die Stammkomponente beim ersten Rendern der Anwendung:

* Definieren Sie eine Klasse mit allen Daten, die Sie an die Blazor-App übergeben möchten.
* Füllen Sie diese Daten auf der Razor-Seite mit den zu diesem Zeitpunkt verfügbaren <xref:Microsoft.AspNetCore.Http.HttpContext>.
* Übergeben Sie die Daten an die Blazor-App als Parameter an die Stammkomponente (App).
* Definieren Sie einen Parameter in der Stammkomponente, der die an die Anwendung zu übergebenden Daten enthält.
* Verwenden Sie die benutzerspezifischen Daten innerhalb der App. Alternativ dazu können Sie diese Daten in einen bereichsbezogenen Dienst innerhalb von <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> kopieren, damit sie in der gesamten App verwendet werden können.

Weitere Informationen und Beispielcode finden Sie unter <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.
