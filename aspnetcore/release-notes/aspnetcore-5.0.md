---
title: Neuerungen in ASP.NET Core 5.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: e25549d557dd971d0f2f4d67a182574f07138acb
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508122"
---
# <a name="whats-new-in-aspnet-core-50"></a>Neuerungen in ASP.NET Core 5.0

In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 5.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a>ASP.NET Core MVC- und Razor-Verbesserungen

### <a name="model-binding-datetime-as-utc"></a>Modellbindung von DateTime als UTC

Die Modellbindung unterstützt jetzt die Bindung von UTC-Zeitzeichenfolgen an `DateTime`. Wenn die Anforderung eine UTC-Zeitzeichenfolge enthält, bindet die Modellbindung Sie an ein UTC-`DateTime`-Element. Beispielsweise wird die folgende Zeitzeichenfolge an das UTC-`DateTime`-Element gebunden: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`

### <a name="model-binding-and-validation-with-c-9-record-types"></a>Modellbindung und Validierung mit Datensatztypen in C# 9.0

[Datensatztypen in C# 9.0](/dotnet/csharp/whats-new/csharp-9#record-types) können mit der Modellbindung in einem Model View Controller (MVC) oder einer Razor-Seite verwendet werden. Datensatztypen lassen sich gut zur Modellierung von Daten verwenden, die über das Netzwerk übertragen werden.

Beispielsweise wird im folgenden `PersonController`-Element der `Person`-Datensatztyp mit Modellbindung und Formularvalidierung verwendet:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

Die Datei `Person/Index.cshtml` enthält Folgendes:

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a>Verbesserungen an DynamicRouteValueTransformer

Mit ASP.NET Core 3.1 wurde <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> als Methode zur Verwendung eines benutzerdefinierten Endpunkts für die dynamische Auswahl einer MVC-Aktion oder einer Razor-Seite eingeführt. ASP.NET Core 5.0-Anwendungen können den Status an ein `DynamicRouteValueTransformer`-Element übergeben und die ausgewählten Endpunkte filtern.

### <a name="miscellaneous"></a>Sonstiges

* Das [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attribut kann auf Eigenschaften eines Razor-Seitenmodells angewendet werden.
* Parameter und Eigenschaften, die vom Text gebunden werden, gelten standardmäßig als erforderlich. <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a>Web-API

### <a name="openapi-specification-on-by-default"></a>OpenAPI-Spezifikation standardmäßig aktiviert

[Die OpenAPI-Spezifikation](http://spec.openapis.org/oas/v3.0.3) ist ein Branchenstandard zum Beschreiben von HTTP-APIs und deren Integration in komplexe Geschäftsprozesse oder Drittanbieter. OpenAPI wird von allen Cloudanbietern und zahlreichen API-Registrierungen weitgehend unterstützt. Apps, die OpenAPI-Dokumente aus Web-APIs ausgeben, haben eine Vielzahl neuer Möglichkeiten, mit denen diese APIs verwendet werden können. In Zusammenarbeit mit den Betreuern des Open Source-Projekts [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) enthält die ASP.NET Core-API-Vorlage eine NuGet-Abhängigkeit von [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore). Swashbuckle ist ein gängiges Open-Source-NuGet-Paket, das OpenAPI-Dokumente dynamisch ausgibt. Swashbuckle führt dies durch das Introspektieren über die API-Controller und das Erstellen des OpenAPI-Dokuments zur Laufzeit oder zur Buildzeit mithilfe der Swashbuckle-CLI aus.

In ASP.NET Core 5.0 wird die OpenAPI-Unterstützung von den Web-API-Vorlagen standardmäßig aktiviert. So deaktivieren Sie OpenAPI:

* Über die Befehlszeile:

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* Über Visual Studio: Deaktivieren Sie die Option **Support für OpenAPI aktivieren**.

Alle *.csproj*-Dateien, die für Web-API-Projekte erstellt werden, enthalten den Verweis auf das [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)-NuGet-Paket.

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

Der von der Vorlage generierte Code enthält Code in `Startup.ConfigureServices`, der die Erstellung des OpenAPI-Dokuments aktiviert:

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

Die `Startup.Configure`-Methode fügt die Swashbuckle-Middleware hinzu, die Folgendes ermöglicht:

* Dokumenterstellungsprozess
* Die Seite für die Swagger-Benutzeroberfläche ist standardmäßig im Entwicklungsmodus.

Der von der Vorlage generierte Code wird nicht versehentlich die Beschreibung der API bei der Veröffentlichung in der Produktion verfügbar machen.

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a>Azure API Management-Import

Wenn ASP.NET Core API-Projekte OpenAPI aktivieren, bietet die Veröffentlichung mit Visual Studio 2019 Version 16.8 und höher automatisch einen zusätzlichen Schritt bei der Veröffentlichung. Entwickler, die [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) verwenden, haben die Möglichkeit, die APIs während des Veröffentlichungsflusses automatisch in Azure API Management zu importieren:

![Azure API Management-Import im Vergleich zur Veröffentlichung](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a>Bessere Startbedingungen für Web-API-Projekte

Wenn OpenAPI standardmäßig aktiviert ist, werden die Startbedingungen für die App (F5) für Web-API-Entwickler erheblich verbessert. In ASP.NET Core 5.0 ist die Web-API-Vorlage zum Laden der Seite für die Swagger-Benutzeroberfläche vorkonfiguriert. Auf der Seite für die Swagger-Benutzeroberfläche finden Sie die Dokumentation, die für die veröffentlichte API hinzugefügt wurde. Zudem können Sie von dort aus auch APIs mit einem einzigen Mausklick testen.

![Swagger/index.html-Sicht](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a>Leistungsverbesserungen

Für .NET 5 wurden erhebliche Verbesserungen der Blazor WebAssembly-Laufzeitleistung mit besonderem Schwerpunkt auf komplexem Benutzeroberflächenrendering und JSON-Serialisierung vorgenommen. In unseren Leistungstests ist Blazor WebAssembly in .NET 5 in den meisten Szenarios zwei- bis dreimal schneller. Weitere Informationen finden Sie im [ASP.NET-Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements) (ASP.NET Core-Updates in .NET 5 Release Candidate).

### <a name="css-isolation"></a>CSS-Isolation

Blazor unterstützt jetzt das Definieren von CSS-Formaten, die auf eine bestimmte Komponente zugeschnitten sind. Komponentenspezifische CSS-Formate erleichtern die Auseinandersetzung mit den Formaten in einer Anwendung und die Vermeidung unbeabsichtigter Begleiterscheinungen von globalen Formaten. Weitere Informationen finden Sie unter <xref:blazor/components/css-isolation>.

### <a name="new-inputfile-component"></a>Neue `InputFile`-Komponente

Die `InputFile`-Komponente ermöglicht das Lesen einer oder mehrerer Dateien, die von einem Benutzer für den Upload ausgewählt wurden. Weitere Informationen finden Sie unter <xref:blazor/file-uploads>.

### <a name="new-inputradio-and-inputradiogroup-components"></a>Neue `InputRadio`- und `InputRadioGroup`-Komponenten

Blazor verfügt über integrierte `InputRadio`- und `InputRadioGroup`-Komponenten, die die Datenbindung an Optionsfeldgruppen mit integrierter Validierung vereinfachen. Weitere Informationen finden Sie unter <xref:blazor/forms-validation>.

### <a name="component-virtualization"></a>Komponentenvirtualisierung

Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks. Weitere Informationen finden Sie unter <xref:blazor/forms-validation#radio-buttons>.

### <a name="ontoggle-event-support"></a>Unterstützung von `ontoggle`-Ereignissen

Blazor-Ereignisse unterstützen jetzt das `ontoggle`-DOM-Ereignis. Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#event-argument-types>.

### <a name="set-ui-focus-in-no-locblazor-apps"></a>Festlegen des Fokus auf die Benutzeroberfläche in Blazor-Apps

Verwenden Sie die `FocusAsync`-Hilfsmethode für Elementverweise, um den Benutzeroberflächenfokus auf dieses Element festzulegen. Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#focus-an-element>.

### <a name="custom-validation-class-attributes"></a>Benutzerdefinierte Validierungsklassenattribute

Benutzerdefinierte Validierungsklassennamen sind nützlich, wenn eine Integration in CSS-Frameworks wie Bootstrap erfolgt. Weitere Informationen finden Sie unter <xref:blazor/forms-validation#custom-validation-class-attributes>.

### <a name="iasyncdisposable-support"></a>IAsyncDisposable-Unterstützung

Blazor-Komponenten unterstützen jetzt die <xref:System.IAsyncDisposable>-Schnittstelle für das asynchrone Release zugeordneter Ressourcen.

### <a name="javascript-isolation-and-object-references"></a>-JavaScript-Isolation und Objektverweise

Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules). Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.

### <a name="form-components-support-display-name"></a>Formularkomponenten unterstützen Anzeigenamen

Die folgenden integrierten Komponenten unterstützen Anzeigenamen mit dem `DisplayName`-Parameter:

* `InputDate`
* `InputNumber`
* `InputSelect`

Weitere Informationen finden Sie unter <xref:blazor/forms-validation#display-name-support>.

### <a name="catch-all-route-parameters"></a>Catch-All-Routenparameter

Catch-All-Routenparameter, die Pfade über mehrere Ordnergrenzen hinweg erfassen, werden in Komponenten unterstützt. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="debugging-improvements"></a>Verbesserungen beim Debugging

Das Debuggen von Blazor WebAssembly-Apps wurde in ASP.NET Core 5.0 verbessert. Außerdem wird das Debuggen jetzt für Visual Studio für Mac unterstützt. Weitere Informationen finden Sie unter <xref:blazor/debug>.

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a>Microsoft Identity v2.0 und MSAL v2.0

Blazor-Sicherheit verwendet jetzt Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) und [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) und MSAL v2.0. Weitere Informationen finden Sie in den Themen unter [Blazor Sicherheit und Identity Knoten](xref:blazor/security/index).

### <a name="protected-browser-storage-for-no-locblazor-server"></a>Geschützter Browserspeicher für Blazor Server

Für Blazor Server-Apps gibt es jetzt eine integrierte Unterstützung zum Speichern des App-Status im Browser, der mit ASP.NET Core-Datenschutzfunktionen vor unbefugtem Zugriff geschützt wird. Daten können entweder im lokalen Browserspeicher oder im Sitzungsspeicher gespeichert werden. Weitere Informationen finden Sie unter <xref:blazor/state-management>.

### <a name="no-locblazor-webassembly-prerendering"></a>Blazor WebAssembly-Prerendering

Die Komponentenintegration wurde für verschiedene Hostingmodelle verbessert, und Blazor WebAssembly-Apps können jetzt die Ausgabe auf dem Server vorab rendern. <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a>Verbesserungen beim Kürzen/Verknüpfen

Blazor WebAssembly führt die IL-Kürzung/Verknüpfung (Intermediate Language) während eines Builds durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der Anwendung zu kürzen. Ab dem Release von ASP.NET Core 5.0 führt Blazor WebAssembly eine verbesserte Kürzung mit zusätzlichen Konfigurationsoptionen durch. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-trimmer> und [Kürzungsoptionen](/dotnet/core/deploying/trimming-options).

### <a name="browser-compatibility-analyzer"></a>Browserkompatibilitäts-Analysetool

Blazor WebAssembly-Apps zielen auf die vollständige .NET-API-Oberfläche ab, aber aufgrund von Browsersandboxeinschränkungen werden nicht alle .NET-APIs in WebAssembly unterstützt. Nicht unterstützte APIs lösen <xref:System.PlatformNotSupportedException> bei der Ausführung in Webassembly aus. Ein Plattformkompatibilitäts-Analysetool warnt den Entwickler, wenn die API APIs verwendet, die nicht von den Zielplattformen der App unterstützt werden. Weitere Informationen finden Sie unter <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.

### <a name="lazy-load-assemblies"></a>Lazy Loading-Assemblys

Die Startleistung einer Blazor WebAssembly-App kann verbessert werden, indem das Laden einiger Anwendungsassemblys verzögert wird, bis sie erforderlich sind. Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="updated-globalization-support"></a>Aktualisierte Globalisierungsunterstützung

Die Globalisierungsunterstützung ist für Blazor WebAssembly basierend auf den internationalen Komponenten für Unicode (ICU) verfügbar. Weitere Informationen finden Sie unter <xref:blazor/globalization-localization>.

## <a name="grpc"></a>gRPC

In [gRPC](https://grpc.io/) wurden viele Leistungsverbesserungen vorgenommen. Weitere Informationen finden Sie unter [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/) (gPRC-Leistungsverbesserungen in .NET 5).

Weitere Informationen zu gRPC finden Sie unter <xref:grpc/index>.

## SignalR

### <a name="no-locsignalr-hub-filters"></a>SignalR-Hubfilter

SignalR Hubfilter, die in ASP.NET als Hubpipelines bezeichnet werdenSignalR, sind ein Feature, das die Ausführung von Code vor und nach dem Aufruf von Hubmethoden ermöglicht. Das Ausführen von Code vor und nach dem Aufruf von Hubmethoden ist vergleichbar damit, wie mithilfe von Middleware Code vor und nach einer HTTP-Anforderung ausgeführt werden kann. Zu den allgemeinen Verwendungsmöglichkeiten zählen die Protokollierung, die Fehlerbehandlung und die Argumentvalidierung.

Weitere Informationen finden Sie unter [Use hub filters in ASP.NET CoreSignalR](xref:signalr/hub-filters) (Verwenden von Hubfiltern in ASP.NET Core).

### <a name="no-locsignalr-parallel-hub-invocations"></a>Parallele SignalR-Hubaufrufe

ASP.NET Core SignalR ist nun in der Lage, parallele Hubaufrufe zu verarbeiten. Das Standardverhalten kann so geändert werden, dass Clients gleichzeitig mehr als eine Hubmethode aufrufen:

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a>Hinzugefügte MessagePack-Unterstützung im SignalR-Java-Client

Ein neues Paket, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), bietet MessagePack-Unterstützung für den SignalR-Java-Client. Um das MessagePack-Hubprotokoll zu verwenden, fügen Sie `.withHubProtocol(new MessagePackHubProtocol())` zum Verbindungs-Generator hinzu:

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* Erneut ladbare Endpunkte über Konfiguration: Kestrel kann Konfigurationsänderungen erkennen, die an [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) übergeben werden, und die Bindung von vorhandenen Endpunkten lösen und an neue Endpunkte binden, ohne dass ein Neustart der Anwendung erforderlich ist, wenn der Parameter `reloadOnChange` auf `true` gesetzt ist. Standardmäßig wird bei Verwendung von <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> oder <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel mit aktiviertem `reloadOnChange`-Element an den Konfigurationsunterabschnitt ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) gebunden. Apps müssen `reloadOnChange: true` übergeben, wenn `KestrelServerOptions.Configure` manuell aufgerufen wird, um erneut ladbare Endpunkte abzurufen.
* Verbesserungen bei HTTP/2-Antwortheadern. Weitere Informationen finden Sie im nächsten Abschnitt [Leistungsverbesserungen](#performance-improvements).
* Unterstützung für zusätzliche Endpunkttypen im Socketstransport: Als Ergänzung der neuen API, die in <xref:System.Net.Sockets?displayProperty=nameWithType> eingeführt wurde, ermöglicht der Socketsstandardtransport in [Kestrel](xref:fundamentals/servers/kestrel) die Bindung sowohl an vorhandene Dateihandles als auch an Unix-Domänensockets. Die Unterstützung für die Bindung an vorhandene Dateihandles ermöglicht die Verwendung der vorhandenen `Systemd`-Integration, ohne dass der `libuv`-Transport erforderlich ist.
* Decodierung von benutzerdefiniertem Header in Kestrel: Mithilfe von Apps kann festgelegt werden, welche Codierung (<xref:System.Text.Encoding>) verwendet werden soll, um eingehende Header anhand des Headernamens zu interpretieren, anstatt standardmäßig UTF-8 zu verwenden. Legen Sie den Schalter  <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> Eigenschaft `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` zum Festlegen der zu verwendenden Codierung:
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a>Für den Kestrel-Endpunkt spezifische Optionen über die Konfiguration

Ab sofort wird das Konfigurieren der endpunktspezifischen Optionen für Kestrel über die [Konfiguration](xref:fundamentals/configuration/index) unterstützt. Die endpunktspezifischen Konfigurationen umfassen Folgendes:

* Verwendete HTTP-Protokolle
* Verwendete TLS-Protokolle
* Ausgewähltes Zertifikat
* Clientzertifikatmodus

Die Konfiguration ermöglicht die Angabe, welches Zertifikat auf Grundlage des angegebenen Servernamens ausgewählt wird. Der Servername ist Teil der SNI-Erweiterung (Servernamensanzeige) des TLS-Protokolls, wie vom Client angegeben. Die Konfiguration von Kestrel unterstützt auch ein Platzhalterpräfix im Hostnamen.

Im folgenden Beispiel wird gezeigt, wie eine endpunktspezifische Option mithilfe einer Konfigurationsdatei angegeben wird:

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

Servernamensanzeige (SNI) ist eine TLS-Erweiterung zum Einbinden einer virtuellen Domäne als Teil der SSL-Aushandlung. Dies bedeutet, dass der Name der virtuellen Domäne oder eines Hosts zur Identifizierung des Netzwerkendpunkts verwendet werden kann.

## <a name="performance-improvements"></a>Leistungsverbesserungen

### <a name="http2"></a>HTTP/2

* Erhebliche Reduzierung der Zuweisungen im HTTP/2-Codepfad.
* Unterstützung für [dynamische HPack-Komprimierung](https://tools.ietf.org/html/rfc7541) von HTTP/2-Antwortheadern in [Kestrel](xref:fundamentals/servers/kestrel). Weitere Informationen finden Sie unter [Größe der Headertabelle](xref:fundamentals/servers/kestrel#header-table-size) und [HPACK: The Silent Killer (Feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/) (HPACK: Der lautlose Killer (Feature) von HTTP/2).
* Senden von HTTP/2-PING-Frames: HTTP/2 verfügt über einen Mechanismus zum Senden von PING-Frames, um sicherzustellen, dass eine Verbindung im Leerlauf noch funktionsfähig ist. Das Sicherstellen einer funktionsfähigen Verbindung ist besonders nützlich, wenn mit lange dauernden Streams gearbeitet wird, die oft im Leerlauf sind und nur sporadisch Aktivität aufweisen, z. B. gRPC-Streams. In [Kestrel](xref:fundamentals/servers/kestrel) können Apps periodische PING-Frames senden, indem sie Grenzwerte für <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> festlegen:

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a>Container

Vor .NET 5.0 war es zum Erstellen und Veröffentlichen einer *Dockerfile* für eine ASP.NET Core-Anwendung erforderlich, das gesamte .NET Core SDK und das ASP.NET Core-Image zu pullen. Mit dieser Version wird das Pullen der SDK-Imagebytes reduziert, und die für das ASP.NET Core-Image gepullten Bytes werden größtenteils entfernt. Weitere Informationen finden Sie in [diesem GitHub-Issue-Kommentar](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a>Azure Active Directory-Authentifizierung bei Microsoft.Identity.Web

Die ASP.NET Core-Projektvorlagen lassen sich jetzt in <xref:Microsoft.Identity.Web?displayProperty=fullName> integrieren, um die Authentifizierung mit [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) abzuwickeln (Azure AD). Das [Microsoft.Identity.Web-Paket](https://www.nuget.org/packages/Microsoft.Identity.Web/) bietet Folgendes:

* Eine bessere Authentifizierung über Azure AD.
* Eine einfachere Möglichkeit, auf Azure-Ressourcen im Namen Ihrer Benutzer zuzugreifen, einschließlich [Microsoft Graph](/graph/overview). Weitere Informationen finden Sie im [Microsoft.Identity.Web-Beispiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), das mit einer einfachen Anmeldung beginnt und sich über Mehrinstanzenfähigkeit, die Verwendung von Azure-APIs, die Verwendung von Microsoft Graph und den Schutz Ihrer eigenen APIs erstreckt. `Microsoft.Identity.Web` ist neben .NET 5 verfügbar.

### <a name="allow-anonymous-access-to-an-endpoint"></a>Zulassen des anonymen Zugriffs auf einen Endpunkt

Die `AllowAnonymous`-Erweiterungsmethode ermöglicht den anonymen Zugriff auf einen Endpunkt:

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a>Benutzerdefinierte Behandlung von Autorisierungsfehlern

Die benutzerdefinierte Behandlung von Autorisierungsfehlern ist mit der neuen [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs)-Schnittstelle, die von der [Middleware](xref:fundamentals/middleware/index) zur [Autorisierung](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) aufgerufen wird, jetzt einfacher. Die Standardimplementierung bleibt die gleiche, aber ein benutzerdefinierter Handler kann in Dependency Injection registriert werden. So werden benutzerdefinierte HTTP-Antworten zugelassen, die sich danach richten, warum die Autorisierung fehlgeschlagen ist. Weitere Informationen finden Sie in [diesem Beispiel](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs), das die Verwendung von `IAuthorizationMiddlewareResultHandler` veranschaulicht.

### <a name="authorization-when-using-endpoint-routing"></a>Autorisierung bei Verwendung des Endpunktroutings

Die Autorisierung bei Verwendung des Endpunktroutings empfängt jetzt das `HttpContext`-Element statt der Endpunktinstanz. Dadurch kann die Autorisierungsmiddleware auf die `RouteData`-Eigenschaft und andere Eigenschaften von `HttpContext` zugreifen, auf die über die <xref:Microsoft.AspNetCore.Http.Endpoint>-Klasse nicht zugegriffen werden konnte. Der Endpunkt kann mit [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A) aus dem Kontext abgerufen werden.

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a>Rollenbasierte Zugriffssteuerung mit Kerberos-Authentifizierung und LDAP unter Linux

Weitere Informationen finden Sie unter [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac) (Kerberos-Authentifizierung und rollenbasierte Zugriffssteuerung (RBAC)).

## <a name="api-improvements"></a>API-Verbesserungen

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a>JSON-Erweiterungsmethoden für HttpRequest und HttpResponse

JSON-Daten können mit den neuen Erweiterungsmethoden <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> und `WriteAsJsonAsync` aus einem `HttpRequest`- und `HttpResponse`-Element gelesen und geschrieben werden. Diese Erweiterungsmethoden verarbeiten die JSON-Daten mithilfe des Serialisierungsmoduls [System.Text.Json](xref:System.Text.Json). Mit der neuen `HasJsonContentType`-Erweiterungsmethode lässt sich auch überprüfen, ob eine Anforderung einen JSON-Inhaltstyp aufweist.

Die JSON-Erweiterungsmethoden können mit [Endpunktrouting](xref:fundamentals/routing) kombiniert werden, um JSON-APIs in einem Programmierstil zu erstellen, der als ***Route-zu-Code** _ bezeichnet wird. Diese neue Option bietet Entwicklern die Möglichkeit, grundlegende JSON-APIs auf einfache Art und Weise zu erstellen. Beispielsweise kann eine Web-App, die nur über wenige Endpunkte verfügt, anstatt der vollständigen Funktionalität von ASP.NET Core-MVC auch Route-zu-Code nutzen:

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

Weitere Informationen zu den neuen JSON-Erweiterungsmethoden und zu Route-zu-Code finden Sie in dieser [.NET-Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).

### <a name="systemdiagnosticsactivity"></a>System.Diagnostics.Activity

Als Standardformat für <xref:System.Diagnostics.Activity?displayProperty=fullName> wird jetzt standardmäßig das W3C-Format verwendet. Dadurch wird die Unterstützung für verteilte Ablaufverfolgung in ASP.NET Core standardmäßig mit mehr Frameworks interoperabel.

### <a name="frombodyattribute"></a>FromBodyAttribute

<xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> unterstützt nun die Konfiguration einer Option, mit der diese Parameter oder Eigenschaften als optional betrachtet werden können:

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a>Sonstige Verbesserungen

Ab sofort werden auf ASP.NET-Core-Assemblys [Nullable-Anmerkungen](/dotnet/csharp/nullable-references#attributes-describe-apis) angewendet. Außerdem ist vorgesehen, den größten Teil der allgemeinen öffentlichen API-Oberfläche des .NET 5-Frameworks mit Anmerkungen zu versehen. <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a>Steuerung der Startklassenaktivierung

Es wurde eine zusätzliche <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>-Überladung hinzugefügt, mit der eine App eine Factorymethode zum Steuern der `Startup`-Klassenaktivierung bereitstellen kann. Das Steuern der `Startup`-Klassenaktivierung ist nützlich, um zusätzliche Parameter an `Startup` zu übergeben, die zusammen mit dem Host initialisiert werden:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a>Automatische Aktualisierung mit „dotnet watch“

In .NET 5 wird beim Ausführen von [dotnet watch](xref:tutorials/dotnet-watch) in einem ASP.NET Core-Projekt der Standardbrowser gestartet und automatisch aktualisiert, wenn Änderungen am Code vorgenommen werden. Dies bedeutet, Sie haben folgende Möglichkeiten:

_ Öffnen Sie ein ASP.NET Core-Projekt in einem Text-Editor.
* Führen Sie `dotnet watch` aus.
* Konzentrieren Sie sich auf die Codeänderungen, während das Tool das Neuerstellen, Neustarten und erneute Laden der App übernimmt.

Die Funktion zur automatischen Aktualisierung soll in Zukunft in Visual Studio eingeführt werden.

### <a name="console-logger-formatter"></a>Formatierer für die Konsolenprotokollierung

Es wurden Verbesserungen an der Konsolenprotokollierung in der `Microsoft.Extensions.Logging`-Bibliothek vorgenommen. Entwickler können nun ein benutzerdefiniertes `ConsoleFormatter`-Programm implementieren, um Formatierungen und Farbgebungen der Konsolenausgabe vollständig zu steuern. Die Formatierungs-APIs ermöglichen eine umfangreiche Formatierung, indem eine Teilmenge der VT-100-Escapesequenzen implementiert wird. VT-100 wird von den meisten modernen Terminals unterstützt. Die Konsolenprotokollierung kann Escapesequenzen auf nicht unterstützten Terminals analysieren, sodass Entwickler einen einzelnen Formatierer für alle Terminals erstellen können.

### <a name="json-console-logger"></a>JSON-Konsolenprotokollierung

Zusätzlich zur Unterstützung von benutzerdefinierten Formatierern gibt es nun auch einen integrierten JSON-Formatierer, der strukturierte JSON-Protokolle an die Konsole ausgibt. Der folgende Code zeigt, wie Sie von der Standardprotokollierung zu JSON wechseln:

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

An die Konsole ausgegebene Protokollmeldungen sind JSON-formatiert:

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
