---
title: Testen von Diensten mit gRPC-Tools
author: jamesnk
description: Hier erfahren Sie, wie sie Dienste mit gRPC-Tools testen. gRPCurl ist ein Befehlszeilentool für die Interaktion mit gRPC-Diensten. gRPCui ist eine interaktive Webbenutzeroberfläche.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594366"
---
# <a name="test-services-with-grpc-tools"></a>Testen von Diensten mit gRPC-Tools

Von [James Newton-King](https://twitter.com/jamesnk)

Für gRPC gibt es Tools, mit deren Hilfe Entwickler Dienste testen können, ohne Client-Apps zu erstellen. [gRPCurl](https://github.com/fullstorydev/grpcurl) ist ein Befehlszeilentool, das die Interaktion mit gRPC-Diensten ermöglicht. [gRPCui](https://github.com/fullstorydev/grpcui) stellt eine interaktive Webbenutzeroberfläche für gRPC bereit.

In diesem Artikel wird Folgendes erläutert:

* Herunterladen und Installieren von gRPCurl und gRPCui
* Einrichten einer gRPC-Reflexion mit einer gRPC ASP.NET Core-App
* Ermitteln und Testen von gRPC-Diensten mit `grpcurl`
* Interagieren mit gRPC-Diensten über einen Browser mithilfe von `grpcui`

## <a name="about-grpcurl"></a>Informationen zu gRPCurl

gRPCurl ist ein von der gRPC-Community erstelltes Befehlszeilentool. Es bietet folgende Features:

* Aufrufen von gRPC-Diensten wie Streamingdienste
* Dienstermittlung mithilfe der [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)
* Auflisten und Beschreiben von gRPC-Diensten
* Kann mit sicheren Servern (TLS-Servern) und unsicheren (unverschlüsselten) Servern verwendet werden.

Informationen zum Herunterladen und Installieren von `grpcurl` finden Sie auf der [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl#installation).

## <a name="setup-grpc-reflection"></a>Einrichten einer gRPC-Reflexion

`grpcurl` muss den Protobuf-Vertrag für Dienste kennen, bevor diese aufgerufen werden können. Hierfür gibt es zwei Möglichkeiten:

* Verwenden der gRPC-Reflexion zur Ermittlung von Dienstverträgen
* Angeben von *PROTO*-Dateien in Befehlszeilenargumenten

Es ist einfacher, gRPCurl mit gRPC-Reflexion und Dienstermittlung zu verwenden. gRPC ASP.NET Core bietet mit dem [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)-Pakte integrierte Unterstützung für gRPC-Reflexion. So konfigurieren Sie die Reflexion in einer App:

* Fügen Sie einen `Grpc.AspNetCore.Server.Reflection`-Paketverweis hinzu.
* Registrieren Sie die Reflexion in *Startup.cs*:
  * `AddGrpcReflection` zum Registrieren von Diensten, die die Reflexion ermöglichen
  * `MapGrpcReflectionService` zum Hinzufügen des Dienstendpunkts der Reflexion

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a>Verwenden Sie `grpcurl`

Das Argument `-help` erläutert `grpcurl`-Befehlszeilenoptionen:

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a>Ermitteln von Diensten

Verwenden Sie das Verb `describe`, um die vom Server definierten Dienste anzuzeigen:

```powershell
> grpcurl.exe localhost:5001 describe
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
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a>Aufrufen von gRPC-Diensten

Rufen Sie einen gRPC-Dienst auf, indem Sie einen Dienst- oder Methodennamen zusammen mit einem JSON-Argument angeben, das die Anforderungsnachricht darstellt. Der JSON-Code wird in Protobuf konvertiert und an den Dienst gesendet.

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

Für das vorherige Beispiel gilt Folgendes:

* Das Argument `-d` gibt eine Anforderungsnachricht mit JSON an. Dieses Argument muss vor der Serveradresse und dem Methodennamen angegeben werden.
* Ruft die `SayHello`-Methode für den `greeter.Greeter`-Dienst auf.
* Druckt die Antwortnachricht als JSON-Code.

## <a name="about-grpcui"></a>Informationen zu gRPCui

gRPCui ist eine interaktive Webbenutzeroberfläche für gRPC. Es basiert auf gRPCurl und stellt eine grafische Benutzeroberfläche zum Ermitteln und Testen von gRPC-Diensten bereit – vergleichbar mit Postman bei HTTP-Tools.

Informationen zum Herunterladen und Installieren von `grpcui` finden Sie auf der [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui#installation).

## <a name="using-grpcui"></a>Verwenden von `grpcui`

Führen Sie `grpcui` aus, wobei mit der Serveradresse als Argument interagiert wird.

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

Das Tool ruft in der interaktiven Webbenutzeroberfläche ein Browserfenster auf. gRPC-Dienste werden mithilfe der gRPC-Reflexion automatisch ermittelt.

![gRPCui-Webbenutzeroberfläche](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl)
* [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui)
* [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
