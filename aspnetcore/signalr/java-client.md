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
ms.openlocfilehash: 92941d21820de90eb2ae8fb76c21c588ed9f1ffb
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024755"
---
# <a name="aspnet-core-no-locsignalr-java-client"></a><span data-ttu-id="49189-103">ASP.net Core SignalR Java-Client</span><span class="sxs-lookup"><span data-stu-id="49189-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="49189-104">Von [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="49189-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="49189-105">Der Java-Client ermöglicht das Herstellen einer Verbindung mit einem ASP.net Core SignalR Server aus Java-Code, einschließlich Android-Apps.</span><span class="sxs-lookup"><span data-stu-id="49189-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="49189-106">Wie der [JavaScript-Client](xref:signalr/javascript-client) und der [.NET-Client](xref:signalr/dotnet-client)ermöglicht der Java-Client das empfangen und Senden von Nachrichten an einen Hub in Echtzeit.</span><span class="sxs-lookup"><span data-stu-id="49189-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="49189-107">Der Java-Client ist in ASP.net Core 2,2 und höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="49189-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="49189-108">Die Java-Konsolenanwendung, auf die in diesem Artikel verwiesen wird, verwendet den SignalR Java-Client.</span><span class="sxs-lookup"><span data-stu-id="49189-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="49189-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49189-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-no-locsignalr-java-client-package"></a><span data-ttu-id="49189-110">Installieren des SignalR Java-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="49189-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="49189-111">Die jar *-Datei signalr-1.0.0* ermöglicht Clients das Herstellen einer Verbindung mit SignalR Hubs.</span><span class="sxs-lookup"><span data-stu-id="49189-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="49189-112">Die aktuelle JAR-Datei-Versionsnummer finden Sie in den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span><span class="sxs-lookup"><span data-stu-id="49189-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="49189-113">Wenn Sie gradle verwenden, fügen Sie die folgende Zeile zum- `dependencies` Abschnitt der Datei " *Build. gradle* " hinzu:</span><span class="sxs-lookup"><span data-stu-id="49189-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="49189-114">Wenn Sie Maven verwenden, fügen Sie die folgenden Zeilen im- `<dependencies>` Element ihrer *pom.xml* Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="49189-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="49189-115">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="49189-115">Connect to a hub</span></span>

<span data-ttu-id="49189-116">Zum Einrichten eines `HubConnection` `HubConnectionBuilder` sollte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="49189-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="49189-117">Die Hub-URL und die Protokollebene können während der Verbindungs Herstellung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="49189-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="49189-118">Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden vor Aufrufen `build` .</span><span class="sxs-lookup"><span data-stu-id="49189-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="49189-119">Starten Sie die Verbindung mit `start` .</span><span class="sxs-lookup"><span data-stu-id="49189-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="49189-120">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="49189-120">Call hub methods from client</span></span>

<span data-ttu-id="49189-121">Ein Aufruf von `send` Ruft eine Hub-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="49189-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="49189-122">Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `send` .</span><span class="sxs-lookup"><span data-stu-id="49189-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="49189-123">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49189-123">Calling hub methods from a client is only supported when using the Azure SignalR Service in *Default* mode.</span></span> <span data-ttu-id="49189-124">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="49189-124">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="49189-125">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="49189-125">Call client methods from hub</span></span>

<span data-ttu-id="49189-126">Verwenden `hubConnection.on` Sie, um Methoden auf dem Client zu definieren, die der Hub abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="49189-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="49189-127">Definieren Sie die Methoden nach dem Aufbau, aber vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="49189-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="49189-128">Hinzufügen der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="49189-128">Add logging</span></span>

<span data-ttu-id="49189-129">Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="49189-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="49189-130">Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden.</span><span class="sxs-lookup"><span data-stu-id="49189-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="49189-131">Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49189-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="49189-132">Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="49189-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="49189-133">Dies kann sicher ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="49189-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="49189-134">Hinweise zur Android-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="49189-134">Android development notes</span></span>

<span data-ttu-id="49189-135">Beachten Sie bei der Android SDK Kompatibilität der SignalR Client Features die folgenden Punkte, wenn Sie die Ziel Android SDK Version angeben:</span><span class="sxs-lookup"><span data-stu-id="49189-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="49189-136">Der SignalR Java-Client kann auf Android-API-Ebene 16 und höher ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="49189-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="49189-137">Zum Herstellen einer Verbindung über den Azure SignalR -Dienst sind Android-API-Ebene 20 und höher erforderlich, da der [Azure- SignalR Dienst](/azure/azure-signalr/signalr-overview) TLS 1,2 erfordert und keine SHA-1-basierten Verschlüsselungs Sammlungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="49189-138">Android hat [Unterstützung für die Verschlüsselungs Sammlungen SHA-256 (und höher)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) auf API-Ebene 20 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="49189-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="49189-139">Konfigurieren der bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="49189-139">Configure bearer token authentication</span></span>

<span data-ttu-id="49189-140">Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie dem [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)eine "zugriffstokenfactory" bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="49189-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="49189-141">Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="49189-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="49189-142">Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="49189-142">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a><span data-ttu-id="49189-143">Übergeben von Klassen Informationen in Java</span><span class="sxs-lookup"><span data-stu-id="49189-143">Passing Class information in Java</span></span>

<span data-ttu-id="49189-144">Wenn die- `on` ,- `invoke` oder- `stream` Methoden von `HubConnection` im Java-Client aufgerufen werden, sollten Benutzer anstelle eines-Objekts ein-Objekt übergeben, `Type` `Class<?>` um alle `Object` an die-Methode übergebenen generischen zu beschreiben.</span><span class="sxs-lookup"><span data-stu-id="49189-144">When calling the `on`, `invoke`, or `stream` methods of `HubConnection` in the Java client, users should pass a `Type` object rather than a `Class<?>` object to describe any generic `Object` passed to the method.</span></span> <span data-ttu-id="49189-145">Eine `Type` kann mithilfe der angegebenen-Klasse abgerufen werden `TypeReference` .</span><span class="sxs-lookup"><span data-stu-id="49189-145">A `Type` can be acquired using the provided `TypeReference` class.</span></span> <span data-ttu-id="49189-146">Mit `Foo<T>` dem folgenden Code wird beispielsweise eine benutzerdefinierte generische Klasse mit dem Namen abgerufen `Type` :</span><span class="sxs-lookup"><span data-stu-id="49189-146">For example, using a custom generic class named `Foo<T>`, the following code gets the `Type`:</span></span>

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

<span data-ttu-id="49189-147">Bei nicht-Generika, z. b. primitiven oder anderen nicht parametrisierten Typen wie `String` , können Sie einfach das integrierte verwenden `.class` .</span><span class="sxs-lookup"><span data-stu-id="49189-147">For non-generics, such as primitives or other non-parameterized types like `String`, you can simply use the built-in `.class`.</span></span>

<span data-ttu-id="49189-148">Wenn Sie eine dieser Methoden mit einem oder mehreren Objekttypen aufrufen, verwenden Sie die Generika Syntax, wenn Sie die Methode aufrufen.</span><span class="sxs-lookup"><span data-stu-id="49189-148">When calling one of these methods with one or more object types, use the generics syntax when invoking the method.</span></span> <span data-ttu-id="49189-149">Wenn Sie z. b. einen `on` Handler für eine Methode mit dem Namen registrieren `func` , die als Argumente für eine Zeichenfolge und ein- `Foo<String>` Objekt verwendet, verwenden Sie den folgenden Code, um eine Aktion zum Drucken der Argumente festzulegen:</span><span class="sxs-lookup"><span data-stu-id="49189-149">For example, when registering an `on` handler for a method named `func`, which takes as arguments a String and a `Foo<String>` object, use the following code to set an action to print the arguments:</span></span>

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

<span data-ttu-id="49189-150">Diese Konvention ist erforderlich, da wir `Object.getClass` aufgrund der typlöschung in Java keine umfassenden Informationen zu komplexen Typen mit der-Methode abrufen können.</span><span class="sxs-lookup"><span data-stu-id="49189-150">This convention is necessary because we can not retrieve complete information about complex types with the `Object.getClass` method due to type erasure in Java.</span></span> <span data-ttu-id="49189-151">Wenn Sie z. b. für einen aufrufen, wird `getClass` `ArrayList<String>` nicht zurückgegeben `Class<ArrayList<String>>` , sondern `Class<ArrayList>` der Deserialisierer verfügt nicht über genügend Informationen, um eine eingehende Nachricht ordnungsgemäß zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="49189-151">For example, calling `getClass` on an `ArrayList<String>` would not return `Class<ArrayList<String>>`, but rather `Class<ArrayList>`, which does not give the deserializer enough information to correctly deserialize an incoming message.</span></span> <span data-ttu-id="49189-152">Das gleiche gilt für benutzerdefinierte Objekte.</span><span class="sxs-lookup"><span data-stu-id="49189-152">The same is true for custom objects.</span></span>

::: moniker-end

## <a name="known-limitations"></a><span data-ttu-id="49189-153">Bekannte Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="49189-153">Known limitations</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="49189-154">Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-154">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* <span data-ttu-id="49189-155">Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-155">Transport fallback and the Server Sent Events transport aren't supported.</span></span>
* <span data-ttu-id="49189-156">Nur das JSON-Protokoll wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-156">Only the JSON protocol is supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="49189-157">Nur das JSON-Protokoll wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-157">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="49189-158">Nur der websockets-Transport wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-158">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="49189-159">Streaming wird noch nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49189-159">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="49189-160">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="49189-160">Additional resources</span></span>

* [<span data-ttu-id="49189-161">Java-API-Referenz</span><span class="sxs-lookup"><span data-stu-id="49189-161">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="49189-162">Azure- SignalR Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="49189-162">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
