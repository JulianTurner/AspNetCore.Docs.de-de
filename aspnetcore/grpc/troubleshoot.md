---
title: Problembehandlung für gRPC in .NET Core
author: jamesnk
description: Beheben Sie Fehler bei der Verwendung von gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
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
uid: grpc/troubleshoot
ms.openlocfilehash: 61d4d2204886f26b4ff55bc876825012809f1dfa
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253097"
---
# <a name="troubleshoot-grpc-on-net-core"></a><span data-ttu-id="82416-103">Problembehandlung für gRPC in .NET Core</span><span class="sxs-lookup"><span data-stu-id="82416-103">Troubleshoot gRPC on .NET Core</span></span>

<span data-ttu-id="82416-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="82416-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="82416-105">In diesem Dokument werden häufig auftretende Probleme bei der Entwicklung von gRPC-Apps in .NET behandelt.</span><span class="sxs-lookup"><span data-stu-id="82416-105">This document discusses commonly encountered problems when developing gRPC apps on .NET.</span></span>

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a><span data-ttu-id="82416-106">Nichtübereinstimmung der SSL-/TLS-Konfiguration von Client und Dienst</span><span class="sxs-lookup"><span data-stu-id="82416-106">Mismatch between client and service SSL/TLS configuration</span></span>

<span data-ttu-id="82416-107">In der gRPC-Vorlage und den gRPC-Beispielen werden gRPC-Dienste standardmäßig durch [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert.</span><span class="sxs-lookup"><span data-stu-id="82416-107">The gRPC template and samples use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) to secure gRPC services by default.</span></span> <span data-ttu-id="82416-108">gRPC-Clients müssen eine sichere Verbindung verwenden, um gesicherte gRPC-Dienste aufzurufen zu können.</span><span class="sxs-lookup"><span data-stu-id="82416-108">gRPC clients need to use a secure connection to call secured gRPC services successfully.</span></span>

<span data-ttu-id="82416-109">Sie können in den Protokollen, die beim Starten der App geschrieben werden, überprüfen, ob ein ASP.NET Core-gRPC-Dienst TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="82416-109">You can verify the ASP.NET Core gRPC service is using TLS in the logs written on app start.</span></span> <span data-ttu-id="82416-110">Der Dienst lauscht in diesem Fall auf einen HTTPS-Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="82416-110">The service will be listening on an HTTPS endpoint:</span></span>

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

<span data-ttu-id="82416-111">Der .NET Core-Client muss in der Serveradresse `https` verwenden, um Aufrufe über eine sichere Verbindung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="82416-111">The .NET Core client must use `https` in the server address to make calls with a secured connection:</span></span>

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

<span data-ttu-id="82416-112">Alle gRPC-Clientimplementierungen unterstützen TLS.</span><span class="sxs-lookup"><span data-stu-id="82416-112">All gRPC client implementations support TLS.</span></span> <span data-ttu-id="82416-113">Bei gRPC-Clients mit anderen Sprachen ist üblicherweise ein mit `SslCredentials` konfigurierter Kanal erforderlich.</span><span class="sxs-lookup"><span data-stu-id="82416-113">gRPC clients from other languages typically require the channel configured with `SslCredentials`.</span></span> <span data-ttu-id="82416-114">`SslCredentials` gibt das Zertifikat an, das der Client verwendet, und muss anstelle von unsicheren Anmeldeinformationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82416-114">`SslCredentials` specifies the certificate that the client will use, and it must be used instead of insecure credentials.</span></span> <span data-ttu-id="82416-115">Beispiele für das Konfigurieren der verschiedenen gRPC-Clientimplementierungen für die Verwendung von TLS finden Sie unter [gRPC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).</span><span class="sxs-lookup"><span data-stu-id="82416-115">For examples of configuring the different gRPC client implementations to use TLS, see [gRPC Authentication](https://www.grpc.io/docs/guides/auth/).</span></span>

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a><span data-ttu-id="82416-116">Aufrufen eines gRPC-Diensts mit einem nicht vertrauenswürdigen/ungültigen Zertifikat</span><span class="sxs-lookup"><span data-stu-id="82416-116">Call a gRPC service with an untrusted/invalid certificate</span></span>

<span data-ttu-id="82416-117">Der .NET-gRPC-Client erfordert, dass der Dienst über ein vertrauenswürdiges Zertifikat verfügt.</span><span class="sxs-lookup"><span data-stu-id="82416-117">The .NET gRPC client requires the service to have a trusted certificate.</span></span> <span data-ttu-id="82416-118">Die folgende Fehlermeldung wird zurückgegeben, wenn ein gRPC-Dienst ohne vertrauenswürdiges Zertifikat aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="82416-118">The following error message is returned when calling a gRPC service without a trusted certificate:</span></span>

> <span data-ttu-id="82416-119">Ausnahmefehler.</span><span class="sxs-lookup"><span data-stu-id="82416-119">Unhandled exception.</span></span> <span data-ttu-id="82416-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span><span class="sxs-lookup"><span data-stu-id="82416-120">System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.</span></span>
> <span data-ttu-id="82416-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure. (Ausnahmefehler. System.Net.Http.HttpRequestException: Es konnte keine SSL-Verbindung hergestellt werden, siehe interne Ausnahme. ---> System.Security.Authentication.AuthenticationException: Das Remotezertifikat ist laut Validierungsverfahren ungültig.)</span><span class="sxs-lookup"><span data-stu-id="82416-121">---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure.</span></span>

<span data-ttu-id="82416-122">Dieser Fehler wird möglicherweise angezeigt, wenn Sie Ihre App lokal testen und das ASP.NET Core-HTTPS-Entwicklungszertifikat nicht vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="82416-122">You may see this error if you are testing your app locally and the ASP.NET Core HTTPS development certificate is not trusted.</span></span> <span data-ttu-id="82416-123">Anweisungen zum Beheben dieses Problems finden Sie unter [Einstufen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="82416-123">For instructions to fix this issue, see [Trust the ASP.NET Core HTTPS development certificate on Windows and macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span></span>

<span data-ttu-id="82416-124">Wenn Sie einen gRPC-Dienst auf einem anderen Computer aufrufen und das Zertifikat nicht als vertrauenswürdig einstufen können, kann der gRPC-Client so konfiguriert werden, dass er das ungültige Zertifikat ignoriert.</span><span class="sxs-lookup"><span data-stu-id="82416-124">If you are calling a gRPC service on another machine and are unable to trust the certificate then the gRPC client can be configured to ignore the invalid certificate.</span></span> <span data-ttu-id="82416-125">Im folgenden Code wird [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) verwendet, um Aufrufe ohne vertrauenswürdiges Zertifikat zuzulassen:</span><span class="sxs-lookup"><span data-stu-id="82416-125">The following code uses [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) to allow calls without a trusted certificate:</span></span>

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> <span data-ttu-id="82416-126">Nicht vertrauenswürdige Zertifikate sollten nur während der App-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82416-126">Untrusted certificates should only be used during app development.</span></span> <span data-ttu-id="82416-127">Produktions-Apps sollten stets gültige Zertifikate verwenden.</span><span class="sxs-lookup"><span data-stu-id="82416-127">Production apps should always use valid certificates.</span></span>

## <a name="call-insecure-grpc-services-with-net-core-client"></a><span data-ttu-id="82416-128">Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client</span><span class="sxs-lookup"><span data-stu-id="82416-128">Call insecure gRPC services with .NET Core client</span></span>

<span data-ttu-id="82416-129">Wenn eine App .NET Core 3.x verwendet, sind weitere Konfigurationen erforderlich, um unsichere gRPC-Dienste mit dem .NET Core-Client aufrufen zu können.</span><span class="sxs-lookup"><span data-stu-id="82416-129">When an app is using .NET Core 3.x, additional configuration is required to call insecure gRPC services with the .NET Core client.</span></span> <span data-ttu-id="82416-130">Der gRPC-Client muss für den Parameter `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` `true` festlegen und in der Serveradresse `http` verwenden:</span><span class="sxs-lookup"><span data-stu-id="82416-130">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="82416-131">Bei .NET 5-Apps ist keine zusätzliche Konfiguration erforderlich. Damit jedoch unsichere gRPC-Dienste aufgerufen werden können, muss mindestens Version 2.32.0 von `Grpc.Net.Client` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82416-131">.NET 5 apps don't need additional configuration, but to call insecure gRPC services they must use `Grpc.Net.Client` version 2.32.0 or later.</span></span>

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a><span data-ttu-id="82416-132">ASP.NET Core-gRPC-App kann unter macOS nicht gestartet werden</span><span class="sxs-lookup"><span data-stu-id="82416-132">Unable to start ASP.NET Core gRPC app on macOS</span></span>

<span data-ttu-id="82416-133">Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht.</span><span class="sxs-lookup"><span data-stu-id="82416-133">Kestrel doesn't support HTTP/2 with TLS on macOS and older Windows versions such as Windows 7.</span></span> <span data-ttu-id="82416-134">In der ASP.NET Core-gRPC-Vorlage und den ASP.NET Core-gRPC-Beispielen wird standardmäßig TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="82416-134">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="82416-135">Wenn Sie versuchen, den gRPC-Server zu starten, wird folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="82416-135">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="82416-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'. (Eine Bindung an „https://localhost:5001“ auf der IPv4-Loopback-Schnittstelle kann nicht erfolgen: „HTTP/2 über TLS wird unter macOS aufgrund von fehlender ALPN-Unterstützung nicht unterstützt.“)</span><span class="sxs-lookup"><span data-stu-id="82416-136">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="82416-137">Konfigurieren Sie Kestrel und den gRPC-Client so, dass HTTP/2 *ohne* TLS verwendet wird, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="82416-137">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 *without* TLS.</span></span> <span data-ttu-id="82416-138">Dies sollten Sie nur während der Entwicklung tun.</span><span class="sxs-lookup"><span data-stu-id="82416-138">You should only do this during development.</span></span> <span data-ttu-id="82416-139">Wenn TLS nicht verwendet wird, führt dies dazu, dass gRPC-Nachrichten unverschlüsselt gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="82416-139">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="82416-140">Kestrel muss einen HTTP/2-Endpunkt ohne TLS in *Program.cs* konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="82416-140">Kestrel must configure an HTTP/2 endpoint without TLS in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="82416-141">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="82416-141">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="82416-142">`HttpProtocols.Http1AndHttp2` kann nicht verwendet werden, da zum Aushandeln von HTTP/2 TLS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="82416-142">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="82416-143">Ohne TLS wird für alle Verbindungen zu diesem Endpunkt standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="82416-143">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-5.0"
<span data-ttu-id="82416-144">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="82416-144">When an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="82416-145">`HttpProtocols.Http1AndHttp2` kann nicht verwendet werden, da zum Aushandeln von HTTP/2 TLS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="82416-145">`HttpProtocols.Http1AndHttp2` can't be used because TLS is required to negotiate HTTP/2.</span></span> <span data-ttu-id="82416-146">Ohne TLS wird für alle Verbindungen zu diesem Endpunkt standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="82416-146">Without TLS, all connections to the endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>
::: moniker-end

<span data-ttu-id="82416-147">Der gRPC-Client muss ebenfalls so konfiguriert sein, dass er kein TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="82416-147">The gRPC client must also be configured to not use TLS.</span></span> <span data-ttu-id="82416-148">Weitere Informationen finden Sie im Abschnitt [Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client](#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="82416-148">For more information, see [Call insecure gRPC services with .NET Core client](#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!WARNING]
> <span data-ttu-id="82416-149">HTTP/2 ohne TLS sollte nur während der App-Entwicklung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="82416-149">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="82416-150">Produktions-Apps sollten stets Transportsicherheit verwenden.</span><span class="sxs-lookup"><span data-stu-id="82416-150">Production apps should always use transport security.</span></span> <span data-ttu-id="82416-151">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen zu gRPC für ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="82416-151">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a><span data-ttu-id="82416-152">Kein Generieren von Code aus PROTO-Dateien bei gRPC-C#-Objekten</span><span class="sxs-lookup"><span data-stu-id="82416-152">gRPC C# assets are not code generated from .proto files</span></span>

<span data-ttu-id="82416-153">Für das Generieren von gRPC-Code für konkrete Clients und Dienstbasisklassen muss in einem Projekt auf Protobuf-Dateien und -Tools verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="82416-153">gRPC code generation of concrete clients and service base classes requires protobuf files and tooling to be referenced from a project.</span></span> <span data-ttu-id="82416-154">Folgendes muss enthalten sein:</span><span class="sxs-lookup"><span data-stu-id="82416-154">You must include:</span></span>

* <span data-ttu-id="82416-155">*.proto*-Dateien, die Sie in der Elementgruppe `<Protobuf>` verwenden wollen.</span><span class="sxs-lookup"><span data-stu-id="82416-155">*.proto* files you want to use in the `<Protobuf>` item group.</span></span> <span data-ttu-id="82416-156">Auf [importierte *.proto*-Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss im Projekt verwiesen werden.</span><span class="sxs-lookup"><span data-stu-id="82416-156">[Imported *.proto* files](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) must be referenced by the project.</span></span>
* <span data-ttu-id="82416-157">Paketverweis auf das gRPC-Toolpaket [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)</span><span class="sxs-lookup"><span data-stu-id="82416-157">Package reference to the gRPC tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/).</span></span>

<span data-ttu-id="82416-158">Weitere Informationen zum Generieren von gRPC-C#-Objekten finden Sie unter <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="82416-158">For more information on generating gRPC C# assets, see <xref:grpc/basics>.</span></span>

<span data-ttu-id="82416-159">Bei einer ASP.NET Core-Web-App, die gRPC-Dienste hostet, muss nur die Dienstbasisklasse generiert werden:</span><span class="sxs-lookup"><span data-stu-id="82416-159">An ASP.NET Core web app hosting gRPC services only needs the service base class generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

<span data-ttu-id="82416-160">Bei einer gRPC-Client-App, die gRPC-Aufrufe ausführt, muss nur der konkrete Client generiert werden:</span><span class="sxs-lookup"><span data-stu-id="82416-160">A gRPC client app making gRPC calls only needs the concrete client generated:</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a><span data-ttu-id="82416-161">Generieren von gRPC-C#-Objekten aus PROTO-Dateien in WPF-Projekten nicht möglich</span><span class="sxs-lookup"><span data-stu-id="82416-161">WPF projects unable to generate gRPC C# assets from .proto files</span></span>

<span data-ttu-id="82416-162">Es gibt ein [bekanntes Problem](https://github.com/dotnet/wpf/issues/810) bei WPF-Projekten, das verhindert, dass das Generieren von gRPC-Code richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="82416-162">WPF projects have a [known issue](https://github.com/dotnet/wpf/issues/810) that prevents gRPC code generation from working correctly.</span></span> <span data-ttu-id="82416-163">Bei der Verwendung aller gRPC-Typen, die in einem WPF-Projekt durch Verweise auf `Grpc.Tools` und *.proto*-Dateien generiert werden, treten Kompilierungsfehler auf:</span><span class="sxs-lookup"><span data-stu-id="82416-163">Any gRPC types generated in a WPF project by referencing `Grpc.Tools` and *.proto* files will create compilation errors when used:</span></span>

> <span data-ttu-id="82416-164">Fehler CS0246: Der Typ- oder Namespacename „MyGrpcServices“ wurde nicht gefunden (möglicherweise fehlt eine using-Direktive oder ein Assemblyverweis).</span><span class="sxs-lookup"><span data-stu-id="82416-164">error CS0246: The type or namespace name 'MyGrpcServices' could not be found (are you missing a using directive or an assembly reference?)</span></span>

<span data-ttu-id="82416-165">So umgehen Sie dieses Problem:</span><span class="sxs-lookup"><span data-stu-id="82416-165">You can workaround this issue by:</span></span>

1. <span data-ttu-id="82416-166">Erstellen Sie ein neues .NET Core-Klassenbibliotheksprojekt.</span><span class="sxs-lookup"><span data-stu-id="82416-166">Create a new .NET Core class library project.</span></span>
2. <span data-ttu-id="82416-167">Fügen Sie im neuen Projekt Verweise hinzu, um das [Generieren von C#-Code aus *\*.proto*-Dateien](xref:grpc/basics#generated-c-assets) zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="82416-167">In the new project, add references to enable [C# code generation from *\*.proto* files](xref:grpc/basics#generated-c-assets):</span></span>
    * <span data-ttu-id="82416-168">Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="82416-168">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
    * <span data-ttu-id="82416-169">Fügen Sie der `<Protobuf>`-Elementgruppe *\*.proto*-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="82416-169">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>
3. <span data-ttu-id="82416-170">Fügen Sie in der WPF-Anwendung einen Verweis auf das neue Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="82416-170">In the WPF application, add a reference to the new project.</span></span>

<span data-ttu-id="82416-171">Die WPF-Anwendung kann die in gRPC generierten Typen aus dem neuen Klassenbibliotheksprojekt verwenden.</span><span class="sxs-lookup"><span data-stu-id="82416-171">The WPF application can use the gRPC generated types from the new class library project.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]
