---
title: ':::no-loc(SignalR)::: API-Entwurfs Überlegungen'
author: anurse
description: 'Erfahren Sie, wie Sie :::no-loc(SignalR)::: APIs für die Kompatibilität zwischen verschiedenen Versionen Ihrer APP entwerfen.'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/api-design
ms.openlocfilehash: 87665a7950edbc70b664230d2f078598e9dbc0aa
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059649"
---
# <a name="no-locsignalr-api-design-considerations"></a><span data-ttu-id="3733a-103">:::no-loc(SignalR)::: API-Entwurfs Überlegungen</span><span class="sxs-lookup"><span data-stu-id="3733a-103">:::no-loc(SignalR)::: API design considerations</span></span>

<span data-ttu-id="3733a-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3733a-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="3733a-105">Dieser Artikel enthält Anleitungen zum Entwickeln von :::no-loc(SignalR)::: -basierten APIs.</span><span class="sxs-lookup"><span data-stu-id="3733a-105">This article provides guidance for building :::no-loc(SignalR):::-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="3733a-106">Verwenden von benutzerdefinierten Objekt Parametern, um Abwärtskompatibilität zu gewährleisten</span><span class="sxs-lookup"><span data-stu-id="3733a-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="3733a-107">Das Hinzufügen von Parametern zu einer :::no-loc(SignalR)::: Hub-Methode (auf dem Client oder auf dem Server) ist eine *Breaking Change* .</span><span class="sxs-lookup"><span data-stu-id="3733a-107">Adding parameters to a :::no-loc(SignalR)::: hub method (on either the client or the server) is a *breaking change* .</span></span> <span data-ttu-id="3733a-108">Dies bedeutet, dass ältere Clients/Server Fehler erhalten, wenn Sie versuchen, die Methode ohne die erforderliche Anzahl von Parametern aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="3733a-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="3733a-109">Das Hinzufügen von Eigenschaften zu einem benutzerdefinierten Objekt Parameter ist jedoch **keine** Breaking Change.</span><span class="sxs-lookup"><span data-stu-id="3733a-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="3733a-110">Dies kann zum Entwerfen kompatibler APIs verwendet werden, die für Änderungen auf dem Client oder dem Server stabil sind.</span><span class="sxs-lookup"><span data-stu-id="3733a-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="3733a-111">Stellen Sie sich beispielsweise eine serverseitige API wie die folgende vor:</span><span class="sxs-lookup"><span data-stu-id="3733a-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="3733a-112">Der JavaScript-Client ruft diese Methode mit `invoke` wie folgt auf:</span><span class="sxs-lookup"><span data-stu-id="3733a-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="3733a-113">Wenn Sie später der Server Methode einen zweiten Parameter hinzufügen, wird dieser Parameterwert von älteren Clients nicht bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3733a-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="3733a-114">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3733a-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="3733a-115">Wenn der alte Client versucht, diese Methode aufzurufen, wird eine Fehlermeldung wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3733a-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.:::no-loc(SignalR):::.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="3733a-116">Auf dem Server wird eine Protokollmeldung wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3733a-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="3733a-117">Der alte Client hat nur einen Parameter gesendet, aber die neuere Server-API erforderte zwei Parameter.</span><span class="sxs-lookup"><span data-stu-id="3733a-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="3733a-118">Die Verwendung von benutzerdefinierten Objekten als Parameter bietet Ihnen mehr Flexibilität.</span><span class="sxs-lookup"><span data-stu-id="3733a-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="3733a-119">Wir wollen die ursprüngliche API so umgestalten, dass Sie ein benutzerdefiniertes Objekt verwendet:</span><span class="sxs-lookup"><span data-stu-id="3733a-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="3733a-120">Nun verwendet der Client ein-Objekt, um die-Methode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="3733a-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="3733a-121">Fügen Sie dem-Objekt eine Eigenschaft hinzu, anstatt einen Parameter hinzuzufügen `TotalLengthRequest` :</span><span class="sxs-lookup"><span data-stu-id="3733a-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="3733a-122">Wenn der alte Client einen einzelnen Parameter sendet, bleibt die zusätzliche `Param2` Eigenschaft erhalten `null` .</span><span class="sxs-lookup"><span data-stu-id="3733a-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="3733a-123">Sie können eine Nachricht erkennen, die von einem älteren Client gesendet wurde, indem Sie den `Param2` für prüfen `null` und einen Standardwert anwenden.</span><span class="sxs-lookup"><span data-stu-id="3733a-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="3733a-124">Ein neuer Client kann beide Parameter senden.</span><span class="sxs-lookup"><span data-stu-id="3733a-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="3733a-125">Dieselbe Technik funktioniert auch für Methoden, die auf dem Client definiert sind.</span><span class="sxs-lookup"><span data-stu-id="3733a-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="3733a-126">Sie können ein benutzerdefiniertes Objekt von der Serverseite senden:</span><span class="sxs-lookup"><span data-stu-id="3733a-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="3733a-127">Auf der Clientseite greifen Sie auf die-Eigenschaft zu, `Message` anstatt einen Parameter zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="3733a-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="3733a-128">Wenn Sie sich später entscheiden, den Absender der Nachricht der Nutzlast hinzuzufügen, fügen Sie dem-Objekt eine Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="3733a-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="3733a-129">Die älteren Clients erwarten den Wert nicht `Sender` , sodass Sie ihn ignorieren.</span><span class="sxs-lookup"><span data-stu-id="3733a-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="3733a-130">Ein neuer Client kann ihn akzeptieren, indem er aktualisiert, um die neue Eigenschaft zu lesen:</span><span class="sxs-lookup"><span data-stu-id="3733a-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="3733a-131">In diesem Fall ist der neue Client auch für einen alten Server tolerant, der den Wert nicht bereitstellt `Sender` .</span><span class="sxs-lookup"><span data-stu-id="3733a-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="3733a-132">Da der alte Server den Wert nicht bereitstellt `Sender` , überprüft der Client, ob er vorhanden ist, bevor er darauf zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="3733a-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
