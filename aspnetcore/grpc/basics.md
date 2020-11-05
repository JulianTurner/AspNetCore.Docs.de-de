---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von gRPC-Diensten mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061014"
---
# <a name="grpc-services-with-c"></a>gRPC-Dienste mit C\#

In diesem Artikel werden die Konzepte erläutert, die zum Schreiben von [gRPC](https://grpc.io/docs/guides/)-Apps in C# erforderlich sind. Die hier behandelten Themen gelten für gRPC-Apps, die auf [C-Core](https://grpc.io/blog/grpc-stacks) und auf ASP.NET Core basieren.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>PROTO-Datei

gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung. Protokollpuffer (protobuf) werden standardmäßig als IDL (Interface Definition Language) verwendet. Die *\*.proto* -Datei enthält Folgendes:

* Die Definition des gRPC-Diensts
* Die zwischen Clients und Servern übermittelten Nachrichten

Weitere Informationen zur Syntax von protobuf-Dateien finden Sie unter <xref:grpc/protobuf>.

Sehen Sie sich beispielsweise die Datei *greet.proto* an, die in den [ersten Schritten mit dem gRPC-Dienst](xref:tutorials/grpc/grpc-start) verwendet wird:

* Die Datei definiert einen `Greeter`-Dienst.
* Der `Greeter`-Dienst definiert einen `SayHello`-Aufruf.
* `SayHello` sendet eine `HelloRequest`-Nachricht und empfängt eine `HelloReply`-Nachricht:

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>Hinzufügen einer PROTO-Datei zu einer C\#-App

Die *\*.proto* -Datei wird in ein Projekt eingefügt, indem sie zur Elementgruppe `<Protobuf>` hinzugefügt wird:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Durch einen `<Protobuf>`-Verweis werden standardmäßig ein konkreter Client und eine Dienstbasisklasse generiert. Mit dem Attribut `GrpcServices` des Verweiselements kann das Generieren von C#-Objekten beschränkt werden. Gültige Optionen für `GrpcServices` sind:

* `Both` (Standardwert, wenn nicht vorhanden)
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>C#-Toolunterstützung für PROTO-Dateien

Das Toolpaket [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) ist zum Generieren der C#-Ressourcen aus *\*.proto* -Dateien erforderlich. Die generierten Ressourcen (Dateien):

* Werden jedes Mal nach Bedarf generiert, wenn der Buildvorgang für das Projekt ausgeführt wird.
* Werden nicht zum Projekt hinzugefügt oder in der Quellcodeverwaltung eingecheckt.
* Sind Buildartefakte, die im *obj* -Verzeichnis enthalten sind.

Dieses Paket ist für die Server- und Clientprojekte erforderlich. Das Metapaket `Grpc.AspNetCore` enthält einen Verweis auf `Grpc.Tools`. Serverprojekte können `Grpc.AspNetCore` mithilfe des Paket-Managers in Visual Studio oder durch Hinzufügen von `<PackageReference>` zur Projektdatei hinzufügen:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

Clientprojekte sollten direkt auf `Grpc.Tools` sowie auf die anderen zum Verwenden des gRPC-Clients erforderlichen Pakete verweisen. Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert:

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>Generierte C#-Ressourcen

Das Toolpaket generiert die C#-Typen, die die Nachrichten darstellen, die in den enthaltenen *\*.proto* -Dateien definiert werden.

Für serverseitige Ressourcen wird ein abstrakter Dienstbasistyp generiert. Der Basistyp enthält die Definitionen für alle gRPC-Aufrufe, die in der *PROTO* -Datei enthalten sind. Erstellen Sie eine konkrete Dienstimplementierung, die von diesem Basistyp abgeleitet wird und die Logik für die gRPC-Aufrufe implementiert. Für das zuvor beschriebene Beispiel `greet.proto` wird ein abstrakter `GreeterBase`-Typ generiert, der eine virtuelle `SayHello`-Methode enthält. Eine konkrete `GreeterService`-Implementierung überschreibt die Methode und implementiert die Logik, die den gRPC-Aufruf verarbeitet.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

Für clientseitige Ressourcen wird ein konkreter Clienttyp generiert. Die gRPC-Aufrufe in der *PROTO* -Datei werden in Methoden für den konkreten Typ übersetzt, die aufgerufen werden können. Für das zuvor beschriebene Beispiel `greet.proto` wird ein konkreter `GreeterClient`-Typ generiert. Rufen Sie `GreeterClient.SayHelloAsync` auf, um einen gRPC-Aufruf an den Server zu initiieren.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

Server- und Clientressourcen werden standardmäßig für alle *\*.proto* -Dateien generiert, die in der Elementgruppe `<Protobuf>` enthalten sind. Das `GrpcServices`-Attribut wird auf `Server` festgelegt, um sicherzustellen, dass nur die Serverressourcen in einem Serverprojekt generiert werden.

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

Entsprechend wird das Attribut bei Clientprojekten auf `Client` festgelegt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
