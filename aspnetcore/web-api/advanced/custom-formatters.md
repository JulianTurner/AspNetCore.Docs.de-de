---
title: Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen und Verwenden von benutzerdefinierten Formatierern für Web-APIs in ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058921"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core

Von [Kirk Larkin](https://twitter.com/serpent5) und [Tom Dykstra](https://github.com/tdykstra).

ASP.NET Core MVC unterstützt den Datenaustausch in Web-APIs mithilfe von Eingabe- und Ausgabeformatierern. Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet. Ausgabe Formatierer werden verwendet, um [Antworten zu formatieren](xref:web-api/advanced/formatting).

Das Framework stellt integrierte Eingabe- und Ausgabeformatierer für JSON und XML bereit. Es stellt einen integrierten Ausgabeformatierer für Nur-Text bereit, jedoch keinen Eingabeformatierer.

In diesem Artikel wird dargestellt, wie Sie die Unterstützung von zusätzlichen Formaten hinzufügen, indem Sie benutzerdefinierte Formatierer erstellen. Ein Beispiel für einen benutzerdefinierten Formatierer für die nur-Text-Eingabe finden Sie unter [textplaininputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) auf GitHub.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="when-to-use-custom-formatters"></a>Empfohlene Verwendung von benutzerdefinierten Formatierern

Verwenden Sie einen benutzerdefinierten Formatierer, um Unterstützung für einen Inhaltstyp hinzuzufügen, der nicht von den integrierten Formatierern verarbeitet wird.

## <a name="overview-of-how-to-use-a-custom-formatter"></a>Übersicht über die Verwendung des Formatierers

So erstellen Sie einen benutzerdefinierten Formatierer:

* Zum Serialisieren von Daten, die an den Client gesendet werden, erstellen Sie eine Ausgabe formatterklasse.
* Zum Deserialisieren von Daten, die vom Client empfangen werden, erstellen Sie eine eingabeformatterklasse.
* Fügen Sie den-und-Auflistungen in Instanzen von formatiererklassen hinzu `InputFormatters` `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .

## <a name="how-to-create-a-custom-formatter-class"></a>Erstellen einer benutzerdefinierten Formatiererklasse

Erstellen eines Formatierers:

* Leiten Sie die Klasse von der entsprechenden Basisklasse ab. Die Beispiel-APP wird von <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> und abgeleitet <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .
* Geben Sie gültige Medientypen und Codierungen im Konstruktor an.
* Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>- und <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>-Methoden.
* Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>- und `WriteResponseBodyAsync`-Methoden.

Der folgende Code zeigt die- `VcardOutputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>Ableiten von der entsprechenden Basisklasse

Leiten Sie für Text Medientypen (z. b. vCard) von der- <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> oder- <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> Basisklasse ab.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

Leiten Sie für binäre Typen von der- <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> oder- <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> Basisklasse ab.

### <a name="specify-valid-media-types-and-encodings"></a>Angeben von gültigen Medientypen und Codierungen

Geben Sie im Konstruktor gültige Medientypen und Codierungen an, indem Sie `SupportedMediaTypes`- und `SupportedEncodings`-Sammlungen hinzufügen.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

Eine Formatiererklasse kann **keine** Konstruktorinjektion für ihre Abhängigkeiten verwenden. `ILogger<VcardOutputFormatter>`Kann z. b. nicht als Parameter zum-Konstruktor hinzugefügt werden. Verwenden Sie zum Zugreifen auf Dienste das Kontext Objekt, das an die-Methoden übermittelt wird. Ein Codebeispiel in diesem Artikel und das [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) zeigen, wie Sie dies tun.

### <a name="override-canreadtype-and-canwritetype"></a>Überschreiben von "canlestype" und "CanWrite Type"

Geben Sie den zu deserialisierenden oder zu serialisierenden Typ durch Überschreiben der- `CanReadType` Methode oder der- `CanWriteType` Methode an Beispielsweise das Erstellen von vCard-Text von einem `Contact` Typ und umgekehrt.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>Die CanWriteResult-Methode

In einigen Szenarien `CanWriteResult` muss anstelle von überschrieben werden `CanWriteType` . Verwenden Sie `CanWriteResult`, wenn alle der folgenden Bedingungen zutreffen:

* Die Aktionsmethode gibt eine Modell Klasse zurück.
* Es gibt abgeleitete Klassen, die möglicherweise zur Laufzeit zurückgegeben werden.
* Die von der Aktion zurückgegebene abgeleitete Klasse muss zur Laufzeit bekannt sein.

Angenommen, die Aktionsmethode lautet wie folgt:

* Signature gibt einen `Person` Typ zurück.
* Kann einen- `Student` oder-Typ zurückgeben `Instructor` , der von abgeleitet wird `Person` . 

Damit der Formatierer nur- `Student` Objekte behandelt, überprüfen Sie den Typ von <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> im Kontext Objekt, das für die-Methode bereitgestellt wird `CanWriteResult` . Wenn die Aktionsmethode Folgendes zurückgibt `IActionResult` :

* Es ist nicht erforderlich, zu verwenden `CanWriteResult` .
* Die- `CanWriteType` Methode empfängt den Lauf Zeittyp.

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>Überschreiben von "Read requestbodyasync" und "schreiteresponsebodyasync"

Die Deserialisierung oder Serialisierung wird in `ReadRequestBodyAsync` oder ausgeführt `WriteResponseBodyAsync` . Im folgenden Beispiel wird gezeigt, wie Sie Dienste aus dem Container für die Abhängigkeitsinjektion erhalten. Dienste können nicht aus Konstruktorparametern abgerufen werden.

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>Konfigurieren von MVC zum Verwenden eines benutzerdefinierten Formatierers

Wenn Sie einen benutzerdefinierten Formatierer verwenden, fügen Sie der `InputFormatters`- oder `OutputFormatters`-Sammlung eine Instanz der Formatiererklasse hinzu.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

Formatierer werden in der Reihenfolge überprüft, in der Sie sie einfügen. Der erste hat Vorrang.

## <a name="the-complete-vcardinputformatter-class"></a>Die komplette `VcardInputFormatter` Klasse

Der folgende Code zeigt die- `VcardInputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>Testen der App

[Führen Sie die Beispiel-App für diesen Artikel aus](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), der grundlegende vCard-Eingabe-und-Ausgabe Formatierer implementiert. Die APP liest und schreibt vCards ähnlich der folgenden:

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

Um die vcardausgabe anzuzeigen, führen Sie die APP aus, und senden Sie eine GET-Anforderung mit dem Accept-Header `text/vcard` an `https://localhost:5001/api/contacts` .

So fügen Sie der Auflistung von Kontakten im Arbeitsspeicher eine vCard hinzu:

* Senden Sie eine `Post` Anforderung an `/api/contacts` mit einem Tool wie postman.
* Legen Sie den Header `Content-Type` auf `text/vcard` fest.
* Legen Sie `vCard` Text im Textkörper fest, wie im vorherigen Beispiel dargestellt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
