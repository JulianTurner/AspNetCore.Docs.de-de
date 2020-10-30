---
title: Schlüssel Unveränderlichkeit und Schlüssel Einstellungen in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails der ASP.net Core Datenschutz schlüsselimmuability-APIs.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061248"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Schlüssel Unveränderlichkeit und Schlüssel Einstellungen in ASP.net Core

Wenn ein Objekt im Sicherungs Speicher persistent gespeichert wird, wird seine Darstellung immer wieder korrigiert. Dem Sicherungs Speicher können neue Daten hinzugefügt werden, vorhandene Daten können jedoch nie mutiert werden. Der Hauptzweck dieses Verhaltens besteht darin, die Beschädigung von Daten zu verhindern.

Eine Folge dieses Verhaltens besteht darin, dass ein Schlüssel, der in den Sicherungs Speicher geschrieben wird, unveränderlich ist. Die Erstellungs-, Aktivierungs-und Ablaufdatums Angaben können niemals geändert werden, obwohl Sie mithilfe von widerrufen werden können `IKeyManager` . Außerdem sind die zugrunde liegenden algorithmische Informationen, die Eigenschaften für das Masterschlüssel Material und die Verschlüsselung ruhender Daten unveränderlich.

Wenn der Entwickler eine Einstellung ändert, die die Schlüssel Persistenz beeinflusst, werden diese Änderungen erst beim nächsten Generieren eines Schlüssels wirksam, entweder über einen expliziten Aufruf von `IKeyManager.CreateNewKey` oder über das eigene Verhalten der [automatischen Schlüsselgenerierung](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) des Datenschutzsystems. Folgende Einstellungen haben Auswirkungen auf die Schlüssel Persistenz:

* [Die Standard Lebensdauer des Schlüssels](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Der Verschlüsselungsmechanismus für Schlüssel Verschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Die algorithmischen Informationen, die im Schlüssel enthalten sind.](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Wenn Sie diese Einstellungen vor der nächsten automatischen Zeit zum automatischen Ausführen von Schlüsseln benötigen, sollten Sie einen expliziten Aufruf von erstellen, `IKeyManager.CreateNewKey` um die Erstellung eines neuen Schlüssels zu erzwingen. Stellen Sie sicher, dass Sie ein explizites Aktivierungsdatum angeben ({Now + 2 Days} ist eine gute Faustregel, um Zeit für die Änderung der Änderung zuzulassen) und das Ablaufdatum im Aufruf.

>[!TIP]
> Alle Anwendungen, die das Repository berühren, sollten dieselben Einstellungen mit den `IDataProtectionBuilder` Erweiterungs Methoden angeben. Andernfalls werden die Eigenschaften des beibehaltenen Schlüssels von der jeweiligen Anwendung abhängen, die die Schlüssel Generierungs Routinen aufgerufen hat.
