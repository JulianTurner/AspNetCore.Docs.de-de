---
title: Hash Kennwörter in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Kenn Wörter mithilfe der ASP.net Core-Datenschutz-APIs Kenn Wörtern.
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
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: a970d44a1ca6b9f3534bddb34b037e7c2fdc5389
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051862"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hash Kennwörter in ASP.net Core

Die Codebasis für den Datenschutz umfasst das Paket *Microsoft. aspnetcore. Cryptography. keyderivations* , das die Funktionen der kryptografieschlüsselableitung enthält. Bei diesem Paket handelt es sich um eine eigenständige Komponente, die keine Abhängigkeiten vom Rest des Datenschutzsystems aufweist. Sie kann vollständig unabhängig verwendet werden. Die Quelle ist neben der Datenschutz-Codebasis vorhanden.

Das Paket bietet derzeit eine Methode `KeyDerivation.Pbkdf2` , die das Hashen eines Kennworts mithilfe des [PBKDF2-Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2)zulässt. Diese API ähnelt dem vorhandenen [Rfc2898DeriveBytes-Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes)der .NET Framework, aber es gibt drei wichtige Unterschiede:

1. Die- `KeyDerivation.Pbkdf2` Methode unterstützt die Verwendung mehrerer prfs (zurzeit `HMACSHA1` , `HMACSHA256` und `HMACSHA512` ), während der- `Rfc2898DeriveBytes` Typ nur unterstützt `HMACSHA1` .

2. Mit der `KeyDerivation.Pbkdf2` -Methode wird das aktuelle Betriebssystem erkannt, und es wird versucht, die am besten optimierte Implementierung der Routine zu wählen, um in bestimmten Fällen eine viel bessere Leistung zu erzielen (Unter Windows 8 bietet das Angebot ungefähr Zehnfache den Durchsatz von `Rfc2898DeriveBytes` .)

3. Die-Methode erfordert, dass der Aufrufer `KeyDerivation.Pbkdf2` alle Parameter (Salt, PRF und Iterations Anzahl) angibt. Der- `Rfc2898DeriveBytes` Typ stellt Standardwerte für diese bereit.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Sehen Sie sich den [Quellcode](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) für ASP.NET Core Identity den `PasswordHasher` Typ für einen realen Anwendungsfall an.
