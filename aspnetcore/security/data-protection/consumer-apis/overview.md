---
title: Übersicht über Consumer-APIs für ASP.net Core
author: rick-anderson
description: Erhalten Sie einen kurzen Überblick über die verschiedenen Consumer-APIs, die in der ASP.net Core Datenschutz Bibliothek verfügbar sind.
ms.author: riande
ms.date: 06/11/2019
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
uid: security/data-protection/consumer-apis/overview
ms.openlocfilehash: 485ea3f669b518f2979d04493b281bd116b05f65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051875"
---
# <a name="consumer-apis-overview-for-aspnet-core"></a>Übersicht über Consumer-APIs für ASP.net Core

Die `IDataProtectionProvider` -Schnittstelle und die- `IDataProtector` Schnittstelle sind die Basis Schnittstellen, über die Consumer das Datenschutzsystem verwenden. Sie befinden sich im Paket [Microsoft. aspnetcore. dataprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) .

## <a name="idataprotectionprovider"></a>Idataschutzprovider

Die Anbieter Schnittstelle stellt den Stamm des Datenschutzsystems dar. Sie kann nicht direkt verwendet werden, um Daten zu schützen oder deren Schutz aufzuheben. Stattdessen muss der Consumer einen Verweis auf einen `IDataProtector` abrufen, indem er aufgerufen `IDataProtectionProvider.CreateProtector(purpose)` wird, wobei Zweck eine Zeichenfolge ist, die den vorgesehenen consumerfallfall beschreibt. Weitere Informationen zum Zweck dieses Parameters und zum Auswählen eines geeigneten Werts finden Sie unter [Zweck](xref:security/data-protection/consumer-apis/purpose-strings) Zeichenfolgen.

## <a name="idataprotector"></a>Idataprotector

Die Schutzvorrichtung wird durch einen-Rückruf zurückgegeben `CreateProtector` , und es handelt sich um diese Schnittstelle, mit der Consumer Vorgänge zum Schutz und zum Aufheben des Schutzes durchführen können.

Um ein Datenelement zu schützen, übergeben Sie die Daten an die- `Protect` Methode. Die grundlegende Schnittstelle definiert eine Methode, die Byte []-> Byte [] konvertiert, aber es gibt auch eine Überladung (als Erweiterungsmethode bereitgestellt), die Zeichen folgen > Zeichenfolge konvertiert. Die von den beiden Methoden angebotene Sicherheit ist identisch. der Entwickler sollte auswählen, welche Überladung für den Anwendungsfall am bequemsten ist. Unabhängig von der gewählten Überladung wird der von der Protect-Methode zurückgegebene Wert nun geschützt (verschlüsselt und manipuliert), und die Anwendung kann Sie an einen nicht vertrauenswürdigen Client senden.

Um den Schutz für ein zuvor geschütztes Datenelement aufzuheben, übergeben Sie die geschützten Daten an die- `Unprotect` Methode. (Es gibt Byte []-basierte und Zeichen folgen basierte über Ladungen für Entwickler.) Wenn die geschützte Nutzlast von einem früheren-Befehl in generiert wurde `Protect` `IDataProtector` , gibt die- `Unprotect` Methode die ursprüngliche ungeschützte Nutzlast zurück. Wenn die geschützte Nutzlast manipuliert wurde oder von einer anderen erstellt wurde `IDataProtector` , löst die `Unprotect` Methode CryptographicException aus.

Das Konzept der gleichen und unterschiedlichen Verknüpfungen `IDataProtector` zurück zum Konzept des zwecks. Wenn zwei `IDataProtector` Instanzen aus demselben Stamm generiert wurden `IDataProtectionProvider` , aber über unterschiedliche Zweck Zeichenfolgen im Aufruf von `IDataProtectionProvider.CreateProtector` , werden Sie als [unterschiedliche Schutz](xref:security/data-protection/consumer-apis/purpose-strings)Vorrichtungen angesehen, und eine kann nicht den Schutz von Nutzlasten aufheben, die von der anderen generiert werden.

## <a name="consuming-these-interfaces"></a>Verwenden dieser Schnittstellen

Bei einer di-fähigen Komponente besteht die beabsichtigte Verwendung darin, dass die Komponente einen `IDataProtectionProvider` -Parameter im Konstruktor annimmt und dass das di-System diesen Dienst automatisch bereitstellt, wenn die Komponente instanziiert wird.

> [!NOTE]
> Einige Anwendungen (z. b. Konsolen Anwendungen oder ASP.NET 4. x-Anwendungen) sind möglicherweise nicht di-fähig und können den hier beschriebenen Mechanismus nicht verwenden. Informationen zu diesen Szenarien finden Sie in den Dokumenten zu nicht-di-fähigen [Szenarios](xref:security/data-protection/configuration/non-di-scenarios) , um weitere Informationen zum Abrufen einer Instanz eines Anbieters ohne Verwendung von di zu erhalten `IDataProtection` .

Im folgenden Beispiel werden drei Konzepte veranschaulicht:

1. [Hinzufügen des Datenschutzsystems](xref:security/data-protection/configuration/overview) zum Dienst Container

2. Verwenden von di zum Empfangen einer Instanz von `IDataProtectionProvider` und

3. Erstellen eines `IDataProtector` aus einem `IDataProtectionProvider` und verwenden dieses zum schützen und Aufheben des Schutzes von Daten.

[!code-csharp[](../using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

Das Paket Microsoft. aspnetcore. dataprotection. Abstractions enthält eine Erweiterungsmethode, die `IServiceProvider.GetDataProtector` für den Entwickler von Vorteil ist. Sie wird als einzelner Vorgang gekapselt, wobei sowohl ein `IDataProtectionProvider` vom Dienstanbieter abgerufen als auch aufgerufen wird `IDataProtectionProvider.CreateProtector` . Im folgenden Beispiel wird die Verwendung veranschaulicht.

[!code-csharp[](./overview/samples/getdataprotector.cs?highlight=15)]

>[!TIP]
> Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher. Wenn eine Komponente einen Verweis auf einen `IDataProtector` über einen Aufruf von erhält `CreateProtector` , wird dieser Verweis für mehrere Aufrufe von `Protect` und verwendet `Unprotect` . Ein-Aufrufe von löst `Unprotect` CryptographicException aus, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann. Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs-e liest, cookie kann diesen Fehler verarbeiten und die Anforderung so behandeln, als wäre sie überhaupt nicht voll cookie ständig. Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.
