---
title: Neuerungen in ASP.NET Core 3.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: 235daac5c08248ca2052de6b44e66a8162ce23ad
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051238"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="e57c8-103">Neuerungen in ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="e57c8-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="e57c8-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## :::no-loc(Blazor):::

<span data-ttu-id="e57c8-105">:::no-loc(Blazor)::: ist ein Framework in ASP.NET Core zum Erstellen von interaktiven clientseitigen Webbenutzeroberflächen mit .NET:</span><span class="sxs-lookup"><span data-stu-id="e57c8-105">:::no-loc(Blazor)::: is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="e57c8-106">Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.</span><span class="sxs-lookup"><span data-stu-id="e57c8-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="e57c8-107">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="e57c8-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="e57c8-108">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="e57c8-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="e57c8-109">Vom :::no-loc(Blazor):::-Framework unterstützte Szenarien:</span><span class="sxs-lookup"><span data-stu-id="e57c8-109">:::no-loc(Blazor)::: framework supported scenarios:</span></span>

* <span data-ttu-id="e57c8-110">Wiederverwendbare Benutzeroberflächenkomponenten (:::no-loc(Razor):::-Komponenten)</span><span class="sxs-lookup"><span data-stu-id="e57c8-110">Reusable UI components (:::no-loc(Razor)::: components)</span></span>
* <span data-ttu-id="e57c8-111">Clientseitiges Routing</span><span class="sxs-lookup"><span data-stu-id="e57c8-111">Client-side routing</span></span>
* <span data-ttu-id="e57c8-112">Komponentenlayouts</span><span class="sxs-lookup"><span data-stu-id="e57c8-112">Component layouts</span></span>
* <span data-ttu-id="e57c8-113">Unterstützung für Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="e57c8-113">Support for dependency injection</span></span>
* <span data-ttu-id="e57c8-114">Formulare und Überprüfung</span><span class="sxs-lookup"><span data-stu-id="e57c8-114">Forms and validation</span></span>
* <span data-ttu-id="e57c8-115">Erstellen von Komponentenbibliotheken mit :::no-loc(Razor):::-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="e57c8-115">Build component libraries with :::no-loc(Razor)::: class libraries</span></span>
* <span data-ttu-id="e57c8-116">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="e57c8-116">JavaScript interop</span></span>

<span data-ttu-id="e57c8-117">Weitere Informationen finden Sie unter <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-117">For more information, see <xref:blazor/index>.</span></span>

### :::no-loc(Blazor Server):::

<span data-ttu-id="e57c8-118">:::no-loc(Blazor)::: entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="e57c8-118">:::no-loc(Blazor)::: decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="e57c8-119">:::no-loc(Blazor Server)::: bietet Unterstützung zum Hosten von :::no-loc(Razor):::-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="e57c8-119">:::no-loc(Blazor Server)::: provides support for hosting :::no-loc(Razor)::: components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="e57c8-120">Aktualisierungen der Benutzeroberfläche werden über eine :::no-loc(SignalR):::-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e57c8-120">UI updates are handled over a :::no-loc(SignalR)::: connection.</span></span> <span data-ttu-id="e57c8-121">:::no-loc(Blazor Server)::: wird in ASP.NET Core 3.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-121">:::no-loc(Blazor Server)::: is supported in ASP.NET Core 3.0.</span></span>

### <a name="no-locblazor-webassembly-preview"></a><span data-ttu-id="e57c8-122">:::no-loc(Blazor WebAssembly)::: (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="e57c8-122">:::no-loc(Blazor WebAssembly)::: (Preview)</span></span>

<span data-ttu-id="e57c8-123">:::no-loc(Blazor):::-Apps können auch mit einer WebAssembly-basierten .NET-Runtime direkt im Browser ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-123">:::no-loc(Blazor)::: apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> <span data-ttu-id="e57c8-124">:::no-loc(Blazor WebAssembly)::: befindet sich in der Vorschau und wird in ASP.NET Core 3.0 *nicht* unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-124">:::no-loc(Blazor WebAssembly)::: is in preview and *not* supported in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e57c8-125">:::no-loc(Blazor WebAssembly)::: wird in einem zukünftigen Release von ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-125">:::no-loc(Blazor WebAssembly)::: will be supported in a future release of ASP.NET Core.</span></span>

### <a name="no-locrazor-components"></a><span data-ttu-id="e57c8-126">:::no-loc(Razor):::-Komponenten</span><span class="sxs-lookup"><span data-stu-id="e57c8-126">:::no-loc(Razor)::: components</span></span>

<span data-ttu-id="e57c8-127">:::no-loc(Blazor):::-Apps setzen sich aus Komponenten zusammen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-127">:::no-loc(Blazor)::: apps are built from components.</span></span> <span data-ttu-id="e57c8-128">Komponenten sind eigenständige Elemente einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="e57c8-128">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="e57c8-129">Komponenten sind normale .NET-Klassen, die die Renderinglogik für die Benutzeroberfläche sowie clientseitige Ereignishandler definieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-129">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="e57c8-130">Damit können Sie umfangreiche interaktive Web-Apps ohne JavaScript erstellen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-130">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="e57c8-131">Komponenten in :::no-loc(Blazor)::: werden üblicherweise mit der :::no-loc(Razor):::-Syntax erstellt, einer natürlichen Mischung aus HTML und C#.</span><span class="sxs-lookup"><span data-stu-id="e57c8-131">Components in :::no-loc(Blazor)::: are typically authored using :::no-loc(Razor)::: syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="e57c8-132">:::no-loc(Razor):::-Komponenten ähneln :::no-loc(Razor)::: Pages und MVC-Ansichten dahingehend, dass sie ebenfalls :::no-loc(Razor)::: verwenden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-132">:::no-loc(Razor)::: components are similar to :::no-loc(Razor)::: Pages and MVC views in that they both use :::no-loc(Razor):::.</span></span> <span data-ttu-id="e57c8-133">Im Gegensatz zu Pages und Ansichten, die auf einem Anforderung/Antwort-Modell basieren, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="e57c8-133">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="e57c8-134">gRPC</span><span class="sxs-lookup"><span data-stu-id="e57c8-134">gRPC</span></span>

<span data-ttu-id="e57c8-135">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="e57c8-135">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="e57c8-136">Ein beliebtes, hochleistungsfähiges RPC-Framework (Remote Procedure Call, Remoteprozeduraufruf).</span><span class="sxs-lookup"><span data-stu-id="e57c8-136">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="e57c8-137">Bietet einen strikten Contract First-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-137">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="e57c8-138">Verwendet moderne Technologien wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="e57c8-138">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="e57c8-139">HTTP/2 für den Transport.</span><span class="sxs-lookup"><span data-stu-id="e57c8-139">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="e57c8-140">Protokollpuffer als Sprache zur Schnittstellenbeschreibung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-140">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="e57c8-141">Format der binären Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-141">Binary serialization format.</span></span>
* <span data-ttu-id="e57c8-142">Bietet beispielsweise folgende Features:</span><span class="sxs-lookup"><span data-stu-id="e57c8-142">Provides features such as:</span></span>

  * <span data-ttu-id="e57c8-143">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e57c8-143">Authentication</span></span>
  * <span data-ttu-id="e57c8-144">Bidirektionales Streaming und Flusssteuerung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-144">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="e57c8-145">Stornierung und Timeouts.</span><span class="sxs-lookup"><span data-stu-id="e57c8-145">Cancellation and timeouts.</span></span>

<span data-ttu-id="e57c8-146">Die gRPC-Funktionalität in ASP.NET Core 3.0 umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e57c8-146">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="e57c8-147">[Grpc.AspNetCore:](https://www.nuget.org/packages/Grpc.AspNetCore) Dies ist ein ASP.NET Core-Framework zum Hosten von gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="e57c8-147">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore): An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="e57c8-148">gRPC in ASP.NET Core lässt sich in ASP.NET Core-Standardfeatures wie Protokollierung, Abhängigkeitsinjektion (Dependency Injection, DI), Authentifizierung und Autorisierung integrieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-148">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="e57c8-149">[Grpc.Net.Client:](https://www.nuget.org/packages/Grpc.Net.Client) Dies ist ein gRPC-Client für .NET Core, der auf dem bekannten `HttpClient` aufbaut.</span><span class="sxs-lookup"><span data-stu-id="e57c8-149">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client): A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="e57c8-150">[Grpc.Net.ClientFactory:](https://www.nuget.org/packages/Grpc.Net.ClientFactory) Dies ist eine gRPC-Clientintegration mit `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-150">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory): gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="e57c8-151">Weitere Informationen finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-151">For more information, see <xref:grpc/index>.</span></span>

## :::no-loc(SignalR):::

<span data-ttu-id="e57c8-152">Anweisungen zur Migration finden Sie unter [Aktualisieren von :::no-loc(SignalR):::-Code](xref:migration/22-to-30#signalr).</span><span class="sxs-lookup"><span data-stu-id="e57c8-152">See [Update :::no-loc(SignalR)::: code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> <span data-ttu-id="e57c8-153">:::no-loc(SignalR)::: verwendet jetzt `System.Text.Json`, um JSON-Nachrichten zu serialisieren bzw. zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-153">:::no-loc(SignalR)::: now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="e57c8-154">Anleitungen zum Wiederherstellen des `Newtonsoft.Json`-basierten Serialisierungsmoduls finden Sie unter [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) (Wechseln zu Newtonsoft.Json).</span><span class="sxs-lookup"><span data-stu-id="e57c8-154">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="e57c8-155">In den JavaScript- und .NET-Clients für :::no-loc(SignalR)::: wurde Unterstützung für eine automatische erneute Verbindungsherstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-155">In the JavaScript and .NET Clients for :::no-loc(SignalR):::, support was added for automatic reconnection.</span></span> <span data-ttu-id="e57c8-156">Standardmäßig versucht der Client sofort, die Verbindung wiederherzustellen, und wiederholt den Versuch nötigenfalls nach 2, 10 und 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-156">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="e57c8-157">Wenn der Client die Verbindung erfolgreich wiederherstellt, erhält er eine neue Verbindungs-ID.</span><span class="sxs-lookup"><span data-stu-id="e57c8-157">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="e57c8-158">Die automatische erneute Herstellung einer Verbindung kann optional aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="e57c8-158">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="e57c8-159">Die Intervalle für die Wiederherstellung einer Verbindung können durch Übergabe eines Arrays aus millisekundenbasierten Zeitspannen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="e57c8-159">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="e57c8-160">Zur vollständigen Steuerung der Wiederherstellungsintervalle kann eine benutzerdefinierte Implementierung übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-160">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="e57c8-161">Wenn die Verbindung nicht wiederhergestellt werden kann, passiert nach dem letzten Intervall Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e57c8-161">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="e57c8-162">Der Client betrachtet die Verbindung als offline.</span><span class="sxs-lookup"><span data-stu-id="e57c8-162">The client considers the connection is offline.</span></span>
* <span data-ttu-id="e57c8-163">Der Client versucht nicht mehr, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-163">The client stops trying to reconnect.</span></span>

<span data-ttu-id="e57c8-164">Aktualisieren Sie die App-Benutzeroberfläche während der Verbindungsversuche, um den Benutzer darüber zu informieren, dass versucht wird, erneut eine Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-164">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="e57c8-165">Um bei einer unterbrochenen Verbindung Feedback auf der Benutzeroberfläche bereitstellen zu können, wurde die :::no-loc(SignalR):::-Client-API um die folgenden Ereignishandler ergänzt:</span><span class="sxs-lookup"><span data-stu-id="e57c8-165">To provide UI feedback when the connection is interrupted, the :::no-loc(SignalR)::: client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="e57c8-166">`onreconnecting`:  Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu deaktivieren oder Benutzer darüber zu informieren, dass die App offline ist.</span><span class="sxs-lookup"><span data-stu-id="e57c8-166">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="e57c8-167">`onreconnected`: Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu aktualisieren, sobald die Verbindung wiederhergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e57c8-167">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="e57c8-168">Der folgende Code verwendet `onreconnecting`, um die Benutzeroberfläche während der Verbindungsversuche zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="e57c8-168">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="e57c8-169">Der folgende Code verwendet `onreconnected`, um die Benutzeroberfläche nach erfolgreich hergestellter Verbindung zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="e57c8-169">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="e57c8-170">:::no-loc(SignalR)::: 3.0 und höher bietet eine benutzerdefinierte Ressource für Autorisierungshandler, wenn eine Hubmethode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-170">:::no-loc(SignalR)::: 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="e57c8-171">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-171">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="e57c8-172">Der `HubInvocationContext` umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e57c8-172">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="e57c8-173">Den Namen der aufgerufenen Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="e57c8-173">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="e57c8-174">Argumente für die Hubmethode.</span><span class="sxs-lookup"><span data-stu-id="e57c8-174">Arguments to the hub method.</span></span>

<span data-ttu-id="e57c8-175">Sehen Sie sich das folgende Beispiel einer Chatroom-App an, die mehreren Mitgliedern einer Organisation die Anmeldung über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="e57c8-175">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="e57c8-176">Jede Person mit einem Microsoft-Konto kann sich beim Chat anmelden, aber nur Mitglieder der besitzenden Organisation können Benutzer sperren oder den Chatverlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-176">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="e57c8-177">Die App könnte bestimmte Funktionen für bestimmte Benutzer einschränken.</span><span class="sxs-lookup"><span data-stu-id="e57c8-177">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.:::no-loc(Identity):::?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.:::no-loc(Identity):::.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="e57c8-178">Im vorherigen Code dient `DomainRestrictedRequirement` als benutzerdefinierte `IAuthorizationRequirement`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-178">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="e57c8-179">Da der Ressourcenparameter `HubInvocationContext` übergeben wird, kann die interne Logik folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-179">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="e57c8-180">Sie kann den Kontext untersuchen, in dem der Hub aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e57c8-180">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="e57c8-181">Sie kann Entscheidungen darüber treffen, ob einem Benutzer die Ausführung einzelner Hubmethoden gestattet wird.</span><span class="sxs-lookup"><span data-stu-id="e57c8-181">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="e57c8-182">Einzelne Hubmethoden können mit dem Namen der Richtlinie gekennzeichnet werden, die der Code zur Laufzeit prüft.</span><span class="sxs-lookup"><span data-stu-id="e57c8-182">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="e57c8-183">Wenn Clients versuchen, einzelne Hubmethoden aufzurufen, wird der `DomainRestrictedRequirement`-Handler ausgeführt und steuert den Zugriff auf die Methoden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-183">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="e57c8-184">Basierend auf der Art und Weise, in der `DomainRestrictedRequirement` den Zugriff steuert, kann Folgendes passieren:</span><span class="sxs-lookup"><span data-stu-id="e57c8-184">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="e57c8-185">Alle angemeldeten Benutzer können die `SendMessage`-Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-185">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="e57c8-186">Nur Benutzer, die sich mit einer `@jabbr.net`-E-Mail-Adresse angemeldet haben, können den Verlauf anderer Benutzer anzeigen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-186">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="e57c8-187">Nur `bob42@jabbr.net` kann Benutzer aus dem Chatroom ausschließen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-187">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="e57c8-188">Die Erstellung der `DomainRestricted`-Richtlinie kann Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-188">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="e57c8-189">Fügen Sie die neue Richtlinie in *Startup.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e57c8-189">In *Startup.cs* , adding the new policy.</span></span>
* <span data-ttu-id="e57c8-190">Stellen Sie die benutzerdefinierte Anforderung `DomainRestrictedRequirement` als Parameter bereit.</span><span class="sxs-lookup"><span data-stu-id="e57c8-190">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="e57c8-191">Registrieren Sie `DomainRestricted` bei der Autorisierungsmiddleware.</span><span class="sxs-lookup"><span data-stu-id="e57c8-191">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

<span data-ttu-id="e57c8-192">:::no-loc(SignalR):::-Hubs verwenden [Endpunktrouting](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="e57c8-192">:::no-loc(SignalR)::: hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="e57c8-193">Eine :::no-loc(SignalR):::-Hubverbindung wurde zuvor explizit eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="e57c8-193">:::no-loc(SignalR)::: hub connection was previously done explicitly:</span></span>

```csharp
app.Use:::no-loc(SignalR):::(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="e57c8-194">In der vorherigen Version mussten Entwickler Controller, :::no-loc(Razor):::-Seiten und Hubs an verschiedenen Orten miteinander verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-194">In the previous version, developers needed to wire up controllers, :::no-loc(Razor)::: pages, and hubs in a variety of places.</span></span> <span data-ttu-id="e57c8-195">Explizite Verbindungen führen zu einer Reihe nahezu identischer Routingsegmente:</span><span class="sxs-lookup"><span data-stu-id="e57c8-195">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.Use:::no-loc(SignalR):::(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.Map:::no-loc(Razor):::Pages();
});
```

<span data-ttu-id="e57c8-196">:::no-loc(SignalR)::: 3.0-Hubs können per Endpunktrouting weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-196">:::no-loc(SignalR)::: 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="e57c8-197">Beim Endpunktrouting kann in der Regel das gesamte Routing in `UseRouting` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="e57c8-197">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.Map:::no-loc(Razor):::Pages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="e57c8-198">In ASP.NET Core 3.0 :::no-loc(SignalR)::: wurde Folgendes hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="e57c8-198">ASP.NET Core 3.0 :::no-loc(SignalR)::: added:</span></span>

<span data-ttu-id="e57c8-199">Streaming vom Client zum Server.</span><span class="sxs-lookup"><span data-stu-id="e57c8-199">Client-to-server streaming.</span></span> <span data-ttu-id="e57c8-200">Mit dem Streaming vom Client zum Server können serverseitige Methoden Instanzen von `IAsyncEnumerable<T>` oder `ChannelReader<T>` akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-200">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="e57c8-201">Im folgenden C#-Beispiel empfängt die `UploadStream`-Methode im Hub einen Zeichenfolgenstream vom Client:</span><span class="sxs-lookup"><span data-stu-id="e57c8-201">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="e57c8-202">.NET-Client-Apps können entweder eine `IAsyncEnumerable<T>`-oder eine `ChannelReader<T>`-Instanz als `stream`-Argument der oben genannten `UploadStream`-Hubmethode übergeben.</span><span class="sxs-lookup"><span data-stu-id="e57c8-202">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="e57c8-203">Nachdem die `for`-Schleife abgeschlossen ist und die lokale Funktion existiert, wird der Streamabschluss gesendet:</span><span class="sxs-lookup"><span data-stu-id="e57c8-203">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="e57c8-204">JavaScript-Client-Apps verwenden :::no-loc(SignalR)::: `Subject` (oder [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) für das `stream`-Argument der oben genannten Hubmethode `UploadStream`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-204">JavaScript client apps use the :::no-loc(SignalR)::: `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="e57c8-205">Der JavaScript-Code könnte die `subject.next`-Methode verwenden, um Zeichenfolgen zu verarbeiten, sobald diese erfasst werden und zum Senden an den Server bereit sin.</span><span class="sxs-lookup"><span data-stu-id="e57c8-205">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="e57c8-206">Mit Code wie in den beiden obigen Codeausschnitten lassen sich Echtzeitstreamingfunktionen erzeugen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-206">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="e57c8-207">Neue JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="e57c8-207">New JSON serialization</span></span>

<span data-ttu-id="e57c8-208">ASP.NET Core 3.0 verwendet jetzt standardmäßig <xref:System.Text.Json> zur JSON-Serialisierung:</span><span class="sxs-lookup"><span data-stu-id="e57c8-208">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="e57c8-209">Lese- und Schreibvorgänge in JSON erfolgen asynchron.</span><span class="sxs-lookup"><span data-stu-id="e57c8-209">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="e57c8-210">Der Code ist für UTF-8-Text optimiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-210">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="e57c8-211">In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-211">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="e57c8-212">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="e57c8-212">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-no-locrazor-directives"></a><span data-ttu-id="e57c8-213">Neue :::no-loc(Razor):::-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="e57c8-213">New :::no-loc(Razor)::: directives</span></span>

<span data-ttu-id="e57c8-214">Die folgende Liste enthält neue :::no-loc(Razor):::-Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-214">The following list contains new :::no-loc(Razor)::: directives:</span></span>

* <span data-ttu-id="e57c8-215">[`@attribute`](xref:mvc/views/razor#attribute): die Anweisung `@attribute` wendet das angegebene Attribut auf die Klasse der generierten Seite oder Ansicht an.</span><span class="sxs-lookup"><span data-stu-id="e57c8-215">[`@attribute`](xref:mvc/views/razor#attribute): The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="e57c8-216">Beispielsweise `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-216">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="e57c8-217">[`@implements`](xref:mvc/views/razor#implements): Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.</span><span class="sxs-lookup"><span data-stu-id="e57c8-217">[`@implements`](xref:mvc/views/razor#implements): The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="e57c8-218">Beispielsweise `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="e57c8-218">For example, `@implements IDisposable`.</span></span>

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="e57c8-219">:::no-loc(Identity):::Server4 unterstützt Authentifizierung und Autorisierung für Web-APIs und SPAs.</span><span class="sxs-lookup"><span data-stu-id="e57c8-219">:::no-loc(Identity):::Server4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="e57c8-220">ASP.NET Core 3.0 bietet eine Authentifizierung in Single-Page-Webanwendungen (SPAs) unter Verwendung der Unterstützung für die Web-API-Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-220">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="e57c8-221">:::no-loc(ASP.NET Core Identity)::: zum Authentifizieren und Speichern von Benutzern wird mit [:::no-loc(Identity):::Server4](https://identityserver.io/) zur Implementierung von OpenID Connect kombiniert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-221">:::no-loc(ASP.NET Core Identity)::: for authenticating and storing users is combined with [:::no-loc(Identity):::Server4](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="e57c8-222">:::no-loc(Identity):::Server4 ist ein OpenID Connect- und OAuth 2.0-Framework für ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="e57c8-222">:::no-loc(Identity):::Server4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="e57c8-223">Es ermöglicht die folgenden Sicherheitsfunktionen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-223">It enables the following security features:</span></span>

* <span data-ttu-id="e57c8-224">Authentifizierung als Dienst</span><span class="sxs-lookup"><span data-stu-id="e57c8-224">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="e57c8-225">Einmaliges Anmelden und einmaliges Abmelden für mehrere Anwendungstypen</span><span class="sxs-lookup"><span data-stu-id="e57c8-225">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="e57c8-226">Zugriffssteuerung für APIs</span><span class="sxs-lookup"><span data-stu-id="e57c8-226">Access control for APIs</span></span>
* <span data-ttu-id="e57c8-227">Federation Gateway</span><span class="sxs-lookup"><span data-stu-id="e57c8-227">Federation Gateway</span></span>

<span data-ttu-id="e57c8-228">Weitere Informationen finden Sie in der [:::no-loc(Identity):::Server4-Dokumentation](http://docs.identityserver.io/en/latest/index.html) oder unter [Authentifizierung und Autorisierung für SPAs](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="e57c8-228">For more information, see [the :::no-loc(Identity):::Server4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="e57c8-229">Zertifikat- und Kerberos-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e57c8-229">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="e57c8-230">Die Zertifikatsauthentifizierung erfordert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e57c8-230">Certificate authentication requires:</span></span>

* <span data-ttu-id="e57c8-231">Der Server muss so konfiguriert werden, dass er Zertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-231">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="e57c8-232">Die Authentifizierungsmiddleware muss in `Startup.Configure` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-232">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="e57c8-233">Der Zertifikatauthentifizierungsdienst muss in `Startup.ConfigureServices` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-233">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="e57c8-234">Die Optionen für die Zertifikatauthentifizierung umfassen folgende Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="e57c8-234">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="e57c8-235">Akzeptieren selbstsignierter Zertifikate.</span><span class="sxs-lookup"><span data-stu-id="e57c8-235">Accept self-signed certificates.</span></span>
* <span data-ttu-id="e57c8-236">Überprüfen auf Zertifikatswiderruf.</span><span class="sxs-lookup"><span data-stu-id="e57c8-236">Check for certificate revocation.</span></span>
* <span data-ttu-id="e57c8-237">Überprüfen, ob das angebotene Zertifikat die richtigen Nutzungsflags enthält.</span><span class="sxs-lookup"><span data-stu-id="e57c8-237">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="e57c8-238">Aus den Zertifikateigenschaften wird ein Standardbenutzerprinzipal erstellt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-238">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="e57c8-239">Der Benutzerprinzipal enthält ein Ereignis, das das Ergänzen oder Ersetzen des Prinzipals ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="e57c8-239">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="e57c8-240">Weitere Informationen finden Sie unter <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-240">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="e57c8-241">Die [Windows-Authentifizierung](/windows-server/security/windows-authentication/windows-authentication-overview) wurde auf Linux und macOS erweitert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-241">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="e57c8-242">In vorherigen Versionen war die Windows-Authentifizierung auf [IIS](xref:host-and-deploy/iis/index) und [HttpSys](xref:fundamentals/servers/httpsys) beschränkt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-242">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="e57c8-243">In ASP.NET Core 3.0 besitzt [Kestrel](xref:fundamentals/servers/kestrel) die Möglichkeit, Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) und [NTLM unter Windows](/windows-server/security/kerberos/ntlm-overview), Linux und macOS für Hosts zu verwenden, die der Windows-Domäne beigetreten sind.</span><span class="sxs-lookup"><span data-stu-id="e57c8-243">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="e57c8-244">Die Kestrel-Unterstützung für diese Authentifizierungsschemas wird durch das Paket [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-244">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="e57c8-245">Wie bei anderen Authentifizierungsdiensten auch sollte erst die Authentifizierung auf App-Ebene und dann der Dienst konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="e57c8-245">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="e57c8-246">Hostanforderungen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-246">Host requirements:</span></span>

* <span data-ttu-id="e57c8-247">Windows-Hosts müssen dem Benutzerkonto, das die App hostet, [Dienstprinzipalnamen](/windows/win32/ad/service-principal-names) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-247">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="e57c8-248">Linux- und macOS-Computer müssen der Domäne beigetreten sein.</span><span class="sxs-lookup"><span data-stu-id="e57c8-248">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="e57c8-249">Für den Webprozess müssen Dienstprinzipalnamen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-249">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="e57c8-250">[Keytab-Dateien](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) müssen generiert und auf dem Hostcomputer konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-250">[Keytab files](/archive/blogs/pie/all-you-need-to-know-about-keytab-files) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="e57c8-251">Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-251">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="e57c8-252">Änderungen an Vorlagen</span><span class="sxs-lookup"><span data-stu-id="e57c8-252">Template changes</span></span>

<span data-ttu-id="e57c8-253">Aus den Vorlagen für Webbenutzeroberflächen (:::no-loc(Razor)::: Pages, MVC mit Controller und Ansichten) wurde Folgendes entfernt:</span><span class="sxs-lookup"><span data-stu-id="e57c8-253">The web UI templates (:::no-loc(Razor)::: Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="e57c8-254">Die Benutzeroberfläche für die Zustimmung zu :::no-loc(cookie):::s ist nicht mehr enthalten.</span><span class="sxs-lookup"><span data-stu-id="e57c8-254">The :::no-loc(cookie)::: consent UI is no longer included.</span></span> <span data-ttu-id="e57c8-255">Informationen dazu, wie Sie die Zustimmung zu :::no-loc(cookie):::s in einer aus einer ASP.NET Core 3.0-Vorlage generierten App aktivieren, finden Sie unter <xref:security/gdpr>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-255">To enable the :::no-loc(cookie)::: consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="e57c8-256">Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs mehr dafür verwendet.</span><span class="sxs-lookup"><span data-stu-id="e57c8-256">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="e57c8-257">Weitere Informationen finden Sie unter [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350) (Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs basierend auf der aktuellen Umgebung mehr verwendet).</span><span class="sxs-lookup"><span data-stu-id="e57c8-257">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="e57c8-258">Die Angular-Vorlage wurde auf Angular 8 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-258">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="e57c8-259">Die Vorlage der :::no-loc(Razor):::-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die :::no-loc(Razor):::-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-259">The :::no-loc(Razor)::: class library (RCL) template defaults to :::no-loc(Razor)::: component development by default.</span></span> <span data-ttu-id="e57c8-260">Eine neue Vorlagenoption in Visual Studio bietet Vorlagenunterstützung für Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="e57c8-260">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="e57c8-261">Übergeben Sie beim Erstellen einer RCL aus einer Vorlage in einer Befehlsshell die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="e57c8-261">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="e57c8-262">Generischer Host</span><span class="sxs-lookup"><span data-stu-id="e57c8-262">Generic Host</span></span>

<span data-ttu-id="e57c8-263">Die ASP.NET Core 3.0-Vorlagen verwenden <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-263">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="e57c8-264">In vorherigen Versionen wurde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> verwendet.</span><span class="sxs-lookup"><span data-stu-id="e57c8-264">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="e57c8-265">Durch Verwendung des generischen .NET Core-Hosts (<xref:Microsoft.Extensions.Hosting.HostBuilder>) lässt sich eine bessere Integration von ASP.NET Core-Apps in andere, nicht webspezifische Serverszenarios erzielen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-265">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="e57c8-266">Weitere Informationen finden Sie unter [HostBuilder ersetzt WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="e57c8-266">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="e57c8-267">Konfiguration des Hosts</span><span class="sxs-lookup"><span data-stu-id="e57c8-267">Host configuration</span></span>

<span data-ttu-id="e57c8-268">Vor ASP.NET Core 3.0 wurden Umgebungsvariablen mit dem Präfix `ASPNETCORE_` für die Hostkonfiguration des Webhosts geladen.</span><span class="sxs-lookup"><span data-stu-id="e57c8-268">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="e57c8-269">In Version 3.0 wird `AddEnvironmentVariables` zum Laden von Umgebungsvariablen mit dem Präfix `DOTNET_` zur Hostkonfiguration mit `CreateDefaultBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="e57c8-269">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="e57c8-270">Änderungen an der Startkonstruktorinjektion</span><span class="sxs-lookup"><span data-stu-id="e57c8-270">Changes to Startup constructor injection</span></span>

<span data-ttu-id="e57c8-271">Der generische Host unterstützt nur die folgenden Typen für die Injektion des `Startup`-Konstruktors:</span><span class="sxs-lookup"><span data-stu-id="e57c8-271">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="e57c8-272">Alle Dienste können weiterhin direkt als Argumente in die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-272">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="e57c8-273">Weitere Informationen finden Sie unter [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353) (Generischer Host schränkt Startkonstruktorinjektion ein).</span><span class="sxs-lookup"><span data-stu-id="e57c8-273">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="e57c8-274">Kestrel</span><span class="sxs-lookup"><span data-stu-id="e57c8-274">Kestrel</span></span>

* <span data-ttu-id="e57c8-275">Die Kestrel-Konfiguration wurde für die Migration zum generischen Host aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-275">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="e57c8-276">In Version 3.0 ist Kestrel im Webhostgenerator konfiguriert, der durch `ConfigureWebHostDefaults` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e57c8-276">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="e57c8-277">Verbindungsadapter wurden aus Kestrel entfernt und durch Verbindungsmiddleware ersetzt. Diese ähnelt der HTTP-Middleware in der ASP.NET Core-Pipeline, ist aber für Verbindungen auf niedrigerer Ebene konzipiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-277">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="e57c8-278">Die Kestrel-Transportschicht wurde in `Connections.Abstractions` als öffentliche Schnittstelle verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e57c8-278">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="e57c8-279">Eine Mehrdeutigkeit zwischen Headern und Trailern wurde aufgelöst, indem nachstehende Header in eine neue Sammlung verschoben wurden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-279">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="e57c8-280">APIs mit synchronen E/A-Vorgängen, wie z. B. `HttpRequest.Body.Read`, sind eine häufige Quelle für einen Ressourcenmangel im Thread, der zu App-Abstürzen führt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-280">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="e57c8-281">In 3.0 ist `AllowSynchronousIO` standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-281">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="e57c8-282">Weitere Informationen finden Sie unter <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-282">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="e57c8-283">HTTP/2 standardmäßig aktiviert</span><span class="sxs-lookup"><span data-stu-id="e57c8-283">HTTP/2 enabled by default</span></span>

<span data-ttu-id="e57c8-284">HTTP/2 ist in Kestrel für HTTPS-Endpunkte standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-284">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="e57c8-285">Die HTTP/2-Unterstützung für IIS oder HTTP.sys ist aktiviert, wenn dies vom Betriebssystem unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="e57c8-285">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="e57c8-286">EventCounters auf Anforderung</span><span class="sxs-lookup"><span data-stu-id="e57c8-286">EventCounters on request</span></span>

<span data-ttu-id="e57c8-287">Die Hostingereignisquelle `Microsoft.AspNetCore.Hosting` gibt die folgenden neuen <xref:System.Diagnostics.Tracing.EventCounter>-Typen zurück, die im Zusammenhang mit eingehenden Anforderungen stehen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-287">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="e57c8-288">Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="e57c8-288">Endpoint routing</span></span>

<span data-ttu-id="e57c8-289">Das Endpunktrouting, das den reibungslosen Einsatz von Frameworks (z. B. MVC) mit Middleware ermöglicht, wurde erweitert:</span><span class="sxs-lookup"><span data-stu-id="e57c8-289">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="e57c8-290">Die Reihenfolge von Middlewarekomponenten und Endpunkten kann in der `Startup.Configure`-Pipeline für die Anforderungsverarbeitung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-290">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="e57c8-291">Endpunkte und Middlewarekomponenten lassen sich gut mit anderen ASP.NET Core-basierten Technologien wie z. B. Integritätsprüfungen kombinieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-291">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="e57c8-292">Endpunkte können sowohl in der Middleware als auch in MVC eine Richtlinie wie beispielsweise CORS oder Autorisierung implementieren.</span><span class="sxs-lookup"><span data-stu-id="e57c8-292">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="e57c8-293">Filter und Attribute können in Controllermethoden platziert werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-293">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="e57c8-294">Weitere Informationen finden Sie unter <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="e57c8-294">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="e57c8-295">Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="e57c8-295">Health Checks</span></span>

<span data-ttu-id="e57c8-296">Integritätsprüfungen verwenden das Endpunktrouting mit dem generischen Host.</span><span class="sxs-lookup"><span data-stu-id="e57c8-296">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="e57c8-297">Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:</span><span class="sxs-lookup"><span data-stu-id="e57c8-297">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="e57c8-298">Für Endpunkte für Integritätsprüfungen gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e57c8-298">Health Checks endpoints can:</span></span>

* <span data-ttu-id="e57c8-299">Sie geben mindestens einen zugelassenen Host oder Port an.</span><span class="sxs-lookup"><span data-stu-id="e57c8-299">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="e57c8-300">Sie erfordern Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-300">Require authorization.</span></span>
* <span data-ttu-id="e57c8-301">Sie erfordern CORS.</span><span class="sxs-lookup"><span data-stu-id="e57c8-301">Require CORS.</span></span>

<span data-ttu-id="e57c8-302">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="e57c8-302">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="e57c8-303">Pipes in HttpContext</span><span class="sxs-lookup"><span data-stu-id="e57c8-303">Pipes on HttpContext</span></span>

<span data-ttu-id="e57c8-304">Es ist jetzt möglich, mit der <xref:System.IO.Pipelines>-API den Anforderungstext zu lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="e57c8-304">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="e57c8-305">Die</span><span class="sxs-lookup"><span data-stu-id="e57c8-305">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="e57c8-306">`HttpRequest.BodyReader`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeReader> bereit, der zum Lesen des Anforderungstexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e57c8-306">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="e57c8-307">Die</span><span class="sxs-lookup"><span data-stu-id="e57c8-307">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="e57c8-308">`HttpResponse.BodyWriter`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeWriter> bereit, der zum Schreiben des Antworttexts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e57c8-308">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="e57c8-309">`HttpRequest.BodyReader` funktioniert analog zum `HttpRequest.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="e57c8-309">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="e57c8-310">`HttpResponse.BodyWriter` funktioniert analog zum `HttpResponse.Body`-Stream.</span><span class="sxs-lookup"><span data-stu-id="e57c8-310">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="e57c8-311">Verbesserte Fehlerberichterstellung in IIS</span><span class="sxs-lookup"><span data-stu-id="e57c8-311">Improved error reporting in IIS</span></span>

<span data-ttu-id="e57c8-312">Startfehler beim Hosten von ASP.NET Core-Apps in IIS erzeugen jetzt umfangreichere Diagnosedaten.</span><span class="sxs-lookup"><span data-stu-id="e57c8-312">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="e57c8-313">Diese Fehler werden mit Stapelüberwachung im Windows-Ereignisprotokoll gemeldet, wann immer möglich.</span><span class="sxs-lookup"><span data-stu-id="e57c8-313">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="e57c8-314">Darüber hinaus werden alle Warnungen, Fehler und Ausnahmefehler im Windows-Ereignisprotokoll protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-314">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="e57c8-315">Workerdienst und Worker SDK</span><span class="sxs-lookup"><span data-stu-id="e57c8-315">Worker Service and Worker SDK</span></span>

<span data-ttu-id="e57c8-316">.NET Core 3.0 führt eine neue Vorlage für die Workerdienst-App ein.</span><span class="sxs-lookup"><span data-stu-id="e57c8-316">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="e57c8-317">Diese Vorlage dient als Ausgangspunkt für das Schreiben von zeitintensiven Diensten in .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e57c8-317">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="e57c8-318">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="e57c8-318">For more information, see:</span></span>

* <span data-ttu-id="e57c8-319">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/) (.NET Core-Worker als Windows-Dienste)</span><span class="sxs-lookup"><span data-stu-id="e57c8-319">[.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)</span></span>
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="e57c8-320">Verbesserungen an Middleware für weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="e57c8-320">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="e57c8-321">In früheren Versionen von ASP.NET Core war der Aufruf von <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> und <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problematisch, wenn die Bereitstellung auf einem Azure Linux-Computer oder hinter einem anderen Reverseproxy als IIS erfolgt war.</span><span class="sxs-lookup"><span data-stu-id="e57c8-321">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="e57c8-322">Die Problembehebung für frühere Versionen ist unter [Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies) dokumentiert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-322">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="e57c8-323">Dieses Szenario wurde in ASP.NET Core 3.0 behoben.</span><span class="sxs-lookup"><span data-stu-id="e57c8-323">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="e57c8-324">Der Host aktiviert die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options), wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="e57c8-324">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="e57c8-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` ist in unseren Containerimages auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="e57c8-325">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="e57c8-326">Leistungsverbesserungen</span><span class="sxs-lookup"><span data-stu-id="e57c8-326">Performance improvements</span></span>

<span data-ttu-id="e57c8-327">ASP.NET Core 3.0 enthält viele Verbesserungen, die die Arbeitsspeichernutzung reduzieren und den Durchsatz erhöhen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-327">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="e57c8-328">Bei Verwendung des integrierten Containers für die Abhängigkeitsinjektion für bereichsbezogene Dienste wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="e57c8-328">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="e57c8-329">Im gesamten Framework, einschließlich Middlewareszenarien und Routing, wurden Zuweisungen reduziert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-329">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="e57c8-330">Für Websocketverbindungen wird weniger Arbeitsspeicher verbraucht.</span><span class="sxs-lookup"><span data-stu-id="e57c8-330">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="e57c8-331">Bei HTTPS-Verbindungen wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-331">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="e57c8-332">Es steht ein neues optimiertes und vollständig asynchrones JSON-Serialisierungsmodul zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="e57c8-332">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="e57c8-333">Bei der Formularanalyse wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.</span><span class="sxs-lookup"><span data-stu-id="e57c8-333">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="e57c8-334">ASP.NET Core 3.0 funktioniert nur in .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="e57c8-334">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="e57c8-335">Ab ASP.NET Core 3.0 ist .NET Framework kein unterstütztes Zielframework mehr.</span><span class="sxs-lookup"><span data-stu-id="e57c8-335">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="e57c8-336">Projekte, die auf .NET Framework abzielen, können mit dem [.NET Core 2.1 LTS-Release](https://dotnet.microsoft.com/download/dotnet-core/2.1) weiterhin mit vollständiger Unterstützung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e57c8-336">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="e57c8-337">Die meisten auf ASP.NET Core 2.1.x bezogenen Pakete werden unbegrenzt unterstützt, auch nach dem dreijährigen LTS-Zeitraum für .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="e57c8-337">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="e57c8-338">Informationen zur Migration finden Sie unter [Portieren Ihres Codes von .NET Framework auf .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="e57c8-338">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="e57c8-339">Verwenden des freigegebenen ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="e57c8-339">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="e57c8-340">Das freigegebene ASP.NET Core 3.0-Framework, das im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten ist, erfordert kein explizites `<PackageReference />`-Element in der Projektdatei mehr.</span><span class="sxs-lookup"><span data-stu-id="e57c8-340">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="e57c8-341">Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:</span><span class="sxs-lookup"><span data-stu-id="e57c8-341">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="e57c8-342">Aus dem freigegebenen ASP.NET Core-Framework entfernte Assemblys</span><span class="sxs-lookup"><span data-stu-id="e57c8-342">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="e57c8-343">Die wichtigsten Assemblys, die aus dem freigegebenen ASP.NET Core 3.0-Framework entfernt wurden:</span><span class="sxs-lookup"><span data-stu-id="e57c8-343">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="e57c8-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="e57c8-344">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="e57c8-345">Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="e57c8-345">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="e57c8-346">ASP.NET Core 3.0 führt `System.Text.Json` zum Lesen und Schreiben von JSON ein.</span><span class="sxs-lookup"><span data-stu-id="e57c8-346">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="e57c8-347">Weitere Informationen finden Sie im Abschnitt [Neue JSON-Serialisierung](#new-json-serialization) im vorliegenden Dokument.</span><span class="sxs-lookup"><span data-stu-id="e57c8-347">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="e57c8-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e57c8-348">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="e57c8-349">Eine vollständige Liste der Assemblys, die aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755) (Assemblys, die aus Microsoft.AspNetCore.App 3.0 entfernt werden).</span><span class="sxs-lookup"><span data-stu-id="e57c8-349">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="e57c8-350">Weitere Informationen zu den Beweggründen für diese Änderung finden Sie unter [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) (Breaking Changes an Microsoft.AspNetCore.App in Version 3.0) und [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Ein erster Blick auf die Änderungen in ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="e57c8-350">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
