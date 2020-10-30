---
title: Zweck Hierarchie und mehr Instanzen Fähigkeit in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Zeichen folgen Hierarchie und die mehr Instanzen Fähigkeit in Bezug auf die ASP.net Core Datenschutz-APIs.
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
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 84714cd852a3cbc7ff77d0fd0c88931536dead1a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052785"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Zweck Hierarchie und mehr Instanzen Fähigkeit in ASP.net Core

Da ein `IDataProtector` auch implizit eine ist `IDataProtectionProvider` , können-Zwecke miteinander verkettet werden. In diesem Sinne `provider.CreateProtector([ "purpose1", "purpose2" ])` entspricht `provider.CreateProtector("purpose1").CreateProtector("purpose2")` .

Dies ermöglicht einige interessante hierarchische Beziehungen durch das Datenschutzsystem. Im vorherigen Beispiel von " [fitoso. Messaging. securemessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)" kann die securemessage-Komponente `provider.CreateProtector("Contoso.Messaging.SecureMessage")` einmal vorab aufrufen und das Ergebnis in einem privaten Feld Zwischenspeichern `_myProvider` . Zukünftige Schutzvorrichtungen können dann über Aufrufe von erstellt werden `_myProvider.CreateProtector("User: username")` , und diese Schutzvorrichtungen werden zum Sichern der einzelnen Nachrichten verwendet.

Dies kann auch gekippt werden. Stellen Sie sich eine einzige logische Anwendung vor, die mehrere Mandanten hostet (ein CMS erscheint vernünftig), und jeder Mandant kann mit einem eigenen Authentifizierungs-und Zustands Verwaltungssystem konfiguriert werden. Die Anwendung "Umbrella" verfügt über einen einzigen Master Anbieter und ruft `provider.CreateProtector("Tenant 1")` und `provider.CreateProtector("Tenant 2")` auf, um jedem Mandanten einen eigenen isolierten Slice des Datenschutzsystems zu übergeben. Die Mandanten können dann Ihre eigenen individuellen Schutzvorrichtungen basierend auf Ihren eigenen Anforderungen ableiten, aber unabhängig davon, wie schwierig Sie sind, können Sie keine Schutzvorrichtungen erstellen, die mit einem anderen Mandanten im System kollidieren. Grafisch dargestellt wird dies wie unten dargestellt.

![Mehr Instanzen Fähigkeit](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Dabei wird davon ausgegangen, dass die Anwendung steuert, welche APIs einzelnen Mandanten zur Verfügung stehen und dass Mandanten keinen beliebigen Code auf dem Server ausführen können. Wenn ein Mandant beliebigen Code ausführen kann, kann er eine private Reflektion durchführen, um die Isolations Garantien zu unterbrechen, oder er könnte einfach das Hauptschlüssel Material direkt lesen und die gewünschten Unterschlüssel ableiten.

Das Datenschutzsystem verwendet in seiner standardmäßigen Standardkonfiguration tatsächlich eine Art mehr Instanzen Fähigkeit. Standardmäßig wird das Masterschlüssel Material im Benutzerprofil Ordner des Arbeitsprozess Kontos (oder in der Registrierung für IIS-Anwendungs Pool Identitäten) gespeichert. Es ist jedoch ziemlich üblich, ein einzelnes Konto zum Ausführen mehrerer Anwendungen zu verwenden. Daher würden alle diese Anwendungen das Hauptmaterial für die schlüsselfreigabe freigeben. Um dieses Problem zu beheben, fügt das Datenschutzsystem automatisch einen eindeutigen Bezeichner für die Anwendung als erstes Element in der gesamten Ziel Kette ein. Dieser implizite Zweck dient dazu, [einzelne Anwendungen](xref:security/data-protection/configuration/overview#per-application-isolation) voneinander zu isolieren, indem jede Anwendung als eindeutiger Mandant innerhalb des Systems behandelt wird und der Erstellungs Prozess der Schutzvorrichtung mit der obigen Abbildung identisch aussieht.
