---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
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
uid: signalr/javascript-client
ms.openlocfilehash: 6fc586d144547585ef75d653bf54193def5c8b7f
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606684"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="d85d0-103">ASP.net Core SignalR JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="d85d0-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d85d0-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="d85d0-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="d85d0-105">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="d85d0-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d85d0-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="d85d0-107">Installieren des SignalR Client Pakets</span><span class="sxs-lookup"><span data-stu-id="d85d0-107">Install the SignalR client package</span></span>

<span data-ttu-id="d85d0-108">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="d85d0-109">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="d85d0-110">Installieren mit NPM</span><span class="sxs-lookup"><span data-stu-id="d85d0-110">Install with npm</span></span>

<span data-ttu-id="d85d0-111">Führen Sie in Visual Studio die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus.</span><span class="sxs-lookup"><span data-stu-id="d85d0-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="d85d0-112">Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="d85d0-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="d85d0-113">NPM installiert den Paket Inhalt im Ordner *" \\ @microsoft\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="d85d0-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d85d0-114">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d85d0-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d85d0-115">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="d85d0-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="d85d0-116">Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="d85d0-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="d85d0-117">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d85d0-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="d85d0-118">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="d85d0-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="d85d0-119">Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="d85d0-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="d85d0-120">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d85d0-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="d85d0-121">Die Client Bibliothek ist unter den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d85d0-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="d85d0-122">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="d85d0-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="d85d0-123">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="d85d0-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="d85d0-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="d85d0-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="d85d0-125">Installieren mit Libman</span><span class="sxs-lookup"><span data-stu-id="d85d0-125">Install with LibMan</span></span>

<span data-ttu-id="d85d0-126">[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="d85d0-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="d85d0-127">Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="d85d0-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="d85d0-128">Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="d85d0-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="d85d0-129">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="d85d0-129">Connect to a hub</span></span>

<span data-ttu-id="d85d0-130">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="d85d0-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="d85d0-131">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d85d0-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="d85d0-132">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="d85d0-132">Cross-origin connections</span></span>

<span data-ttu-id="d85d0-133">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="d85d0-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="d85d0-134">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="d85d0-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="d85d0-135">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="d85d0-136">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="d85d0-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="d85d0-137">Callhub-Methoden vom Client</span><span class="sxs-lookup"><span data-stu-id="d85d0-137">Call hub methods from the client</span></span>

<span data-ttu-id="d85d0-138">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) von [hubconnection](/javascript/api/%40microsoft/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="d85d0-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="d85d0-139">Die- `invoke` Methode akzeptiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d85d0-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="d85d0-140">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="d85d0-140">The name of the hub method.</span></span>
* <span data-ttu-id="d85d0-141">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d85d0-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="d85d0-142">Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="d85d0-143">Das zweite und dritte Argument, das an `invoke` das-Argument und das-Argument der Hub-Methode übermittelt wird `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="d85d0-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="d85d0-144">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="d85d0-145">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="d85d0-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="d85d0-146">Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="d85d0-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="d85d0-147">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="d85d0-148">Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d85d0-149">Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="d85d0-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="d85d0-150">JavaScript-Clients können auch öffentliche Methoden auf Hubs über die [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) -Methode von aufzurufen `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="d85d0-151">Anders als bei der- `invoke` Methode wartet die- `send` Methode nicht auf eine Antwort vom Server.</span><span class="sxs-lookup"><span data-stu-id="d85d0-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="d85d0-152">Die- `send` Methode gibt ein JavaScript zurück `Promise` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="d85d0-153">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="d85d0-154">Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d85d0-155">Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="d85d0-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="d85d0-156">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="d85d0-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="d85d0-157">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d85d0-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="d85d0-158">Client Methoden aus dem Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="d85d0-158">Call client methods from the hub</span></span>

<span data-ttu-id="d85d0-159">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) -Methode von eine Methode `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="d85d0-160">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="d85d0-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="d85d0-161">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="d85d0-162">Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="d85d0-163">Die Argument Namen lauten `user` und `message` :</span><span class="sxs-lookup"><span data-stu-id="d85d0-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="d85d0-164">Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft:</span><span class="sxs-lookup"><span data-stu-id="d85d0-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="d85d0-165">SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="d85d0-166">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="d85d0-167">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="d85d0-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="d85d0-168">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d85d0-168">Error handling and logging</span></span>

<span data-ttu-id="d85d0-169">Verwenden `try` `catch` Sie und mit `async` und `await` oder der `Promise` -Methode von `catch` , um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="d85d0-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="d85d0-170">Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben:</span><span class="sxs-lookup"><span data-stu-id="d85d0-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="d85d0-171">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="d85d0-172">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="d85d0-173">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d85d0-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="d85d0-174">`signalR.LogLevel.Error`: Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="d85d0-175">Protokolliert `Error` nur Meldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="d85d0-176">`signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="d85d0-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="d85d0-177">Protokolle `Warning` und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d85d0-178">`signalR.LogLevel.Information`: Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="d85d0-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="d85d0-179">Protokolliert `Information` die `Warning` Meldungen, und `Error` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d85d0-180">`signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="d85d0-181">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="d85d0-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="d85d0-182">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d85d0-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="d85d0-183">Nachrichten werden in der Browser Konsole protokolliert:</span><span class="sxs-lookup"><span data-stu-id="d85d0-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="d85d0-184">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="d85d0-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="d85d0-185">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="d85d0-185">Automatically reconnect</span></span>

<span data-ttu-id="d85d0-186">Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mithilfe der- `withAutomaticReconnect` Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="d85d0-187">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="d85d0-188">Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="d85d0-189">Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand und löst seine Rückrufe `onreconnecting` aus, anstatt in den `Disconnected` -Zustand zu wechseln und seine Rückrufe wie eine auszulösen, `onclose` ohne dass die `HubConnection` Automatische Verbindungs Wiederherstellung konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="d85d0-190">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="d85d0-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d85d0-191">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst seine `onreconnected` Rückrufe aus.</span><span class="sxs-lookup"><span data-stu-id="d85d0-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="d85d0-192">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="d85d0-193">Da die Verbindung mit dem Server vollständig neu ist, `connectionId` wird dem Rückruf ein neuer bereitgestellt `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="d85d0-194">Der `onreconnected` -Parameter des Rückrufs ist nicht `connectionId` definiert `HubConnection` , wenn der zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d85d0-195">`withAutomaticReconnect()` konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:</span><span class="sxs-lookup"><span data-stu-id="d85d0-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

<span data-ttu-id="d85d0-196">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="d85d0-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="d85d0-197">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="d85d0-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="d85d0-198">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `withAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d85d0-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="d85d0-199">Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="d85d0-200">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d85d0-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="d85d0-201">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d85d0-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d85d0-202">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d85d0-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="d85d0-203">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d85d0-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d85d0-204">Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `withAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="d85d0-205">`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ an `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="d85d0-206">`RetryContext`Verfügt über drei Eigenschaften: `previousRetryCount` , `elapsedMilliseconds` und `retryReason` die sind `number` , und `number` `Error` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="d85d0-207">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` NULL, und der Fehler, der bewirkt hat, `retryReason` dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="d85d0-208">Nach jedem fehlgeschlagenen Wiederholungsversuch `previousRetryCount` wird der Wert um eins erhöht `elapsedMilliseconds` . der Wert wird aktualisiert, um die Zeitspanne in Millisekunden wiederherzustellen, die bis zum letzten Verbindungsversuch aufgewendet wurde. Dies ist der Fehler, der `retryReason` zum Fehlschlagen des letzten Wiederholungs Versuchs geführt hat.</span><span class="sxs-lookup"><span data-stu-id="d85d0-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="d85d0-209">`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder, `null` Wenn die `HubConnection` Verbindung nicht mehr herstellen soll.</span><span class="sxs-lookup"><span data-stu-id="d85d0-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

<span data-ttu-id="d85d0-210">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="d85d0-211">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="d85d0-211">Manually reconnect</span></span>

<span data-ttu-id="d85d0-212">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="d85d0-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="d85d0-213">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="d85d0-214">Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="d85d0-215">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="troubleshoot-websocket-handshake-errors"></a><span data-ttu-id="d85d0-216">Problembehandlung bei WebSocket-Hand Shake Fehlern</span><span class="sxs-lookup"><span data-stu-id="d85d0-216">Troubleshoot WebSocket handshake errors</span></span>

<span data-ttu-id="d85d0-217">Dieser Abschnitt enthält Hilfe zur Ausnahme *"Fehler während des WebSocket-Handshakes"* , die beim Herstellen einer Verbindung mit ASP.net Core SignalR Hub auftritt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-217">This section provides help on the *"Error during WebSocket handshake"* exception that occurs when trying to establish connection to ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="d85d0-218">Antwort Code 400 oder 503</span><span class="sxs-lookup"><span data-stu-id="d85d0-218">Response code 400 or 503</span></span>

<span data-ttu-id="d85d0-219">Für den folgenden Fehler:</span><span class="sxs-lookup"><span data-stu-id="d85d0-219">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="d85d0-220">Der Fehler wird normalerweise dadurch verursacht, dass der Client websockets-Transport verwendet, das websockets-Protokoll auf dem Server jedoch nicht aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="d85d0-220">The error is usually caused by client only use WebSockets transport but WebSockets protocol is not enabled on server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="d85d0-221">Antwort Code 307</span><span class="sxs-lookup"><span data-stu-id="d85d0-221">Response code 307</span></span>

```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="d85d0-222">Dies tritt häufig auf, wenn der SignalR Hub-Server:</span><span class="sxs-lookup"><span data-stu-id="d85d0-222">Frequently this occurs when the SignalR hub server:</span></span>

* <span data-ttu-id="d85d0-223">Überwacht und antwortet sowohl über HTTP als auch über HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d85d0-223">Listens to and responds over both HTTP and HTTPS.</span></span>
* <span data-ttu-id="d85d0-224">Ist zum Erzwingen von HTTPS durch Aufrufen von `UseHttpsRedirection` in konfiguriert `Startup` oder erzwingt HTTPS per URL-Rewrite-Regel.</span><span class="sxs-lookup"><span data-stu-id="d85d0-224">Is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="d85d0-225">Dieser Fehler kann dadurch verursacht werden, dass die http-URL auf Clientseite mithilfe von angegeben wird `.withUrl("http://xxx/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-225">This error can be caused by specifying the HTTP URL on client side using `.withUrl("http://xxx/HubName")`.</span></span> <span data-ttu-id="d85d0-226">Die Behebung für diesen Fall besteht darin, den Code so zu ändern, dass eine HTTPS-URL verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-226">The fix for this case is modifying the code to use an HTTPS URL.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="d85d0-227">Antwort Code 404</span><span class="sxs-lookup"><span data-stu-id="d85d0-227">Response code 404</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

<span data-ttu-id="d85d0-228">Wenn die APP auf "localhost" funktioniert, aber nach der Veröffentlichung auf dem IIS-Server diesen Fehler zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="d85d0-228">If the app works on localhost, but returns this error after publishing to IIS server:</span></span>

* <span data-ttu-id="d85d0-229">Vergewissern Sie sich, dass die ASP.net Core- SignalR App als IIS-unter Anwendung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-229">Verify the ASP.NET Core SignalR app is hosted as an IIS sub-application.</span></span>
* <span data-ttu-id="d85d0-230">Legen Sie die URL nicht mit der pathbase der untergeordneten App auf der SignalR Clientseite von JavaScript fest `.withUrl("/SubAppName/HubName")` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-230">Don't set URL with the sub-app's pathbase on SignalR JavaScript client side `.withUrl("/SubAppName/HubName")`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d85d0-231">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d85d0-231">Additional resources</span></span>

* [<span data-ttu-id="d85d0-232">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="d85d0-232">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="d85d0-233">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="d85d0-233">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d85d0-234">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="d85d0-234">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="d85d0-235">Hubs</span><span class="sxs-lookup"><span data-stu-id="d85d0-235">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d85d0-236">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="d85d0-236">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d85d0-237">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="d85d0-237">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d85d0-238">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="d85d0-238">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="d85d0-239">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="d85d0-239">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d85d0-240">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="d85d0-240">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="d85d0-241">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-241">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="d85d0-242">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d85d0-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="d85d0-243">Installieren des SignalR Client Pakets</span><span class="sxs-lookup"><span data-stu-id="d85d0-243">Install the SignalR client package</span></span>

<span data-ttu-id="d85d0-244">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-244">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="d85d0-245">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-245">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="d85d0-246">Installieren mit NPM</span><span class="sxs-lookup"><span data-stu-id="d85d0-246">Install with npm</span></span>

<span data-ttu-id="d85d0-247">Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus.</span><span class="sxs-lookup"><span data-stu-id="d85d0-247">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="d85d0-248">Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="d85d0-248">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="d85d0-249">NPM installiert den Paket Inhalt im Ordner *" \\ @aspnet\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="d85d0-249">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="d85d0-250">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="d85d0-250">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="d85d0-251">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="d85d0-251">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="d85d0-252">Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="d85d0-252">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="d85d0-253">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d85d0-253">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="d85d0-254">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="d85d0-254">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="d85d0-255">Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="d85d0-255">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="d85d0-256">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d85d0-256">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="d85d0-257">Die Client Bibliothek ist unter den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d85d0-257">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="d85d0-258">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="d85d0-258">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="d85d0-259">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="d85d0-259">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="d85d0-260">unpkg</span><span class="sxs-lookup"><span data-stu-id="d85d0-260">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="d85d0-261">Installieren mit Libman</span><span class="sxs-lookup"><span data-stu-id="d85d0-261">Install with LibMan</span></span>

<span data-ttu-id="d85d0-262">[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="d85d0-262">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="d85d0-263">Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="d85d0-263">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="d85d0-264">Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="d85d0-264">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="d85d0-265">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="d85d0-265">Connect to a hub</span></span>

<span data-ttu-id="d85d0-266">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="d85d0-266">The following code creates and starts a connection.</span></span> <span data-ttu-id="d85d0-267">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d85d0-267">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="d85d0-268">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="d85d0-268">Cross-origin connections</span></span>

<span data-ttu-id="d85d0-269">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="d85d0-269">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="d85d0-270">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="d85d0-270">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="d85d0-271">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-271">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="d85d0-272">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d85d0-272">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d85d0-273">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="d85d0-273">Call hub methods from client</span></span>

<span data-ttu-id="d85d0-274">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="d85d0-274">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="d85d0-275">Die- `invoke` Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="d85d0-275">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="d85d0-276">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="d85d0-276">The name of the hub method.</span></span> <span data-ttu-id="d85d0-277">Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-277">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="d85d0-278">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="d85d0-278">Any arguments defined in the hub method.</span></span> <span data-ttu-id="d85d0-279">Im folgenden Beispiel lautet der Argument Name `message` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-279">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="d85d0-280">Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-280">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="d85d0-281">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-281">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="d85d0-282">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="d85d0-282">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="d85d0-283">Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="d85d0-283">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="d85d0-284">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-284">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="d85d0-285">Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-285">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d85d0-286">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-286">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="d85d0-287">Die- `send` Methode gibt ein JavaScript zurück `Promise` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-287">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="d85d0-288">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d85d0-288">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="d85d0-289">Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-289">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="d85d0-290">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-290">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="d85d0-291">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="d85d0-291">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="d85d0-292">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="d85d0-292">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d85d0-293">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="d85d0-293">Call client methods from hub</span></span>

<span data-ttu-id="d85d0-294">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode von eine Methode `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-294">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="d85d0-295">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="d85d0-295">The name of the JavaScript client method.</span></span> <span data-ttu-id="d85d0-296">Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-296">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="d85d0-297">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-297">Arguments the hub passes to the method.</span></span> <span data-ttu-id="d85d0-298">Im folgenden Beispiel ist der Argument Wert `message` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-298">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="d85d0-299">Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="d85d0-299">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="d85d0-300">SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-300">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="d85d0-301">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-301">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="d85d0-302">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="d85d0-302">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="d85d0-303">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d85d0-303">Error handling and logging</span></span>

<span data-ttu-id="d85d0-304">Verketten Sie eine `catch` Methode an das Ende der `start` Methode, um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="d85d0-304">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="d85d0-305">Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben.</span><span class="sxs-lookup"><span data-stu-id="d85d0-305">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="d85d0-306">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-306">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="d85d0-307">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-307">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="d85d0-308">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d85d0-308">Available log levels are as follows:</span></span>

* <span data-ttu-id="d85d0-309">`signalR.LogLevel.Error`: Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-309">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="d85d0-310">Protokolliert `Error` nur Meldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-310">Logs `Error` messages only.</span></span>
* <span data-ttu-id="d85d0-311">`signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="d85d0-311">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="d85d0-312">Protokolle `Warning` und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-312">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d85d0-313">`signalR.LogLevel.Information`: Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="d85d0-313">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="d85d0-314">Protokolliert `Information` die `Warning` Meldungen, und `Error` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-314">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="d85d0-315">`signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="d85d0-315">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="d85d0-316">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="d85d0-316">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="d85d0-317">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="d85d0-317">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="d85d0-318">Nachrichten werden in der Browser Konsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="d85d0-318">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="d85d0-319">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="d85d0-319">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="d85d0-320">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="d85d0-320">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="d85d0-321">Vor 3,0 wird vom JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung hergestellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-321">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="d85d0-322">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="d85d0-322">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="d85d0-323">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="d85d0-323">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="d85d0-324">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="d85d0-324">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="d85d0-325">Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .</span><span class="sxs-lookup"><span data-stu-id="d85d0-325">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="d85d0-326">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d85d0-326">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d85d0-327">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d85d0-327">Additional resources</span></span>

* [<span data-ttu-id="d85d0-328">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="d85d0-328">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="d85d0-329">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="d85d0-329">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d85d0-330">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="d85d0-330">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="d85d0-331">Hubs</span><span class="sxs-lookup"><span data-stu-id="d85d0-331">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d85d0-332">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="d85d0-332">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d85d0-333">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="d85d0-333">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d85d0-334">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="d85d0-334">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="d85d0-335">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="d85d0-335">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
