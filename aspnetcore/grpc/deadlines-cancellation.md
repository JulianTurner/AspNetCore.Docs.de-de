---
title: Zuverlässige gRPC-Dienste mit Fristen und Abbrüchen
author: jamesnk
description: Hier erfahren Sie, wie Sie zuverlässige gRPC-Dienste mit Fristen und Abbrüchen in .NET erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/07/2020
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
uid: grpc/deadlines-cancellation
ms.openlocfilehash: a735ed4d2ca8db1c9b7998acd14f9be761fe7ec6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059922"
---
# <a name="reliable-grpc-services-with-deadlines-and-cancellation"></a><span data-ttu-id="51a0f-103">Zuverlässige gRPC-Dienste mit Fristen und Abbrüchen</span><span class="sxs-lookup"><span data-stu-id="51a0f-103">Reliable gRPC services with deadlines and cancellation</span></span>

<span data-ttu-id="51a0f-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="51a0f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="51a0f-105">Fristen und Abbrüche sind Features, die von gRPC-Clients zum Abbrechen von aktiven Aufrufen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-105">Deadlines and cancellation are features used by gRPC clients to abort in-progress calls.</span></span> <span data-ttu-id="51a0f-106">In diesem Artikel werden die Bedeutung von Fristen und Abbrüchen sowie deren Verwendung in .NET-gRPC-Apps erläutert.</span><span class="sxs-lookup"><span data-stu-id="51a0f-106">This article discusses why deadlines and cancellation are important, and how to use them in .NET gRPC apps.</span></span>

## <a name="deadlines"></a><span data-ttu-id="51a0f-107">Fristen</span><span class="sxs-lookup"><span data-stu-id="51a0f-107">Deadlines</span></span>

<span data-ttu-id="51a0f-108">Mit einer Frist kann ein gRPC-Client angeben, wie lange er bereit ist, auf den Abschluss eines Aufrufs zu warten.</span><span class="sxs-lookup"><span data-stu-id="51a0f-108">A deadline allows a gRPC client to specify how long it will wait for a call to complete.</span></span> <span data-ttu-id="51a0f-109">Bei Überschreiten der Frist wird der Aufruf abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="51a0f-109">When a deadline is exceeded, the call is canceled.</span></span> <span data-ttu-id="51a0f-110">Das Festlegen einer Frist ist wichtig, da sie eine Obergrenze für den Zeitraum vorgibt, in dem ein Aufruf ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="51a0f-110">Setting a deadline is important because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="51a0f-111">Dadurch wird verhindert, dass fehlerhafte Dienste unbegrenzt lange ausgeführt werden und die Serverressourcen belasten.</span><span class="sxs-lookup"><span data-stu-id="51a0f-111">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="51a0f-112">Fristen stellen einen wichtigen Aspekt bei der Erstellung zuverlässiger Apps dar und sollten daher konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-112">Deadlines are a useful tool for building reliable apps and should be configured.</span></span>

<span data-ttu-id="51a0f-113">Konfiguration von Fristen:</span><span class="sxs-lookup"><span data-stu-id="51a0f-113">Deadline configuration:</span></span>

* <span data-ttu-id="51a0f-114">Die Konfiguration einer Frist für die Ausführung eines Aufrufs erfolgt mit `CallOptions.Deadline`.</span><span class="sxs-lookup"><span data-stu-id="51a0f-114">A deadline is configured using `CallOptions.Deadline` when a call is made.</span></span>
* <span data-ttu-id="51a0f-115">Es gibt keinen Standardwert für Fristen.</span><span class="sxs-lookup"><span data-stu-id="51a0f-115">There is no default deadline value.</span></span> <span data-ttu-id="51a0f-116">gRPC-Aufrufe sind zeitlich nicht begrenzt, es sei denn, es wird eine Frist festgelegt.</span><span class="sxs-lookup"><span data-stu-id="51a0f-116">gRPC calls aren't time limited unless a deadline is specified.</span></span>
* <span data-ttu-id="51a0f-117">Fristen werden im UTC-Zeitformat angegeben.</span><span class="sxs-lookup"><span data-stu-id="51a0f-117">A deadline is the UTC time of when the deadline is exceeded.</span></span> <span data-ttu-id="51a0f-118">`DateTime.UtcNow.AddSeconds(5)` entspricht beispielsweise einer Frist von 5 Sekunden ab dem aktuellen Zeitpunkt.</span><span class="sxs-lookup"><span data-stu-id="51a0f-118">For example, `DateTime.UtcNow.AddSeconds(5)` is a deadline of 5 seconds from now.</span></span>
* <span data-ttu-id="51a0f-119">Wenn ein früherer oder der aktuelle Zeitpunkt angegeben wird, führt dies sofort zur Fristüberschreitung des Aufrufs.</span><span class="sxs-lookup"><span data-stu-id="51a0f-119">If a past or current time is used then the call immediately exceeds the deadline.</span></span>
* <span data-ttu-id="51a0f-120">Die Frist wird mit dem gRPC-Aufruf an den Dienst gesendet und vom Client und Dienst unabhängig voneinander nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="51a0f-120">The deadline is sent with the gRPC call to the service and is independently tracked by both the client and the service.</span></span> <span data-ttu-id="51a0f-121">Es ist möglich, dass ein gRPC-Aufruf auf einem Computer abgeschlossen ist, die Frist jedoch überschritten ist, wenn die Antwort an den Client zurückgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="51a0f-121">It is possible that a gRPC call completes on one machine, but by the time the response has returned to the client the deadline has been exceeded.</span></span>

<span data-ttu-id="51a0f-122">Bei Überschreiten der Frist verhalten sich Client und Dienst unterschiedlich:</span><span class="sxs-lookup"><span data-stu-id="51a0f-122">If a deadline is exceeded, the client and service have different behavior:</span></span>

* <span data-ttu-id="51a0f-123">Der Client bricht die zugrunde liegende HTTP-Anforderung sofort ab und löst den Fehler `DeadlineExceeded` aus.</span><span class="sxs-lookup"><span data-stu-id="51a0f-123">The client immediately aborts the underlying HTTP request and throws a `DeadlineExceeded` error.</span></span> <span data-ttu-id="51a0f-124">Die Client-App kann den Fehler abfangen und dem Benutzer eine Meldung mit dem Hinweis auf eine Zeitüberschreitung anzeigen.</span><span class="sxs-lookup"><span data-stu-id="51a0f-124">The client app can choose to catch the error and display a timeout message to the user.</span></span>
* <span data-ttu-id="51a0f-125">Die ausgeführte HTTP-Anforderung wird auf dem Server abgebrochen, und der Fehler [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="51a0f-125">On the server, the executing HTTP request is aborted and [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="51a0f-126">Die HTTP-Anforderung wird zwar abgebrochen, der gRPC-Aufruf wird allerdings weiterhin auf dem Server ausgeführt, bis die Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="51a0f-126">Although the HTTP request is aborted, the gRPC call continues to run on the server until the method completes.</span></span> <span data-ttu-id="51a0f-127">Es ist wichtig, dass das Abbruchtoken an asynchrone Methoden übergeben wird, damit sie zusammen mit dem Aufruf abgebrochen werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-127">It's important that the cancellation token is passed to async methods so they are cancelled along with the call.</span></span> <span data-ttu-id="51a0f-128">Beispiel: Ein Abbruchtoken wird an asynchrone Datenbankabfragen und HTTP-Anforderungen übergeben.</span><span class="sxs-lookup"><span data-stu-id="51a0f-128">For example, passing a cancellation token to async database queries and HTTP requests.</span></span> <span data-ttu-id="51a0f-129">Mit Übergabe eines Abbruchtokens können abgebrochene Aufrufe schnell auf dem Server ausgeführt und Ressourcen für andere Aufrufe freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-129">Passing a cancellation token allows the canceled call to complete quickly on the server and free up resources for other calls.</span></span>

<span data-ttu-id="51a0f-130">Konfigurieren Sie `CallOptions.Deadline`, um eine Frist für einen gRPC-Aufruf festzulegen:</span><span class="sxs-lookup"><span data-stu-id="51a0f-130">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="51a0f-131">Verwenden von `ServerCallContext.CancellationToken` in einem gRPC-Dienst:</span><span class="sxs-lookup"><span data-stu-id="51a0f-131">Using `ServerCallContext.CancellationToken` in a gRPC service:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-server.cs?highlight=5)]

### <a name="propagating-deadlines"></a><span data-ttu-id="51a0f-132">Übertragen von Fristen</span><span class="sxs-lookup"><span data-stu-id="51a0f-132">Propagating deadlines</span></span>

<span data-ttu-id="51a0f-133">Wenn ein gRPC-Aufruf von einem ausgeführten gRPC-Dienst verarbeitet wird, muss die Frist übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-133">When a gRPC call is made from an executing gRPC service, the deadline should be propagated.</span></span> <span data-ttu-id="51a0f-134">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="51a0f-134">For example:</span></span>

1. <span data-ttu-id="51a0f-135">Client-App ruft `FrontendService.GetUser` mit einer Frist auf.</span><span class="sxs-lookup"><span data-stu-id="51a0f-135">Client app calls `FrontendService.GetUser` with a deadline.</span></span>
2. <span data-ttu-id="51a0f-136">`FrontendService` ruft `UserService.GetUser` auf.</span><span class="sxs-lookup"><span data-stu-id="51a0f-136">`FrontendService` calls `UserService.GetUser`.</span></span> <span data-ttu-id="51a0f-137">Die vom Client angegebene Frist muss mit dem neuen gRPC-Aufruf angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-137">The deadline specified by the client should be specified with the new gRPC call.</span></span>
3. <span data-ttu-id="51a0f-138">`UserService.GetUser` empfängt die Frist.</span><span class="sxs-lookup"><span data-stu-id="51a0f-138">`UserService.GetUser` receives the deadline.</span></span> <span data-ttu-id="51a0f-139">Wenn die Frist für die Client-App überschritten wird, kommt es zu einer Zeitüberschreitung.</span><span class="sxs-lookup"><span data-stu-id="51a0f-139">It correctly times-out if the client app's deadline is exceeded.</span></span>

<span data-ttu-id="51a0f-140">Im Aufrufkontext wird die Frist mit `ServerCallContext.Deadline` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="51a0f-140">The call context provides the deadline with `ServerCallContext.Deadline`:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-propagate.cs?highlight=7)]

<span data-ttu-id="51a0f-141">Das manuelle Übertragen von Fristen kann mühsam sein.</span><span class="sxs-lookup"><span data-stu-id="51a0f-141">Manually propagating deadlines can be cumbersome.</span></span> <span data-ttu-id="51a0f-142">Die Frist muss an jeden einzelnen Aufruf übermittelt werden. Dabei kann schnell einer übersehen werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-142">The deadline needs to be passed to every call, and it's easy to accidentally miss.</span></span> <span data-ttu-id="51a0f-143">Die gRPC-Clientfactory stellt eine automatische Lösung bereit.</span><span class="sxs-lookup"><span data-stu-id="51a0f-143">An automatic solution is available with gRPC client factory.</span></span> <span data-ttu-id="51a0f-144">Durch Angeben von `EnableCallContextPropagation` wird Folgendes sichergestellt:</span><span class="sxs-lookup"><span data-stu-id="51a0f-144">Specifying `EnableCallContextPropagation`:</span></span>

* <span data-ttu-id="51a0f-145">Automatische Weitergabe des Frist- und Abbruchtokens an untergeordnete Aufrufe</span><span class="sxs-lookup"><span data-stu-id="51a0f-145">Automatically propagates the deadline and cancellation token to child calls.</span></span>
* <span data-ttu-id="51a0f-146">Zuverlässige Übertragung der Frist und des Abbruchs komplexer und verschachtelter gRPC-Szenarios</span><span class="sxs-lookup"><span data-stu-id="51a0f-146">Is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/clientfactory-propagate.cs?highlight=6)]

<span data-ttu-id="51a0f-147">Weitere Informationen finden Sie unter <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span><span class="sxs-lookup"><span data-stu-id="51a0f-147">For more information, see <xref:grpc/clientfactory#deadline-and-cancellation-propagation>.</span></span>

## <a name="cancellation"></a><span data-ttu-id="51a0f-148">Abbruch</span><span class="sxs-lookup"><span data-stu-id="51a0f-148">Cancellation</span></span>

<span data-ttu-id="51a0f-149">Mit Festlegen eines Abbruchs kann ein gRPC-Client nicht mehr benötigte Aufrufe mit langer Ausführungszeit abbrechen.</span><span class="sxs-lookup"><span data-stu-id="51a0f-149">Cancellation allows a gRPC client to cancel long running calls that are no longer needed.</span></span> <span data-ttu-id="51a0f-150">So startet beispielsweise ein gRPC-Aufruf, mit dem Echtzeitupdates gestreamt werden, wenn der Benutzer eine Seite auf einer Website besucht.</span><span class="sxs-lookup"><span data-stu-id="51a0f-150">For example, a gRPC call that streams realtime updates is started when the user visits a page on a website.</span></span> <span data-ttu-id="51a0f-151">Der Streamingvorgang sollte abgebrochen werden, wenn der Benutzer von der Seite wieder verlässt.</span><span class="sxs-lookup"><span data-stu-id="51a0f-151">The stream should be canceled when the user navigates away from the page.</span></span>

<span data-ttu-id="51a0f-152">Der gRPC-Aufruf kann im Client abgebrochen werden, indem ein Abbruchtoken mit [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) oder durch Aufrufen von `Dispose` weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="51a0f-152">A gRPC call can be canceled in the client by passing a cancellation token with [CallOptions.CancellationToken](xref:System.Threading.CancellationToken) or calling `Dispose` on the call.</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/cancellation-client.cs?highlight=19)]

<span data-ttu-id="51a0f-153">gRPC-Dienste, die abgebrochen werden können, sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="51a0f-153">gRPC services that can be cancelled should:</span></span>
* <span data-ttu-id="51a0f-154">Übergeben von `ServerCallContext.CancellationToken` an asynchrone Methoden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-154">Pass `ServerCallContext.CancellationToken` to async methods.</span></span> <span data-ttu-id="51a0f-155">Durch Abbrechen von asynchronen Methoden kann ein Aufruf schnell auf dem Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-155">Canceling async methods allows the call on the server to complete quickly.</span></span>
* <span data-ttu-id="51a0f-156">Weitergabe des Abbruchtokens an untergeordnete Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="51a0f-156">Propagate the cancellation token to child calls.</span></span> <span data-ttu-id="51a0f-157">Mit der Weitergabe des Abbruchtokens wird sichergestellt, dass untergeordnete Aufrufe mit ihren übergeordneten Aufrufen abgebrochen werden.</span><span class="sxs-lookup"><span data-stu-id="51a0f-157">Propagating the cancellation token ensures that child calls are canceled with their parent.</span></span> <span data-ttu-id="51a0f-158">[gRPC-Clientfactory](xref:grpc/clientfactory) und `EnableCallContextPropagation()` geben das Abbruchtoken automatisch weiter.</span><span class="sxs-lookup"><span data-stu-id="51a0f-158">[gRPC client factory](xref:grpc/clientfactory) and `EnableCallContextPropagation()` automatically propagates the cancellation token.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51a0f-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="51a0f-159">Additional resources</span></span>

* <xref:grpc/client>
* <xref:grpc/clientfactory>
