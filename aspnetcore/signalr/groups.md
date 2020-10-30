---
title: 'Verwalten von Benutzern und Gruppen in :::no-loc(SignalR):::'
author: bradygaster
description: 'Übersicht über ASP.net Core :::no-loc(SignalR)::: Benutzer-und Gruppenverwaltung.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050952"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="c086a-103">Verwalten von Benutzern und Gruppen in :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="c086a-103">Manage users and groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="c086a-104">Von [Brennan](https://github.com/BrennanConroy) "a"</span><span class="sxs-lookup"><span data-stu-id="c086a-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="c086a-105">:::no-loc(SignalR)::: ermöglicht das Senden von Nachrichten an alle Verbindungen, die einem bestimmten Benutzer zugeordnet sind, sowie an benannte Verbindungs Gruppen.</span><span class="sxs-lookup"><span data-stu-id="c086a-105">:::no-loc(SignalR)::: allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="c086a-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="c086a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="c086a-107">Benutzer in :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="c086a-107">Users in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="c086a-108">Ein einzelner Benutzer in :::no-loc(SignalR)::: kann über mehrere Verbindungen mit einer App verfügen.</span><span class="sxs-lookup"><span data-stu-id="c086a-108">A single user in :::no-loc(SignalR)::: can have multiple connections to an app.</span></span> <span data-ttu-id="c086a-109">Beispielsweise könnte ein Benutzer sowohl auf seinem Desktop als auch auf seinem Telefon verbunden sein.</span><span class="sxs-lookup"><span data-stu-id="c086a-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="c086a-110">Jedes Gerät verfügt über eine separate :::no-loc(SignalR)::: Verbindung, aber alle sind dem gleichen Benutzer zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c086a-110">Each device has a separate :::no-loc(SignalR)::: connection, but they're all associated with the same user.</span></span> <span data-ttu-id="c086a-111">Wenn eine Nachricht an den Benutzer gesendet wird, erhalten alle Verbindungen, die diesem Benutzer zugeordnet sind, die Nachricht.</span><span class="sxs-lookup"><span data-stu-id="c086a-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="c086a-112">Der Benutzer Bezeichner für eine Verbindung kann über die- `Context.UserIdentifier` Eigenschaft im Hub aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="c086a-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="c086a-113">Standardmäßig :::no-loc(SignalR)::: verwendet die `ClaimTypes.NameIdentifier` aus der, die `ClaimsPrincipal` der Verbindung zugeordnet ist, als Benutzer Bezeichner.</span><span class="sxs-lookup"><span data-stu-id="c086a-113">By default, :::no-loc(SignalR)::: uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="c086a-114">Informationen zum Anpassen dieses Verhaltens finden Sie unter [Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span><span class="sxs-lookup"><span data-stu-id="c086a-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="c086a-115">Senden Sie eine Nachricht an einen bestimmten Benutzer, indem Sie die Benutzer-ID an die `User` Funktion in einer Hub-Methode übergeben, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="c086a-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="c086a-116">Bei der Benutzerkennung wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="c086a-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="c086a-117">Gruppen in :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="c086a-117">Groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="c086a-118">Eine Gruppe ist eine Auflistung von Verbindungen, die einem Namen zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="c086a-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="c086a-119">Nachrichten können an alle Verbindungen in einer Gruppe gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="c086a-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="c086a-120">Gruppen sind die empfohlene Vorgehensweise zum Senden an eine Verbindung oder mehrere Verbindungen, da die Gruppen von der Anwendung verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="c086a-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="c086a-121">Eine Verbindung kann Mitglied mehrerer Gruppen sein.</span><span class="sxs-lookup"><span data-stu-id="c086a-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="c086a-122">Gruppen eignen sich ideal für eine Chat-Anwendung, bei der jeder Raum als Gruppe dargestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c086a-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="c086a-123">Verbindungen werden über die `AddToGroupAsync` -Methode und die-Methode zu Gruppen hinzugefügt oder aus diesen entfernt `RemoveFromGroupAsync` .</span><span class="sxs-lookup"><span data-stu-id="c086a-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="c086a-124">Die Gruppenmitgliedschaft wird nicht beibehalten, wenn eine Verbindung erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c086a-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="c086a-125">Die Verbindung muss der Gruppe erneut beitreten, wenn Sie wieder hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c086a-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="c086a-126">Es ist nicht möglich, die Mitglieder einer Gruppe zu zählen, da diese Informationen nicht verfügbar sind, wenn die Anwendung auf mehrere Server skaliert wird.</span><span class="sxs-lookup"><span data-stu-id="c086a-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="c086a-127">Zum Schutz des Zugriffs auf Ressourcen bei der Verwendung von Gruppen verwenden Sie die [Authentifizierungs-und Autorisierungs](xref:signalr/authn-and-authz) Funktionen in ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="c086a-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="c086a-128">Wenn ein Benutzer einer Gruppe nur dann hinzugefügt wird, wenn die Anmelde Informationen für diese Gruppe gültig sind, werden an diese Gruppe gesendete Nachrichten nur an autorisierte Benutzer übermittelt.</span><span class="sxs-lookup"><span data-stu-id="c086a-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="c086a-129">Allerdings handelt es sich bei Gruppen nicht um eine Sicherheitsfunktion.</span><span class="sxs-lookup"><span data-stu-id="c086a-129">However, groups are not a security feature.</span></span> <span data-ttu-id="c086a-130">Authentifizierungs Ansprüche verfügen über Funktionen, die von Gruppen nicht unterliegen, z. b. Ablauf und Sperrung.</span><span class="sxs-lookup"><span data-stu-id="c086a-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="c086a-131">Wenn die Berechtigung eines Benutzers für den Zugriff auf die Gruppe widerrufen wird, muss die APP den Benutzer explizit aus der Gruppe entfernen.</span><span class="sxs-lookup"><span data-stu-id="c086a-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="c086a-132">Bei Gruppennamen wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="c086a-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="c086a-133">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c086a-133">Related resources</span></span>

* [<span data-ttu-id="c086a-134">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="c086a-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="c086a-135">Hubs</span><span class="sxs-lookup"><span data-stu-id="c086a-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c086a-136">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="c086a-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
