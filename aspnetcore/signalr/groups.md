---
title: Verwalten von Benutzern und Gruppen in SignalR
author: bradygaster
description: Übersicht über ASP.net Core SignalR Benutzer-und Gruppenverwaltung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050952"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a>Verwalten von Benutzern und Gruppen in SignalR

Von [Brennan](https://github.com/BrennanConroy) "a"

SignalR ermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind, sowie an benannte Verbindungs Gruppen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="users-in-no-locsignalr"></a>Benutzer in SignalR

Ein einzelner Benutzer in SignalR kann über mehrere Verbindungen mit einer App verfügen. Beispielsweise könnte ein Benutzer sowohl auf seinem Desktop als auch auf seinem Telefon verbunden sein. Jedes Gerät verfügt über eine separate SignalR Verbindung, aber alle sind dem gleichen Benutzer zugeordnet. Wenn eine Nachricht an den Benutzer gesendet wird, erhalten alle Verbindungen, die diesem Benutzer zugeordnet sind, die Nachricht. Der Benutzer Bezeichner für eine Verbindung kann über die- `Context.UserIdentifier` Eigenschaft im Hub aufgerufen werden.

Standardmäßig SignalR verwendet die `ClaimTypes.NameIdentifier` aus der, die `ClaimsPrincipal` der Verbindung zugeordnet ist, als Benutzer Bezeichner. Informationen zum Anpassen dieses Verhaltens finden Sie unter [Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).

Senden Sie eine Nachricht an einen bestimmten Benutzer, indem Sie die Benutzer-ID an die `User` Funktion in einer Hub-Methode übergeben, wie im folgenden Beispiel gezeigt:

> [!NOTE]
> Bei der Benutzerkennung wird die Groß-/Kleinschreibung beachtet

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a>Gruppen in SignalR

Eine Gruppe ist eine Auflistung von Verbindungen, die einem Namen zugeordnet sind. Nachrichten können an alle Verbindungen in einer Gruppe gesendet werden. Gruppen sind die empfohlene Vorgehensweise zum Senden an eine Verbindung oder mehrere Verbindungen, da die Gruppen von der Anwendung verwaltet werden. Eine Verbindung kann Mitglied mehrerer Gruppen sein. Gruppen eignen sich ideal für eine Chat-Anwendung, bei der jeder Raum als Gruppe dargestellt werden kann. Verbindungen werden über die `AddToGroupAsync` -Methode und die-Methode zu Gruppen hinzugefügt oder aus diesen entfernt `RemoveFromGroupAsync` .

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

Die Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt wird. Die Verbindung muss der Gruppe erneut beitreten, wenn Sie wieder hergestellt wird. Es ist nicht möglich, die Mitglieder einer Gruppe zu zählen, da diese Informationen nicht verfügbar sind, wenn die Anwendung auf mehrere Server skaliert wird.

Zum Schutz des Zugriffs auf Ressourcen bei der Verwendung von Gruppen verwenden Sie die [Authentifizierungs-und Autorisierungs](xref:signalr/authn-and-authz) Funktionen in ASP.net Core. Wenn ein Benutzer einer Gruppe nur dann hinzugefügt wird, wenn die Anmelde Informationen für diese Gruppe gültig sind, werden an diese Gruppe gesendete Nachrichten nur an autorisierte Benutzer übermittelt. Allerdings handelt es sich bei Gruppen nicht um eine Sicherheitsfunktion. Authentifizierungs Ansprüche verfügen über Funktionen, die von Gruppen nicht unterliegen, z. b. Ablauf und Sperrung. Wenn die Berechtigung eines Benutzers für den Zugriff auf die Gruppe widerrufen wird, muss die APP den Benutzer explizit aus der Gruppe entfernen.

> [!NOTE]
> Bei Gruppennamen wird die Groß-/Kleinschreibung beachtet.

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
