---
title: Erstellen eines .NET Core-gRPC-Clients und -Servers in ASP.NET Core
author: juntaoluo
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
ms.author: johluo
ms.date: 04/08/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424255"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.

Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen Sie einen gRPC-Server.
> * Erstellen Sie einen gRPC-Client.
> * Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>Erstellen eines gRPC-Diensts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus. Alternativ dazu können Sie auch im Visual Studio-Menü **Datei** auf **Neu** > **Projekt** klicken.
* Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **gRPC-Dienst** aus, und klicken Sie auf **Weiter**:

  ![Dialogfeld „Neues Projekt erstellen“](~/tutorials/grpc/grpc-start/static/cnp.png)

* Nennen Sie das Projekt **GrpcGreeter**. Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.
* Wählen Sie **Erstellen** aus.
* Im Dialogfeld **Neuen gRPC-Dienst erstellen**:
  * Die **gRPC-Dienst** ist ausgewählt.
  * Wählen Sie **Erstellen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie die folgenden Befehle aus:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.
  * Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.

  Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**
* Wählen Sie **Ja**.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie über ein Terminal die folgenden Befehle aus:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Durch die obigen Befehle wird über die [.NET Core-CLI](/dotnet/core/tools/dotnet) ein gRPC-Dienst erstellt.

### <a name="open-the-project"></a>Öffnen des Projekts

Klicken Sie in Visual Studio auf **Datei** > **Öffnen**, und wählen Sie die Datei *GrpcGreeter.csproj* aus.

---

### <a name="run-the-service"></a>Ausführen des Diensts

  [!INCLUDE[](~/includes/run-the-app.md)]

In den Protokollen wird vermerkt, dass der Dienst auf `https://localhost:5001` lauscht.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Die gRPC-Vorlage ist für die Verwendung von [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) konfiguriert. gRPC-Clients müssen zum Aufrufen des Servers HTTPS verwenden.
>
> macOS unterstützt ASP.NET Core gRPC mit TLS nicht. Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

*GrpcGreeter*-Projektdateien:

* *greet.proto*: Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert. Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).
* *Services*-Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.
* *appSettings.json*: Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.
* *Program.cs*: Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird. Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Erstellen des gRPC-Clients in einer .NET-Konsolen-App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Öffnen Sie eine zweite Instanz von Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
* Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Konsolen-App (.NET Core)** aus, und klicken Sie auf **Weiter**.
* Geben Sie im Textfeld **Projektname** den Namen **GrpcGreeterClient** ein, und klicken Sie auf **Erstellen**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie die folgenden Befehle aus:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Befolgen Sie die Anweisungen unter [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution), um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.

---

### <a name="add-required-packages"></a>Hinzufügen von erforderlichen Paketen

Das gRPC-Clientprojekt erfordert die folgenden Pakete:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält. Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.

#### <a name="pmc-option-to-install-packages"></a>PMC-Option zum Installieren von Paketen

* Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.
* Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `cd GrpcGreeterClient` aus, um zu dem Verzeichnis zu wechseln, in dem sich die *GrpcGreeterClient.csproj*-Dateien befinden.
* Führen Sie die folgenden Befehle aus:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Option „NuGet-Pakete verwalten“ zum Installieren von Paketen

* Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
* Wählen Sie die Registerkarte **Durchsuchen** aus.
* Geben Sie **Grpc.Net.Client** in das Suchfeld ein.
* Wählen Sie das Paket **Grpc.Net.Client** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.
* Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner **Pakete** unter **Lösungspad** > **Pakete hinzufügen**.
* Geben Sie **Grpc.Net.Client** in das Suchfeld ein.
* Wählen Sie das Paket **Grpc.Net.Client** im Ergebnisbereich aus, und wählen Sie **Paket hinzufügen** aus.
* Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.

---

### <a name="add-greetproto"></a>Fügen Sie „greet.proto“ hinzu:

* Erstellen Sie einen *Protos*-Ordner im gRPC-Clientprojekt.
* Kopieren Sie die Datei *Protos\greet.proto* aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.
* Ändern Sie den Namespace in der Datei `greet.proto` in den Namespace des Projekts:

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj*:

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.

  # <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Extras** > **Datei bearbeiten** aus.

  ---

* Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei *greet.proto* verweist:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Erstellen des Greeter-Clients

Erstellen Sie das Projekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen. Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.

Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.

Der Greeter-Client wird folgendermaßen erstellt:

* Instanziieren eines `GrpcChannel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält
* Verwenden von `GrpcChannel` zum Erstellen des Greeter-Clients:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Der Greeter-Client ruft die asynchrone Methode `SayHello` auf. Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.
* Drücken Sie im `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Client ohne den Debugger zu starten.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Starten Sie den Greeterdienst.
* Starten Sie den Client.


# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Starten Sie den Greeterdienst.
* Starten Sie den Client.

---

Der Client sendet eine Begrüßungsnachricht an den Dienst, die den Namen des Diensts enthält: *GreeterClient*. Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus. Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Der gRPC-Dienst zeichnet die Details des erfolgreichen Aufrufs in den Protokollen auf, die an die Eingabeaufforderung ausgegeben werden:

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> Der Code in diesem Artikel erfordert das ASP.NET Core-HTTPS-Entwicklungszertifikat, um den gRPC-Dienst zu sichern. Wenn auf dem .NET gRPC-Client ein Fehler mit der Meldung `The remote certificate is invalid according to the validation procedure.` oder `The SSL connection could not be established.` auftritt, wird das Entwicklungszertifikat als nicht vertrauenswürdig eingestuft. Informationen zum Beheben dieses Problems finden Sie unter [Aufrufen eines gRPC-Diensts mit einem nicht vertrauenswürdigen/ungültigen Zertifikat](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Nächste Schritte

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
