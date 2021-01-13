---
title: Code-First-gRPC-Dienste und -Clients mit .NET
author: jamesnk
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von Code-First-gRPC mit .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880618"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>Code-First-gRPC-Dienste und -Clients mit .NET

Von [James Newton-King](https://twitter.com/jamesnk) und [Marc Gravell](https://twitter.com/marcgravell)

Code-First-gRPC verwendet .NET-Typen, um Dienst- und Nachrichtenverträge zu definieren.

Code-First ist eine gute Wahl, wenn ein gesamtes System .NET verwendet:

* .NET-Dienst- und -Datenvertragstypen können von .NET-Server und -Clients gemeinsam genutzt werden.
* Verhindert, dass Verträge in `.proto`-Dateien und Codegenerierung definiert werden müssen.

Code-First sollte nicht in mehrsprachigen Systemen verwendet werden. .NET-Dienst- und -Datenvertragstypen können nicht mit Nicht-.NET-Plattformen verwendet werden. Um einen mit Code-First geschriebenen gRPC-Dienst aufzurufen, müssen andere Plattformen einen `.proto`-Vertrag erstellen, der mit dem Dienst übereinstimmt.

## <a name="protobuf-netgrpc"></a>protobuf-net.Grpc

> [!IMPORTANT]
> Hilfe zu protobuf-net.Grpc finden Sie auf der [protobuf-net.Grpc-Website](https://protobuf-net.github.io/protobuf-net.Grpc/), oder erstellen Sie ein Problem im [protobuf-net.Grpc-GitHub-Repository](https://github.com/protobuf-net/protobuf-net.Grpc).

[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) ist ein Communityprojekt und wird von Microsoft nicht unterstützt. Die Code-First-Unterstützung wird `Grpc.AspNetCore` und `Grpc.Net.Client` hinzugefügt. Es werden mit Attributen versehene .NET-Typen verwendet, um die gRPC-Dienste und -Nachrichten einer App zu definieren.

Der erste Schritt beim Erstellen eines Code-First-gRPC-Diensts ist die Definition des Codevertrags:

* Erstellen Sie ein neues Projekt, das von Server und Client gemeinsam genutzt wird.
* Fügen Sie einen [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc)-Paketverweis hinzu.
* Erstellen Sie Dienst- und Datenvertragstypen.

[!code-csharp[](code-first/Contracts.cs)]

Der vorangehende Code:

* Definiert `HelloRequest`- und `HelloReply`-Nachrichten.
* Definiert die `IGreeterService`-Vertragsschnittstelle mit der unären `SayHelloAsync`-gRPC-Methode.

Der Dienstvertrag wird auf dem Server implementiert und vom Client aufgerufen. Auf Dienstschnittstellen definierte Methoden müssen mit bestimmten Signaturen identisch sein, in Abhängigkeit davon, ob sie unär, Serverstreaming, Clientstreaming oder bidirektionales Streaming sind.

Weitere Informationen zum Definieren von Dienstverträgen finden Sie in der [Dokumentation zu den ersten Schritten mit protobuf-net.GrpC](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).

## <a name="create-a-code-first-grpc-service"></a>Erstellen eines Code-First-gRPC-Diensts

So fügen Sie einen gRPC-Code-First-Dienst einer ASP.NET Core-App hinzu:

* Fügen Sie einen [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore)-Paketverweis hinzu.
* Fügen Sie dem freigegebenen Code-Vertragsprojekt einen Verweis hinzu.

Erstellen Sie eine neue `GreeterService.cs`-Datei, und implementieren Sie die `IGreeterService`-Dienstschnittstelle:

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

Aktualisieren Sie die Datei `Startup.cs`:

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

Im obigen Code:

* `AddCodeFirstGrpc` registriert Dienste, die Code-First aktivieren.
* `MapGrpcService<GreeterService>` fügt den Code-First-Dienstendpunkt hinzu.

Mit Code-First und `.proto`-Dateien implementierte gRPC-Dienste können in der gleichen App nebeneinander vorhanden sein. Für alle gRPC-Dienste wird die [gRPC-Dienstkonfiguration](xref:grpc/configuration#configure-services-options) verwendet.

## <a name="create-a-code-first-grpc-client"></a>Erstellen eines Code-First-gRPC-Clients

Ein Code-First-gRPC-Client verwendet den Dienstvertrag, um gRPC-Dienste aufzurufen. So rufen Sie einen gRPC-Dienst mit einem Code-First-Client auf:

* Fügen Sie einen [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc)-Paketverweis hinzu.
* Fügen Sie dem freigegebenen Code-Vertragsprojekt einen Verweis hinzu.

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

Der vorangehende Code:

* Erstellt einen gRPC-Kanal.
* Erstellt einen Code-First-Client aus dem Kanal mit der `CreateGrpcService<IGreeterService>`-Erweiterungsmethode.
* Ruft den gRPC-Dienst mit `SayHelloAsync` auf.

Ein Code-First-gRPC-Client wird aus einem Kanal erstellt. Genau wie ein regulärer Client verwendet ein Code-First-Client seine [Kanalkonfiguration](xref:grpc/configuration#configure-client-options).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [protobuf-net.Grpc-Website](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net.GrpC-GitHub-Repository](https://github.com/protobuf-net/protobuf-net.Grpc)
