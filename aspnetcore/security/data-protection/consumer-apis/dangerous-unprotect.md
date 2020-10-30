---
title: Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in einer ASP.net Core-App aufheben können, die mit Schlüsseln geschützt sind, die seit dem widerrufen wurden
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051095"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Die ASP.net Core-Datenschutz-APIs sind nicht in erster Linie für die unbegrenzte Persistenz von vertraulichen Nutzlasten vorgesehen. Andere Technologien wie [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) und [Azure Rights Management](/rights-management/) sind besser für das Szenario der unbegrenzten Speicherung geeignet und verfügen über die entsprechenden leistungsstarken Schlüssel Verwaltungsfunktionen. Dies bedeutet, dass ein Entwickler nicht daran hindert, die ASP.net Core Datenschutz-APIs für den langfristigen Schutz vertraulicher Daten zu verwenden. Schlüssel werden nie aus dem Schlüsselbund entfernt. so `IDataProtector.Unprotect` können vorhandene Nutzlasten immer wieder hergestellt werden, solange die Schlüssel verfügbar und gültig sind.

Es tritt jedoch ein Problem auf, wenn der Entwickler versucht, den Schutz von Daten aufzuheben, die mit einem gesperrten Schlüssel geschützt wurden, da `IDataProtector.Unprotect` in diesem Fall eine Ausnahme auslöst. Dies kann bei kurzlebigen oder vorübergehenden Nutzlasten (wie z. b. Authentifizierungs Token) in Ordnung sein, da diese Arten von Nutzlasten problemlos vom System neu erstellt werden können und sich der Standort Besucher im schlimmsten Fall erneut anmelden muss. Für persistente Nutzlasten könnte das Auslösen von " `Unprotect` throw" zu nicht akzeptablen Datenverlusten führen.

## <a name="ipersisteddataprotector"></a>Ipersisteddataprotector

Um das Szenario zu unterstützen, in dem Nutzlasten auch bei widerrufenen Schlüsseln nicht geschützt werden können, enthält das Datenschutzsystem einen- `IPersistedDataProtector` Typ. Um eine Instanz von zu erhalten `IPersistedDataProtector` , können Sie einfach eine Instanz von `IDataProtector` in der normalen Weise erhalten und versuchen, die in umzuwandeln `IDataProtector` `IPersistedDataProtector` .

> [!NOTE]
> Nicht alle `IDataProtector` Instanzen können in umgewandelt werden `IPersistedDataProtector` . Entwickler sollten den c#-Operator oder ähnlich verwenden, um Lauf Zeit Ausnahmen zu vermeiden, die durch ungültige Umwandlungen verursacht werden, und Sie sollten darauf vorbereitet sein, den Fehlerfall entsprechend zu behandeln.

`IPersistedDataProtector` macht die folgende API-Oberfläche verfügbar:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Diese API übernimmt die geschützte Nutzlast (als Bytearray) und gibt die ungeschützte Nutzlast zurück. Es gibt keine Zeichen folgen basierte Überladung. Die zwei out-Parameter lauten wie folgt.

* `requiresMigration`: wird auf "true" festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, nicht mehr der aktive Standardschlüssel ist, z. b. wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, veraltet ist und ein Schlüssel rollender Vorgang stattfindet. Der Aufrufer möchte ggf. den erneuten Schutz der Nutzlast abhängig von Ihren geschäftlichen Anforderungen in Erwägung gezogen.

* `wasRevoked`: wird auf true festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wurde, widerrufen wurde.

>[!WARNING]
> Gehen Sie beim übergeben `ignoreRevocationErrors: true` an die-Methode mit äußerster Vorsicht vor `DangerousUnprotect` . Wenn nach dem Aufrufen dieser Methode der `wasRevoked` Wert true ist, wurde der zum Schutz dieser Nutzlast verwendete Schlüssel widerrufen, und die Authentizität der Nutzlast sollte als Fehler verdächtig behandelt werden. In diesem Fall wird nur der Betrieb der nicht geschützten Nutzlast fortgesetzt, wenn Sie eine separate Gewissheit haben, dass Sie authentisch ist, z. b., dass Sie von einer sicheren Datenbank stammt, anstatt von einem nicht vertrauenswürdigen WebClient gesendet zu werden.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
