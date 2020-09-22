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
ms.openlocfilehash: 359aa2b9e6b7f826d75f10645b7f2b565ab48b7a
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847688"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a><span data-ttu-id="11ad8-103">ASP.net Core SignalR JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="11ad8-103">ASP.NET Core SignalR JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11ad8-104">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="11ad8-104">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="11ad8-105">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-105">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="11ad8-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11ad8-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="11ad8-107">Installieren des SignalR Client Pakets</span><span class="sxs-lookup"><span data-stu-id="11ad8-107">Install the SignalR client package</span></span>

<span data-ttu-id="11ad8-108">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-108">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="11ad8-109">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-109">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="11ad8-110">Installieren mit NPM</span><span class="sxs-lookup"><span data-stu-id="11ad8-110">Install with npm</span></span>

<span data-ttu-id="11ad8-111">Führen Sie in Visual Studio die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus.</span><span class="sxs-lookup"><span data-stu-id="11ad8-111">For Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="11ad8-112">Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="11ad8-112">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @microsoft/signalr
```

<span data-ttu-id="11ad8-113">NPM installiert den Paket Inhalt im Ordner *" \\ @microsoft\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="11ad8-113">npm installs the package contents in the *node_modules\\@microsoft\signalr\dist\browser* folder.</span></span> <span data-ttu-id="11ad8-114">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="11ad8-114">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="11ad8-115">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="11ad8-115">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="11ad8-116">Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="11ad8-116">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="11ad8-117">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11ad8-117">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="11ad8-118">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="11ad8-118">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="11ad8-119">Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="11ad8-119">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="11ad8-120">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11ad8-120">For example:</span></span>

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

<span data-ttu-id="11ad8-121">Die Client Bibliothek ist unter den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="11ad8-121">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="11ad8-122">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="11ad8-122">cdnjs</span></span>](https://cdnjs.com/libraries/microsoft-signalr)
* [<span data-ttu-id="11ad8-123">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="11ad8-123">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [<span data-ttu-id="11ad8-124">unpkg</span><span class="sxs-lookup"><span data-stu-id="11ad8-124">unpkg</span></span>](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="11ad8-125">Installieren mit Libman</span><span class="sxs-lookup"><span data-stu-id="11ad8-125">Install with LibMan</span></span>

<span data-ttu-id="11ad8-126">[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="11ad8-126">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="11ad8-127">Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="11ad8-127">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="11ad8-128">Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="11ad8-128">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="11ad8-129">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="11ad8-129">Connect to a hub</span></span>

<span data-ttu-id="11ad8-130">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="11ad8-130">The following code creates and starts a connection.</span></span> <span data-ttu-id="11ad8-131">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="11ad8-131">The hub's name is case insensitive:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a><span data-ttu-id="11ad8-132">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="11ad8-132">Cross-origin connections</span></span>

<span data-ttu-id="11ad8-133">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="11ad8-133">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="11ad8-134">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="11ad8-134">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="11ad8-135">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-135">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="11ad8-136">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="11ad8-136">To allow a cross-origin request, enable it in the `Startup` class:</span></span>

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a><span data-ttu-id="11ad8-137">Callhub-Methoden vom Client</span><span class="sxs-lookup"><span data-stu-id="11ad8-137">Call hub methods from the client</span></span>

<span data-ttu-id="11ad8-138">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) von [hubconnection](/javascript/api/%40microsoft/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="11ad8-138">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) method of the [HubConnection](/javascript/api/%40microsoft/signalr/hubconnection).</span></span> <span data-ttu-id="11ad8-139">Die- `invoke` Methode akzeptiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="11ad8-139">The `invoke` method accepts:</span></span>

* <span data-ttu-id="11ad8-140">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="11ad8-140">The name of the hub method.</span></span>
* <span data-ttu-id="11ad8-141">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="11ad8-141">Any arguments defined in the hub method.</span></span>

<span data-ttu-id="11ad8-142">Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-142">In the following example, the method name on the hub is `SendMessage`.</span></span> <span data-ttu-id="11ad8-143">Das zweite und dritte Argument, das an `invoke` das-Argument und das-Argument der Hub-Methode übermittelt wird `user` `message` :</span><span class="sxs-lookup"><span data-stu-id="11ad8-143">The second and third arguments passed to `invoke` map to the hub method's `user` and `message` arguments:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> <span data-ttu-id="11ad8-144">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-144">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="11ad8-145">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="11ad8-145">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="11ad8-146">Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="11ad8-146">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="11ad8-147">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-147">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="11ad8-148">Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-148">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="11ad8-149">Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="11ad8-149">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

<span data-ttu-id="11ad8-150">JavaScript-Clients können auch öffentliche Methoden auf Hubs über die [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) -Methode von aufzurufen `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-150">JavaScript clients can also call public methods on hubs via the the [send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) method of the `HubConnection`.</span></span> <span data-ttu-id="11ad8-151">Anders als bei der- `invoke` Methode wartet die- `send` Methode nicht auf eine Antwort vom Server.</span><span class="sxs-lookup"><span data-stu-id="11ad8-151">Unlike the `invoke` method, the `send` method doesn't wait for a response from the server.</span></span> <span data-ttu-id="11ad8-152">Die- `send` Methode gibt ein JavaScript zurück `Promise` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-152">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="11ad8-153">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-153">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="11ad8-154">Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-154">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="11ad8-155">Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="11ad8-155">Use `async` and `await` or the `Promise`'s `then` and `catch` methods to handle these cases.</span></span>

> [!NOTE]
> <span data-ttu-id="11ad8-156">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="11ad8-156">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="11ad8-157">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="11ad8-157">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-the-hub"></a><span data-ttu-id="11ad8-158">Client Methoden aus dem Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="11ad8-158">Call client methods from the hub</span></span>

<span data-ttu-id="11ad8-159">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) -Methode von eine Methode `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-159">To receive messages from the hub, define a method using the [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) method of the `HubConnection`.</span></span>

* <span data-ttu-id="11ad8-160">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="11ad8-160">The name of the JavaScript client method.</span></span>
* <span data-ttu-id="11ad8-161">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-161">Arguments the hub passes to the method.</span></span>

<span data-ttu-id="11ad8-162">Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-162">In the following example, the method name is `ReceiveMessage`.</span></span> <span data-ttu-id="11ad8-163">Die Argument Namen lauten `user` und `message` :</span><span class="sxs-lookup"><span data-stu-id="11ad8-163">The argument names are `user` and `message`:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

<span data-ttu-id="11ad8-164">Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft:</span><span class="sxs-lookup"><span data-stu-id="11ad8-164">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method:</span></span>

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

<span data-ttu-id="11ad8-165">SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-165">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="11ad8-166">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-166">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="11ad8-167">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="11ad8-167">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="11ad8-168">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="11ad8-168">Error handling and logging</span></span>

<span data-ttu-id="11ad8-169">Verwenden `try` `catch` Sie und mit `async` und `await` oder der `Promise` -Methode von `catch` , um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="11ad8-169">Use `try` and `catch` with `async` and `await` or the `Promise`'s `catch` method to handle client-side errors.</span></span> <span data-ttu-id="11ad8-170">Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben:</span><span class="sxs-lookup"><span data-stu-id="11ad8-170">Use `console.error` to output errors to the browser's console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

<span data-ttu-id="11ad8-171">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-171">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="11ad8-172">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-172">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="11ad8-173">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="11ad8-173">Available log levels are as follows:</span></span>

* <span data-ttu-id="11ad8-174">`signalR.LogLevel.Error`: Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-174">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="11ad8-175">Protokolliert `Error` nur Meldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-175">Logs `Error` messages only.</span></span>
* <span data-ttu-id="11ad8-176">`signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="11ad8-176">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="11ad8-177">Protokolle `Warning` und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-177">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="11ad8-178">`signalR.LogLevel.Information`: Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="11ad8-178">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="11ad8-179">Protokolliert `Information` die `Warning` Meldungen, und `Error` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-179">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="11ad8-180">`signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-180">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="11ad8-181">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="11ad8-181">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="11ad8-182">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="11ad8-182">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="11ad8-183">Nachrichten werden in der Browser Konsole protokolliert:</span><span class="sxs-lookup"><span data-stu-id="11ad8-183">Messages are logged to the browser console:</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a><span data-ttu-id="11ad8-184">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="11ad8-184">Reconnect clients</span></span>

### <a name="automatically-reconnect"></a><span data-ttu-id="11ad8-185">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="11ad8-185">Automatically reconnect</span></span>

<span data-ttu-id="11ad8-186">Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mithilfe der- `withAutomaticReconnect` Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-186">The JavaScript client for SignalR can be configured to automatically reconnect using the `withAutomaticReconnect` method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder).</span></span> <span data-ttu-id="11ad8-187">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-187">It won't automatically reconnect by default.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="11ad8-188">Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-188">Without any parameters, `withAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="11ad8-189">Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand und löst seine Rückrufe `onreconnecting` aus, anstatt in den `Disconnected` -Zustand zu wechseln und seine Rückrufe wie eine auszulösen, `onclose` ohne dass die `HubConnection` Automatische Verbindungs Wiederherstellung konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-189">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire its `onreconnecting` callbacks instead of transitioning to the `Disconnected` state and triggering its `onclose` callbacks like a `HubConnection` without automatic reconnect configured.</span></span> <span data-ttu-id="11ad8-190">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="11ad8-190">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span>

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="11ad8-191">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst seine `onreconnected` Rückrufe aus.</span><span class="sxs-lookup"><span data-stu-id="11ad8-191">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire its `onreconnected` callbacks.</span></span> <span data-ttu-id="11ad8-192">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-192">This provides an opportunity to inform users the connection has been reestablished.</span></span>

<span data-ttu-id="11ad8-193">Da die Verbindung mit dem Server vollständig neu ist, `connectionId` wird dem Rückruf ein neuer bereitgestellt `onreconnected` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-193">Since the connection looks entirely new to the server, a new `connectionId` will be provided to the `onreconnected` callback.</span></span>

> [!WARNING]
> <span data-ttu-id="11ad8-194">Der `onreconnected` -Parameter des Rückrufs ist nicht `connectionId` definiert `HubConnection` , wenn der zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-194">The `onreconnected` callback's `connectionId` parameter will be undefined if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="11ad8-195">`withAutomaticReconnect()` konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:</span><span class="sxs-lookup"><span data-stu-id="11ad8-195">`withAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

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

<span data-ttu-id="11ad8-196">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe.</span><span class="sxs-lookup"><span data-stu-id="11ad8-196">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire its [onclose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) callbacks.</span></span> <span data-ttu-id="11ad8-197">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="11ad8-197">This provides an opportunity to inform users the connection has been permanently lost and recommend refreshing the page:</span></span>

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

<span data-ttu-id="11ad8-198">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `withAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="11ad8-198">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `withAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

<span data-ttu-id="11ad8-199">Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-199">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="11ad8-200">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="11ad8-200">This is also true for the default configuration.</span></span>

<span data-ttu-id="11ad8-201">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="11ad8-201">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="11ad8-202">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="11ad8-202">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="11ad8-203">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="11ad8-203">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure instead of trying one more reconnect attempt in another 30 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="11ad8-204">Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `withAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `nextRetryDelayInMilliseconds` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-204">If you want even more control over the timing and number of automatic reconnect attempts, `withAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `nextRetryDelayInMilliseconds`.</span></span>

<span data-ttu-id="11ad8-205">`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ an `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-205">`nextRetryDelayInMilliseconds` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="11ad8-206">`RetryContext`Verfügt über drei Eigenschaften: `previousRetryCount` , `elapsedMilliseconds` und `retryReason` die sind `number` , und `number` `Error` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-206">The `RetryContext` has three properties: `previousRetryCount`, `elapsedMilliseconds` and `retryReason` which are a `number`, a `number` and an `Error` respectively.</span></span> <span data-ttu-id="11ad8-207">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` NULL, und der Fehler, der bewirkt hat, `retryReason` dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-207">Before the first reconnect attempt, both `previousRetryCount` and `elapsedMilliseconds` will be zero, and the `retryReason` will be the Error that caused the connection to be lost.</span></span> <span data-ttu-id="11ad8-208">Nach jedem fehlgeschlagenen Wiederholungsversuch `previousRetryCount` wird der Wert um eins erhöht `elapsedMilliseconds` . der Wert wird aktualisiert, um die Zeitspanne in Millisekunden wiederherzustellen, die bis zum letzten Verbindungsversuch aufgewendet wurde. Dies ist der Fehler, der `retryReason` zum Fehlschlagen des letzten Wiederholungs Versuchs geführt hat.</span><span class="sxs-lookup"><span data-stu-id="11ad8-208">After each failed retry attempt, `previousRetryCount` will be incremented by one, `elapsedMilliseconds` will be updated to reflect the amount of time spent reconnecting so far in milliseconds, and the `retryReason` will be the Error that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="11ad8-209">`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder, `null` Wenn die `HubConnection` Verbindung nicht mehr herstellen soll.</span><span class="sxs-lookup"><span data-stu-id="11ad8-209">`nextRetryDelayInMilliseconds` must return either a number representing the number of milliseconds to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

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

<span data-ttu-id="11ad8-210">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-210">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="11ad8-211">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="11ad8-211">Manually reconnect</span></span>

<span data-ttu-id="11ad8-212">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="11ad8-212">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="11ad8-213">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-213">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="11ad8-214">Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-214">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

<span data-ttu-id="11ad8-215">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-215">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11ad8-216">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="11ad8-216">Additional resources</span></span>

* [<span data-ttu-id="11ad8-217">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="11ad8-217">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [<span data-ttu-id="11ad8-218">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="11ad8-218">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="11ad8-219">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="11ad8-219">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="11ad8-220">Hubs</span><span class="sxs-lookup"><span data-stu-id="11ad8-220">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="11ad8-221">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="11ad8-221">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="11ad8-222">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="11ad8-222">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="11ad8-223">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="11ad8-223">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="11ad8-224">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="11ad8-224">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="11ad8-225">Von [Rachel Appel](https://twitter.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="11ad8-225">By [Rachel Appel](https://twitter.com/rachelappel)</span></span>

<span data-ttu-id="11ad8-226">Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-226">The ASP.NET Core SignalR JavaScript client library enables developers to call server-side hub code.</span></span>

<span data-ttu-id="11ad8-227">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="11ad8-227">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-client-package"></a><span data-ttu-id="11ad8-228">Installieren des SignalR Client Pakets</span><span class="sxs-lookup"><span data-stu-id="11ad8-228">Install the SignalR client package</span></span>

<span data-ttu-id="11ad8-229">Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-229">The SignalR JavaScript client library is delivered as an [npm](https://www.npmjs.com/) package.</span></span> <span data-ttu-id="11ad8-230">In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-230">The following sections outline different ways to install the client library.</span></span>

### <a name="install-with-npm"></a><span data-ttu-id="11ad8-231">Installieren mit NPM</span><span class="sxs-lookup"><span data-stu-id="11ad8-231">Install with npm</span></span>

<span data-ttu-id="11ad8-232">Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus.</span><span class="sxs-lookup"><span data-stu-id="11ad8-232">If using Visual Studio, run the following commands from **Package Manager Console** while in the root folder.</span></span> <span data-ttu-id="11ad8-233">Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal**aus.</span><span class="sxs-lookup"><span data-stu-id="11ad8-233">For Visual Studio Code, run the following commands from the **Integrated Terminal**.</span></span>

```bash
npm init -y
npm install @aspnet/signalr
```

<span data-ttu-id="11ad8-234">NPM installiert den Paket Inhalt im Ordner *" \\ @aspnet\signalr\dist\browser node_modules* ".</span><span class="sxs-lookup"><span data-stu-id="11ad8-234">npm installs the package contents in the *node_modules\\@aspnet\signalr\dist\browser* folder.</span></span> <span data-ttu-id="11ad8-235">Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* .</span><span class="sxs-lookup"><span data-stu-id="11ad8-235">Create a new folder named *signalr* under the *wwwroot\\lib* folder.</span></span> <span data-ttu-id="11ad8-236">Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".</span><span class="sxs-lookup"><span data-stu-id="11ad8-236">Copy the *signalr.js* file to the *wwwroot\lib\signalr* folder.</span></span>

<span data-ttu-id="11ad8-237">Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element.</span><span class="sxs-lookup"><span data-stu-id="11ad8-237">Reference the SignalR JavaScript client in the `<script>` element.</span></span> <span data-ttu-id="11ad8-238">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11ad8-238">For example:</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a><span data-ttu-id="11ad8-239">Verwenden eines Content Delivery Network (CDN)</span><span class="sxs-lookup"><span data-stu-id="11ad8-239">Use a Content Delivery Network (CDN)</span></span>

<span data-ttu-id="11ad8-240">Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="11ad8-240">To use the client library without the npm prerequisite, reference a CDN-hosted copy of the client library.</span></span> <span data-ttu-id="11ad8-241">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="11ad8-241">For example:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

<span data-ttu-id="11ad8-242">Die Client Bibliothek ist unter den folgenden CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="11ad8-242">The client library is available on the following CDNs:</span></span>

* [<span data-ttu-id="11ad8-243">cdnjs verfügbar</span><span class="sxs-lookup"><span data-stu-id="11ad8-243">cdnjs</span></span>](https://cdnjs.com/libraries/aspnet-signalr)
* [<span data-ttu-id="11ad8-244">jsdelivr</span><span class="sxs-lookup"><span data-stu-id="11ad8-244">jsDelivr</span></span>](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [<span data-ttu-id="11ad8-245">unpkg</span><span class="sxs-lookup"><span data-stu-id="11ad8-245">unpkg</span></span>](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a><span data-ttu-id="11ad8-246">Installieren mit Libman</span><span class="sxs-lookup"><span data-stu-id="11ad8-246">Install with LibMan</span></span>

<span data-ttu-id="11ad8-247">[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren.</span><span class="sxs-lookup"><span data-stu-id="11ad8-247">[LibMan](xref:client-side/libman/index) can be used to install specific client library files from the CDN-hosted client library.</span></span> <span data-ttu-id="11ad8-248">Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="11ad8-248">For example, only add the minified JavaScript file to the project.</span></span> <span data-ttu-id="11ad8-249">Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).</span><span class="sxs-lookup"><span data-stu-id="11ad8-249">For details on that approach, see [Add the SignalR client library](xref:tutorials/signalr#add-the-signalr-client-library).</span></span>

## <a name="connect-to-a-hub"></a><span data-ttu-id="11ad8-250">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="11ad8-250">Connect to a hub</span></span>

<span data-ttu-id="11ad8-251">Mit dem folgenden Code wird eine Verbindung erstellt und gestartet.</span><span class="sxs-lookup"><span data-stu-id="11ad8-251">The following code creates and starts a connection.</span></span> <span data-ttu-id="11ad8-252">Der Name des Hubs wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="11ad8-252">The hub's name is case insensitive.</span></span>

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a><span data-ttu-id="11ad8-253">Ursprungs übergreifende Verbindungen</span><span class="sxs-lookup"><span data-stu-id="11ad8-253">Cross-origin connections</span></span>

<span data-ttu-id="11ad8-254">Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite.</span><span class="sxs-lookup"><span data-stu-id="11ad8-254">Typically, browsers load connections from the same domain as the requested page.</span></span> <span data-ttu-id="11ad8-255">Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="11ad8-255">However, there are occasions when a connection to another domain is required.</span></span>

<span data-ttu-id="11ad8-256">Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-256">To prevent a malicious site from reading sensitive data from another site, [cross-origin connections](xref:security/cors) are disabled by default.</span></span> <span data-ttu-id="11ad8-257">Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="11ad8-257">To allow a cross-origin request, enable it in the `Startup` class.</span></span>

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="11ad8-258">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="11ad8-258">Call hub methods from client</span></span>

<span data-ttu-id="11ad8-259">JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf.</span><span class="sxs-lookup"><span data-stu-id="11ad8-259">JavaScript clients call public methods on hubs via the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) method of the [HubConnection](/javascript/api/%40aspnet/signalr/hubconnection).</span></span> <span data-ttu-id="11ad8-260">Die- `invoke` Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="11ad8-260">The `invoke` method accepts two arguments:</span></span>

* <span data-ttu-id="11ad8-261">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="11ad8-261">The name of the hub method.</span></span> <span data-ttu-id="11ad8-262">Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-262">In the following example, the method name on the hub is `SendMessage`.</span></span>
* <span data-ttu-id="11ad8-263">Alle Argumente, die in der Hub-Methode definiert sind.</span><span class="sxs-lookup"><span data-stu-id="11ad8-263">Any arguments defined in the hub method.</span></span> <span data-ttu-id="11ad8-264">Im folgenden Beispiel lautet der Argument Name `message` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-264">In the following example, the argument name is `message`.</span></span> <span data-ttu-id="11ad8-265">Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-265">The example code uses arrow function syntax that is supported in current versions of all major browsers except Internet Explorer.</span></span>

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> <span data-ttu-id="11ad8-266">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-266">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="11ad8-267">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="11ad8-267">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

<span data-ttu-id="11ad8-268">Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück.</span><span class="sxs-lookup"><span data-stu-id="11ad8-268">The `invoke` method returns a JavaScript [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise).</span></span> <span data-ttu-id="11ad8-269">Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-269">The `Promise` is resolved with the return value (if any) when the method on the server returns.</span></span> <span data-ttu-id="11ad8-270">Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-270">If the method on the server throws an error, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="11ad8-271">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-271">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

<span data-ttu-id="11ad8-272">Die- `send` Methode gibt ein JavaScript zurück `Promise` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-272">The `send` method returns a JavaScript `Promise`.</span></span> <span data-ttu-id="11ad8-273">Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="11ad8-273">The `Promise` is resolved when the message has been sent to the server.</span></span> <span data-ttu-id="11ad8-274">Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-274">If there is an error sending the message, the `Promise` is rejected with the error message.</span></span> <span data-ttu-id="11ad8-275">Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-275">Use the `then` and `catch` methods on the `Promise` itself to handle these cases (or `await` syntax).</span></span>

> [!NOTE]
> <span data-ttu-id="11ad8-276">Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="11ad8-276">Using `send` doesn't wait until the server has received the message.</span></span> <span data-ttu-id="11ad8-277">Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="11ad8-277">Consequently, it's not possible to return data or errors from the server.</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="11ad8-278">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="11ad8-278">Call client methods from hub</span></span>

<span data-ttu-id="11ad8-279">Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode von eine Methode `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-279">To receive messages from the hub, define a method using the [on](/javascript/api/%40aspnet/signalr/hubconnection#on) method of the `HubConnection`.</span></span>

* <span data-ttu-id="11ad8-280">Der Name der JavaScript-Client Methode.</span><span class="sxs-lookup"><span data-stu-id="11ad8-280">The name of the JavaScript client method.</span></span> <span data-ttu-id="11ad8-281">Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-281">In the following example, the method name is `ReceiveMessage`.</span></span>
* <span data-ttu-id="11ad8-282">Argumente, die der Hub an die Methode übergibt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-282">Arguments the hub passes to the method.</span></span> <span data-ttu-id="11ad8-283">Im folgenden Beispiel ist der Argument Wert `message` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-283">In the following example, the argument value is `message`.</span></span>

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

<span data-ttu-id="11ad8-284">Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="11ad8-284">The preceding code in `connection.on` runs when server-side code calls it using the <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> method.</span></span>

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

<span data-ttu-id="11ad8-285">SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-285">SignalR determines which client method to call by matching the method name and arguments defined in `SendAsync` and `connection.on`.</span></span>

> [!NOTE]
> <span data-ttu-id="11ad8-286">Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-286">As a best practice, call the [start](/javascript/api/%40aspnet/signalr/hubconnection#start) method on the `HubConnection` after `on`.</span></span> <span data-ttu-id="11ad8-287">Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.</span><span class="sxs-lookup"><span data-stu-id="11ad8-287">Doing so ensures your handlers are registered before any messages are received.</span></span>

## <a name="error-handling-and-logging"></a><span data-ttu-id="11ad8-288">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="11ad8-288">Error handling and logging</span></span>

<span data-ttu-id="11ad8-289">Verketten Sie eine `catch` Methode an das Ende der `start` Methode, um Client seitige Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="11ad8-289">Chain a `catch` method to the end of the `start` method to handle client-side errors.</span></span> <span data-ttu-id="11ad8-290">Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben.</span><span class="sxs-lookup"><span data-stu-id="11ad8-290">Use `console.error` to output errors to the browser's console.</span></span>

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

<span data-ttu-id="11ad8-291">Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-291">Set up client-side log tracing by passing a logger and type of event to log when the connection is made.</span></span> <span data-ttu-id="11ad8-292">Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-292">Messages are logged with the specified log level and higher.</span></span> <span data-ttu-id="11ad8-293">Folgende Protokoll Ebenen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="11ad8-293">Available log levels are as follows:</span></span>

* <span data-ttu-id="11ad8-294">`signalR.LogLevel.Error`: Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-294">`signalR.LogLevel.Error`: Error messages.</span></span> <span data-ttu-id="11ad8-295">Protokolliert `Error` nur Meldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-295">Logs `Error` messages only.</span></span>
* <span data-ttu-id="11ad8-296">`signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="11ad8-296">`signalR.LogLevel.Warning`: Warning messages about potential errors.</span></span> <span data-ttu-id="11ad8-297">Protokolle `Warning` und `Error` Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-297">Logs `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="11ad8-298">`signalR.LogLevel.Information`: Status Meldungen ohne Fehler.</span><span class="sxs-lookup"><span data-stu-id="11ad8-298">`signalR.LogLevel.Information`: Status messages without errors.</span></span> <span data-ttu-id="11ad8-299">Protokolliert `Information` die `Warning` Meldungen, und `Error` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-299">Logs `Information`, `Warning`, and `Error` messages.</span></span>
* <span data-ttu-id="11ad8-300">`signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen.</span><span class="sxs-lookup"><span data-stu-id="11ad8-300">`signalR.LogLevel.Trace`: Trace messages.</span></span> <span data-ttu-id="11ad8-301">Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.</span><span class="sxs-lookup"><span data-stu-id="11ad8-301">Logs everything, including data transported between hub and client.</span></span>

<span data-ttu-id="11ad8-302">Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="11ad8-302">Use the [configureLogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) method on [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) to configure the log level.</span></span> <span data-ttu-id="11ad8-303">Nachrichten werden in der Browser Konsole protokolliert.</span><span class="sxs-lookup"><span data-stu-id="11ad8-303">Messages are logged to the browser console.</span></span>

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a><span data-ttu-id="11ad8-304">Clients erneut verbinden</span><span class="sxs-lookup"><span data-stu-id="11ad8-304">Reconnect clients</span></span>

### <a name="manually-reconnect"></a><span data-ttu-id="11ad8-305">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="11ad8-305">Manually reconnect</span></span>

> [!WARNING]
> <span data-ttu-id="11ad8-306">Vor 3,0 wird vom JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung hergestellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-306">Prior to 3.0, the JavaScript client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="11ad8-307">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="11ad8-307">You must write code that will reconnect your client manually.</span></span>

<span data-ttu-id="11ad8-308">Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:</span><span class="sxs-lookup"><span data-stu-id="11ad8-308">The following code demonstrates a typical manual reconnection approach:</span></span>

1. <span data-ttu-id="11ad8-309">Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.</span><span class="sxs-lookup"><span data-stu-id="11ad8-309">A function (in this case, the `start` function) is created to start the connection.</span></span>
1. <span data-ttu-id="11ad8-310">Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .</span><span class="sxs-lookup"><span data-stu-id="11ad8-310">Call the `start` function in the connection's `onclose` event handler.</span></span>

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

<span data-ttu-id="11ad8-311">Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="11ad8-311">A real-world implementation would use an exponential back-off or retry a specified number of times before giving up.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11ad8-312">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="11ad8-312">Additional resources</span></span>

* [<span data-ttu-id="11ad8-313">JavaScript-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="11ad8-313">JavaScript API reference</span></span>](/javascript/api/?view=signalr-js-latest)
* [<span data-ttu-id="11ad8-314">JavaScript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="11ad8-314">JavaScript tutorial</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="11ad8-315">WebPack und typescript-Tutorial</span><span class="sxs-lookup"><span data-stu-id="11ad8-315">WebPack and TypeScript tutorial</span></span>](xref:tutorials/signalr-typescript-webpack)
* [<span data-ttu-id="11ad8-316">Hubs</span><span class="sxs-lookup"><span data-stu-id="11ad8-316">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="11ad8-317">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="11ad8-317">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="11ad8-318">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="11ad8-318">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="11ad8-319">Cross-Origin-Anforderungen (cors)</span><span class="sxs-lookup"><span data-stu-id="11ad8-319">Cross-Origin Requests (CORS)</span></span>](xref:security/cors)
* [<span data-ttu-id="11ad8-320">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="11ad8-320">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
