---
title: Problembehandlung und Debuggen von ASP.NET Core-Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059571"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Problembehandlung und Debuggen von ASP.NET Core-Projekten

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Die folgenden Links bieten Hinweise zur Problembehandlung:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET-Blog: Problembehandlung bei ASP. NET Core-Leistungsproblemen](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>.NET Core SDK-Warnungen

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Sowohl die 32-Bit- als auch die 64-Bit-Version des .NET Core SDKs sind installiert.

Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:

> Sowohl 32-Bit- als auch 64-Bit-Versionen des .NET Core SDKs sind installiert. Es werden nur Vorlagen der 64-Bit-Versionen angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.

Diese Warnung wird angezeigt, wenn sowohl 32-Bit- (x86) als auch 64-Bit-Versionen (x64) des [.NET Core SDKs](https://dotnet.microsoft.com/download/dotnet-core) installiert sind. Häufige Gründe für die Installation beider Versionen sind unter anderem:

* Sie haben das .NET Core SDK-Installationsprogramm ursprünglich mit einem 32-Bit-Computer heruntergeladen, es dann aber auf einen 64-Bit-Computer kopiert und installiert.
* Die 32-Bit-Version des .NET Core SDKs wurde von einer anderen Anwendung installiert.
* Die falsche Version wurde heruntergeladen und installiert.

Deinstallieren Sie die 32-Bit-Version des .NET Core SDKs, um diese Warnung zu verhindern. Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**. Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Das .NET Core SDK ist an mehreren Speicherorten installiert.

Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:

> Das .NET Core SDK ist an mehreren Speicherorten installiert. Es werden nur Vorlagen des SDKs angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.

Diese Meldung wird angezeigt, wenn Sie über mindestens eine Installation des .NET Core SDK in einem Verzeichnis außerhalb von *C:\\Programme\\dotnet\\sdk\\* verfügen. Normalerweise geschieht dies, wenn das .NET Core SDKs auf einem Computer über Kopieren/Einfügen anstatt mit dem MSI-Installationsprogramm bereitgestellt wurde.

Deinstallieren Sie alle 32-Bit-Versionen des .NET Core SDKs und der Laufzeiten, um diese Warnung zu verhindern. Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**. Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.

### <a name="no-net-core-sdks-were-detected"></a>Es wurden keine .NET Core SDKs erkannt.

* Im Dialogfeld **Neues Projekt** in Visual Studio für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:

  > Es wurden keine .NET Core SDKs erkannt. Stellen Sie sicher, dass sie in der Umgebungsvariablen `PATH` enthalten sind.

* Wenn Sie einen `dotnet`-Befehl ausführen, wird die Meldung wie folgt angezeigt:

  > Es konnten keine installierten Dotnet-SDKs gefunden werden.

Diese Warnungen werden angezeigt, wenn die Umgebungsvariable `PATH` nicht auf . NET Core SDKs auf dem Computer verweist. Dieses Problem lässt sich wie folgt beheben:

* Installieren Sie das .NET Core-SDK. Das aktuellste Installationsprogramm erhalten Sie unter [.NET-Downloads](https://dotnet.microsoft.com/download).
* Stellen Sie sicher, dass die Umgebungsvariable `PATH` auf den Speicherort verweist, an dem das SDK installiert ist (`C:\Program Files\dotnet\` für 64-Bit/x64 oder `C:\Program Files (x86)\dotnet\` für 32-Bit/x86). Das SDK-Installationsprogramm legt normalerweise den `PATH` fest. Installieren Sie immer SDKs und Laufzeiten mit derselben Bitanzahl auf demselben Computer.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Fehlendes SDK nach der Installation des .NET Core-Hostingpakets

Durch die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) wird der `PATH` bei der Installation der .NET Core-Laufzeit so geändert, dass er auf die 32-Bit-Version (x86) von .NET Core (`C:\Program Files (x86)\dotnet\`) verweist. Dies kann zu fehlenden SDKs führen, wenn der 32-Bit (x86) .NET Core-Befehl `dotnet` verwendet wird ([Es wurden keine .NET Core SDKs erkannt](#no-net-core-sdks-were-detected)). Um dieses Problem zu beheben, verschieben Sie `C:\Program Files\dotnet\` an einen Speicherort vor `C:\Program Files (x86)\dotnet\` in dem `PATH`.

## <a name="obtain-data-from-an-app"></a>Abrufen von Daten aus einer App

Wenn eine App in der Lage ist, auf Anforderungen zu antworten, können Sie die folgenden Daten über eine Middleware aus der App erhalten:

* Anforderung: Methode, Schema, Host, Pfadbasis, Pfad, Abfragezeichenfolge, Header
* Verbindung: Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Clientzertifikat
* Identity: Name, Anzeigename
* Konfigurationseinstellungen
* Umgebungsvariablen

Platzieren Sie den folgenden [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)-Code an den Anfang der Anforderungsverarbeitungspipeline der `Startup.Configure`-Methode. Die Umgebung wird vor der Ausführung der Middleware überprüft, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.

Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:

* Fügen Sie die `IHostingEnvironment` in die `Startup.Configure`-Methode ein, und prüfen Sie die Umgebung mit der lokalen Variablen. Dieser Ansatz wird im folgenden Beispielcode veranschaulicht.

* Weisen Sie die Umgebung einer Eigenschaft der Klasse `Startup` zu. Überprüfen Sie die Umgebung mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a>Debuggen von ASP.NET Core-Apps

Die folgenden Links bieten Informationen zum Debuggen von ASP.NET Core-Apps.

* [Debuggen von ASP Core unter Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Debuggen von .NET Core unter UNIX über SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Schnellstart: Debuggen von ASP.NET mithilfe des Visual Studio-Debuggers](/visualstudio/debugger/quickstart-debug-aspnet)
* Weitere Informationen zum Debuggen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/2960).
