---
title: gRPC für .NET-Konfiguration
author: jamesnk
description: Erfahren Sie, wie Sie gRPC für .NET-Apps konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/23/2020
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
uid: grpc/configuration
ms.openlocfilehash: 617c042c628dc431391f39c2ecb2d2f9c9463fa5
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417590"
---
# <a name="grpc-for-net-configuration"></a>gRPC für .NET-Konfiguration

## <a name="configure-services-options"></a>Konfigurieren von Dienstoptionen

gRPC-Dienste werden mit `AddGrpc` in *Startup.cs* konfiguriert. In der folgenden Tabelle werden die Optionen für die Konfiguration von gRPC-Diensten beschrieben:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | Die maximale Nachrichtengröße in Bytes, die vom Server gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| MaxReceiveMessageSize | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Server empfangen werden kann. Wenn der Server eine Nachricht erhält, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Eine Erhöhung dieses Werts ermöglicht es dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| EnableDetailedErrors | `false` | Bei `true` werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Dienstmethode ausgelöst wird. Der Standardwert ist `false`. Das Festlegen von `EnableDetailedErrors` auf `true` kann zum Verlust von vertraulichen Informationen führen. |
| CompressionProviders | gzip | Eine Sammlung von Komprimierungsanbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Es können benutzerdefinierte Komprimierungsanbieter erstellt und der Sammlung hinzugefügt werden. Die standardmäßig konfigurierten Anbieter unterstützen die **gzip**-Komprimierung. |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | Der Komprimierungsalgorithmus, der zur Komprimierung der vom Server gesendeten Nachrichten verwendet wird. Der Algorithmus muss mit einem Komprimierungsanbieter in `CompressionProviders` übereinstimmen. Damit der Algorithmus eine Antwort komprimieren kann, muss der Client angeben, dass er den Algorithmus unterstützt, indem er ihn im **grpc-accept-encoding**-Header sendet. |
| ResponseCompressionLevel | `null` | Die Komprimierungsstufe, die zur Komprimierung der vom Server gesendeten Nachrichten verwendet wird. |
| Interceptors | Keine | Eine Sammlung von Interceptors, die bei jedem gRPC-Aufruf ausgeführt werden. Interceptors werden in der Reihenfolge ausgeführt, in der sie registriert sind. Global konfigurierte Interceptors werden vor Interceptors ausgeführt, die für einen einzelnen Dienst konfiguriert sind. Weitere Informationen über gRPC-Interceptors finden Sie unter [gRPC-Interceptors im Vergleich zur Middleware](xref:grpc/migration#grpc-interceptors-vs-middleware). |
| IgnoreUnknownServices | `false` | Bei `true` geben Aufrufe unbekannter Dienste und Methoden nicht den Status **UNIMPLEMENTED** zurück, und die Anforderung wird an die nächste registrierte Middleware in ASP.NET Core übergeben. |

Optionen können für alle Dienste konfiguriert werden, indem ein Optionsdelegat für den `AddGrpc`-Aufruf in `Startup.ConfigureServices` bereitgestellt wird:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

Optionen für einen einzelnen Dienst haben Vorrang vor den globalen Optionen in `AddGrpc` und können mit `AddServiceOptions<TService>` konfiguriert werden:

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>Konfigurieren von Clientoptionen

Die gRPC-Clientkonfiguration ist auf `GrpcChannelOptions` festgelegt. In der folgenden Tabelle werden die Optionen für die Konfiguration von gRPC-Kanälen beschrieben:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| HttpHandler | Neue Instanz | Der `HttpMessageHandler` wird für gRPC-Aufrufe verwendet. Ein Client kann so eingestellt werden, dass er einen benutzerdefinierten `HttpClientHandler` konfiguriert oder der HTTP-Pipeline zusätzliche Handler für gRPC-Aufrufe hinzufügt. Wenn kein `HttpMessageHandler` angegeben ist, wird eine neue `HttpClientHandler`-Instanz für den Kanal mit automatischer Entfernung erstellt. |
| HttpClient | `null` | Der `HttpClient` wird für gRPC-Aufrufe verwendet. Diese Einstellung ist eine Alternative zum `HttpHandler`. |
| DisposeHttpClient | `false` | Wenn diese Option auf `true` festgelegt und ein `HttpMessageHandler` oder `HttpClient` angegeben ist, wird entweder der `HttpHandler` oder `HttpClient` entfernt, wenn der `GrpcChannel` entfernt wird. |
| LoggerFactory | `null` | Die `LoggerFactory`, die vom Client zur Protokollierung von Informationen über gRPC-Aufrufe verwendet wird. Eine `LoggerFactory`-Instanz kann aus der Abhängigkeitsinjektion aufgelöst oder mit `LoggerFactory.Create` erstellt werden. Beispiele für die Konfiguration der Protokollierung finden Sie unter <xref:grpc/diagnostics#grpc-client-logging>. |
| MaxSendMessageSize | `null` | Die maximale Nachrichtengröße in Bytes, die vom Client gesendet werden kann. Der Versuch, eine Nachricht zu senden, die die konfigurierte maximale Nachrichtengröße überschreitet, führt zu einer Ausnahme. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| <span style="word-break:normal;word-wrap:normal">MaxReceiveMessageSize</span> | 4 MB | Die maximale Nachrichtengröße in Bytes, die vom Client empfangen werden kann. Wenn der Client eine Nachricht erhält, die diesen Grenzwert überschreitet, wird eine Ausnahme ausgelöst. Eine Erhöhung dieses Werts ermöglicht es dem Client, größere Nachrichten zu empfangen, kann sich jedoch negativ auf den Arbeitsspeicherverbrauch auswirken. Wenn dieser Wert auf `null` festgelegt wird, ist die Größe der Nachricht unbegrenzt. |
| Anmeldeinformationen | `null` | Eine `ChannelCredentials`-Instanz. Anmeldeinformationen werden verwendet, um Authentifizierungsmetadaten zu gRPC-Aufrufen hinzuzufügen. |
| CompressionProviders | gzip | Eine Sammlung von Komprimierungsanbietern, die zum Komprimieren und Dekomprimieren von Nachrichten verwendet werden. Es können benutzerdefinierte Komprimierungsanbieter erstellt und der Sammlung hinzugefügt werden. Die standardmäßig konfigurierten Anbieter unterstützen die **gzip**-Komprimierung. |
| ThrowOperationCanceledOnCancellation | `false` | Wenn dieser Wert auf `true` festgelegt ist, wird <xref:System.OperationCanceledException> ausgelöst, wenn ein Aufruf abgebrochen oder seine Frist überschritten wird. |

Der folgende Code

* Legt die maximale Größe der Nachricht zum Senden und Empfangen für den Kanal fest.
* Erstellt einen Client.

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
