---
title: 'Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core'
author: bradygaster
description: 'Fügen Sie ASP.net Core das messagepack-Hub-Protokoll hinzu SignalR .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: e7d19a42e48048d2be4b87d6b0ac1ba6b2596ff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058167"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="13fe2-103">Verwenden des messagepack-Hub-Protokolls in SignalR für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="13fe2-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="13fe2-104">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="13fe2-105">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="13fe2-105">What is MessagePack?</span></span>

<span data-ttu-id="13fe2-106">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="13fe2-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="13fe2-107">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="13fe2-108">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="13fe2-109">SignalR verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="13fe2-110">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="13fe2-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="13fe2-111">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="13fe2-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="13fe2-112">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-113">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-113">JSON is enabled by default.</span></span> <span data-ttu-id="13fe2-114">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="13fe2-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="13fe2-115">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="13fe2-116">In diesem Delegaten kann die- `SerializerOptions` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="13fe2-117">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="13fe2-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="13fe2-118">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="13fe2-119">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="13fe2-120">Zum Beispiel wird `.WithSecurity(MessagePackSecurity.UntrustedData)` beim Ersetzen von aufgerufen `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="13fe2-121">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-122">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="13fe2-123">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="13fe2-124">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="13fe2-125">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-125">.NET client</span></span>

<span data-ttu-id="13fe2-126">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-127">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="13fe2-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="13fe2-128">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-128">JavaScript client</span></span>

<span data-ttu-id="13fe2-129">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="13fe2-130">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="13fe2-131">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="13fe2-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="13fe2-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="13fe2-133">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="13fe2-134">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="13fe2-135">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-136">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="13fe2-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="13fe2-137">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js* verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="13fe2-138">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-138">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="13fe2-139">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-140">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="13fe2-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="13fe2-141">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="13fe2-141">MessagePack quirks</span></span>

<span data-ttu-id="13fe2-142">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="13fe2-143">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="13fe2-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="13fe2-144">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="13fe2-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="13fe2-145">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="13fe2-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="13fe2-146">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="13fe2-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="13fe2-147">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13fe2-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="13fe2-148">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="13fe2-149">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="13fe2-150">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="13fe2-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="13fe2-151">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="13fe2-152">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="13fe2-153">Folglich konvertiert das messagepack-Hub-Protokoll beim Deserialisieren eines Datums in das UTC-Format, wenn der `DateTime.Kind` andernfalls die `DateTimeKind.Local` Zeit nicht berührt und unverändert übergibt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="13fe2-154">Wenn Sie mit Werten arbeiten `DateTime` , empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="13fe2-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="13fe2-155">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="13fe2-156">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="13fe2-157">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="13fe2-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="13fe2-158">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="13fe2-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="13fe2-159">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="13fe2-160">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="13fe2-161">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="13fe2-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="13fe2-162">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="13fe2-163">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="13fe2-164">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="13fe2-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="13fe2-165">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="13fe2-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="13fe2-166">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="13fe2-167">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="13fe2-168">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="13fe2-169">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="13fe2-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="13fe2-170">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="13fe2-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="13fe2-171">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="13fe2-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="13fe2-172">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="13fe2-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="13fe2-173">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="13fe2-174">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="13fe2-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="13fe2-175">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="13fe2-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="13fe2-176">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13fe2-176">Related resources</span></span>

* [<span data-ttu-id="13fe2-177">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="13fe2-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="13fe2-178">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="13fe2-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="13fe2-180">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="13fe2-181">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="13fe2-181">What is MessagePack?</span></span>

<span data-ttu-id="13fe2-182">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="13fe2-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="13fe2-183">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="13fe2-184">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="13fe2-185">SignalR verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="13fe2-186">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="13fe2-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="13fe2-187">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="13fe2-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="13fe2-188">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-189">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-189">JSON is enabled by default.</span></span> <span data-ttu-id="13fe2-190">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="13fe2-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="13fe2-191">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="13fe2-192">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="13fe2-193">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="13fe2-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="13fe2-194">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="13fe2-195">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="13fe2-196">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="13fe2-197">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="13fe2-198">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="13fe2-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="13fe2-199">`MessagePack` in Version 2. x wurden wichtige Änderungen eingeführt, die mit den SignalR Versionen 3,1 und früher nicht kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="13fe2-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="13fe2-200">Wenn `MessagePackSecurity.Active` nicht auf festgelegt `MessagePackSecurity.UntrustedData` ist, kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="13fe2-201">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="13fe2-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="13fe2-202">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-203">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="13fe2-204">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="13fe2-205">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="13fe2-206">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-206">.NET client</span></span>

<span data-ttu-id="13fe2-207">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-208">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="13fe2-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="13fe2-209">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-209">JavaScript client</span></span>

<span data-ttu-id="13fe2-210">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="13fe2-211">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="13fe2-212">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="13fe2-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="13fe2-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="13fe2-214">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="13fe2-215">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="13fe2-216">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-217">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="13fe2-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="13fe2-218">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js* verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="13fe2-219">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-219">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="13fe2-220">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-221">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="13fe2-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="13fe2-222">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="13fe2-222">MessagePack quirks</span></span>

<span data-ttu-id="13fe2-223">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="13fe2-224">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="13fe2-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="13fe2-225">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="13fe2-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="13fe2-226">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="13fe2-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="13fe2-227">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="13fe2-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="13fe2-228">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13fe2-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="13fe2-229">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="13fe2-230">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="13fe2-231">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="13fe2-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="13fe2-232">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="13fe2-233">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="13fe2-234">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="13fe2-235">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="13fe2-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="13fe2-236">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="13fe2-237">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="13fe2-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="13fe2-238">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="13fe2-239">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="13fe2-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="13fe2-240">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="13fe2-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="13fe2-241">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="13fe2-242">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="13fe2-243">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="13fe2-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="13fe2-244">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="13fe2-245">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="13fe2-246">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="13fe2-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="13fe2-247">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="13fe2-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="13fe2-248">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="13fe2-249">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="13fe2-250">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="13fe2-251">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="13fe2-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="13fe2-252">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="13fe2-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="13fe2-253">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="13fe2-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="13fe2-254">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="13fe2-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="13fe2-255">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="13fe2-256">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="13fe2-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="13fe2-257">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="13fe2-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="13fe2-258">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13fe2-258">Related resources</span></span>

* [<span data-ttu-id="13fe2-259">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="13fe2-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="13fe2-260">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="13fe2-261">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="13fe2-262">In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="13fe2-263">Was ist messagepack?</span><span class="sxs-lookup"><span data-stu-id="13fe2-263">What is MessagePack?</span></span>

<span data-ttu-id="13fe2-264">[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat.</span><span class="sxs-lookup"><span data-stu-id="13fe2-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="13fe2-265">Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="13fe2-266">Die binären Nachrichten sind bei der Betrachtung von Netzwerk Ablauf Verfolgungen und-Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="13fe2-267">SignalR verfügt über integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="13fe2-268">Konfigurieren von messagepack auf dem Server</span><span class="sxs-lookup"><span data-stu-id="13fe2-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="13fe2-269">Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Sie das Paket in Ihrer APP.</span><span class="sxs-lookup"><span data-stu-id="13fe2-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="13fe2-270">Fügen Sie in der- `Startup.ConfigureServices` Methode dem-Befehl hinzu, `AddMessagePackProtocol` um die `AddSignalR` Unterstützung von messagepack auf dem Server zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-271">JSON ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-271">JSON is enabled by default.</span></span> <span data-ttu-id="13fe2-272">Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.</span><span class="sxs-lookup"><span data-stu-id="13fe2-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="13fe2-273">Zum Anpassen der Art und Weise, wie messagepack Ihre Daten formatiert, verwendet einen Delegaten `AddMessagePackProtocol` zum Konfigurieren von Optionen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="13fe2-274">In diesem Delegaten kann die- `FormatterResolvers` Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="13fe2-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="13fe2-275">Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="13fe2-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="13fe2-276">Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="13fe2-277">Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="13fe2-278">Beispielsweise das Festlegen der `MessagePackSecurity.Active` statischen-Eigenschaft auf `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="13fe2-279">Zum Festlegen `MessagePackSecurity.Active` von muss eine [1.9. x-Version von messagepack](https://www.nuget.org/packages/MessagePack/1.9.3)manuell installiert werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="13fe2-280">Durch `MessagePack` die Installation von 1.9. x werden die Versionen von aktualisiert SignalR .</span><span class="sxs-lookup"><span data-stu-id="13fe2-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="13fe2-281">Wenn `MessagePackSecurity.Active` nicht auf festgelegt ist `MessagePackSecurity.UntrustedData` , kann ein böswilliger Client einen Denial-of-Service-Angriff auslösen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="13fe2-282">`MessagePackSecurity.Active`Wird in festgelegt `Program.Main` , wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="13fe2-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="13fe2-283">Konfigurieren von messagepack auf dem Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-284">JSON ist für die unterstützten Clients standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="13fe2-285">Clients können nur ein einzelnes Protokoll unterstützen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="13fe2-286">Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="13fe2-287">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-287">.NET client</span></span>

<span data-ttu-id="13fe2-288">Um messagepack im .NET-Client zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` Paket, und wenden Sie `AddMessagePackProtocol` an `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-289">Dieser `AddMessagePackProtocol` Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.</span><span class="sxs-lookup"><span data-stu-id="13fe2-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="13fe2-290">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-290">JavaScript client</span></span>

<span data-ttu-id="13fe2-291">Die Unterstützung von messagepack für den JavaScript-Client wird durch das [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) NPM-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="13fe2-292">Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="13fe2-293">Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:</span><span class="sxs-lookup"><span data-stu-id="13fe2-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="13fe2-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="13fe2-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="13fe2-295">In einem Browser muss auf die `msgpack5` Bibliothek ebenfalls verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="13fe2-296">Verwenden Sie ein- `<script>` Tag, um einen Verweis zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="13fe2-297">Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js* .</span></span>

> [!NOTE]
> <span data-ttu-id="13fe2-298">Wenn Sie das- `<script>` Element verwenden, ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="13fe2-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="13fe2-299">Wenn auf *signalr-protocol-msgpack.js* vor *msgpack5.js* verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js* , an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="13fe2-300">*signalr.js* ist auch erforderlich, bevor *signalr-protocol-msgpack.js* .</span><span class="sxs-lookup"><span data-stu-id="13fe2-300">*signalr.js* is also required before *signalr-protocol-msgpack.js* .</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="13fe2-301">Durch das Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zum `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls beim Herstellen einer Verbindung mit einem Server konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="13fe2-302">Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="13fe2-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="13fe2-303">Messagepack-Quiri</span><span class="sxs-lookup"><span data-stu-id="13fe2-303">MessagePack quirks</span></span>

<span data-ttu-id="13fe2-304">Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="13fe2-305">Beim messagepack wird die Groß-/Kleinschreibung beachtet</span><span class="sxs-lookup"><span data-stu-id="13fe2-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="13fe2-306">Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="13fe2-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="13fe2-307">Sehen Sie sich beispielsweise die folgende c#-Klasse an:</span><span class="sxs-lookup"><span data-stu-id="13fe2-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="13fe2-308">Beim Senden vom JavaScript-Client müssen Sie `PascalCased` Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der c#-Klasse übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="13fe2-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="13fe2-309">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13fe2-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="13fe2-310">`camelCased`Die Verwendung von Namen wird nicht ordnungsgemäß an die c#-Klasse gebunden.</span><span class="sxs-lookup"><span data-stu-id="13fe2-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="13fe2-311">Sie können dieses Problem umgehen, indem Sie das- `Key` Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben.</span><span class="sxs-lookup"><span data-stu-id="13fe2-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="13fe2-312">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="13fe2-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="13fe2-313">DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="13fe2-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="13fe2-314">Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind` Wert einer zu codieren `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="13fe2-315">Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="13fe2-316">Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="13fe2-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="13fe2-317">Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.</span><span class="sxs-lookup"><span data-stu-id="13fe2-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="13fe2-318">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="13fe2-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="13fe2-319">"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="13fe2-320">Die vom JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek SignalR unterstützt den `timestamp96` Typ in messagepack nicht.</span><span class="sxs-lookup"><span data-stu-id="13fe2-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="13fe2-321">Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft).</span><span class="sxs-lookup"><span data-stu-id="13fe2-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="13fe2-322">Der Wert von `DateTime.MinValue` ist `January 1, 0001` , der in einem-Wert codiert werden muss `timestamp96` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="13fe2-323">Aus diesem Grund wird das Senden `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="13fe2-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="13fe2-324">Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="13fe2-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="13fe2-325">In der Regel `DateTime.MinValue` wird verwendet, um einen "Missing"-oder-Wert zu codieren `null` .</span><span class="sxs-lookup"><span data-stu-id="13fe2-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="13fe2-326">Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen Werte zulässt- `DateTime` Wert ( `DateTime?` ), oder codieren Sie einen separaten Wert, `bool` der angibt, ob das Datum vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="13fe2-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="13fe2-327">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="13fe2-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="13fe2-328">Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung</span><span class="sxs-lookup"><span data-stu-id="13fe2-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="13fe2-329">Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung.</span><span class="sxs-lookup"><span data-stu-id="13fe2-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="13fe2-330">Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="13fe2-331">Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird.</span><span class="sxs-lookup"><span data-stu-id="13fe2-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="13fe2-332">Weitere Informationen finden Sie in [der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="13fe2-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="13fe2-333">Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an folgenden-Konfigurations Delegaten registrieren `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="13fe2-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="13fe2-334">Typüberprüfungen sind in messagepack strenger.</span><span class="sxs-lookup"><span data-stu-id="13fe2-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="13fe2-335">Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch.</span><span class="sxs-lookup"><span data-stu-id="13fe2-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="13fe2-336">Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl () ist, `{ foo: 42 }` aber die-Eigenschaft der .NET-Klasse vom Typ ist `string` , wird der Wert konvertiert.</span><span class="sxs-lookup"><span data-stu-id="13fe2-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="13fe2-337">Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="13fe2-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="13fe2-338">Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="13fe2-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="13fe2-339">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="13fe2-339">Related resources</span></span>

* [<span data-ttu-id="13fe2-340">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="13fe2-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="13fe2-341">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="13fe2-342">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="13fe2-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
