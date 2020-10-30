---
title: Beginnen Sie mit den Datenschutz-APIs in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die ASP.net Core-Datenschutz-APIs verwenden, um Daten in einer APP zu schützen und zu schützen.
ms.author: riande
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051654"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a>Beginnen Sie mit den Datenschutz-APIs in ASP.net Core

<a name="security-data-protection-getting-started"></a>

Am einfachsten besteht der Schutz von Daten aus den folgenden Schritten:

1. Erstellen Sie eine Daten Schutzvorrichtung von einem Datenschutzanbieter.

2. Wenden Sie die- `Protect` Methode mit den Daten an, die Sie schützen möchten.

3. Nennen Sie die- `Unprotect` Methode mit den Daten, die Sie in nur-Text-Daten zurücksetzen möchten.

Bei den meisten Frameworks und App-Modellen, wie ASP.net Core oder SignalR , wird das Datenschutzsystem bereits konfiguriert und einem Dienst Container hinzugefügt, auf den Sie über die Abhängigkeitsinjektion zugreifen. Das folgende Beispiel veranschaulicht das Konfigurieren eines Dienst Containers für die Abhängigkeitsinjektion und das Registrieren des Datenschutz Stapels, das Empfangen des Datenschutz Anbieters über di, das Erstellen einer Schutzvorrichtung und das Schützen des Schutzes von Daten.

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Wenn Sie eine Schutzvorrichtung erstellen, müssen Sie eine oder mehrere [Zweck](xref:security/data-protection/consumer-apis/purpose-strings)Zeichenfolgen angeben. Eine Zweck Zeichenfolge ermöglicht die Isolation zwischen Consumern. Beispielsweise wäre eine Schutzvorrichtung, die mit einer Zweck Zeichenfolge "grün" erstellt wurde, nicht in der Lage, die Daten zu schützen, die von einer Schutzvorrichtung mit dem Zweck "Lila" bereitgestellt werden

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher. Wenn eine Komponente einen Verweis auf einen `IDataProtector` über einen Aufruf von erhält `CreateProtector` , wird dieser Verweis für mehrere Aufrufe von `Protect` und verwendet `Unprotect` .
>
>Ein-Aufrufe von löst `Unprotect` CryptographicException aus, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann. Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs-e liest, cookie kann diesen Fehler verarbeiten und die Anforderung so behandeln, als wäre sie überhaupt nicht voll cookie ständig. Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.
