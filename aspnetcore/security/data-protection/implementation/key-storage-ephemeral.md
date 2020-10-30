---
title: Kurzlebige Datenschutzanbieter in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails der ASP.net Core kurzlebigen Datenschutzanbieter.
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
uid: security/data-protection/implementation/key-storage-ephemeral
ms.openlocfilehash: ed0fca88ecf2b002a4421fb120d90adff1b5b12e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052697"
---
# <a name="ephemeral-data-protection-providers-in-aspnet-core"></a>Kurzlebige Datenschutzanbieter in ASP.net Core

<a name="data-protection-implementation-key-storage-ephemeral"></a>

Es gibt Szenarien, in denen eine Anwendung eine drossöhe benötigt `IDataProtectionProvider` . Der Entwickler kann z. b. einfach nur in einer Konsolenanwendung experimentieren, oder die Anwendung selbst ist flüchtig (es ist ein Skript oder ein Komponenten Testprojekt). Um diese Szenarien zu unterstützen, enthält das Paket [Microsoft. aspnetcore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) einen-Typ `EphemeralDataProtectionProvider` . Dieser Typ stellt eine grundlegende Implementierung von bereit `IDataProtectionProvider` , deren schlüsselrepository ausschließlich im Arbeitsspeicher gespeichert ist und nicht in einen Sicherungs Speicher geschrieben wird.

Jede Instanz von `EphemeralDataProtectionProvider` verwendet Ihren eigenen eindeutigen Hauptschlüssel. Wenn eine, die sich auf einem befindet `IDataProtector` `EphemeralDataProtectionProvider` , eine geschützte Nutzlast generiert, kann diese Nutzlast daher nur von einem Äquivalent `IDataProtector` (bei der gleichen [Zweck](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-consumer-apis-purposes) Kette) geschützt werden, der sich auf derselben `EphemeralDataProtectionProvider` Instanz befindet.

Das folgende Beispiel veranschaulicht das Instanziieren eines `EphemeralDataProtectionProvider` und dessen Verwendung zum schützen und Aufheben des Schutzes von Daten.

```csharp
using System;
using Microsoft.AspNetCore.DataProtection;

public class Program
{
    public static void Main(string[] args)
    {
        const string purpose = "Ephemeral.App.v1";

        // create an ephemeral provider and demonstrate that it can round-trip a payload
        var provider = new EphemeralDataProtectionProvider();
        var protector = provider.CreateProtector(purpose);
        Console.Write("Enter input: ");
        string input = Console.ReadLine();

        // protect the payload
        string protectedPayload = protector.Protect(input);
        Console.WriteLine($"Protect returned: {protectedPayload}");

        // unprotect the payload
        string unprotectedPayload = protector.Unprotect(protectedPayload);
        Console.WriteLine($"Unprotect returned: {unprotectedPayload}");

        // if I create a new ephemeral provider, it won't be able to unprotect existing
        // payloads, even if I specify the same purpose
        provider = new EphemeralDataProtectionProvider();
        protector = provider.CreateProtector(purpose);
        unprotectedPayload = protector.Unprotect(protectedPayload); // THROWS
    }
}

/*
* SAMPLE OUTPUT
*
* Enter input: Hello!
* Protect returned: CfDJ8AAAAAAAAAAAAAAAAAAAAA...uGoxWLjGKtm1SkNACQ
* Unprotect returned: Hello!
* << throws CryptographicException >>
*/
```
