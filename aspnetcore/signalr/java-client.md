---
title: ASP.net Core SignalR Java-Client
author: mikaelm12
description: Erfahren Sie, wie Sie den ASP.net Core SignalR Java-Client verwenden.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/java-client
ms.openlocfilehash: da6876e0540579dac5fb9e92362b38a398bca4d5
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972079"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="14776-103">ASP.net Core SignalR Java-Client</span><span class="sxs-lookup"><span data-stu-id="14776-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="14776-104">Von [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="14776-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="14776-105">Der Java-Client ermöglicht das Herstellen einer Verbindung mit einem ASP.net Core SignalR Server aus Java-Code, einschließlich Android-Apps.</span><span class="sxs-lookup"><span data-stu-id="14776-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="14776-106">Wie der [JavaScript-Client](xref:signalr/javascript-client) und der [.NET-Client](xref:signalr/dotnet-client)ermöglicht der Java-Client das empfangen und Senden von Nachrichten an einen Hub in Echtzeit.</span><span class="sxs-lookup"><span data-stu-id="14776-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="14776-107">Der Java-Client ist in ASP.net Core 2,2 und höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="14776-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="14776-108">Die Java-Konsolenanwendung, auf die in diesem Artikel verwiesen wird, verwendet den SignalR Java-Client.</span><span class="sxs-lookup"><span data-stu-id="14776-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="14776-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14776-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="14776-110">Installieren des SignalR Java-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="14776-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="14776-111">Die jar *-Datei signalr-1.0.0* ermöglicht Clients das Herstellen einer Verbindung mit SignalR Hubs.</span><span class="sxs-lookup"><span data-stu-id="14776-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="14776-112">Die aktuelle JAR-Datei-Versionsnummer finden Sie in den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="14776-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="14776-113">Wenn Sie gradle verwenden, fügen Sie die folgende Zeile zum- `dependencies` Abschnitt der Datei " *Build. gradle* " hinzu:</span><span class="sxs-lookup"><span data-stu-id="14776-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="14776-114">Wenn Sie Maven verwenden, fügen Sie die folgenden Zeilen im- `<dependencies>` Element ihrer *pom.xml* Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="14776-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="14776-115">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="14776-115">Connect to a hub</span></span>

<span data-ttu-id="14776-116">Zum Einrichten eines `HubConnection` `HubConnectionBuilder` sollte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="14776-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="14776-117">Die Hub-URL und die Protokollebene können während der Verbindungs Herstellung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="14776-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="14776-118">Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden vor Aufrufen `build` .</span><span class="sxs-lookup"><span data-stu-id="14776-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="14776-119">Starten Sie die Verbindung mit `start` .</span><span class="sxs-lookup"><span data-stu-id="14776-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="14776-120">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="14776-120">Call hub methods from client</span></span>

<span data-ttu-id="14776-121">Ein Aufruf von `send` Ruft eine Hub-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="14776-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="14776-122">Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `send` .</span><span class="sxs-lookup"><span data-stu-id="14776-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="14776-123">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="14776-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="14776-124">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="14776-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="14776-125">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="14776-125">Call client methods from hub</span></span>

<span data-ttu-id="14776-126">Verwenden `hubConnection.on` Sie, um Methoden auf dem Client zu definieren, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="14776-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="14776-127">Definieren Sie die Methoden nach dem Aufbau, aber vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="14776-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="14776-128">Hinzufügen der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="14776-128">Add logging</span></span>

<span data-ttu-id="14776-129">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="14776-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="14776-130">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="14776-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="14776-131">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="14776-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="14776-132">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="14776-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="14776-133">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="14776-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="14776-134">Hinweise zur Android-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="14776-134">Android development notes</span></span>

<span data-ttu-id="14776-135">Beachten Sie bei der Android SDK Kompatibilität der SignalR Client Features die folgenden Punkte, wenn Sie die Ziel Android SDK Version angeben:</span><span class="sxs-lookup"><span data-stu-id="14776-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="14776-136">Der SignalR Java-Client kann auf Android-API-Ebene 16 und höher ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="14776-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="14776-137">Zum Herstellen einer Verbindung über den Azure SignalR -Dienst sind Android-API-Ebene 20 und höher erforderlich, da der [Azure- SignalR Dienst](/azure/azure-signalr/signalr-overview) TLS 1,2 erfordert und keine SHA-1-basierten Verschlüsselungs Sammlungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="14776-138">Android hat [Unterstützung für die Verschlüsselungs Sammlungen SHA-256 (und höher)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) auf API-Ebene 20 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="14776-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="14776-139">Konfigurieren der bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="14776-139">Configure bearer token authentication</span></span>

<span data-ttu-id="14776-140">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie dem [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)eine "zugriffstokenfactory" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="14776-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="14776-141">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="14776-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="14776-142">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="14776-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="14776-143">Bekannte Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="14776-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="14776-144">Nur das JSON-Protokoll wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="14776-145">Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="14776-146">Nur das JSON-Protokoll wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="14776-147">Nur der websockets-Transport wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="14776-148">Streaming wird noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14776-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="14776-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="14776-149">Additional resources</span></span>

* [<span data-ttu-id="14776-150">Java-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="14776-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="14776-151">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="14776-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
