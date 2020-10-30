---
title: Einführung in die Autorisierung in ASP.net Core
author: rick-anderson
description: Erlernen Sie die Grundlagen der Autorisierung und die Funktionsweise der Autorisierung in ASP.net Core apps.
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
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060247"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>Einführung in die Autorisierung in ASP.net Core

<a name="security-authorization-introduction"></a>

Die Autorisierung bezieht sich auf den Prozess, mit dem bestimmt wird, was ein Benutzer tun kann. Ein Administrator kann z. b. eine Dokumentbibliothek erstellen, Dokumente hinzufügen, Dokumente bearbeiten und löschen. Ein nicht administrativer Benutzer, der mit der Bibliothek arbeitet, ist nur zum Lesen der Dokumente autorisiert.

Die Autorisierung ist orthogonal und unabhängig von der Authentifizierung. Die Autorisierung erfordert jedoch einen Authentifizierungsmechanismus. Die Authentifizierung ist der Prozess, bei dem ermittelt wird, wer ein Benutzer ist. Die Authentifizierung kann mindestens eine Identität für den aktuellen Benutzer erstellen.

Weitere Informationen zur Authentifizierung in ASP.net Core finden Sie unter <xref:security/authentication/index> .

## <a name="authorization-types"></a>Autorisierungs Typen

ASP.net Core Autorisierung bietet eine einfache, deklarative und umfassende [Richtlinien basierte](xref:security/authorization/policies) Modell [Funktion](xref:security/authorization/roles) . Die Autorisierung wird in Anforderungen ausgedrückt, und Handler Werten die Ansprüche eines Benutzers anhand der Anforderungen aus. Imperative Überprüfungen können auf einfachen Richtlinien oder Richtlinien basieren, die sowohl die Benutzeridentität als auch die Eigenschaften der Ressource auswerten, auf die der Benutzer zugreifen möchte.

## <a name="namespaces"></a>Namespaces

Autorisierungs Komponenten, einschließlich des `AuthorizeAttribute` -Attributs und des- `AllowAnonymousAttribute` Attributs, finden Sie im- `Microsoft.AspNetCore.Authorization` Namespace.

Informieren Sie sich in der Dokumentation über die [einfache Autorisierung](xref:security/authorization/simple).
