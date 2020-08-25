---
title: Migrieren von ASP.NET zu ASP.NET Core
author: isaac2004
description: Anweisungen zum Migrieren vorhandener ASP.NET MVC- oder Web-API-Apps zu ASP.NET Core Web.
ms.author: scaddie
ms.date: 10/18/2019
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
uid: migration/proper-to-2x/index
ms.openlocfilehash: f1a5af60f8dce83d9622ed9d2c6bcb4b8fc22b73
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712492"
---
# <a name="migrate-from-aspnet-to-aspnet-core"></a>Migration von ASP.NET zu ASP.NET Core

Von [Isaac Levin](https://isaaclevin.com)

Dieser Artikel dient als Leitfaden zum Migrieren von ASP.NET-Anwendungen zu ASP.NET Core.

## <a name="prerequisites"></a>Voraussetzungen

[.NET Core SDK 2.2 oder höher](https://dotnet.microsoft.com/download)

## <a name="target-frameworks"></a>Zielframeworks

ASP.NET Core-Projekte bieten Entwicklern die Flexibilität, Anwendungen für .NET Core, .NET Framework oder für beide Frameworks zu erstellen. Informationen zur Auswahl eines geeigneten Frameworks finden Sie unter [Wahl zwischen .NET Core und .NET Framework für Server-Apps](/dotnet/standard/choosing-core-framework-server).

Bei der Erstellung von Anwendungen für .NET Framework müssen Projekte auf einzelne NuGet-Pakete verweisen.

Wenn das Zielframework .NET Core ist, können Sie mit dem [Metapaket](xref:fundamentals/metapackage-app) für ASP.NET Core auf die meisten expliziten Paketverweise verzichten. Das `Microsoft.AspNetCore.App`-Metapaket können Sie folgendermaßen in Ihrem Projekt installieren:

```xml
<ItemGroup>
   <PackageReference Include="Microsoft.AspNetCore.App" />
</ItemGroup>
```

Wenn das Metapaket verwendet wird, werden mit der Anwendung keine Pakete bereitgestellt, auf die im Metapaket verwiesen wird. Die notwendigen Objekte sind im .NET Core-Laufzeitspeicher vorhanden und werden zur Verbesserung der Leistung vorkompiliert. Weitere Informationen finden Sie unter [Das Metapaket „Microsoft.AspNetCore.App“ für ASP.NET Core 2.1](xref:fundamentals/metapackage-app).

## <a name="project-structure-differences"></a>Unterschiede bei Projektstrukturen

Das Dateiformat *.csproj* wurde in ASP.NET Core vereinfacht. Zu einigen wichtigen Änderungen gehören folgende Punkte:

- Dateien müssen nicht explizit eingebunden werden, um als Teil des Projekts behandelt zu werden. Dadurch wird in großen Entwicklerteams das Risiko von Konflikten beim Zusammenführen von XML-Dateien reduziert.
- Auf GUID-Verweise zu anderen Projekten wird verzichtet, wodurch die Lesbarkeit von Dateien erhöht wird.
- Die Datei kann bearbeitet werden, ohne in Visual Studio entladen zu werden:

    ![Kontextmenüoption „Edit CSPROJ“ (CSPROJ-Datei bearbeiten) in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Ersetzen der Datei „Global.asax“

Mit ASP.NET Core wurde ein neuer Mechanismus für den Bootstrap einer Anwendung eingeführt. Der Einstiegspunkt für ASP.NET-Anwendungen ist die Datei *Global.asax*. In der Datei *Global.asax* werden Aufgaben wie die Routenkonfiguration, die Einrichtung von Filtern und Bereichsregistrierungen bearbeitet.

[!code-csharp[](samples/globalasax-sample.cs)]

Bei diesem Ansatz werden die Anwendung und der Server, auf dem die Anwendung bereitgestellt wird, so miteinander gekoppelt, dass es zu Konflikten mit der Implementierung kommt. [OWIN](https://owin.org/) wurde mit dem Ziel eingeführt, beide Komponenten zu entkoppeln und so mehrere Frameworks leichter gemeinsam verwenden zu können. OWIN stellt eine Pipeline zur Verfügung, über die nur die benötigten Module hinzugefügt werden. Die Hostingumgebung verwendet eine [Startup](xref:fundamentals/startup)-Funktion, um Dienste und die Anforderungspipeline der Anwendung zu konfigurieren. `Startup` registriert die Middleware bei der Anwendung. Bei jeder Anforderung ruft die Anwendung alle Middlewarekomponenten auf, wobei der Hauptzeiger einer verknüpften Liste auf die vorhandenen Handler zeigt. Jede Middlewarekomponente kann einen oder mehrere Handler zur Anforderungspipeline hinzufügen. Möglich wird dies, indem ein Verweis auf den Handler zurückgegeben wird, der zum neuen ersten Element der Liste wird. Jeder Handler ist dafür verantwortlich, sich den nächsten Handler in der Liste zu merken und diesen aufzurufen. In ASP.NET Core ist `Startup` der Einstiegspunkt für eine Anwendung. Eine Abhängigkeit von *Global.asax* ist nicht mehr vorhanden. Wenn Sie OWIN mit .NET Framework verwenden möchten, können Sie sich beispielsweise an folgendem Code für die Pipeline orientieren:

[!code-csharp[](samples/webapi-owin.cs)]

Hierdurch werden Ihre Standardrouten konfiguriert. Außerdem wird standardmäßig XmlSerialization anstelle von JSON verwendet. Bei Bedarf können Sie weitere Middleware (z.B. zum Laden von Diensten, für Konfigurationseinstellungen, für statische Dateien usw.) zur Pipeline hinzufügen.

ASP.NET Core verwendet einen ähnlichen Ansatz, ist jedoch hinsichtlich des Einstiegspunkts nicht auf OWIN angewiesen. Stattdessen kommt – ähnlich wie bei Konsolenanwendungen – in *Program.cs* die `Main`-Methode zum Einsatz, in der `Startup` geladen wird.

[!code-csharp[](samples/program.cs)]

In `Startup` muss die `Configure`-Methode enthalten sein. Fügen Sie in `Configure` der Pipeline die erforderliche Middleware hinzu. Im folgenden Beispiel, das der Standardwebsitevorlage entnommen wurde, konfigurieren Erweiterungsmethoden die Pipeline mit Unterstützung für Folgendes:

- Fehlerseiten
- HTTP Strict Transport Security
- HTTP-Umleitung zu HTTPS
- ASP.NET Core MVC

[!code-csharp[](samples/startup.cs)]

Durch die Entkopplung von Host und Anwendung wird die Möglichkeit geschaffen, in der Zukunft eine Migration zu einer anderen Plattform vorzunehmen.

> [!NOTE]
> Ausführliche Informationen zum Start einer Anwendung in ASP.NET Core und zu Middleware finden Sie unter [Startup in ASP.NET Core (Starten von Anwendungen in ASP.NET Core)](xref:fundamentals/startup).

## <a name="store-configurations"></a>Speicherkonfigurationen

ASP.NET unterstützt das Speichern von Einstellungen. Diese Einstellungen dienen z.B. der Unterstützung der Umgebung, in der die Anwendungen bereitgestellt werden. Häufig werden alle benutzerdefinierten Schlüssel-Wert-Paare im Abschnitt `<appSettings>` der Datei *Web.config* gespeichert:

[!code-xml[](samples/webconfig-sample.xml)]

Anwendungen lesen diese Einstellungen über die `ConfigurationManager.AppSettings`-Auflistung im `System.Configuration`-Namespace aus:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core kann Konfigurationsdaten der Anwendung in einer beliebigen Datei speichern und diese während des Middleware-Bootstraps laden. Die in Projektvorlagen verwendete Standarddatei ist *appsettings.json*:

[!code-json[](samples/appsettings-sample.json)]

Diese Datei wird in Ihrer Anwendung in eine Instanz von `IConfiguration` in *Startup.cs* geladen:

[!code-csharp[](samples/startup-builder.cs)]

Die Anwendung liest aus `Configuration`, um die Einstellungen abzurufen:

[!code-csharp[](samples/read-appsettings.cs)]

Dieser Ansatz kann erweitert werden, um einen noch stabileren Prozess zu gewährleisten. Beispielsweise kann über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) ein Dienst mit diesen Werten geladen werden. Durch die Abhängigkeitsinjektion wird eine Reihe stark typisierter Konfigurationsobjekte zur Verfügung gestellt.

```csharp
// Assume AppConfiguration is a class representing a strongly-typed version of AppConfiguration section
services.Configure<AppConfiguration>(Configuration.GetSection("AppConfiguration"));
```

> [!NOTE]
> Ausführliche Informationen zur ASP.NET Core-Konfiguration finden Sie unter [Configuration in ASP.NET Core (Konfiguration in ASP.NET Core)](xref:fundamentals/configuration/index).

## <a name="native-dependency-injection"></a>Native Abhängigkeitsinjektion

Ein wichtiges Ziel bei der Erstellung großer, skalierbarer Anwendungen besteht in der losen Kopplung von Komponenten und Diensten. Die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) ist hierfür eine beliebte Methode und eine native Komponente von ASP.NET Core.

Zur Implementierung der Dependency Injection greifen Entwickler in ASP.NET-Anwendungen auf Bibliotheken von Drittanbietern zurück. Eine solche Bibliothek ist [Unity](https://github.com/unitycontainer/unity), die von Microsoft Patterns & Practices bereitgestellt wird.

Ein Beispiel für das Einrichten der Abhängigkeitsinjektion mit Unity ist die Implementierung der Schnittstelle `IDependencyResolver`, die einen `UnityContainer` umschließt:

[!code-csharp[](samples/sample8.cs)]

Erstellen Sie eine Instanz von `UnityContainer`, registrieren Sie den Dienst, und weisen Sie für Ihren Container den Abhängigkeitskonfliktlöser von `HttpConfiguration` der neuen Instanz von `UnityResolver` zu:

[!code-csharp[](samples/sample9.cs)]

Fügen Sie bei Bedarf `IProductRepository` ein:

[!code-csharp[](samples/sample5.cs)]

Da die Abhängigkeitsinjektion eine Komponente von ASP.NET Core ist, können Sie Ihren Dienst in *Startup.cs* der Methode `ConfigureServices` hinzufügen:

[!code-csharp[](samples/configure-services.cs)]

Genau wie bei Unity kann auch hier das Repository an einer beliebigen Stelle eingefügt werden.

> [!NOTE]
> Weitere Informationen zur Dependency Injection finden Sie unter [Dependency Injection](xref:fundamentals/dependency-injection).

## <a name="serve-static-files"></a>Bereitstellen statischer Dateien

Ein wichtiger Teil der Webentwicklung ist die Möglichkeit, statische, clientseitige Objekte bereitzustellen. Die häufigsten Beispiele für statische Dateien sind HTML-, CSS-, JavaScript- und Bilddateien. Diese Dateien müssen am veröffentlichten Speicherort der Anwendung (oder des CDN) gespeichert werden. Außerdem muss auf diese verwiesen werden, damit sie von einer Anforderung geladen werden können. Dieser Prozess wurde in ASP.NET Core geändert.

Statische Dateien werden in ASP.NET in verschiedenen Verzeichnissen gespeichert. Der Verweis auf die Dateien erfolgt in den Ansichten.

In ASP.NET Core werden statische Dateien im Webstammverzeichnis ( *&lt;content root&gt;/wwwroot*) gespeichert, falls keine anderen Einstellungen vorgenommen wurden. Die Dateien werden über den Aufruf der Erweiterungsmethode `UseStaticFiles` aus `Startup.Configure` in die Anforderungspipeline geladen:

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

> [!NOTE]
> Wenn Sie Anwendungen für .NET Framework entwickeln, installieren Sie das NuGet-Paket `Microsoft.AspNetCore.StaticFiles`.

Beispielsweise kann ein Browser an einem Speicherort wie `http://<app>/images/<imageFileName>` auf ein Bildobjekt im Ordner *wwwroot/images* zugreifen.

> [!NOTE]
> Ausführliche Informationen zum Bereitstellen statischer Dateien in ASP.NET Core finden Sie im Artikel zu [statischen Dateien](xref:fundamentals/static-files).

## <a name="multi-value-no-loccookies"></a>Mehrwertige cookies

[Mehrwertige cookies](xref:System.Web.HttpCookie.Values) werden in ASP.NET Core nicht unterstützt. Erstellen Sie ein cookie pro Wert.

## <a name="partial-app-migration"></a>Partielle App-Migration

Ein Ansatz für die partielle App-Migration besteht darin, eine IIS-Unteranwendung zu erstellen und nur bestimmte Routen von ASP.NET 4.x zu ASP.NET Core zu verschieben, wobei die URL-Struktur der App beibehalten wird. Betrachten Sie beispielsweise die URL-Struktur der Anwendung aus der Datei *applicationHost.config*:

```xml
<sites>
    <site name="Default Web Site" id="1" serverAutoStart="true">
        <application path="/">
            <virtualDirectory path="/" physicalPath="D:\sites\MainSite\" />
        </application>
        <application path="/api" applicationPool="DefaultAppPool">
            <virtualDirectory path="/" physicalPath="D:\sites\netcoreapi" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:80:" />
            <binding protocol="https" bindingInformation="*:443:" sslFlags="0" />
        </bindings>
    </site>
    ...
</sites>
```

Verzeichnisstruktur:

```
.
├── MainSite
│   ├── ...
│   └── Web.config
└── NetCoreApi
    ├── ...
    └── web.config
```

## <a name="bind-and-input-formatters"></a>[Bind] und Eingabeformatierer

In [früheren Versionen von ASP.NET](/aspnet/mvc/overview/getting-started/introduction/examining-the-edit-methods-and-edit-view) wurde das `[Bind]`-Attribut zum Schutz vor Overpostingangriffen verwendet. [Eingabeformatierer](xref:mvc/models/model-binding#input-formatters) funktionieren in ASP.NET Core unterschiedlich. Das `[Bind]`-Attribut ist nicht mehr darauf ausgelegt, Overposting zu verhindern, wenn es mit Eingabeformatierern für die Verarbeitung von JSON- oder XML-Dateien verwendet wird. Diese Attribute wirken sich auf die Modellbindung aus, wenn es sich bei den Quelldaten um Formulardaten handelt, die mit dem Inhaltstyp `x-www-form-urlencoded` gepostet wurden.

Für Apps, die JSON-Informationen an Controller senden und JSON-Eingabeformatierer für die Verarbeitung der Daten verwenden, sollten Sie das `[Bind]`-Attribut durch ein Ansichtsmodell ersetzen, das den vom `[Bind]`-Attribut definierten Eigenschaften entspricht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Portieren auf .NET Core – Bibliotheken](/dotnet/core/porting/libraries)
