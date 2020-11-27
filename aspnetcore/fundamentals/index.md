---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: 27b182394abe12a1631e5ba350942904bf4094aa
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035891"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core – Grundlagen

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.

## <a name="the-startup-class"></a>Die Startup-Klasse

In der `Startup`-Klasse:

* werden die von der App erforderlichen Dienste konfiguriert.
* Die Anforderungsverarbeitungspipeline der App wird mit mehreren Middlewarekomponenten definiert.

Beispiel für eine `Startup`-Klasse:

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Abhängigkeitsinjektion (Dienste)

ASP.NET Core umfasst ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste in einer App bereitgestellt werden. Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.

wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt. Zum Beispiel:

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

Dienste werden üblicherweise mit einer Constructor Injection aus der DI aufgelöst. Bei der Constructor Injection deklariert eine Klasse einen Konstruktorparameter des erforderlichen Typs oder einer Schnittstelle. Das DI-Framework stellt zur Laufzeit eine Instanz dieses Diensts bereit.

Im folgenden Beispiel wird die Constructor Injection verwendet, um `RazorPagesMovieContext` aus der DI zu lösen:

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

Wenn der integrierte IoC-Container (Inversion of Control, Steuerungsumkehr) nicht alle Anforderungen einer App erfüllt, kann stattdessen ein IoC-Drittanbietercontainer verwendet werden.

Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten. Jede Komponente führt Vorgänge in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.

Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen einer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt. Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.

Im folgenden Beispiel wird einer Anforderungsverarbeitungspipeline konfiguriert:

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

ASP.NET Core enthält zahlreiche integrierte Middlewareanwendungen. Es können auch benutzerdefinierte Middlewarekomponenten geschrieben werden.

Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Beim Starten erstellt eine ASP.NET Core-App einen *Host*. Der Host kapselt alle Ressourcen der App, zum Beispiel:

* eine HTTP-Serverimplementierung
* Middlewarekomponenten
* Protokollierung
* DI-Dienste
* Konfiguration

Es gibt zwei verschiedene Hosts: 

* Generischer .NET-Host
* ASP.NET Core-Webhost

Der generische .NET-Host wird empfohlen. Der ASP.NET Core-Webhost wird lediglich für die Abwärtskompatibilität zur Verfügung gestellt.

Im folgenden Beispiel wird ein neuer generischer .NET-Host erstellt:

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

Mit den Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults` wird ein Host mit mehreren Standardoptionen konfiguriert, zum Beispiel:

* Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.
* Laden der Konfiguration aus *appsettings.json* , *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.
* Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.

Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

### <a name="non-web-scenarios"></a>Nicht webbasierte Szenarios

Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Server

Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen. Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.

# <a name="windows"></a>[Windows](#tab/windows)

Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:

* *Kestrel* ist ein plattformübergreifender Webserver. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.
* Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet. Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.
* *HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

---

Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfiguration

ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft. Integrierte Konfigurationsanbieter stehen für eine Vielzahl von Quellen zur Verfügung, z. B. für *JSON*-Dateien, *XML*-Dateien, Umgebungsvariablen und Befehlszeilenargumente. Schreiben Sie benutzerdefinierte Konfigurationsanbieter, um andere Quellen zu unterstützen.

ASP.NET Core-Apps werden [standardmäßig](xref:fundamentals/configuration/index#default) zum Lesen aus *appsettings.json* , Umgebungsvariablen, der Befehlszeile und mehr konfiguriert. Wenn die Konfiguration der App geladen wird, überschreiben Werte aus Umgebungsvariablen die Werte von *appsettings.json* .

Die bevorzugte Methode für das Lesen zugehöriger Konfigurationswerte ist die Verwendung des [Optionsmusters](xref:fundamentals/configuration/options). Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster](xref:fundamentals/configuration/index#optpat).

Zum Verwalten vertraulicher Konfigurationsdaten wie Kennwörter bietet .NET Core den [Secret Manager](xref:security/app-secrets#secret-manager). Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).

Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="environments"></a>Umgebungen

Ausführungsumgebungen wie `Development`, `Staging` und `Production` sind in ASP.NET Core von besonderer Bedeutung. Legen Sie die Umgebung fest, in der eine App ausgeführt wird, indem Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen. ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IWebHostEnvironment`-Implementierung. Diese Implementierung ist per DI überall in einer App verfügbar.

Im Folgenden Beispiel wird die App so konfiguriert, dass sie ausführliche Fehlerinformationen angibt, wenn sie in der `Development`-Umgebung ausgeführt wird:

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="logging"></a>Protokollierung

ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet. Folgende Anbieter sind verfügbar:

* Konsole
* Debug
* Ereignisablaufverfolgung unter Windows
* Windows-Ereignisprotokoll
* TraceSource
* Azure App Service
* Azure Application Insights

Zum Erstellen von Protokollen lösen Sie einen <xref:Microsoft.Extensions.Logging.ILogger%601>-Dienst aus DI, und rufen Sie Protokollierungsmethoden wie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> auf. Zum Beispiel:

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

Protokollierungsmethoden wie `LogInformation` unterstützen eine beliebige Anzahl von Feldern. Diese Felder werden häufig zum Erstellen einer `string`-Meldung verwendet, einige Protokollierungsanbieter senden sie jedoch als separate Felder an einen Datenspeicher. Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist. Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware. Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="error-handling"></a>Fehlerbehandlung

ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:

* eine Seite mit Ausnahmen für Entwickler
* benutzerdefinierte Fehlerseiten
* statische Statuscodeseiten
* Fehlerbehandlung während des Starts

Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Übermitteln von HTTP-Anforderungen

Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen. Die Factory:

* Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten. Registrieren und konfigurieren Sie beispielsweise einen *GitHub*-Client für den Zugriff auf GitHub. Registrieren und konfigurieren Sie einen Standardclient für andere Zwecke.
* Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen. Dieses Muster ähnelt der eingehenden Middlewarepipeline von ASP.NET Core. Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen für HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.
* Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.
* Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.
* Eine konfigurierbare Protokollierungsfunktion wird über <xref:Microsoft.Extensions.Logging.ILogger> für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.

Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Inhaltsstammverzeichnis

Der Inhaltsstamm ist der Basispfad für:

* Die ausführbare Datei, die die App hostet ( *.exe*).
* Kompilierten Assemblys, die die App bilden ( *.dll*).
* Inhaltsdateien, die von der App verwendet werden, z. B.:
  * Razor-Dateien ( *.cshtml*, *.razor*)
  * Konfigurationsdateien ( *.json*, *.xml*)
  * Datendateien ( *.db*)
* Der [Webstamm](#web-root), in der Regel der Ordner *wwwroot*

Während der Entwicklung wird standardmäßig das Stammverzeichnis des Projekts als Inhaltsstamm verwendet. Dieses Verzeichnis ist auch der Basispfad für die Inhaltsdateien der App und den [Webstamm](#web-root). Sie können einen anderen Inhaltsstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen. Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/generic-host#contentroot).

## <a name="web-root"></a>Webstammverzeichnis

Der Webstamm ist der Basispfad für öffentliche, statische Ressourcendateien, zum Beispiel:

* Stylesheets ( *.css*)
* JavaScript ( *.js*)
* Bilder ( *.png*, *.jpg*)

Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis und dessen Unterverzeichnissen bereitgestellt. Standardmäßig lautet der Webstammpfad *{Inhaltsstamm}/wwwroot*. Sie können einen anderen Webstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen. Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/generic-host#webroot).

Verhindern Sie das Veröffentlichen von Dateien in *wwwroot* mit dem [\<Content>-Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei. Im folgenden Beispiel wird verhindert, dass Inhalte im Verzeichnis *wwwroot/local* und dessen Unterverzeichnissen veröffentlicht werden:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

In Razor-Dateien ( *.cshtml*) verweisen Tilde und Schrägstrich (`~/`) auf den Webstamm. Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.

Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.

## <a name="the-startup-class"></a>Die Startup-Klasse

In der `Startup`-Klasse:

* werden die von der App erforderlichen Dienste konfiguriert.
* wird die Pipeline zur Anforderungsverarbeitung definiert.

*Dienste* sind Komponenten, die von der App verwendet werden. Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar. wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.

Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*. Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten. Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung. Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.

Beispiel für eine `Startup`-Klasse:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Abhängigkeitsinjektion (Dienste)

ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden. Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs. Der Parameter kann der Diensttyp oder eine Schnittstelle sein. Das DI-System stellt den Dienst zur Laufzeit bereit.

Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen. Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.

Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten. Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.

Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt. Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.

Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.

Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Beim Starten erstellt eine ASP.NET Core-App einen *Host*. Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:

* eine HTTP-Serverimplementierung
* Middlewarekomponenten
* Protokollierung
* DI
* Konfiguration

Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.

Es stehen zwei Hosts zur Verfügung: der Webhost und der generische Host. In ASP.NET Core 2.x ist der generische Host nur für nicht webbasierte Szenarios verfügbar.

Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

Die Methode `CreateDefaultBuilder` konfiguriert einen Host mit häufig verwendeten Optionen wie den folgenden:

* Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.
* Laden der Konfiguration aus *appsettings.json* , *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.
* Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.

Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.

### <a name="non-web-scenarios"></a>Nicht webbasierte Szenarios

Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Server

Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen. Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:

* *Kestrel* ist ein plattformübergreifender Webserver. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.
* Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet. Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.
* *HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[Windows](#tab/windows)

Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:

* *Kestrel* ist ein plattformübergreifender Webserver. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.
* *HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.

# <a name="macos"></a>[macOS](#tab/macos)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

# <a name="linux"></a>[Linux](#tab/linux)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfiguration

ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft. Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente. Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.

Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt. Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json* -Datei. Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.

Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool (Secret Manager)](xref:security/app-secrets). Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).

Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="options"></a>Optionen

Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten. Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.

Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Umgebungen

Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung. Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein. ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung. Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.

Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="logging"></a>Protokollierung

ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet. Zu den verfügbaren Anbietern gehören folgende:

* Konsole
* Debug
* Ereignisablaufverfolgung unter Windows
* Windows-Ereignisprotokoll
* TraceSource
* Azure App Service
* Azure Application Insights

Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.

Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben. Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden. Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist. Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware. Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="error-handling"></a>Fehlerbehandlung

ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:

* eine Seite mit Ausnahmen für Entwickler
* benutzerdefinierte Fehlerseiten
* statische Statuscodeseiten
* Fehlerbehandlung während des Starts

Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Übermitteln von HTTP-Anforderungen

Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen. Die Factory:

* Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten. Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden. Ein Standard-Client kann für andere Zwecke registriert werden.
* Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen. Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core. Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.
* Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.
* Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.
* Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.

Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Inhaltsstammverzeichnis

Der Inhaltsstamm ist der Basispfad zu Folgendem:

* Der ausführbaren Datei ( *.exe*), die die App hostet.
* Kompilierten Assemblys, die die App bilden ( *.dll*).
* Inhaltsdateien ohne Code, die von der App verwendet werden, wie z. B.:
  * Razor-Dateien ( *.cshtml*, *.razor*)
  * Konfigurationsdateien ( *.json*, *.xml*)
  * Datendateien ( *.db*)
* [Webstamm](#web-root), in der Regel der veröffentlichte Ordner *wwwroot*.

Entwicklungsphase:

* Der Inhaltsstamm ist standardmäßig auf das Stammverzeichnis des Projekts festgelegt.
* Das Stammverzeichnis des Projekts dient zum Erstellen von Folgendem:
  * Pfad zu den Inhaltsdateien ohne Code der App im Stammverzeichnis des Projekts.
  * [Webstamm](#web-root), in der Regel der Ordner *wwwroot* im Stammverzeichnis des Projekts.

Ein alternativer Inhaltsstammpfad kann beim [Erstellen des Hosts](#host) angegeben werden. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#content-root>.

## <a name="web-root"></a>Webstammverzeichnis

Der Webstamm ist der Basispfad zu öffentlichen, statischen Ressourcendateien ohne Code, wie z. B.:

* Stylesheets ( *.css*)
* JavaScript ( *.js*)
* Bilder ( *.png*, *.jpg*)

Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis (samt Unterverzeichnissen) bereitgestellt.

Der Webstammpfad ist standardmäßig auf *{Inhaltsstamm}/wwwroot* festgelegt, doch beim [Erstellen des Hosts](#host) kann ein anderer Webstamm angegeben werden. Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/web-host#web-root).

Verhindern Sie das Veröffentlichen von Dateien in *wwwroot* mit dem [\<Content>-Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei. Im folgenden Beispiel wird verhindert, dass Inhalte im *wwwroot/local*-Verzeichnis und in Unterverzeichnisse veröffentlicht werden:

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

In Razor-Dateien ( *.cshtml*) zeigen Tilde und Schrägstrich (`~/`) auf den Webstamm. Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.

Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

::: moniker-end
