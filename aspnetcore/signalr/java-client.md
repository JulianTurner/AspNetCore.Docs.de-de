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
# <a name="aspnet-core-no-locsignalr-java-client"></a>ASP.net Core SignalR Java-Client

Von [Mikael Mengistu](https://twitter.com/MikaelM_12)

Der Java-Client ermöglicht das Herstellen einer Verbindung mit einem ASP.net Core SignalR Server aus Java-Code, einschließlich Android-Apps. Wie der [JavaScript-Client](xref:signalr/javascript-client) und der [.NET-Client](xref:signalr/dotnet-client)ermöglicht der Java-Client das empfangen und Senden von Nachrichten an einen Hub in Echtzeit. Der Java-Client ist in ASP.net Core 2,2 und höher verfügbar.

Die Java-Konsolenanwendung, auf die in diesem Artikel verwiesen wird, verwendet den SignalR Java-Client.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-java-client-package"></a>Installieren des SignalR Java-Client Pakets

Die jar *-Datei signalr-1.0.0* ermöglicht Clients das Herstellen einer Verbindung mit SignalR Hubs. Die aktuelle JAR-Datei-Versionsnummer finden Sie in den [Maven-Suchergebnissen](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).

Wenn Sie gradle verwenden, fügen Sie die folgende Zeile zum- `dependencies` Abschnitt der Datei " *Build. gradle* " hinzu:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Wenn Sie Maven verwenden, fügen Sie die folgenden Zeilen im- `<dependencies>` Element ihrer *pom.xml* Datei hinzu:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Zum Einrichten eines `HubConnection` `HubConnectionBuilder` sollte verwendet werden. Die Hub-URL und die Protokollebene können während der Verbindungs Herstellung konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden vor Aufrufen `build` . Starten Sie die Verbindung mit `start` .

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>Hub-Methoden vom Client abrufen

Ein Aufruf von `send` Ruft eine Hub-Methode auf. Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `send` .

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird. Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Client Methoden aus Hub abrufen

Verwenden `hubConnection.on` Sie, um Methoden auf dem Client zu definieren, die der Hub abrufen kann. Definieren Sie die Methoden nach dem Aufbau, aber vor dem Starten der Verbindung.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>Hinzufügen der Protokollierung

Der SignalR Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung. Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden. Der folgende Code Ausschnitt zeigt, wie `java.util.logging` mit dem Java- SignalR Client verwendet wird.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann sicher ignoriert werden.

## <a name="android-development-notes"></a>Hinweise zur Android-Entwicklung

Beachten Sie bei der Android SDK Kompatibilität der SignalR Client Features die folgenden Punkte, wenn Sie die Ziel Android SDK Version angeben:

* Der SignalR Java-Client kann auf Android-API-Ebene 16 und höher ausgeführt werden.
* Zum Herstellen einer Verbindung über den Azure SignalR -Dienst sind Android-API-Ebene 20 und höher erforderlich, da der [Azure- SignalR Dienst](/azure/azure-signalr/signalr-overview) TLS 1,2 erfordert und keine SHA-1-basierten Verschlüsselungs Sammlungen unterstützt. Android hat [Unterstützung für die Verschlüsselungs Sammlungen SHA-256 (und höher)](https://developer.android.com/reference/javax/net/ssl/SSLSocket) auf API-Ebene 20 hinzugefügt.

## <a name="configure-bearer-token-authentication"></a>Konfigurieren der bearertokenauthentifizierung

Im SignalR Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie dem [httphubconnectionbuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)eine "zugriffstokenfactory" bereitstellen. Verwenden Sie [withaccesstokenfactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) , um eine [einzelne \<String> ](https://reactivex.io/documentation/single.html) [rxjava](https://github.com/ReactiveX/RxJava) -Instanz bereitzustellen. Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>Übergeben von Klassen Informationen in Java

Wenn die- `on` ,- `invoke` oder- `stream` Methoden von `HubConnection` im Java-Client aufgerufen werden, sollten Benutzer anstelle eines-Objekts ein-Objekt übergeben, `Type` `Class<?>` um alle `Object` an die-Methode übergebenen generischen zu beschreiben. Eine `Type` kann mithilfe der angegebenen-Klasse abgerufen werden `TypeReference` . Mit `Foo<T>` dem folgenden Code wird beispielsweise eine benutzerdefinierte generische Klasse mit dem Namen abgerufen `Type` :

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

Bei nicht-Generika, z. b. primitiven oder anderen nicht parametrisierten Typen wie `String` , können Sie einfach das integrierte verwenden `.class` .

Wenn Sie eine dieser Methoden mit einem oder mehreren Objekttypen aufrufen, verwenden Sie die Generika Syntax, wenn Sie die Methode aufrufen. Wenn Sie z. b. einen `on` Handler für eine Methode mit dem Namen registrieren `func` , die als Argumente für eine Zeichenfolge und ein- `Foo<String>` Objekt verwendet, verwenden Sie den folgenden Code, um eine Aktion zum Drucken der Argumente festzulegen:

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

Diese Konvention ist erforderlich, da wir `Object.getClass` aufgrund der typlöschung in Java keine umfassenden Informationen zu komplexen Typen mit der-Methode abrufen können. Wenn Sie z. b. für einen aufrufen, wird `getClass` `ArrayList<String>` nicht zurückgegeben `Class<ArrayList<String>>` , sondern `Class<ArrayList>` der Deserialisierer verfügt nicht über genügend Informationen, um eine eingehende Nachricht ordnungsgemäß zu deserialisieren. Das gleiche gilt für benutzerdefinierte Objekte.

::: moniker-end

## <a name="known-limitations"></a>Bekannte Einschränkungen

::: moniker range=">= aspnetcore-5.0"

* Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* Der Transport Fall Back und der Transport der Server gesendeten Ereignisse werden nicht unterstützt.
* Nur das JSON-Protokoll wird unterstützt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Nur das JSON-Protokoll wird unterstützt.
* Nur der websockets-Transport wird unterstützt.
* Streaming wird noch nicht unterstützt.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Java-API-Referenz](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure- SignalR Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)
