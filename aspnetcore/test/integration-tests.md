---
title: Integrationstests in ASP.NET Core
author: rick-anderson
description: In diesem Artikel erfahren Sie, wie Integrationstests sicherstellen, dass die Komponenten einer App auf der Infrastrukturebene ordnungsgemäß funktionieren, einschließlich der Datenbank, dem Dateisystem und dem Netzwerk.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/14/2020
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
uid: test/integration-tests
ms.openlocfilehash: 9b36a77730a43c7515fcd2c56621412453784c9d
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722539"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrationstests in ASP.NET Core

Von [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) und [Jos van der Til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Integrationstests stellen sicher, dass die Komponenten einer App auf einer Ebene, die die unterstützende Infrastruktur der App (wie die Datenbank, das Dateisystem und das Netzwerk) umfasst, ordnungsgemäß funktionieren. ASP.NET Core unterstützt Integrationstests mithilfe eines Komponententest-Frameworks mit einem Testwebhost und einem In-Memory-Testserver.

In diesem Thema werden Grundkenntnisse über Komponententests vorausgesetzt. Wenn Sie nicht mit Testkonzepten vertraut sind, lesen Sie das Thema [Komponententests in .NET Core und .NET Standard](/dotnet/core/testing/) und zugehörige Inhalte.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Bei der Beispiel-App handelt es sich um eine Razor Pages-App, hierfür werden grundlegende Kenntnisse über Razor Pages vorausgesetzt. Wenn Sie nicht mit Razor Pages vertraut sind, lesen Sie die folgenden Themen:

* [Einführung in Razor Pages](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)

> [!NOTE]
> Zum Testen von Single-Page-Webanwendungen empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/) zu verwenden, mit dem ein Browser automatisiert werden kann.

## <a name="introduction-to-integration-tests"></a>Einführung in Integrationstests

Integrationstests bewerten die Komponenten einer App auf breiterer Ebene als [Komponententests](/dotnet/core/testing/). Komponententests werden verwendet, um isolierte Softwarekomponenten wie z. B. einzelne Klassenmethoden zu testen. Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erzielen, ggf. auch unter Einbindung aller Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.

Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der App zu testen, häufig einschließlich der folgenden Komponenten:

* Datenbank
* Dateisystem
* Netzwerk-Appliances
* Anforderung/Antwort-Pipeline

Komponententests verwenden anstelle von Infrastrukturkomponenten künstliche Komponenten, die als *Fakes* oder *Pseudoobjekte* bezeichnet werden.

Im Gegensatz zu Komponententests gilt für Integrationstests:

* Sie verwenden die tatsächlichen Komponenten, die von der App in der Produktionsumgebung verwendet werden.
* Sie erfordern mehr Code und Datenverarbeitung.
* Ihre Ausführung dauert länger.

Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastrukturszenarios. Wenn ein Verhalten mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponententest.

> [!TIP]
> Schreiben Sie keine Integrationstests für jede denkbare Permutation des Daten- und Dateizugriffs bei Datenbanken und Dateisystemen. Unabhängig davon, wie viele Elemente in einer App mit Datenbanken und Dateisystemen interagieren, ist ein fokussierter Satz von Lese-, Schreib-, Update- und Lösch-Integrationstests üblicherweise in der Lage, die Datenbank- und Dateisystemkomponenten adäquat zu testen. Verwenden Sie Komponententests für Routinetests der Methodenlogik, die mit diesen Komponenten interagieren. Bei Komponententests führt die Verwendung von Infrastruktur-Fakes/-Pseudoobjekten zu einer schnelleren Testausführung.

> [!NOTE]
> Bei der Besprechung von Integrationstests wird das getestete Projekt häufig als *getestetes System* oder kurz „GS“ bezeichnet.
>
> *In diesem Thema wird „GS“ verwendet, um auf die getestete ASP.NET Core-App zu verweisen.*

## <a name="aspnet-core-integration-tests"></a>Integrationstests in ASP.NET Core

Integrationstests in ASP.NET Core erfordern Folgendes:

* Es wird ein Testprojekt verwendet, um die Tests einzugrenzen und auszuführen. Das Testprojekt enthält einen Verweis auf das GS.
* Das Testprojekt erstellt einen Testwebhost für das GS und verwendet einen Testserverclient, um Anforderungen und Antworten im Zusammenhang mit dem GS zu verarbeiten.
* Um die Tests auszuführen und die Testergebnisse zu melden, wird ein Test-Runner verwendet.

Integrationstests folgen einer Sequenz von Ereignissen, die die üblichen Testschritte *Arrange*, *Act* und *Assert* umfassen:

1. Der Webhost des GS wird konfiguriert.
1. Es wird ein Testserverclient erstellt, um Anforderungen an die App zu senden.
1. Der Testschritt *Arrange* wird ausgeführt: Die Test-App bereitet eine Anforderung vor.
1. Der Testschritt *Act* wird ausgeführt: Der Client sendet die Anforderung und empfängt die Antwort.
1. Der Testschritt *Assert* wird ausgeführt: Die *tatsächliche* Antwort wird je nach der *erwarteten* Antwort als *Pass* oder *Fail* bewertet.
1. Der Prozess wird so lange fortgesetzt, bis alle Tests ausgeführt wurden.
1. Die Testergebnisse werden gemeldet.

Üblicherweise ist der Testwebhost anders konfiguriert als der normale Webhost der App für die Testläufe. Beispielsweise könnten für die Tests eine andere Datenbank oder andere App-Einstellungen verwendet werden.

Infrastrukturkomponenten wie der Testwebhost und der In-Memory-Testserver ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) werden durch das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket bereitgestellt oder verwaltet. Durch die Verwendung dieses Pakets werden die Testerstellung und -ausführung optimiert.

Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:

* Es kopiert die Datei für Abhängigkeiten ( *.deps*) aus dem GS in das *bin-* Verzeichnis des Testprojekts.
* Es legt das [Inhaltsstammelement](xref:fundamentals/index#content-root) auf das Projektstammelement des GS fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung der Tests gefunden werden.
* Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings des GS mit `TestServer` bereit.

In der Dokumentation der [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test-Runner einrichten. Ferner finden Sie dort ausführliche Anweisungen zum Ausführen von Tests sowie Empfehlungen zum Benennen von Tests und Testklassen.

> [!NOTE]
> Wenn Sie ein Testprojekt für eine App erstellen, verwenden Sie unterschiedliche Projekte für die Komponententests und die Integrationstests. Dadurch wird sichergestellt, dass Komponenten für Infrastrukturtests nicht versehentlich in die Komponententests eingeschlossen werden. Durch die Trennung von Komponenten- und Integrationstests können Sie außerdem steuern, welche Testsätze ausgeführt werden.

Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-Apps und MVC-Apps. Der einzige Unterschied besteht darin, wie die Tests benannt werden. In einer Razor Pages-App werden Tests von Seitenendpunkten normalerweise nach der Seitenmodellklasse benannt (z. B. `IndexPageTests` für das Testen der Komponentenintegration für die Indexseite). In einer MVC-App werden Tests in der Regel nach Controllerklassen organisiert und nach den von ihnen getesteten Controllern benannt (z. B. `HomeControllerTests` für das Testen der Komponentenintegration für den Startseitencontroller).

## <a name="test-app-prerequisites"></a>Voraussetzungen für Test-Apps

Für das Testprojekt muss Folgendes erfüllt sein:

* Verweisen Sie auf das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket.
* Geben Sie das Web SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Diese Voraussetzungen können Sie in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) sehen. Untersuchen Sie die Datei *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/)-Testframework und die [AngleSharp](https://anglesharp.github.io/)-Parserbibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:

* [xunit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core wird ebenfalls in den Tests verwendet. Die App verweist auf:

* [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>GS-Umgebung

Wenn die [Umgebung](xref:fundamentals/environments) des GS nicht festgelegt ist, wird standardmäßig die Entwicklungsumgebung verwendet.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Grundlegende Tests mit der Standard-WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen. `TEntryPoint` ist die Einstiegspunktklasse des GS, in der Regel die `Startup`-Klasse.

Testklassen implementieren eine *class fixture*-Schnittstelle ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält und um in den Tests in der Klasse gemeinsame Objektinstanzen bereitzustellen.

Die folgende Testklasse (`BasicTests`) verwendet die `WebApplicationFactory` für den Bootstrap des GS und um einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode `Get_EndpointsReturnSuccessAndCorrectContentType` bereitzustellen. Die Methode prüft, ob der Antwortstatuscode erfolgreich ist (Statuscodes im Bereich 200-299) und der `Content-Type`-Header für mehrere App-Seiten `text/html; charset=utf-8` lautet.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen folgt und cookies verarbeitet.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Standardmäßig werden nicht erforderliche cookies nicht über Anforderungen hinweg beibehalten, wenn die [DSGVO-Zustimmungsrichtlinie](xref:security/gdpr) aktiviert ist. Markieren Sie die Cookies in den Tests als unverzichtbar, um nicht erforderliche cookies beizubehalten, wie z. B. diejenigen, die vom TempData-Anbieter verwendet werden. Anweisungen zum Markieren eines cookies als erforderlich finden Sie unter [Erforderliche cookies](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Anpassen von WebApplicationFactory

Die Webhostkonfiguration kann unabhängig von den Testklassen durch Erben von der `WebApplicationFactory` erstellt werden, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:

1. Erben Sie von `WebApplicationFactory`, und überschreiben Sie [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) erlaubt die Konfiguration der Dienstsammlung mit [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Das Datenbankseeding in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird mithilfe der `InitializeDbForTests`-Methode durchgeführt. Die Methode wird im Abschnitt [Beispiel für Integrationstests: Organisation der Test-App](#test-app-organization) beschrieben.

   Der Datenbankkontext des GS wird in dessen `Startup.ConfigureServices`-Methode registriert. Der `builder.ConfigureServices`-Rückruf der Test-App wird ausgeführt, *nachdem* der `Startup.ConfigureServices`-Code der App ausgeführt wurde. Die Ausführungsreihenfolge ist im Release von ASP.NET Core 3.0 eine Breaking Change für den [generischen Host](xref:fundamentals/host/generic-host). Um für die Tests eine andere Datenbank als die Datenbank der App zu verwenden, muss der Datenbankkontext der App in `builder.ConfigureServices` ersetzt werden.

   Für GS, die weiterhin den [Webhost](xref:fundamentals/host/web-host) verwenden, wird der `builder.ConfigureServices`-Rückruf der Test-App ausgeführt, *bevor* der `Startup.ConfigureServices`-Code des GS ausgeführt wird. Der `builder.ConfigureTestServices`-Rückruf der Test-App wird *danach* ausgeführt.

   Die Beispiel-App findet den Dienstdeskriptor für den Datenbankkontext und verwendet den Deskriptor, um die Dienstregistrierung zu entfernen. Als Nächstes fügt die Factory einen neuen `ApplicationDbContext` hinzu, der eine In-Memory-Datenbank für die Tests verwendet.

   Um eine Verbindung mit einer anderen Datenbank als der In-Memory-Datenbank herzustellen, ändern Sie den `UseInMemoryDatabase`-Aufruf, um den Kontext mit einer anderen Datenbank zu verbinden. So verwenden Sie eine SQL Server-Testdatenbank:

   * Verweisen Sie auf das [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet-Paket in der Projektdatei.
   * Rufen Sie `UseSqlServer` mit einer Verbindungszeichenfolge zu der Datenbank auf.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Testklassen. Das folgende Beispiel verwendet die Factory in der `IndexPageTests`-Klasse:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Der Client der Beispiel-App wird so konfiguriert, dass der `HttpClient` keinen Umleitungen folgt. Wie weiter unten im Abschnitt [Pseudoauthentifizierung](#mock-authentication) erläutert wird, können Tests so das Ergebnis der ersten Reaktion der App überprüfen. In vielen dieser Tests mit einem `Location`-Header ist die erste Antwort eine Umleitung.

3. Ein typischer Test verwendet die `HttpClient`- und die Hilfsprogrammmethoden, um die Anforderung und die Antwort zu verarbeiten:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jede POST-Anforderung an das GS muss die Fälschungsschutzprüfung bestehen, die automatisch vom [Daten- und Fälschungsschutzsystem](xref:security/data-protection/introduction) der App durchgeführt wird. Als Vorbereitung auf die POST-Anforderung eines Tests muss die Test-App folgende Schritte ausführen:

1. Senden einer Anforderung für die Seite.
1. Analysieren des Fälschungsschutzcookies und des Anforderungsvalidierungstokens von der Antwort.
1. Senden der POST-Anforderung mit vorhandenem Fälschungsschutzcookie und Anforderungsvalidierungstoken.

Die `SendAsync`-Hilfsprogrammerweiterungsmethoden (*Helpers/HttpClientExtensions.cs*) und die `GetDocumentAsync`-Hilfsprogrammmethode (*Helpers/HtmlHelpers.cs*) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [AngleSharp](https://anglesharp.github.io/)-Parser, um die Fälschungsschutzprüfungen mit den folgenden Methoden durchzuführen:

* `GetDocumentAsync`: Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt ein `IHtmlDocument` zurück. `GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* basierend auf der ursprünglichen `HttpResponseMessage` vorbereitet. Weitere Informationen finden Sie in der [AngleSharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`-Erweiterungsmethoden für den `HttpClient` verfassen eine [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) und rufen [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das GS zu senden. Überladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und Folgendes:
  * Schaltfläche „Senden“ des Formulars (`IHtmlElement`)
  * Formularwerteauflistung (`IEnumerable<KeyValuePair<string, string>>`)
  * Schaltfläche „Senden“ (`IHtmlElement`) und Formularwerte (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) ist eine Drittanbieter-Analysebibliothek, die in diesem Thema und in der Beispiel-App zu Demonstrationszwecken verwendet wird. AngleSharp wird für Integrationstests von ASP.NET Core-Apps weder unterstützt noch benötigt. Andere Parser können verwendet werden, beispielsweise [Html Agility Pack (HAP)](https://html-agility-pack.net/). Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungsüberprüfungstoken und das Fälschungsschutzcookie des Fälschungsschutzsystems direkt verarbeitet.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Anpassen des Clients mit WithWebHostBuilder

Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), der mittels Konfiguration weiter angepasst wird.

Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Testmethode der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) zeigt die Verwendung von `WithWebHostBuilder`. Bei diesem Test wird eine Datensatzlöschung in der Datenbank durch Auslösen einer Formularübermittlung im GS durchführt.

Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang durchführt, der alle Datensätze in der Datenbank löscht und der möglicherweise vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt wird, wird in dieser Testmethode ein erneutes Seeding der Datenbank durchgeführt, um sicherzustellen, dass ein Datensatz vorhanden ist, den das GS löschen kann. Die Auswahl der ersten Löschschaltfläche des `messages`-Formulars im GS wird in der Anforderung an das GS simuliert:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Clientoptionen

In der folgenden Tabelle werden die Standard-[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gezeigt, die bei der Erstellung von `HttpClient`-Instanzen verfügbar sind.

| Option | Beschreibung | Standard |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen automatisch Umleitungsantworten befolgen sollen. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse der `HttpClient`-Instanzen ab oder legt sie fest. | `http://localhost` |
| [Handle](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)sCookie | Ruft ab oder legt fest, ob `HttpClient`-Instanzen cookies verarbeiten sollen. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungsantworten ab, die von `HttpClient`-Instanzen befolgt werden sollen, oder legt diese fest. | 7 |

Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)-Methode (Standardwerte werden im Codebeispiel gezeigt):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Fügen Sie Pseudodienste ein

Dienste können in einem Test mit dem Aufruf [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) auf dem Host-Builder überschrieben werden. **Um Pseudodienste einzufügen, muss das GS über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**

Das Beispiel-GS enthält einen bereichsbezogenen Dienst, der ein Zitat zurückgibt. Wenn die Indexseite angefordert wird, wird das Zitat in ein ausgeblendetes Feld auf der Indexseite eingebettet.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Das folgende Markup wird generiert, wenn die GS-App ausgeführt wird:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Um den Dienst und die Zitateinfügung in einem Integrationstest zu testen, wird vom Test ein Pseudodienst in das GS eingefügt. Der Pseudodienst ersetzt `QuoteService` der App durch einen Dienst namens `TestQuoteService`, der von der Test-App bereitgestellt wird:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` wird aufgerufen, und der bereichsbezogene Dienst wird registriert:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Das während der Ausführung des Tests erstellte Markup gibt das von `TestQuoteService` bereitgestellte Zitat wieder, somit ist die Assertion erfolgreich:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Pseudo-Authentifizierung

Tests in der `AuthTests`-Klasse prüfen, ob ein sicherer Endpunkt:

* Einen nicht authentifizierten Benutzer an die Anmeldeseite der App zurückleitet.
* Den Inhalt für einen authentifizierten Benutzer zurückgibt.

Im GS verwendet die `/SecurePage`-Seite eine [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)-Konvention, um einen [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden. Weitere Informationen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Im `Get_SecurePageRedirectsAnUnauthenticatedUser`-Test werden [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) so festgelegt, dass Umleitungen unzulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false` festgelegt wird:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Indem dem Client untersagt wird, die Umleitung zu befolgen, können folgende Prüfungen durchgeführt werden:

* Der vom GS zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)-Ergebnis verglichen werden – anstatt mit dem endgültigen Statuscode nach der Umleitung zur Anmeldeseite ([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)).
* Der Wert für den `Location`-Header in den Antwortheadern wird geprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login` beginnt. (Es wird nicht die abschließende Antwort der Anmeldeseite verwendet, bei der der `Location`-Header nicht vorhanden wäre.)

Die Test-App kann einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen. Ein minimales Szenario gibt [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) zurück:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

`TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt wird, in dem `AddAuthentication` für `ConfigureTestServices` registriert ist. Es ist wichtig, dass das `Test`-Schema mit dem Schema übereinstimmt, das Ihre App erwartet. Andernfalls funktioniert die Authentifizierung nicht.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Weitere Informationen zu `WebApplicationFactoryClientOptions` finden Sie im Abschnitt [Clientoptionen](#client-options).

## <a name="set-the-environment"></a>Festlegen der Umgebung

Standardmäßig ist die Host- und App-Umgebung des GS für die Verwendung der Entwicklungsumgebung konfiguriert. So überschreiben Sie die Umgebung des GS bei Verwendung von `IHostBuilder`:

* Legen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable fest (z. B. `Staging`, `Production` oder ein anderer benutzerdefinierter Wert wie `Testing`).
* Überschreiben Sie `CreateHostBuilder` in der Test-App, um Umgebungsvariablen mit dem Präfix `ASPNETCORE` zu lesen.

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

Wenn das GS den Webhost (`IWebHostBuilder`) verwendet, überschreiben Sie `CreateWebHostBuilder`:

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Ableitung des Inhaltsstammpfads der App durch die Testinfrastruktur

Der `WebApplicationFactory`-Konstruktor leitet den [Inhaltsstammpfad](xref:fundamentals/index#content-root) der App ab, indem er in der Assembly, die die Integrationstests enthält, nach einem [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) mit einem Schlüssel sucht, der `TEntryPoint` assembly `System.Reflection.Assembly.FullName` entspricht. Wenn kein Attribut mit dem richtigen Schlüssel gefunden wird, greift `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *.sln*) zurück und fügt den `TEntryPoint`-Assemblynamen an das Projektmappenverzeichnis an. Das Stammverzeichnis der App (der Inhaltsstammpfad) wird verwendet, um Sichten und Inhaltsdateien zu ermitteln.

## <a name="disable-shadow-copying"></a>Deaktivieren der Erstellung von Schattenkopien

Das Erstellen von Schattenkopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden. Damit Tests ordnungsgemäß funktionieren, muss die Erstellung von Schattenkopien deaktiviert werden. Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert die Erstellung von Schattenkopien für xUnit durch Einschließen einer *xunit.runner.json*-Datei mit der korrekten Konfigurationseinstellung. Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).

Fügen Sie die Datei *xunit.runner.json* mit folgendem Inhalt zum Stamm des Testprojekts hinzu:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Verwerfen von Objekten

Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) verwirft. Wenn vom Entwickler instanziierte Objekte verworfen werden müssen, müssen Sie dies in der `IClassFixture`-Implementierung tun. Weitere Informationen finden Sie unter [Implementieren einer Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Beispiel für Integrationstests

Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei Apps:

| App | Projektverzeichnis | Beschreibung |
| --- | ----------------- | ----------- |
| Nachrichten-App (das GS) | *src/RazorPagesProject* | Ermöglicht einem Benutzer, Nachrichten hinzuzufügen, eine oder alle Nachrichten zu löschen und Nachrichten zu analysieren. |
| Testen der App | *tests/RazorPagesProject.Tests* | Wird für den Integrationstest des GS verwendet. |

Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](https://visualstudio.microsoft.com) ausgeführt werden. Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl über eine Eingabeaufforderung im Verzeichnis *tests/RazorPagesProject.Tests* aus:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation der Nachrichten-App (GS)

Beim GS handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:

* Die Indexseite der App (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit denen Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durchschnittliche Anzahl von Wörtern pro Nachricht) steuern können.
* Eine Nachricht wird von der `Message`-Klasse (*Data/Message.cs*) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.
* Die App enthält eine Datenzugriffsebene (DAL) in ihrer Datenbankkontextklasse `AppDbContext` (*Data/AppDbContext.cs*).
* Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.
* Die App enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.

&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert. In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet. Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.

Obwohl die App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testcontrollerlogik](../mvc/controllers/testing.md) (im Beispiel wird das Repositorymuster implementiert).

### <a name="test-app-organization"></a>Organisation der Test-App

Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *tests/RazorPagesProject.Tests*.

| Test-App-Verzeichnis | Beschreibung |
| ------------------ | ----------- |
| *AuthTests* | Enthält Testmethoden für Folgendes:<ul><li>Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</li><li>Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Pseudo-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</li></ul> |
| *BasicTests* | Enthält eine Testmethode für Routing und Inhaltstyp. |
| *IntegrationTests* | Enthält die Integrationstests für die Indexseite unter Verwendung der benutzerdefinierten `WebApplicationFactory`-Klasse. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* enthält die `InitializeDbForTests`-Methode, mit der ein Seeding der Datenbank mit Testdaten durchgeführt wird.</li><li>*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein AngleSharp-`IHtmlDocument` zur Verwendung durch die Testmethoden zurückgegeben wird.</li><li>*HttpClientExtensions.cs* stellt Überladungen für `SendAsync` bereit, um Anforderungen an das GS zu senden.</li></ul> |

Das Testframework ist [xUnit](https://xunit.github.io/). Integrationstests werden mit dem [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) durchgeführt, der den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) umfasst. Da das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket zum Konfigurieren des Testhosts und des Testservers verwendet wird, benötigen die `TestHost`- und `TestServer`-Pakete keine direkten Paketverweise in der Projektdatei der Test-App bzw. keine Entwicklerkonfiguration in der Test-App.

**Seeding der Datenbank für Testzwecke**

Für Integrationstests muss die Datenbank in der Regel vor der Testausführung ein kleines Dataset enthalten. Beispielsweise wird bei einem Löschtest ein Löschvorgang eines Datensatzes der Datenbank abgerufen, weshalb die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.

Die Beispiel-App führt ein Seeding der Datenbank mit drei Nachrichten in *Utilities.cs* durch, die von Tests bei der Ausführung verwendet werden können:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Der Datenbankkontext des GS wird in dessen `Startup.ConfigureServices`-Methode registriert. Der `builder.ConfigureServices`-Rückruf der Test-App wird ausgeführt, *nachdem* der `Startup.ConfigureServices`-Code der App ausgeführt wurde. Um eine andere Datenbank für die Tests zu verwenden, muss der Datenbankkontext der App in `builder.ConfigureServices` ersetzt werden. Weitere Informationen finden Sie im Abschnitt [Anpassen von WebApplicationFactory](#customize-webapplicationfactory).

Für GS, die weiterhin den [Webhost](xref:fundamentals/host/web-host) verwenden, wird der `builder.ConfigureServices`-Rückruf der Test-App ausgeführt, *bevor* der `Startup.ConfigureServices`-Code des GS ausgeführt wird. Der `builder.ConfigureTestServices`-Rückruf der Test-App wird *danach* ausgeführt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Integrationstests stellen sicher, dass die Komponenten einer App auf einer Ebene, die die unterstützende Infrastruktur der App (wie die Datenbank, das Dateisystem und das Netzwerk) umfasst, ordnungsgemäß funktionieren. ASP.NET Core unterstützt Integrationstests mithilfe eines Komponententest-Frameworks mit einem Testwebhost und einem In-Memory-Testserver.

In diesem Thema werden Grundkenntnisse über Komponententests vorausgesetzt. Wenn Sie nicht mit Testkonzepten vertraut sind, lesen Sie das Thema [Komponententests in .NET Core und .NET Standard](/dotnet/core/testing/) und zugehörige Inhalte.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Bei der Beispiel-App handelt es sich um eine Razor Pages-App, hierfür werden grundlegende Kenntnisse über Razor Pages vorausgesetzt. Wenn Sie nicht mit Razor Pages vertraut sind, lesen Sie die folgenden Themen:

* [Einführung in Razor Pages](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)

> [!NOTE]
> Zum Testen von Single-Page-Webanwendungen empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/) zu verwenden, mit dem ein Browser automatisiert werden kann.

## <a name="introduction-to-integration-tests"></a>Einführung in Integrationstests

Integrationstests bewerten die Komponenten einer App auf breiterer Ebene als [Komponententests](/dotnet/core/testing/). Komponententests werden verwendet, um isolierte Softwarekomponenten wie z. B. einzelne Klassenmethoden zu testen. Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erzielen, ggf. auch unter Einbindung aller Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.

Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der App zu testen, häufig einschließlich der folgenden Komponenten:

* Datenbank
* Dateisystem
* Netzwerk-Appliances
* Anforderung/Antwort-Pipeline

Komponententests verwenden anstelle von Infrastrukturkomponenten künstliche Komponenten, die als *Fakes* oder *Pseudoobjekte* bezeichnet werden.

Im Gegensatz zu Komponententests gilt für Integrationstests:

* Sie verwenden die tatsächlichen Komponenten, die von der App in der Produktionsumgebung verwendet werden.
* Sie erfordern mehr Code und Datenverarbeitung.
* Ihre Ausführung dauert länger.

Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastrukturszenarios. Wenn ein Verhalten mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponententest.

> [!TIP]
> Schreiben Sie keine Integrationstests für jede denkbare Permutation des Daten- und Dateizugriffs bei Datenbanken und Dateisystemen. Unabhängig davon, wie viele Elemente in einer App mit Datenbanken und Dateisystemen interagieren, ist ein fokussierter Satz von Lese-, Schreib-, Update- und Lösch-Integrationstests üblicherweise in der Lage, die Datenbank- und Dateisystemkomponenten adäquat zu testen. Verwenden Sie Komponententests für Routinetests der Methodenlogik, die mit diesen Komponenten interagieren. Bei Komponententests führt die Verwendung von Infrastruktur-Fakes/-Pseudoobjekten zu einer schnelleren Testausführung.

> [!NOTE]
> Bei der Besprechung von Integrationstests wird das getestete Projekt häufig als *getestetes System* oder kurz „GS“ bezeichnet.
>
> *In diesem Thema wird „GS“ verwendet, um auf die getestete ASP.NET Core-App zu verweisen.*

## <a name="aspnet-core-integration-tests"></a>Integrationstests in ASP.NET Core

Integrationstests in ASP.NET Core erfordern Folgendes:

* Es wird ein Testprojekt verwendet, um die Tests einzugrenzen und auszuführen. Das Testprojekt enthält einen Verweis auf das GS.
* Das Testprojekt erstellt einen Testwebhost für das GS und verwendet einen Testserverclient, um Anforderungen und Antworten im Zusammenhang mit dem GS zu verarbeiten.
* Um die Tests auszuführen und die Testergebnisse zu melden, wird ein Test-Runner verwendet.

Integrationstests folgen einer Sequenz von Ereignissen, die die üblichen Testschritte *Arrange*, *Act* und *Assert* umfassen:

1. Der Webhost des GS wird konfiguriert.
1. Es wird ein Testserverclient erstellt, um Anforderungen an die App zu senden.
1. Der Testschritt *Arrange* wird ausgeführt: Die Test-App bereitet eine Anforderung vor.
1. Der Testschritt *Act* wird ausgeführt: Der Client sendet die Anforderung und empfängt die Antwort.
1. Der Testschritt *Assert* wird ausgeführt: Die *tatsächliche* Antwort wird je nach der *erwarteten* Antwort als *Pass* oder *Fail* bewertet.
1. Der Prozess wird so lange fortgesetzt, bis alle Tests ausgeführt wurden.
1. Die Testergebnisse werden gemeldet.

Üblicherweise ist der Testwebhost anders konfiguriert als der normale Webhost der App für die Testläufe. Beispielsweise könnten für die Tests eine andere Datenbank oder andere App-Einstellungen verwendet werden.

Infrastrukturkomponenten wie der Testwebhost und der In-Memory-Testserver ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) werden durch das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket bereitgestellt oder verwaltet. Durch die Verwendung dieses Pakets werden die Testerstellung und -ausführung optimiert.

Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:

* Es kopiert die Datei für Abhängigkeiten ( *.deps*) aus dem GS in das *bin-* Verzeichnis des Testprojekts.
* Es legt das [Inhaltsstammelement](xref:fundamentals/index#content-root) auf das Projektstammelement des GS fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung der Tests gefunden werden.
* Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings des GS mit `TestServer` bereit.

In der Dokumentation der [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test-Runner einrichten. Ferner finden Sie dort ausführliche Anweisungen zum Ausführen von Tests sowie Empfehlungen zum Benennen von Tests und Testklassen.

> [!NOTE]
> Wenn Sie ein Testprojekt für eine App erstellen, verwenden Sie unterschiedliche Projekte für die Komponententests und die Integrationstests. Dadurch wird sichergestellt, dass Komponenten für Infrastrukturtests nicht versehentlich in die Komponententests eingeschlossen werden. Durch die Trennung von Komponenten- und Integrationstests können Sie außerdem steuern, welche Testsätze ausgeführt werden.

Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-Apps und MVC-Apps. Der einzige Unterschied besteht darin, wie die Tests benannt werden. In einer Razor Pages-App werden Tests von Seitenendpunkten normalerweise nach der Seitenmodellklasse benannt (z. B. `IndexPageTests` für das Testen der Komponentenintegration für die Indexseite). In einer MVC-App werden Tests in der Regel nach Controllerklassen organisiert und nach den von ihnen getesteten Controllern benannt (z. B. `HomeControllerTests` für das Testen der Komponentenintegration für den Startseitencontroller).

## <a name="test-app-prerequisites"></a>Voraussetzungen für Test-Apps

Für das Testprojekt muss Folgendes erfüllt sein:

* Verweisen Sie auf die folgenden Pakete:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Geben Sie das Web SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Das Web SDK ist bei Verweis auf das [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) erforderlich.

Diese Voraussetzungen können Sie in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) sehen. Untersuchen Sie die Datei *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*. Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/)-Testframework und die [AngleSharp](https://anglesharp.github.io/)-Parserbibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>GS-Umgebung

Wenn die [Umgebung](xref:fundamentals/environments) des GS nicht festgelegt ist, wird standardmäßig die Entwicklungsumgebung verwendet.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Grundlegende Tests mit der Standard-WebApplicationFactory

[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen. `TEntryPoint` ist die Einstiegspunktklasse des GS, in der Regel die `Startup`-Klasse.

Testklassen implementieren eine *class fixture*-Schnittstelle ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält und um in den Tests in der Klasse gemeinsame Objektinstanzen bereitzustellen.

Die folgende Testklasse (`BasicTests`) verwendet die `WebApplicationFactory` für den Bootstrap des GS und um einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode `Get_EndpointsReturnSuccessAndCorrectContentType` bereitzustellen. Die Methode prüft, ob der Antwortstatuscode erfolgreich ist (Statuscodes im Bereich 200-299) und der `Content-Type`-Header für mehrere App-Seiten `text/html; charset=utf-8` lautet.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen folgt und cookies verarbeitet.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Standardmäßig werden nicht erforderliche cookies nicht über Anforderungen hinweg beibehalten, wenn die [DSGVO-Zustimmungsrichtlinie](xref:security/gdpr) aktiviert ist. Markieren Sie die Cookies in den Tests als unverzichtbar, um nicht erforderliche cookies beizubehalten, wie z. B. diejenigen, die vom TempData-Anbieter verwendet werden. Anweisungen zum Markieren eines cookies als erforderlich finden Sie unter [Erforderliche cookies](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Anpassen von WebApplicationFactory

Die Webhostkonfiguration kann unabhängig von den Testklassen durch Erben von der `WebApplicationFactory` erstellt werden, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:

1. Erben Sie von `WebApplicationFactory`, und überschreiben Sie [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) erlaubt die Konfiguration der Dienstsammlung mit [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), die vor den `Startup.ConfigureServices` der App ausgeführt werden. Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ermöglicht das Überschreiben und Ändern registrierter Dienste in der Dienstsammlung mit [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Das Datenbankseeding in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird mithilfe der `InitializeDbForTests`-Methode durchgeführt. Die Methode wird im Abschnitt [Beispiel für Integrationstests: Organisation der Test-App](#test-app-organization) beschrieben.

2. Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Testklassen. Das folgende Beispiel verwendet die Factory in der `IndexPageTests`-Klasse:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Der Client der Beispiel-App wird so konfiguriert, dass der `HttpClient` keinen Umleitungen folgt. Wie weiter unten im Abschnitt [Pseudoauthentifizierung](#mock-authentication) erläutert wird, können Tests so das Ergebnis der ersten Reaktion der App überprüfen. In vielen dieser Tests mit einem `Location`-Header ist die erste Antwort eine Umleitung.

3. Ein typischer Test verwendet die `HttpClient`- und die Hilfsprogrammmethoden, um die Anforderung und die Antwort zu verarbeiten:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jede POST-Anforderung an das GS muss die Fälschungsschutzprüfung bestehen, die automatisch vom [Daten- und Fälschungsschutzsystem](xref:security/data-protection/introduction) der App durchgeführt wird. Als Vorbereitung auf die POST-Anforderung eines Tests muss die Test-App folgende Schritte ausführen:

1. Senden einer Anforderung für die Seite.
1. Analysieren des Fälschungsschutzcookies und des Anforderungsvalidierungstokens von der Antwort.
1. Senden der POST-Anforderung mit vorhandenem Fälschungsschutzcookie und Anforderungsvalidierungstoken.

Die `SendAsync`-Hilfsprogrammerweiterungsmethoden (*Helpers/HttpClientExtensions.cs*) und die `GetDocumentAsync`-Hilfsprogrammmethode (*Helpers/HtmlHelpers.cs*) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [AngleSharp](https://anglesharp.github.io/)-Parser, um die Fälschungsschutzprüfungen mit den folgenden Methoden durchzuführen:

* `GetDocumentAsync`: Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt ein `IHtmlDocument` zurück. `GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* basierend auf der ursprünglichen `HttpResponseMessage` vorbereitet. Weitere Informationen finden Sie in der [AngleSharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync`-Erweiterungsmethoden für den `HttpClient` verfassen eine [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) und rufen [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das GS zu senden. Überladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und Folgendes:
  * Schaltfläche „Senden“ des Formulars (`IHtmlElement`)
  * Formularwerteauflistung (`IEnumerable<KeyValuePair<string, string>>`)
  * Schaltfläche „Senden“ (`IHtmlElement`) und Formularwerte (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) ist eine Drittanbieter-Analysebibliothek, die in diesem Thema und in der Beispiel-App zu Demonstrationszwecken verwendet wird. AngleSharp wird für Integrationstests von ASP.NET Core-Apps weder unterstützt noch benötigt. Andere Parser können verwendet werden, beispielsweise [Html Agility Pack (HAP)](https://html-agility-pack.net/). Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungsüberprüfungstoken und das Fälschungsschutzcookie des Fälschungsschutzsystems direkt verarbeitet.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Anpassen des Clients mit WithWebHostBuilder

Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), der mittels Konfiguration weiter angepasst wird.

Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Testmethode der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) zeigt die Verwendung von `WithWebHostBuilder`. Bei diesem Test wird eine Datensatzlöschung in der Datenbank durch Auslösen einer Formularübermittlung im GS durchführt.

Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang durchführt, der alle Datensätze in der Datenbank löscht und der möglicherweise vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt wird, wird in dieser Testmethode ein erneutes Seeding der Datenbank durchgeführt, um sicherzustellen, dass ein Datensatz vorhanden ist, den das GS löschen kann. Die Auswahl der ersten Löschschaltfläche des `messages`-Formulars im GS wird in der Anforderung an das GS simuliert:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Clientoptionen

In der folgenden Tabelle werden die Standard-[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gezeigt, die bei der Erstellung von `HttpClient`-Instanzen verfügbar sind.

| Option | BESCHREIBUNG | Standard |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen automatisch Umleitungsantworten befolgen sollen. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse der `HttpClient`-Instanzen ab oder legt sie fest. | `http://localhost` |
| [Handle](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies)sCookie | Ruft ab oder legt fest, ob `HttpClient`-Instanzen cookies verarbeiten sollen. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungsantworten ab, die von `HttpClient`-Instanzen befolgt werden sollen, oder legt diese fest. | 7 |

Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)-Methode (Standardwerte werden im Codebeispiel gezeigt):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Fügen Sie Pseudodienste ein

Dienste können in einem Test mit dem Aufruf [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) auf dem Host-Builder überschrieben werden. **Um Pseudodienste einzufügen, muss das GS über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**

Das Beispiel-GS enthält einen bereichsbezogenen Dienst, der ein Zitat zurückgibt. Wenn die Indexseite angefordert wird, wird das Zitat in ein ausgeblendetes Feld auf der Indexseite eingebettet.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Das folgende Markup wird generiert, wenn die GS-App ausgeführt wird:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Um den Dienst und die Zitateinfügung in einem Integrationstest zu testen, wird vom Test ein Pseudodienst in das GS eingefügt. Der Pseudodienst ersetzt `QuoteService` der App durch einen Dienst namens `TestQuoteService`, der von der Test-App bereitgestellt wird:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` wird aufgerufen, und der bereichsbezogene Dienst wird registriert:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Das während der Ausführung des Tests erstellte Markup gibt das von `TestQuoteService` bereitgestellte Zitat wieder, somit ist die Assertion erfolgreich:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Pseudo-Authentifizierung

Tests in der `AuthTests`-Klasse prüfen, ob ein sicherer Endpunkt:

* Einen nicht authentifizierten Benutzer an die Anmeldeseite der App zurückleitet.
* Den Inhalt für einen authentifizierten Benutzer zurückgibt.

Im GS verwendet die `/SecurePage`-Seite eine [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)-Konvention, um einen [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden. Weitere Informationen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Im `Get_SecurePageRedirectsAnUnauthenticatedUser`-Test werden [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) so festgelegt, dass Umleitungen unzulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false` festgelegt wird:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Indem dem Client untersagt wird, die Umleitung zu befolgen, können folgende Prüfungen durchgeführt werden:

* Der vom GS zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)-Ergebnis verglichen werden – anstatt mit dem endgültigen Statuscode nach der Umleitung zur Anmeldeseite ([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)).
* Der Wert für den `Location`-Header in den Antwortheadern wird geprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login` beginnt. (Es wird nicht die abschließende Antwort der Anmeldeseite verwendet, bei der der `Location`-Header nicht vorhanden wäre.)

Die Test-App kann einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen. Ein minimales Szenario gibt [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) zurück:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Der `TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt ist, in dem `AddAuthentication` für `ConfigureTestServices` registriert ist:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Weitere Informationen zu `WebApplicationFactoryClientOptions` finden Sie im Abschnitt [Clientoptionen](#client-options).

## <a name="set-the-environment"></a>Festlegen der Umgebung

Standardmäßig ist die Host- und App-Umgebung des GS für die Verwendung der Entwicklungsumgebung konfiguriert. So überschreiben Sie die Umgebung des GS:

* Legen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable fest (z. B. `Staging`, `Production` oder ein anderer benutzerdefinierter Wert wie `Testing`).
* Überschreiben Sie `CreateWebHostBuilder` in der Test-App, um die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable zu lesen.

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

Die Umgebung kann auch direkt auf dem Hostgenerator in einer benutzerdefinierten <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> festgelegt werden:

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Ableitung des Inhaltsstammpfads der App durch die Testinfrastruktur

Der `WebApplicationFactory`-Konstruktor leitet den [Inhaltsstammpfad](xref:fundamentals/index#content-root) der App ab, indem er in der Assembly, die die Integrationstests enthält, nach einem [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) mit einem Schlüssel sucht, der `TEntryPoint` assembly `System.Reflection.Assembly.FullName` entspricht. Wenn kein Attribut mit dem richtigen Schlüssel gefunden wird, greift `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *.sln*) zurück und fügt den `TEntryPoint`-Assemblynamen an das Projektmappenverzeichnis an. Das Stammverzeichnis der App (der Inhaltsstammpfad) wird verwendet, um Sichten und Inhaltsdateien zu ermitteln.

## <a name="disable-shadow-copying"></a>Deaktivieren der Erstellung von Schattenkopien

Das Erstellen von Schattenkopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden. Damit Tests ordnungsgemäß funktionieren, muss die Erstellung von Schattenkopien deaktiviert werden. Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert die Erstellung von Schattenkopien für xUnit durch Einschließen einer *xunit.runner.json*-Datei mit der korrekten Konfigurationseinstellung. Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).

Fügen Sie die Datei *xunit.runner.json* mit folgendem Inhalt zum Stamm des Testprojekts hinzu:

```json
{
  "shadowCopy": false
}
```

Wenn Sie Visual Studio verwenden, legen Sie die Eigenschaft **In Ausgabeverzeichnis kopieren** der Datei auf **Immer kopieren** fest. Wenn Sie Visual Studio nicht verwenden, fügen Sie der Projektdatei der Test-App ein `Content`-Ziel hinzu:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Verwerfen von Objekten

Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) verwirft. Wenn vom Entwickler instanziierte Objekte verworfen werden müssen, müssen Sie dies in der `IClassFixture`-Implementierung tun. Weitere Informationen finden Sie unter [Implementieren einer Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Beispiel für Integrationstests

Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei Apps:

| App | Projektverzeichnis | Beschreibung |
| --- | ----------------- | ----------- |
| Nachrichten-App (das GS) | *src/RazorPagesProject* | Ermöglicht einem Benutzer, Nachrichten hinzuzufügen, eine oder alle Nachrichten zu löschen und Nachrichten zu analysieren. |
| Testen der App | *tests/RazorPagesProject.Tests* | Wird für den Integrationstest des GS verwendet. |

Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](https://visualstudio.microsoft.com) ausgeführt werden. Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl über eine Eingabeaufforderung im Verzeichnis *tests/RazorPagesProject.Tests* aus:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Organisation der Nachrichten-App (GS)

Beim GS handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:

* Die Indexseite der App (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit denen Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durchschnittliche Anzahl von Wörtern pro Nachricht) steuern können.
* Eine Nachricht wird von der `Message`-Klasse (*Data/Message.cs*) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.
* Die App enthält eine Datenzugriffsebene (DAL) in ihrer Datenbankkontextklasse `AppDbContext` (*Data/AppDbContext.cs*).
* Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.
* Die App enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.

&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert. In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet. Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.

Obwohl die App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testcontrollerlogik](../mvc/controllers/testing.md) (im Beispiel wird das Repositorymuster implementiert).

### <a name="test-app-organization"></a>Organisation der Test-App

Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *tests/RazorPagesProject.Tests*.

| Test-App-Verzeichnis | Beschreibung |
| ------------------ | ----------- |
| *AuthTests* | Enthält Testmethoden für Folgendes:<ul><li>Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</li><li>Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Pseudo-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</li></ul> |
| *BasicTests* | Enthält eine Testmethode für Routing und Inhaltstyp. |
| *IntegrationTests* | Enthält die Integrationstests für die Indexseite unter Verwendung der benutzerdefinierten `WebApplicationFactory`-Klasse. |
| *Helpers/Utilities* | <ul><li>*Utilities.cs* enthält die `InitializeDbForTests`-Methode, mit der ein Seeding der Datenbank mit Testdaten durchgeführt wird.</li><li>*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein AngleSharp-`IHtmlDocument` zur Verwendung durch die Testmethoden zurückgegeben wird.</li><li>*HttpClientExtensions.cs* stellt Überladungen für `SendAsync` bereit, um Anforderungen an das GS zu senden.</li></ul> |

Das Testframework ist [xUnit](https://xunit.github.io/). Integrationstests werden mit dem [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) durchgeführt, der den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) umfasst. Da das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket zum Konfigurieren des Testhosts und des Testservers verwendet wird, benötigen die `TestHost`- und `TestServer`-Pakete keine direkten Paketverweise in der Projektdatei der Test-App bzw. keine Entwicklerkonfiguration in der Test-App.

**Seeding der Datenbank für Testzwecke**

Für Integrationstests muss die Datenbank in der Regel vor der Testausführung ein kleines Dataset enthalten. Beispielsweise wird bei einem Löschtest ein Löschvorgang eines Datensatzes der Datenbank abgerufen, weshalb die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.

Die Beispiel-App führt ein Seeding der Datenbank mit drei Nachrichten in *Utilities.cs* durch, die von Tests bei der Ausführung verwendet werden können:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>