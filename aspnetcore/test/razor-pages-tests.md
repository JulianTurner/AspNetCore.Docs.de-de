---
title: Komponententests für Razor Pages in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Komponententests für Razor Pages-Apps erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: test/razor-pages-tests
ms.openlocfilehash: 2486eb8c9fd0fc33ea77b0fedd99795218d7f4ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058037"
---
# <a name="no-locrazor-pages-unit-tests-in-aspnet-core"></a>Komponententests für Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core unterstützt Komponententests von Razor Pages-Apps. Tests der Datenzugriffsebene (Data Access Layer, DAL) und Seitenmodelle helfen dabei, Folgendes sicherzustellen:

* Teile einer Razor Pages-App funktionieren während der App-Erstellung unabhängig und zusammen als Einheit.
* Klassen und Methoden weisen eingeschränkte Zuständigkeitsbereiche auf.
* Es gibt zusätzliche Dokumentation zum Verhalten der App.
* Regressionen, bei denen es sich um Fehler bei Aktualisierungen des Codes handelt, werden während der automatisierten Erstellung und Bereitstellung gefunden.

In diesem Thema wird davon ausgegangen, dass Sie über grundlegende Kenntnisse über Razor Pages-Apps und -Komponententests verfügen. Wenn Sie mit Razor Pages-Apps oder Testkonzepten nicht vertraut sind, finden Sie weitere Informationen in den folgenden Themen:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Komponententests für C# in .NET Core mit „dotnet test“ und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispielprojekt besteht aus zwei Apps:

| App         | Projektordner                     | Beschreibung |
| ----------- | ---------------------------------- | ----------- |
| Nachrichten-App | *src/RazorPagesTestSample*         | Ermöglicht es dem Benutzer, eine Nachricht hinzuzufügen, eine Nachricht zu löschen, alle Nachrichten zu löschen und Nachrichten zu analysieren (durch Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht). |
| Testen der App    | *tests/RazorPagesTestSample.Tests* | Wird verwendet, um Komponententests für die DAL und das Indexseitenmodell der Nachrichten-App durchzusetzen. |

Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](/visualstudio/test/unit-test-your-code) oder [Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution), ausgeführt werden. Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl über eine Eingabeaufforderung im Ordner *tests/RazorPagesTestSample.Tests* aus:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organisation der Nachrichten-App

Bei der Nachrichten-App handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:

* Die Indexseite der App ( *Pages/Index.cshtml* und *Pages/Index.cshtml.cs* ) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit der Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durch Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht) steuern können.
* Eine Nachricht wird von der `Message`-Klasse ( *Data/Message.cs* ) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.
* Die App enthält eine DAL in ihrer Datenbankkontext-Klasse, `AppDbContext` ( *Data/AppDbContext.cs* ). Die DAL-Methoden sind als `virtual` gekennzeichnet, sodass die Methoden für die Verwendung in den Tests simuliert werden können.
* Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *per Seeding hinzugefügten Nachrichten* werden auch in Tests verwendet.

&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert. In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet. Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.

Obwohl die Beispiel-App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und <xref:mvc/controllers/testing> (im Beispiel wird das Repositorymuster implementiert).

## <a name="test-app-organization"></a>Organisation der Test-App

Bei der Test-App handelt es sich um eine Konsolen-App innerhalb des Ordners *tests/RazorPagesTestSample.Tests*.

| Test-App-Ordner | Beschreibung |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* enthält die Komponententests für die DAL.</li><li>*IndexPageTests.cs* enthält die Komponententests für das Indexseitenmodell.</li></ul> |
| *Utilities*     | Enthält die `TestDbContextOptions`-Methode, mit der neue Datenbankkontext-Optionen für jeden DAL-Komponententest erstellt werden, sodass die Datenbank für jeden Test auf ihren Ausgangszustand zurückgesetzt wird. |

Das Testframework ist [xUnit](https://xunit.github.io/). Das Framework für die Objektsimulation ist [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Komponententests der Datenzugriffsebene (Data Access Layer, DAL)

Die Nachrichten-App verfügt über eine Datenzugriffsebene mit vier Methoden, die in der `AppDbContext`-Klasse ( *src/RazorPagesTestSample/Data/AppDbContext.cs* ) enthalten sind. Jede Methode verfügt über einen oder zwei Komponententests in der Test-App.

| DAL-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft eine nach der Eigenschaft `Text` sortierte `List<Message>` aus der Datenbank ab. |
| `AddMessageAsync`        | Fügt der Datenbank eine `Message` hinzu.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message`-Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne, nach `Id` sortierte `Message` aus der Datenbank.                      |

Komponententests der DAL erfordern <xref:Microsoft.EntityFrameworkCore.DbContextOptions>, wenn für jeden Test ein neuer `AppDbContext` erstellt wird. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung eines <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz besteht darin, dass jeder Test die Datenbank in dem Zustand empfängt, in dem sie sich im vorherigen Test zum Schluss befand. Dies kann problematisch sein, wenn versucht wird, atomische Komponententests zu schreiben, die sich nicht einander beeinträchtigen. Um zu erzwingen, dass der `AppDbContext` für jeden Test einen neuen Datenbankkontext verwendet, stellen Sie eine `DbContextOptions`-Instanz bereit, die auf einem neuen Dienstanbieter basiert. Die Test-App zeigt, wie dies mit der `Utilities`-Klassenmethode `TestDbContextOptions` ( *tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs* ) möglich ist:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Wenn Sie die `DbContextOptions` in den DAL-Komponententests verwenden, kann jeder Test atomisch mit einer neuen Datenbankinstanz ausgeführt werden:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jede Testmethode in der `DataAccessLayerTest`-Klasse ( *UnitTests/DataAccessLayerTest.cs* ) folgt einem ähnlichen „Arrange-Act-Assert“-Muster:

1. „Arrange“ (Anordnen) Die Datenbank ist für den Test konfiguriert, und/oder das erwartete Ergebnis ist definiert.
1. „Act“ (Aktion ausführen): Der Test wird ausgeführt.
1. Assert (Bestätigen): Es werden Assertionen erstellt, um zu bestimmen, ob das Testergebnis ein Erfolg ist.

Beispielsweise ist die Methode `DeleteMessageAsync` für das Entfernen einer einzelnen Nachricht verantwortlich, die durch `Id` ( *src/RazorPagesTestSample/Data/AppDbContext.cs* ) identifiziert wird:

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Für diese Methode gibt es zwei Tests. Ein Test prüft, ob die Methode eine Nachricht löscht, wenn die Nachricht in der Datenbank vorhanden ist. Die andere Methode testet, ob die Datenbank nicht geändert wird, wenn die Nachrichten-`Id` zum Löschen nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`-Methode wird im folgenden Beispiel dargestellt:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Zuerst führt die Methode den Schritt „Arrange“ (Anordnen) aus, bei dem die Vorbereitung für den Schritt „Act“ (Aktion ausführen) stattfindet. Die Seeding-Nachrichten werden abgerufen und in `seedMessages` gespeichert. Die Seeding-Nachrichten werden in der Datenbank gespeichert. Die Nachricht mit einer `Id` `1` wird zum Löschen festgelegt. Wenn die `DeleteMessageAsync`-Methode ausgeführt wird, sollten die erwarteten Nachrichten alle Nachrichten umfassen, mit Ausnahme derjenigen, die eine `Id` `1` aufweisen. Die Variable `expectedMessages` stellt dieses erwartete Ergebnis dar.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode verhält sich wie folgt: Die `DeleteMessageAsync`-Methode wird ausgeführt, indem die `recId` `1` übergeben wird:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Abschließend ruft die Methode die `Messages` aus dem Kontext ab und vergleicht sie mit den `expectedMessages` mit der Behauptung, dass die beiden identisch sind:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Um vergleichen zu können, ob die beiden `List<Message>` identisch sind, werden folgende Aktionen ausgeführt:

* Die Nachrichten werden nach `Id` sortiert.
* Nachrichtenpaare werden anhand der `Text`-Eigenschaft verglichen.

`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`, eine ähnliche Testmethode, überprüft das Ergebnis des Versuchs, eine Meldung zu löschen, die nicht vorhanden ist. In diesem Fall sollten die erwarteten Nachrichten in der Datenbank mit den tatsächlichen Nachrichten übereinstimmen, nachdem die `DeleteMessageAsync`-Methode ausgeführt wurde. Der Inhalt der Datenbank sollte nicht geändert werden:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Komponententests der Seitenmodellmethoden

Weitere Komponententests sind für Tests von Seitenmodellmethoden verantwortlich. In der Nachrichten-App befinden sich die Indexseitenmodelle in der `IndexModel`-Klasse in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Seitenmodellmethode | Funktion |
| ----------------- | -------- |
| `OnGetAsync` | Ruft die Nachrichten aus der DAL für die Benutzeroberfläche mithilfe der `GetMessagesAsync`-Methode ab. |
| `OnPostAddMessageAsync` | Wenn der [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) gültig ist, wird `AddMessageAsync` aufgerufen, um der Datenbank eine Nachricht hinzuzufügen. |
| `OnPostDeleteAllMessagesAsync` | Ruft `DeleteAllMessagesAsync` auf, um alle Nachrichten in der Datenbank zu löschen. |
| `OnPostDeleteMessageAsync` | Führt `DeleteMessageAsync` aus, um eine Meldung mit der angegebenen `Id` zu löschen. |
| `OnPostAnalyzeMessagesAsync` | Wenn sich eine oder mehrere Nachrichten in der Datenbank befinden, wird die durchschnittliche Anzahl von Wörtern pro Nachricht berechnet. |

Die Seitenmodellmethoden werden mithilfe von sieben Tests in der `IndexPageTests`-Klasse ( *tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs* ) getestet. Bei den Tests wird das bekannte „Arrange-Act-Assert“-Muster verwendet. Diese Tests konzentrieren sich auf folgende Punkte:

* Es wird ermittelt, ob die Methoden das richtige Verhalten aufweisen, wenn der [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) ungültig ist.
* Es wird überprüft, ob nach dem Bestätigen der Methode das richtige <xref:Microsoft.AspNetCore.Mvc.IActionResult> erzeugt wird.
* Es wird überprüft, ob die Eigenschaftswerte korrekt zugewiesen werden.

Diese Gruppe von Tests simulieren häufig die Methoden der DAL, um erwartete Daten für den Schritt „Act“ (Aktion ausführen) zu erzeugen, in dem eine Seitenmodellmethode ausgeführt wird. Beispielsweise wird die `GetMessagesAsync` des `AppDbContext`-Methode simuliert, um eine Ausgabe zu erzeugen. Wenn diese Methode von einer Seitenmodellmethode ausgeführt wird, gibt die Simulation das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch werden vorhersagbare, zuverlässige Testbedingungen für die Verwendung der DAL in den Seitenmodelltests erstellt.

Der Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` zeigt, wie die `GetMessagesAsync`-Methode für das Seitenmodell simuliert wird:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync`-Methode im Schritt „Act“ (Aktion ausführen) ausgeführt wird, wird die `GetMessagesAsync`-Methode des Seitenmodells aufgerufen.

Schritt „Act“ (Aktion ausführen) im Komponententest ( *tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`OnGetAsync`-Methode des `IndexPage`-Seitenmodells ( *src/RazorPagesTestSample/Pages/Index.cshtml.cs* ):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync`-Methode in der DAL gibt kein Ergebnis für diesen Methodenaufruf zurück. Die simulierte Version der Methode gibt das Ergebnis zurück.

Im Schritt `Assert` werden die tatsächlichen Nachrichten (`actualMessages`) aus der `Messages`-Eigenschaft des Seitenmodells zugewiesen. Außerdem wird eine Typüberprüfung ausgeführt, wenn die Nachrichten zugewiesen werden. Die erwarteten und tatsächlichen Nachrichten werden anhand ihrer `Text`-Eigenschaften miteinander verglichen. Der Test bestätigt, dass die beiden `List<Message>`-Instanzen die gleichen Nachrichten enthalten.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bei anderen Tests in dieser Gruppe werden Seitenmodellobjekte erstellt, die den <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, das <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, einen <xref:Microsoft.AspNetCore.Mvc.ActionContext> zum Einrichten des `PageContext`, eines `ViewDataDictionary` und eines `PageContext` enthalten. Diese sind beim Durchführen von Tests nützlich. Beispielsweise produziert die Nachrichten-App einen `ModelState`-Fehler mit <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, um zu überprüfen, ob beim Ausführen von `OnPostAddMessageAsync` ein gültiges <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurückgegeben wird:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests für C# in .NET Core mit „dotnet test“ und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Komponententests für Code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/).
* [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [ Erste Schritte mit xUnit.net: Verwenden von .NET Core mit der .NET SDK-Befehlszeile](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq-Schnellstart](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core unterstützt Komponententests von Razor Pages-Apps. Tests der Datenzugriffsebene (Data Access Layer, DAL) und Seitenmodelle helfen dabei, Folgendes sicherzustellen:

* Teile einer Razor Pages-App funktionieren während der App-Erstellung unabhängig und zusammen als Einheit.
* Klassen und Methoden weisen eingeschränkte Zuständigkeitsbereiche auf.
* Es gibt zusätzliche Dokumentation zum Verhalten der App.
* Regressionen, bei denen es sich um Fehler bei Aktualisierungen des Codes handelt, werden während der automatisierten Erstellung und Bereitstellung gefunden.

In diesem Thema wird davon ausgegangen, dass Sie über grundlegende Kenntnisse über Razor Pages-Apps und -Komponententests verfügen. Wenn Sie mit Razor Pages-Apps oder Testkonzepten nicht vertraut sind, finden Sie weitere Informationen in den folgenden Themen:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Komponententests für C# in .NET Core mit „dotnet test“ und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispielprojekt besteht aus zwei Apps:

| App         | Projektordner                     | Beschreibung |
| ----------- | ---------------------------------- | ----------- |
| Nachrichten-App | *src/RazorPagesTestSample*         | Ermöglicht es dem Benutzer, eine Nachricht hinzuzufügen, eine Nachricht zu löschen, alle Nachrichten zu löschen und Nachrichten zu analysieren (durch Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht). |
| Testen der App    | *tests/RazorPagesTestSample.Tests* | Wird verwendet, um Komponententests für die DAL und das Indexseitenmodell der Nachrichten-App durchzusetzen. |

Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](/visualstudio/test/unit-test-your-code) oder [Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution), ausgeführt werden. Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl über eine Eingabeaufforderung im Ordner *tests/RazorPagesTestSample.Tests* aus:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Organisation der Nachrichten-App

Bei der Nachrichten-App handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:

* Die Indexseite der App ( *Pages/Index.cshtml* und *Pages/Index.cshtml.cs* ) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit der Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durch Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht) steuern können.
* Eine Nachricht wird von der `Message`-Klasse ( *Data/Message.cs* ) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.
* Die App enthält eine DAL in ihrer Datenbankkontext-Klasse, `AppDbContext` ( *Data/AppDbContext.cs* ). Die DAL-Methoden sind als `virtual` gekennzeichnet, sodass die Methoden für die Verwendung in den Tests simuliert werden können.
* Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *per Seeding hinzugefügten Nachrichten* werden auch in Tests verwendet.

&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert. In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet. Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.

Obwohl die Beispiel-App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und <xref:mvc/controllers/testing> (im Beispiel wird das Repositorymuster implementiert).

## <a name="test-app-organization"></a>Organisation der Test-App

Bei der Test-App handelt es sich um eine Konsolen-App innerhalb des Ordners *tests/RazorPagesTestSample.Tests*.

| Test-App-Ordner | Beschreibung |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* enthält die Komponententests für die DAL.</li><li>*IndexPageTests.cs* enthält die Komponententests für das Indexseitenmodell.</li></ul> |
| *Utilities*     | Enthält die `TestDbContextOptions`-Methode, mit der neue Datenbankkontext-Optionen für jeden DAL-Komponententest erstellt werden, sodass die Datenbank für jeden Test auf ihren Ausgangszustand zurückgesetzt wird. |

Das Testframework ist [xUnit](https://xunit.github.io/). Das Framework für die Objektsimulation ist [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Komponententests der Datenzugriffsebene (Data Access Layer, DAL)

Die Nachrichten-App verfügt über eine Datenzugriffsebene mit vier Methoden, die in der `AppDbContext`-Klasse ( *src/RazorPagesTestSample/Data/AppDbContext.cs* ) enthalten sind. Jede Methode verfügt über einen oder zwei Komponententests in der Test-App.

| DAL-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft eine nach der Eigenschaft `Text` sortierte `List<Message>` aus der Datenbank ab. |
| `AddMessageAsync`        | Fügt der Datenbank eine `Message` hinzu.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message`-Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne, nach `Id` sortierte `Message` aus der Datenbank.                      |

Komponententests der DAL erfordern <xref:Microsoft.EntityFrameworkCore.DbContextOptions>, wenn für jeden Test ein neuer `AppDbContext` erstellt wird. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung eines <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz besteht darin, dass jeder Test die Datenbank in dem Zustand empfängt, in dem sie sich im vorherigen Test zum Schluss befand. Dies kann problematisch sein, wenn versucht wird, atomische Komponententests zu schreiben, die sich nicht einander beeinträchtigen. Um zu erzwingen, dass der `AppDbContext` für jeden Test einen neuen Datenbankkontext verwendet, stellen Sie eine `DbContextOptions`-Instanz bereit, die auf einem neuen Dienstanbieter basiert. Die Test-App zeigt, wie dies mit der `Utilities`-Klassenmethode `TestDbContextOptions` ( *tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs* ) möglich ist:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Wenn Sie die `DbContextOptions` in den DAL-Komponententests verwenden, kann jeder Test atomisch mit einer neuen Datenbankinstanz ausgeführt werden:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jede Testmethode in der `DataAccessLayerTest`-Klasse ( *UnitTests/DataAccessLayerTest.cs* ) folgt einem ähnlichen „Arrange-Act-Assert“-Muster:

1. „Arrange“ (Anordnen) Die Datenbank ist für den Test konfiguriert, und/oder das erwartete Ergebnis ist definiert.
1. „Act“ (Aktion ausführen): Der Test wird ausgeführt.
1. Assert (Bestätigen): Es werden Assertionen erstellt, um zu bestimmen, ob das Testergebnis ein Erfolg ist.

Beispielsweise ist die Methode `DeleteMessageAsync` für das Entfernen einer einzelnen Nachricht verantwortlich, die durch `Id` ( *src/RazorPagesTestSample/Data/AppDbContext.cs* ) identifiziert wird:

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Für diese Methode gibt es zwei Tests. Ein Test prüft, ob die Methode eine Nachricht löscht, wenn die Nachricht in der Datenbank vorhanden ist. Die andere Methode testet, ob die Datenbank nicht geändert wird, wenn die Nachrichten-`Id` zum Löschen nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound`-Methode wird im folgenden Beispiel dargestellt:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Zuerst führt die Methode den Schritt „Arrange“ (Anordnen) aus, bei dem die Vorbereitung für den Schritt „Act“ (Aktion ausführen) stattfindet. Die Seeding-Nachrichten werden abgerufen und in `seedMessages` gespeichert. Die Seeding-Nachrichten werden in der Datenbank gespeichert. Die Nachricht mit einer `Id` `1` wird zum Löschen festgelegt. Wenn die `DeleteMessageAsync`-Methode ausgeführt wird, sollten die erwarteten Nachrichten alle Nachrichten umfassen, mit Ausnahme derjenigen, die eine `Id` `1` aufweisen. Die Variable `expectedMessages` stellt dieses erwartete Ergebnis dar.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode verhält sich wie folgt: Die `DeleteMessageAsync`-Methode wird ausgeführt, indem die `recId` `1` übergeben wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Abschließend ruft die Methode die `Messages` aus dem Kontext ab und vergleicht sie mit den `expectedMessages` mit der Behauptung, dass die beiden identisch sind:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Um vergleichen zu können, ob die beiden `List<Message>` identisch sind, werden folgende Aktionen ausgeführt:

* Die Nachrichten werden nach `Id` sortiert.
* Nachrichtenpaare werden anhand der `Text`-Eigenschaft verglichen.

`DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`, eine ähnliche Testmethode, überprüft das Ergebnis des Versuchs, eine Meldung zu löschen, die nicht vorhanden ist. In diesem Fall sollten die erwarteten Nachrichten in der Datenbank mit den tatsächlichen Nachrichten übereinstimmen, nachdem die `DeleteMessageAsync`-Methode ausgeführt wurde. Der Inhalt der Datenbank sollte nicht geändert werden:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Komponententests der Seitenmodellmethoden

Weitere Komponententests sind für Tests von Seitenmodellmethoden verantwortlich. In der Nachrichten-App befinden sich die Indexseitenmodelle in der `IndexModel`-Klasse in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Seitenmodellmethode | Funktion |
| ----------------- | -------- |
| `OnGetAsync` | Ruft die Nachrichten aus der DAL für die Benutzeroberfläche mithilfe der `GetMessagesAsync`-Methode ab. |
| `OnPostAddMessageAsync` | Wenn der [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) gültig ist, wird `AddMessageAsync` aufgerufen, um der Datenbank eine Nachricht hinzuzufügen. |
| `OnPostDeleteAllMessagesAsync` | Ruft `DeleteAllMessagesAsync` auf, um alle Nachrichten in der Datenbank zu löschen. |
| `OnPostDeleteMessageAsync` | Führt `DeleteMessageAsync` aus, um eine Meldung mit der angegebenen `Id` zu löschen. |
| `OnPostAnalyzeMessagesAsync` | Wenn sich eine oder mehrere Nachrichten in der Datenbank befinden, wird die durchschnittliche Anzahl von Wörtern pro Nachricht berechnet. |

Die Seitenmodellmethoden werden mithilfe von sieben Tests in der `IndexPageTests`-Klasse ( *tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs* ) getestet. Bei den Tests wird das bekannte „Arrange-Act-Assert“-Muster verwendet. Diese Tests konzentrieren sich auf folgende Punkte:

* Es wird ermittelt, ob die Methoden das richtige Verhalten aufweisen, wenn der [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) ungültig ist.
* Es wird überprüft, ob nach dem Bestätigen der Methode das richtige <xref:Microsoft.AspNetCore.Mvc.IActionResult> erzeugt wird.
* Es wird überprüft, ob die Eigenschaftswerte korrekt zugewiesen werden.

Diese Gruppe von Tests simulieren häufig die Methoden der DAL, um erwartete Daten für den Schritt „Act“ (Aktion ausführen) zu erzeugen, in dem eine Seitenmodellmethode ausgeführt wird. Beispielsweise wird die `GetMessagesAsync` des `AppDbContext`-Methode simuliert, um eine Ausgabe zu erzeugen. Wenn diese Methode von einer Seitenmodellmethode ausgeführt wird, gibt die Simulation das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch werden vorhersagbare, zuverlässige Testbedingungen für die Verwendung der DAL in den Seitenmodelltests erstellt.

Der Test `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` zeigt, wie die `GetMessagesAsync`-Methode für das Seitenmodell simuliert wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync`-Methode im Schritt „Act“ (Aktion ausführen) ausgeführt wird, wird die `GetMessagesAsync`-Methode des Seitenmodells aufgerufen.

Schritt „Act“ (Aktion ausführen) im Komponententest ( *tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`OnGetAsync`-Methode des `IndexPage`-Seitenmodells ( *src/RazorPagesTestSample/Pages/Index.cshtml.cs* ):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync`-Methode in der DAL gibt kein Ergebnis für diesen Methodenaufruf zurück. Die simulierte Version der Methode gibt das Ergebnis zurück.

Im Schritt `Assert` werden die tatsächlichen Nachrichten (`actualMessages`) aus der `Messages`-Eigenschaft des Seitenmodells zugewiesen. Außerdem wird eine Typüberprüfung ausgeführt, wenn die Nachrichten zugewiesen werden. Die erwarteten und tatsächlichen Nachrichten werden anhand ihrer `Text`-Eigenschaften miteinander verglichen. Der Test bestätigt, dass die beiden `List<Message>`-Instanzen die gleichen Nachrichten enthalten.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bei anderen Tests in dieser Gruppe werden Seitenmodellobjekte erstellt, die den <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, das <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, einen <xref:Microsoft.AspNetCore.Mvc.ActionContext> zum Einrichten des `PageContext`, eines `ViewDataDictionary` und eines `PageContext` enthalten. Diese sind beim Durchführen von Tests nützlich. Beispielsweise produziert die Nachrichten-App einen `ModelState`-Fehler mit <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>, um zu überprüfen, ob beim Ausführen von `OnPostAddMessageAsync` ein gültiges <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurückgegeben wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests für C# in .NET Core mit „dotnet test“ und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [Komponententests für Code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/).
* [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [ Erste Schritte mit xUnit.net: Verwenden von .NET Core mit der .NET SDK-Befehlszeile](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq-Schnellstart](https://github.com/Moq/moq4/wiki/Quickstart)
* [JustMockLite:](https://github.com/telerik/JustMockLite) Dies ist ein Beispielframework für .NET-Entwickler. ( *Wird von Microsoft nicht verwaltet oder unterstützt.* )

::: moniker-end
