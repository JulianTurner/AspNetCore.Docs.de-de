---
title: Migrieren von ASP.NET zu ASP.NET Core 2.0
author: isaac2004
description: Hier erhalten Sie Anleitungen zum Migrieren vorhandener ASP.NET MVC-oder Web-API-Anwendungen zu ASP.net Core 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/24/2018
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
uid: migration/mvc2
ms.openlocfilehash: cf7d2e3a94c14fb752180d9349536d17b4557e0a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051329"
---
# <a name="migrate-from-aspnet-to-aspnet-core-20"></a>Migrieren von ASP.NET zu ASP.NET Core 2.0

Von [Isaac Levin](https://isaaclevin.com)

Dieser Artikel dient als Leitfaden zum Migrieren von ASP.NET-Anwendungen zu ASP.NET Core 2.0.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie **einen** der folgenden Schritte aus [.net-Downloads: Windows](https://dotnet.microsoft.com/download):

* .NET Core SDK
* Visual Studio für Windows
  * Workload **ASP.NET und Webentwicklung**
  * Workload **Plattformübergreifende .NET Core-Entwicklung**

## <a name="target-frameworks"></a>Zielframeworks

ASP.NET Core 2.0-Projekte bieten Entwicklern die Flexibilität, Anwendungen für .NET Core, .NET Framework oder für beide Frameworks zu erstellen. Informationen zur Auswahl eines geeigneten Frameworks finden Sie unter [Wahl zwischen .NET Core und .NET Framework für Server-Apps](/dotnet/standard/choosing-core-framework-server).

Bei der Erstellung von Anwendungen für .NET Framework müssen Projekte auf einzelne NuGet-Pakete verweisen.

Wenn das Zielframework .NET Core ist, können Sie mit dem [Metapaket](xref:fundamentals/metapackage) für ASP.NET Core 2.0 auf die meisten expliziten Paketverweise verzichten. Das `Microsoft.AspNetCore.All`-Metapaket können Sie folgendermaßen in Ihrem Projekt installieren:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.9" />
</ItemGroup>
```

Wenn das Metapaket verwendet wird, werden mit der Anwendung keine Pakete bereitgestellt, auf die im Metapaket verwiesen wird. Die notwendigen Objekte sind im .NET Core-Laufzeitspeicher vorhanden und werden zur Verbesserung der Leistung vorkompiliert. Weitere Informationen finden Sie unter <xref:fundamentals/metapackage> .

## <a name="project-structure-differences"></a>Unterschiede bei Projektstrukturen

Das Dateiformat *.csproj* wurde in ASP.NET Core vereinfacht. Zu einigen wichtigen Änderungen gehören folgende Punkte:

* Dateien müssen nicht explizit eingebunden werden, um als Teil des Projekts behandelt zu werden. Dadurch wird in großen Entwicklerteams das Risiko von Konflikten beim Zusammenführen von XML-Dateien reduziert.
* Auf GUID-Verweise zu anderen Projekten wird verzichtet, wodurch die Lesbarkeit von Dateien erhöht wird.
* Die Datei kann bearbeitet werden, ohne in Visual Studio entladen zu werden:

  ![Kontextmenüoption „Edit CSPROJ“ (CSPROJ-Datei bearbeiten) in Visual Studio 2017](_static/EditProjectVs2017.png)

## <a name="globalasax-file-replacement"></a>Ersetzen der Datei „Global.asax“

Mit ASP.NET Core wurde ein neuer Mechanismus für den Bootstrap einer Anwendung eingeführt. Der Einstiegspunkt für ASP.NET-Anwendungen ist die Datei *Global.asax* . In der Datei *Global.asax* werden Aufgaben wie die Routenkonfiguration, die Einrichtung von Filtern und Bereichsregistrierungen bearbeitet.

[!code-csharp[](samples/globalasax-sample.cs)]

Bei diesem Ansatz werden die Anwendung und der Server, auf dem die Anwendung bereitgestellt wird, so miteinander gekoppelt, dass es zu Konflikten mit der Implementierung kommt. [OWIN](https://owin.org/) wurde mit dem Ziel eingeführt, beide Komponenten zu entkoppeln und so mehrere Frameworks leichter gemeinsam verwenden zu können. OWIN stellt eine Pipeline zur Verfügung, über die nur die benötigten Module hinzugefügt werden. Die Hostingumgebung verwendet eine [Startup](xref:fundamentals/startup)-Funktion, um Dienste und die Anforderungspipeline der Anwendung zu konfigurieren. `Startup` registriert die Middleware bei der Anwendung. Bei jeder Anforderung ruft die Anwendung alle Middlewarekomponenten auf, wobei der Hauptzeiger einer verknüpften Liste auf die vorhandenen Handler zeigt. Jede Middlewarekomponente kann einen oder mehrere Handler zur Anforderungspipeline hinzufügen. Möglich wird dies, indem ein Verweis auf den Handler zurückgegeben wird, der zum neuen ersten Element der Liste wird. Jeder Handler ist dafür verantwortlich, sich den nächsten Handler in der Liste zu merken und diesen aufzurufen. In ASP.NET Core ist `Startup` der Einstiegspunkt für eine Anwendung. Eine Abhängigkeit von *Global.asax* ist nicht mehr vorhanden. Wenn Sie OWIN mit .NET Framework verwenden möchten, können Sie sich beispielsweise an folgendem Code für die Pipeline orientieren:

[!code-csharp[](samples/webapi-owin.cs)]

Hierdurch werden Ihre Standardrouten konfiguriert. Außerdem wird standardmäßig XmlSerialization anstelle von JSON verwendet. Bei Bedarf können Sie weitere Middleware (z.B. zum Laden von Diensten, für Konfigurationseinstellungen, für statische Dateien usw.) zur Pipeline hinzufügen.

ASP.NET Core verwendet einen ähnlichen Ansatz, ist jedoch hinsichtlich des Einstiegspunkts nicht auf OWIN angewiesen. Stattdessen kommt – ähnlich wie bei Konsolenanwendungen – in *Program.cs* die `Main`-Methode zum Einsatz, in der `Startup` geladen wird.

[!code-csharp[](samples/program.cs)]

In `Startup` muss die `Configure`-Methode enthalten sein. Fügen Sie in `Configure` der Pipeline die erforderliche Middleware hinzu. Im folgenden Beispiel, das der Standardwebsitevorlage entnommen wurde, werden mehrere Erweiterungsmethoden zum Konfigurieren der Pipeline verwendet. Hierdurch wird Folgendes unterstützt:

* [BrowserLink](https://vswebessentials.com/features/browserlink)
* Fehlerseiten
* Statische Dateien
* ASP.NET Core MVC
* Identity

[!code-csharp[](../../common/samples/WebApplication1/Startup.cs?highlight=8,9,10,14,17,19,21&start=58&end=84)]

Durch die Entkopplung von Host und Anwendung wird die Möglichkeit geschaffen, in der Zukunft eine Migration zu einer anderen Plattform vorzunehmen.

Einen ausführlicheren Verweis auf ASP.net Core Start und Middleware finden Sie unter <xref:fundamentals/startup> .

## <a name="storing-configurations"></a>Speichern von Konfigurationsdaten

ASP.NET unterstützt das Speichern von Einstellungen. Diese Einstellungen dienen z.B. der Unterstützung der Umgebung, in der die Anwendungen bereitgestellt werden. Häufig werden alle benutzerdefinierten Schlüssel-Wert-Paare im Abschnitt `<appSettings>` der Datei *Web.config* gespeichert:

[!code-xml[](samples/webconfig-sample.xml)]

Anwendungen lesen diese Einstellungen über die `ConfigurationManager.AppSettings`-Auflistung im `System.Configuration`-Namespace aus:

[!code-csharp[](samples/read-webconfig.cs)]

ASP.NET Core kann Konfigurationsdaten der Anwendung in einer beliebigen Datei speichern und diese während des Middleware-Bootstraps laden. Die in den Projektvorlagen verwendete Standarddatei lautet wie folgt *appsettings.json* :

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

**Hinweis:** Einen ausführlicheren Verweis auf ASP.net Core Konfiguration finden Sie unter <xref:fundamentals/configuration/index> .

## <a name="native-dependency-injection"></a>Native Abhängigkeitsinjektion

Ein wichtiges Ziel bei der Erstellung großer, skalierbarer Anwendungen besteht in der losen Kopplung von Komponenten und Diensten. Die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) ist eine gängige Vorgehensweise, um dies zu erreichen, und es handelt sich um eine native Komponente von ASP.net Core.

In ASP.NET-Anwendungen greifen Entwickler auf eine Drittanbieter Bibliothek zurück, um eine Abhängigkeitsinjektion zu implementieren. Eine solche Bibliothek ist [Unity](https://github.com/unitycontainer/unity), die von Microsoft Patterns & Practices bereitgestellt wird.

Ein Beispiel für das Einrichten der Abhängigkeitsinjektion mit Unity ist die Implementierung von `IDependencyResolver` , die einen umschließt `UnityContainer` :

[!code-csharp[](samples/sample8.cs)]

Erstellen Sie eine Instanz von `UnityContainer`, registrieren Sie den Dienst, und weisen Sie für Ihren Container den Abhängigkeitskonfliktlöser von `HttpConfiguration` der neuen Instanz von `UnityResolver` zu:

[!code-csharp[](samples/sample9.cs)]

Fügen Sie bei Bedarf `IProductRepository` ein:

[!code-csharp[](samples/sample5.cs)]

Da die Abhängigkeitsinjektion Teil ASP.net Core ist, können Sie Ihren Dienst in hinzufügen `Startup.ConfigureServices` :

[!code-csharp[](samples/configure-services.cs)]

Genau wie bei Unity kann auch hier das Repository an einer beliebigen Stelle eingefügt werden.

Weitere Informationen zur Abhängigkeitsinjektion in ASP.net Core finden Sie unter <xref:fundamentals/dependency-injection> .

## <a name="serving-static-files"></a>Bereitstellen statischer Dateien

Ein wichtiger Teil der Webentwicklung ist die Möglichkeit, statische, clientseitige Objekte bereitzustellen. Die häufigsten Beispiele für statische Dateien sind HTML-, CSS-, JavaScript- und Bilddateien. Diese Dateien müssen am veröffentlichten Speicherort der Anwendung (oder des CDN) gespeichert werden. Außerdem muss auf diese verwiesen werden, damit sie von einer Anforderung geladen werden können. Dieser Prozess wurde in ASP.NET Core geändert.

Statische Dateien werden in ASP.NET in verschiedenen Verzeichnissen gespeichert. Der Verweis auf die Dateien erfolgt in den Ansichten.

In ASP.NET Core werden statische Dateien im Webstammverzeichnis ( *&lt;content root&gt;/wwwroot* ) gespeichert, falls keine anderen Einstellungen vorgenommen wurden. Die Dateien werden über den Aufruf der Erweiterungsmethode `UseStaticFiles` aus `Startup.Configure` in die Anforderungspipeline geladen:

[!code-csharp[](../../fundamentals/static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?highlight=3&name=snippet_ConfigureMethod)]

**Hinweis:** Wenn Sie Anwendungen für .NET Framework entwickeln, installieren Sie das NuGet-Paket `Microsoft.AspNetCore.StaticFiles`.

Beispielsweise kann ein Browser an einem Speicherort wie `http://<app>/images/<imageFileName>` auf ein Bildobjekt im Ordner *wwwroot/images* zugreifen.

**Hinweis:** Eine ausführlichere Referenz zum betreuen statischer Dateien in ASP.net Core finden Sie unter <xref:fundamentals/static-files> .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Portieren auf .NET Core – Bibliotheken](/dotnet/core/porting/libraries)
