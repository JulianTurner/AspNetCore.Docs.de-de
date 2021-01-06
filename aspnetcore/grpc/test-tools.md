---
title: Testen von gRPC-Diensten mit gRPCurl in ASP.NET Core
author: jamesnk
description: Hier erfahren Sie, wie sie Dienste mit gRPC-Tools testen. gRPCurl ist ein Befehlszeilentool für die Interaktion mit gRPC-Diensten. gRPCui ist eine interaktive Webbenutzeroberfläche.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058791"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a>Testen von gRPC-Diensten mit gRPCurl in ASP.NET Core

Von [James Newton-King](https://twitter.com/jamesnk)

Für gRPC sind Tools verfügbar, mit deren Hilfe Entwickler Dienste testen können, ohne Client-Apps zu erstellen:

* [gRPCurl](https://github.com/fullstorydev/grpcurl) ist ein Befehlszeilentool, das die Interaktion mit gRPC-Diensten ermöglicht.
* [gRPCui](https://github.com/fullstorydev/grpcui) baut auf gRPCurl auf und fügt eine interaktive Webbenutzeroberfläche für gRPC hinzu, ähnlich wie Tools wie Postman und die Swagger-Benutzeroberfläche.

In diesem Artikel wird Folgendes erläutert:

* Herunterladen und Installieren von gRPCurl und gRPCui
* Einrichten von gRPC-Reflexion mit einer gRPC ASP.NET Core-App.
* Ermitteln und Testen von gRPC-Diensten mit `grpcurl`
* Interagieren mit gRPC-Diensten über einen Browser mithilfe von `grpcui`

## <a name="about-grpcurl"></a>Informationen zu gRPCurl

gRPCurl ist ein von der gRPC-Community erstelltes Befehlszeilentool. Es bietet folgende Features:

* Aufrufen von gRPC-Diensten wie Streamingdienste
* Dienstermittlung mithilfe der [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)
* Auflisten und Beschreiben von gRPC-Diensten
* Kann mit sicheren Servern (TLS-Servern) und unsicheren (unverschlüsselten) Servern verwendet werden.

Informationen zum Herunterladen und Installieren von `grpcurl` finden Sie auf der [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl#installation).

![gRPCurl-Befehlszeile](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a>Einrichten von gRPC-Reflexion

`grpcurl` muss den Protobuf-Vertrag für Dienste kennen, bevor diese aufgerufen werden können. Hierfür gibt es zwei Möglichkeiten:

* Richten Sie [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) auf dem Server ein. gRPCurl ermittelt automatisch Dienstverträge.
* Geben Sie `.proto`-Dateien in Befehlszeilenargumenten für gRPCurl an.

Es ist einfacher, gRPCurl mit gRPC-Reflexion zu verwenden. gRPC-Reflexion fügt der App einen neuen gRPC-Dienst hinzu, der von Clients zum Ermitteln von Diensten aufgerufen werden kann.

gRPC ASP.NET Core bietet mit dem [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)-Pakte integrierte Unterstützung für gRPC-Reflexion. So konfigurieren Sie die Reflexion in einer App:

* Fügen Sie einen `Grpc.AspNetCore.Server.Reflection`-Paketverweis hinzu.
* Registrieren Sie die Reflexion in `Startup.cs`:
  * `AddGrpcReflection` zum Registrieren von Diensten, die die Reflexion ermöglichen
  * `MapGrpcReflectionService` zum Hinzufügen eines Dienstendpunkts der Reflexion.

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

Wenn gRPC-Reflexion eingerichtet ist:

* Der Server-App wird ein gRPC-Reflexionsdienst hinzugefügt.
* Client-Apps, die gRPC-Reflexion unterstützen, können den Reflexionsdienst zum Ermitteln von vom Server gehosteten Diensten aufrufen.
* gRPC-Dienste werden weiterhin vom Client aufgerufen. Reflexion ermöglicht nur Dienstermittlung und umgeht nicht die serverseitige Sicherheit. Endpunkte, die durch [Authentifizierung und Autorisierung](xref:grpc/authn-and-authz) geschützt sind, erfordern für einen erfolgreichen Aufruf, dass der Aufrufer Anmeldeinformationen für den Endpunkt übergibt.

## <a name="use-grpcurl"></a>Verwenden Sie `grpcurl`

Das Argument `-help` erläutert `grpcurl`-Befehlszeilenoptionen:

```console
$ grpcurl -help
```

### <a name="discover-services"></a>Ermitteln von Diensten

Verwenden Sie das Verb `describe`, um die vom Server definierten Dienste anzuzeigen:

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

Für das vorherige Beispiel gilt Folgendes:

* Führt das Verb `describe` auf Server `localhost:5001` aus.
* Druckt von der gRPC-Reflexion zurückgegebene Dienste und Methoden.
  * `Greeter` ist ein von der App implementierter Dienst.
  * `ServerReflection` ist der vom `Grpc.AspNetCore.Server.Reflection`-Paket hinzugefügter Dienst.

Kombinieren Sie `describe` mit einem Dienst-, Methoden- oder Meldungsnamen, um die entsprechenden Details anzuzeigen:

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Aufrufen von gRPC-Diensten

Rufen Sie einen gRPC-Dienst auf, indem Sie einen Dienst- oder Methodennamen zusammen mit einem JSON-Argument angeben, das die Anforderungsnachricht darstellt. Der JSON-Code wird in Protobuf konvertiert und an den Dienst gesendet.

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Im vorherigen Beispiel:

* Das Argument `-d` gibt eine Anforderungsnachricht mit JSON an. Dieses Argument muss vor der Serveradresse und dem Methodennamen angegeben werden.
* Ruft die `SayHello`-Methode für den `greeter.Greeter`-Dienst auf.
* Druckt die Antwortnachricht als JSON-Code.

## <a name="about-grpcui"></a>Informationen zu gRPCui

gRPCui ist eine interaktive Webbenutzeroberfläche für gRPC. Sie basiert auf gRPCurl und stellt eine grafische Benutzeroberfläche zum Ermitteln und Testen von gRPC-Diensten bereit – vergleichbar mit HTTP-Tools wie Postman oder die Swagger-Benutzeroberfläche.

Informationen zum Herunterladen und Installieren von `grpcui` finden Sie auf der [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Verwenden von `grpcui`

Führen Sie `grpcui` mit der Serveradresse aus, mit der als Argument interagiert wird:

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Das Tool ruft in der interaktiven Webbenutzeroberfläche ein Browserfenster auf. gRPC-Dienste werden mithilfe der gRPC-Reflexion automatisch ermittelt.

![gRPCui-Webbenutzeroberfläche](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl)
* [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
