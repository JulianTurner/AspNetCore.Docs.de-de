---
title: Hochleistungsprotokollierung mit LoggerMessage in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie LoggerMessage verwenden, um Delegate zu erstellen, die zwischengespeichert werden können und die weniger Objektzuweisungen für Hochleistungsprotokollierungen benötigen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 0224e768bd0e016eac5165dc4d9745f4b0867094
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060455"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Hochleistungsprotokollierung mit LoggerMessage in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>-Features erstellen Delegate, die zwischengespeichert werden können und die im Vergleich zu [Methoden zur Protokollierungserweiterung](xref:Microsoft.Extensions.Logging.LoggerExtensions) (wie z. B. <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>) weniger Objektzuweisungen benötigen und den Rechenaufwand reduzieren. Verwenden Sie das <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster für Hochleistungsprotokollierungen.

<xref:Microsoft.Extensions.Logging.LoggerMessage> hat im Vergleich zu Protokollierungserweiterungsmethoden die folgenden Vorzüge:

* Protokollierungserweiterungsmethoden erfordern das Konvertieren von Werttypen wie `int` in `object` (sogenanntes Boxing). Das <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster verhindert das Konvertieren mithilfe von statischen <xref:System.Action>-Feldern und mithilfe von Erweiterungsmethoden mit stark typisierten Parametern.
* Protokollierungserweiterungsmethoden müssen die Meldungsvorlagen (sogenannte Formatzeichenfolgen) jedes Mal analysieren, wenn eine Protokollmeldung geschrieben wird. <xref:Microsoft.Extensions.Logging.LoggerMessage> erfordert das Analysieren einer Vorlage nur einmal beim Festlegen der Meldung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App veranschaulicht <xref:Microsoft.Extensions.Logging.LoggerMessage>-Features mit einem einfachen System zur Zitatnachverfolgung. Die App fügt Zitate hinzu und löscht diese mithilfe einer speicherinternen Datenbank. Während dieser Vorgänge werden Protokollmeldungen mit dem <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster erstellt.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) erstellt einen <xref:System.Action>-Delegaten zum Protokollieren von Meldungen. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> überlädt die Zulassung und übergibt bis zu sechs Typparameter an eine benannte Formatzeichenfolge (Vorlage).

Die für die Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> bereitgestellte Zeichenfolge ist eine Vorlage und keine interpolierte Zeichenfolge. Platzhalter werden in der Reihenfolge der angegebenen Typen ersetzt. Die Platzhalternamen in den Vorlagen sollten eindeutig und in allen Vorlagen einheitlich sein. Sie fungieren als Eigenschaftennamen in strukturierten Protokolldaten. Es wird empfohlen, für Platzhalternamen die [Pascal-Schreibweise](/dotnet/standard/design-guidelines/capitalization-conventions) zu verwenden. Platzhalter in einer derartigen Schreibweise sind z.B. `{Count}` und `{FirstName}`.

Jede Protokollmeldung ist ein <xref:System.Action>-Objekt, das in einem von [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) erstellten statischen Feld enthalten ist. Beispiel: Die Beispiel-App erstellt ein Feld, das eine Protokollmeldung für eine GET-Anforderung für die Indexseite beschreibt (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Geben Sie für das <xref:System.Action>-Objekt Folgendes an:

* die Protokollierungsebene
* Einen eindeutigen Ereignis-Bezeichner (<xref:Microsoft.Extensions.Logging.EventId>) mit dem Namen der statischen Erweiterungsmethode
* die Meldungsvorlage (eine benannte Formatzeichenfolge) 

Eine Anforderung der Indexseite der Beispiel-App legt Folgendes fest:

* Die Protokollierungsebene ist `Information`.
* Die Ereignis-ID ist `1` mit dem Namen der `IndexPageRequested`-Methode.
* Die Meldungsvorlage (eine benannte Formatzeichenfolge) ist eine Zeichenfolge.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Strukturierte Protokollspeicher verwenden möglicherweise den Ereignisnamen wenn dieser mit der Ereignis-ID bereitgestellt wird. Dadurch wird die Protokollierung ergänzt. [Serilog](https://github.com/serilog/serilog-extensions-logging) verwendet z.B. den Ereignisnamen.

Das <xref:System.Action>-Objekt wird mit einer stark typisierten Erweiterungsmethode aufgerufen. Die `IndexPageRequested`-Methode protokolliert eine Meldung für eine GET-Anforderung der Indexseite in der Beispiel-App:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested` wird in der Protokollierung in der `OnGetAsync`-Methode in *Pages/Index.cshtml.cs* aufgerufen:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Sehen Sie sich folgende Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Definieren Sie bis zu sechs Typen, wenn Sie das statische Feld erstellen, um Parameter an eine Protokollmeldung zu übergeben. Die Beispiel-App protokolliert eine Zeichenfolge beim Hinzufügen eines Zitats durch die Definition eines `string`-Typs für das <xref:System.Action>-Feld:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Die Protokollmeldungsvorlage des Delegaten erhält ihre Platzhalterwerte von den bereitgestellten Typen. Die Beispiel-App definiert einen Delegaten zu Hinzufügen eines Zitats, wo der Zitatparameter ein `string`-Objekt ist:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

`QuoteAdded`, die statische Erweiterungsmethode zum Hinzufügen von Zitaten, erhält den Wert des Zitatarguments und übergibt diesen an den <xref:System.Action>-Delegaten:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Im Seitenmodell der Indexseite (*Pages/Index.cshtml.cs*) wird `QuoteAdded` aufgerufen, um die Meldung zu protokollieren:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Sehen Sie sich folgende Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Die Beispiel-App implementiert ein [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Muster für das Löschen von Zitaten. Es wird eine Meldung protokolliert, die angibt, dass der Löschvorgang erfolgreich war. Es wird eine Fehlermeldung protokolliert, die angibt, dass bei einem Löschvorgang eine Ausnahme ausgelöst wurde. Die Protokollmeldung für den fehlgeschlagenen Löschvorgang enthält die Ausnahmenprotokollnachverfolgung (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Beachten Sie, wie die Ausnahme an den Delegaten in `QuoteDeleteFailed` übergeben wird:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Im Seitenmodell für die Indexseite ruft ein erfolgreicher Zitatlöschvorgang die `QuoteDeleted`-Methode in der Protokollierung auf: Wenn kein zu löschendes Zitat gefunden wird, wir eine <xref:System.ArgumentNullException> ausgelöst. Die Ausnahme wird von der [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung aufgefangen und durch den Aufruf der `QuoteDeleteFailed`-Methode in der Protokollierung im [catch](/dotnet/csharp/language-reference/keywords/try-catch)-Block protokolliert (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Wenn ein Zitat erfolgreich gelöscht wurde, sehen Sie sich die Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Wenn der Zitatlöschvorgang fehlschlägt, sehen Sie sich die Konsolenausgabe der App an. Beachten Sie, dass die Ausnahme in der Protokollmeldung beinhaltet ist:

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) erstellt einen <xref:System.Func%601>-Delegaten zum Definieren eines [Protokollbereichs](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> überlädt die Zulassung und übergibt bis zu drei Typparameter an eine benannte Formatzeichenfolge (Vorlage).

Wie bei der Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> ist die Zeichenfolge,die für die Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> bereitgestellt wurde, eine Vorlage und keine interpolierte Zeichenfolge. Platzhalter werden in der Reihenfolge der angegebenen Typen ersetzt. Die Platzhalternamen in den Vorlagen sollten eindeutig und in allen Vorlagen einheitlich sein. Sie fungieren als Eigenschaftennamen in strukturierten Protokolldaten. Es wird empfohlen, für Platzhalternamen die [Pascal-Schreibweise](/dotnet/standard/design-guidelines/capitalization-conventions) zu verwenden. Platzhalter in einer derartigen Schreibweise sind z.B. `{Count}` und `{FirstName}`.

Definieren Sie einen [Protokollbereich](xref:fundamentals/logging/index#log-scopes), um mehrere Protokollmeldungen mit der <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>-Methode anzuwenden.

Die Beispiel-App verfügt über die Schaltfläche **Clear all** (Alles löschen) zum Löschen aller Zitate in der Datenbank. Die Zitate werden nacheinander entfernt und gelöscht. Bei jedem Zitatlöschvorgang wird die `QuoteDeleted`-Methode in der Protokollierung aufgerufen. Diesen Protokollmeldung wird ein Protokollbereich hinzugefügt.

Aktivieren Sie `IncludeScopes` in den Optionen der Konsolenprotokollierung von *appsettings.json* :

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Fügen Sie zum Erstellen eines Protokollbereichs ein Feld hinzu, dass einen <xref:System.Func%601>-Delegaten für den Bereich enthält. Die Beispiel-App erstellt ein Feld mit dem Namen `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Verwenden Sie <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> zum Erstellen eines Delegaten. Sie können bis zu drei Typen als Vorlagenargumente festlegen, die verwendet werden können, wenn der Delegat aufgerufen wird. Die Beispiel-App verwendet eine Meldungsvorlage, die die Anzahl der gelöschten Zitate enthält (ein `int`-Typ):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Stellen Sie eine statische Erweiterungsmethode für die Protokollmeldung bereit. Beziehen Sie Typparameter für benannte Eigenschaften mit ein, die in der Meldungsvorlage vorhanden sind. Die Beispiel-App erstellt ein `count` der zu löschenden Zitate und gibt `_allQuotesDeletedScope` zurück:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Der Bereich umschließt die Protokollerweiterungsaufrufe in einem [using](/dotnet/csharp/language-reference/keywords/using-statement)-Block:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Sehen Sie sich die Protokollmeldungen in der Konsolenausgabe der App an. Das folgende Ergebnis zeigt drei gelöschte Zitate sowie die Protokollbereichsmeldung:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>-Features erstellen Delegate, die zwischengespeichert werden können und die im Vergleich zu [Methoden zur Protokollierungserweiterung](xref:Microsoft.Extensions.Logging.LoggerExtensions) (wie z. B. <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>) weniger Objektzuweisungen benötigen und den Rechenaufwand reduzieren. Verwenden Sie das <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster für Hochleistungsprotokollierungen.

<xref:Microsoft.Extensions.Logging.LoggerMessage> hat im Vergleich zu Protokollierungserweiterungsmethoden die folgenden Vorzüge:

* Protokollierungserweiterungsmethoden erfordern das Konvertieren von Werttypen wie `int` in `object` (sogenanntes Boxing). Das <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster verhindert das Konvertieren mithilfe von statischen <xref:System.Action>-Feldern und mithilfe von Erweiterungsmethoden mit stark typisierten Parametern.
* Protokollierungserweiterungsmethoden müssen die Meldungsvorlagen (sogenannte Formatzeichenfolgen) jedes Mal analysieren, wenn eine Protokollmeldung geschrieben wird. <xref:Microsoft.Extensions.Logging.LoggerMessage> erfordert das Analysieren einer Vorlage nur einmal beim Festlegen der Meldung.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-App veranschaulicht <xref:Microsoft.Extensions.Logging.LoggerMessage>-Features mit einem einfachen System zur Zitatnachverfolgung. Die App fügt Zitate hinzu und löscht diese mithilfe einer speicherinternen Datenbank. Während dieser Vorgänge werden Protokollmeldungen mit dem <xref:Microsoft.Extensions.Logging.LoggerMessage>-Muster erstellt.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) erstellt einen <xref:System.Action>-Delegaten zum Protokollieren von Meldungen. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> überlädt die Zulassung und übergibt bis zu sechs Typparameter an eine benannte Formatzeichenfolge (Vorlage).

Die für die Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> bereitgestellte Zeichenfolge ist eine Vorlage und keine interpolierte Zeichenfolge. Platzhalter werden in der Reihenfolge der angegebenen Typen ersetzt. Die Platzhalternamen in den Vorlagen sollten eindeutig und in allen Vorlagen einheitlich sein. Sie fungieren als Eigenschaftennamen in strukturierten Protokolldaten. Es wird empfohlen, für Platzhalternamen die [Pascal-Schreibweise](/dotnet/standard/design-guidelines/capitalization-conventions) zu verwenden. Platzhalter in einer derartigen Schreibweise sind z.B. `{Count}` und `{FirstName}`.

Jede Protokollmeldung ist ein <xref:System.Action>-Objekt, das in einem von [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) erstellten statischen Feld enthalten ist. Beispiel: Die Beispiel-App erstellt ein Feld, das eine Protokollmeldung für eine GET-Anforderung für die Indexseite beschreibt (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Geben Sie für das <xref:System.Action>-Objekt Folgendes an:

* die Protokollierungsebene
* Einen eindeutigen Ereignis-Bezeichner (<xref:Microsoft.Extensions.Logging.EventId>) mit dem Namen der statischen Erweiterungsmethode
* die Meldungsvorlage (eine benannte Formatzeichenfolge) 

Eine Anforderung der Indexseite der Beispiel-App legt Folgendes fest:

* Die Protokollierungsebene ist `Information`.
* Die Ereignis-ID ist `1` mit dem Namen der `IndexPageRequested`-Methode.
* Die Meldungsvorlage (eine benannte Formatzeichenfolge) ist eine Zeichenfolge.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Strukturierte Protokollspeicher verwenden möglicherweise den Ereignisnamen wenn dieser mit der Ereignis-ID bereitgestellt wird. Dadurch wird die Protokollierung ergänzt. [Serilog](https://github.com/serilog/serilog-extensions-logging) verwendet z.B. den Ereignisnamen.

Das <xref:System.Action>-Objekt wird mit einer stark typisierten Erweiterungsmethode aufgerufen. Die `IndexPageRequested`-Methode protokolliert eine Meldung für eine GET-Anforderung der Indexseite in der Beispiel-App:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested` wird in der Protokollierung in der `OnGetAsync`-Methode in *Pages/Index.cshtml.cs* aufgerufen:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Sehen Sie sich folgende Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Definieren Sie bis zu sechs Typen, wenn Sie das statische Feld erstellen, um Parameter an eine Protokollmeldung zu übergeben. Die Beispiel-App protokolliert eine Zeichenfolge beim Hinzufügen eines Zitats durch die Definition eines `string`-Typs für das <xref:System.Action>-Feld:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Die Protokollmeldungsvorlage des Delegaten erhält ihre Platzhalterwerte von den bereitgestellten Typen. Die Beispiel-App definiert einen Delegaten zu Hinzufügen eines Zitats, wo der Zitatparameter ein `string`-Objekt ist:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

`QuoteAdded`, die statische Erweiterungsmethode zum Hinzufügen von Zitaten, erhält den Wert des Zitatarguments und übergibt diesen an den <xref:System.Action>-Delegaten:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

Im Seitenmodell der Indexseite (*Pages/Index.cshtml.cs*) wird `QuoteAdded` aufgerufen, um die Meldung zu protokollieren:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Sehen Sie sich folgende Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Die Beispiel-App implementiert ein [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Muster für das Löschen von Zitaten. Es wird eine Meldung protokolliert, die angibt, dass der Löschvorgang erfolgreich war. Es wird eine Fehlermeldung protokolliert, die angibt, dass bei einem Löschvorgang eine Ausnahme ausgelöst wurde. Die Protokollmeldung für den fehlgeschlagenen Löschvorgang enthält die Ausnahmenprotokollnachverfolgung (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Beachten Sie, wie die Ausnahme an den Delegaten in `QuoteDeleteFailed` übergeben wird:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

Im Seitenmodell für die Indexseite ruft ein erfolgreicher Zitatlöschvorgang die `QuoteDeleted`-Methode in der Protokollierung auf: Wenn kein zu löschendes Zitat gefunden wird, wir eine <xref:System.ArgumentNullException> ausgelöst. Die Ausnahme wird von der [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung aufgefangen und durch den Aufruf der `QuoteDeleteFailed`-Methode in der Protokollierung im [catch](/dotnet/csharp/language-reference/keywords/try-catch)-Block protokolliert (*Pages/Index.cshtml.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Wenn ein Zitat erfolgreich gelöscht wurde, sehen Sie sich die Konsolenausgabe der App an:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Wenn der Zitatlöschvorgang fehlschlägt, sehen Sie sich die Konsolenausgabe der App an. Beachten Sie, dass die Ausnahme in der Protokollmeldung beinhaltet ist:

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) erstellt einen <xref:System.Func%601>-Delegaten zum Definieren eines [Protokollbereichs](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> überlädt die Zulassung und übergibt bis zu drei Typparameter an eine benannte Formatzeichenfolge (Vorlage).

Wie bei der Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> ist die Zeichenfolge,die für die Methode <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> bereitgestellt wurde, eine Vorlage und keine interpolierte Zeichenfolge. Platzhalter werden in der Reihenfolge der angegebenen Typen ersetzt. Die Platzhalternamen in den Vorlagen sollten eindeutig und in allen Vorlagen einheitlich sein. Sie fungieren als Eigenschaftennamen in strukturierten Protokolldaten. Es wird empfohlen, für Platzhalternamen die [Pascal-Schreibweise](/dotnet/standard/design-guidelines/capitalization-conventions) zu verwenden. Platzhalter in einer derartigen Schreibweise sind z.B. `{Count}` und `{FirstName}`.

Definieren Sie einen [Protokollbereich](xref:fundamentals/logging/index#log-scopes), um mehrere Protokollmeldungen mit der <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>-Methode anzuwenden.

Die Beispiel-App verfügt über die Schaltfläche **Clear all** (Alles löschen) zum Löschen aller Zitate in der Datenbank. Die Zitate werden nacheinander entfernt und gelöscht. Bei jedem Zitatlöschvorgang wird die `QuoteDeleted`-Methode in der Protokollierung aufgerufen. Diesen Protokollmeldung wird ein Protokollbereich hinzugefügt.

Aktivieren Sie `IncludeScopes` in den Optionen der Konsolenprotokollierung von *appsettings.json* :

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Fügen Sie zum Erstellen eines Protokollbereichs ein Feld hinzu, dass einen <xref:System.Func%601>-Delegaten für den Bereich enthält. Die Beispiel-App erstellt ein Feld mit dem Namen `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Verwenden Sie <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> zum Erstellen eines Delegaten. Sie können bis zu drei Typen als Vorlagenargumente festlegen, die verwendet werden können, wenn der Delegat aufgerufen wird. Die Beispiel-App verwendet eine Meldungsvorlage, die die Anzahl der gelöschten Zitate enthält (ein `int`-Typ):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Stellen Sie eine statische Erweiterungsmethode für die Protokollmeldung bereit. Beziehen Sie Typparameter für benannte Eigenschaften mit ein, die in der Meldungsvorlage vorhanden sind. Die Beispiel-App erstellt ein `count` der zu löschenden Zitate und gibt `_allQuotesDeletedScope` zurück:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Der Bereich umschließt die Protokollerweiterungsaufrufe in einem [using](/dotnet/csharp/language-reference/keywords/using-statement)-Block:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Sehen Sie sich die Protokollmeldungen in der Konsolenausgabe der App an. Das folgende Ergebnis zeigt drei gelöschte Zitate sowie die Protokollbereichsmeldung:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Logging (Protokollierung)](xref:fundamentals/logging/index)
