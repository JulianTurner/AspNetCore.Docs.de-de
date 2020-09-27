---
title: Modellbindung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Modellbindung in ASP.NET Core funktioniert und wie Sie ihr Verhalten anpassen.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393885"
---
# <a name="model-binding-in-aspnet-core"></a>Modellbindung in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Was ist Modellbindung?

Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen. Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen. Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig. Modellbindung automatisiert diesen Vorgang. Das Modellbindungssystem:

* Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.
* Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.
* Konvertiert Zeichenfolgendaten in .NET-Typen.
* Aktualisiert Eigenschaften komplexer Typen.

## <a name="example"></a>Beispiel

Angenommen Sie haben die folgende Aktionsmethode:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

Und die App empfängt eine Anforderung mit dieser URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:

* Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.
* Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.
* Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.
* Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.
* Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge. Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.
* Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.

Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.

Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind. Ziele können aber auch die Eigenschaften eines komplexen Typs sein. Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft. Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert. Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Ziele

Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:

* Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.
* Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird. 
* Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.

### <a name="bindproperty-attribute"></a>[BindProperty]-Attribut

Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>[BindProperties]-Attribut

Verfügbar in ASP.NET Core 2.1 und höher.  Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Modellbindung für HTTP-GET-Anforderungen

Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden. In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter. Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen. Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält. In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Quellen

Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:

1. Formularfelder
1. Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))
1. Routendaten
1. Abfragezeichenfolge-Parameter
1. Hochgeladene Dateien

Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft. Es gibt ein paar Ausnahmen:

* Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.
* Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.

Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ruft Werte aus der Abfrage Zeichenfolge ab. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ruft Werte aus Routendaten ab.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ruft Werte aus den Feldern für gepostete Formulare ab.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ruft Werte aus dem Anforderungs Text ab.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ruft Werte aus HTTP-Headern ab.

Diese Attribute:

* Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Akzeptieren optional einen Modellnamenswert im Konstruktor. Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt. Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody]-Attribut

Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen. Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer. Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.

Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden. Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

Im vorherigen Beispiel:

* Das `[FromQuery]`-Attribut wird ignoriert.
* Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt. 

Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht. Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.

Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an. Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.

### <a name="additional-sources"></a>Zusätzliche Quellen

Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt. Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen. Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand. So rufen Sie Daten aus einer neuen Quelle ab

* Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.
* Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.
* Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.

Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie . Dies ist der Registrierungscode in `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.  Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.

## <a name="no-source-for-a-model-property"></a>Keine Quelle für eine Modelleigenschaft

Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird. Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:

* Einfache Nullable-Typen werden auf `null` festgelegt.
* Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt. Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.
* Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.
* Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.

Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.

Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext. Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.

## <a name="type-conversion-errors"></a>Typkonvertierungsfehler

Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet. Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.

In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.

Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor . Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen. Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet. Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.

Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt. Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde. Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt. Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.

Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen. In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.

## <a name="simple-types"></a>Einfache Typen

Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:

* [Boolescher Wert](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enumeration](xref:System.ComponentModel.EnumConverter)
* [GUID](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [URI](xref:System.UriTypeConverter)
* [Version](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Komplexe Typen

Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können. Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert. 

Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*. Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.

Beim Binden an einen Parameter ist das Präfix der Name des Parameters. Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft. Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.

Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Präfix = Parametername

Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="prefix--property-name"></a>Präfix = Name der Eigenschaft

Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="custom-prefix"></a>Benutzerdefiniertes Präfix

Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="attributes-for-complex-type-targets"></a>Attribute für Ziele komplexen Typs

Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind. Sie wirken sich ***nicht*** auf Eingabe Formatierer aus, die gepostete JSON-und XML-Anforderungs Texte verarbeiten. Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.

### <a name="bind-attribute"></a>[Bind]-Attribut

Kann auf eine Klasse oder einen Methodenparameter angewendet werden. Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen. `[Bind]` wirkt sich ***nicht*** auf Eingabe Formatierer aus.

Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden. Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben. Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs. Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).

### <a name="bindrequired-attribute"></a>[BindRequired]-Attribut

Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter. Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).

### <a name="bindnever-attribute"></a>[BindNever]-Attribut

Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter. Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a>Sammlungen

Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*. Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix. Beispiel:

* Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Das folgende Format wird nur für Formulardaten unterstützt:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null). Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert. Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.

## <a name="dictionaries"></a>Wörterbücher

Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*. Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix. Beispiel:

* Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a>Konstruktorbindungs-und Daten Satz Typen

Die Modell Bindung erfordert, dass komplexe Typen über einen Parameter losen Konstruktor verfügen. Sowohl `System.Text.Json` als auch `Newtonsoft.Json` basierte Eingabe Formatierer unterstützen die Deserialisierung von Klassen, die über keinen Parameter losen Konstruktor verfügen. 

C# 9 führt Daten Satz Typen ein, die eine hervorragend Möglichkeit darstellen, Daten über das Netzwerk zu übermitteln. ASP.net Core fügt Unterstützung für die Modell Bindung und die Validierung von Daten Satz Typen mit einem einzelnen Konstruktor hinzu:

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

`Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

Beim Überprüfen von Daten Satz Typen sucht die Common Language Runtime anstelle von Eigenschaften nach Validierungs Metadaten speziell für Parameter.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung

Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:

* behandeln Werte als invariante Kulturen.
* erwarten, dass URLs kulturinvariant sind.

Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung. Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.

So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:

* Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.
* Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).
* Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).
* Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Spezielle Datentypen

Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.

### <a name="iformfile-and-iformfilecollection"></a>„IFormFile“ und „IFormFileCollection“

Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.  Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.

### <a name="cancellationtoken"></a>CancellationToken

Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.

### <a name="formcollection"></a>„FormCollection“

Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.

## <a name="input-formatters"></a>Eingabeformatierer

Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein. Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist. ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten. Sie können andere Formatierer für andere Inhaltstypen hinzufügen.

ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus. Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

So verwenden Sie die integrierte XML-Eingabeformatierer

* Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).

### <a name="customize-model-binding-with-input-formatters"></a>Anpassen der Modellbindung mit Eingabeformatierern

Ein Eingabeformatierer ist in vollem Umfang für das Lesen von Daten aus dem Anforderungstext verantwortlich. Um diesen Prozess anzupassen, konfigurieren Sie die APIs, die vom Eingabeformatierer verwendet werden. In diesem Abschnitt wird beschrieben, wie Sie den auf `System.Text.Json` basierenden Eingabeformatierer so anpassen, dass er einen benutzerdefinierten Typ mit dem Namen `ObjectId` versteht. 

Betrachten Sie das folgende Modell, das eine benutzerdefinierte `ObjectId`-Eigenschaft mit dem Namen `Id` enthält:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

Um den Modellbindungsprozess bei der Verwendung von `System.Text.Json`anzupassen, erstellen Sie eine aus <xref:System.Text.Json.Serialization.JsonConverter%601> abgeleitete Klasse:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

Um einen benutzerdefinierten Konverter zu verwenden, wenden Sie das <xref:System.Text.Json.Serialization.JsonConverterAttribute>-Attribut auf den Typ an. Im folgenden Beispiel wird der Typ `ObjectId` mit `ObjectIdConverter` als seinem benutzerdefinierten Konverter konfiguriert:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

Weitere Informationen finden Sie unter [Vorgehensweise: Schreiben benutzerdefinierter Konverter](/dotnet/standard/serialization/system-text-json-converters-how-to).

## <a name="exclude-specified-types-from-model-binding"></a>Ausschließen angegebener Typen aus der Modellbindung

Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert. Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen. Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.

Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu. Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu. Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>Benutzerdefinierte Modellbindungen

Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen. Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Manuelle Modellbindung 

Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden. Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert. Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen. Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> verwendet Wertanbieter, um Daten aus dem Formulartext, der Abfragezeichenfolge und den Routendaten abzurufen. `TryUpdateModelAsync` wird normalerweise so verwendet: 

* Wird mit Razor Seiten und MVC-Apps verwendet, die Controller und Ansichten verwenden, um eine Übertragung zu verhindern.
* Nicht zusammen mit einer Web-API, es sei denn, sie wird von Formulardaten, Abfragezeichenfolgen und Routendaten konsumiert. Web-API-Endpunkte, die JSON nutzen, verwenden [Eingabeformatierer](#input-formatters), um den Anforderungstext in ein-Objekt zu deserialisieren.

Weitere Informationen finden Sie unter [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).

## <a name="fromservices-attribute"></a>[FromServices]-Attribut

Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben. Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht. Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab. Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="what-is-model-binding"></a>Was ist Modellbindung?

Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen. Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen. Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig. Modellbindung automatisiert diesen Vorgang. Das Modellbindungssystem:

* Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.
* Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.
* Konvertiert Zeichenfolgendaten in .NET-Typen.
* Aktualisiert Eigenschaften komplexer Typen.

## <a name="example"></a>Beispiel

Angenommen Sie haben die folgende Aktionsmethode:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

Und die App empfängt eine Anforderung mit dieser URL:

```
http://contoso.com/api/pets/2?DogsOnly=true
```

Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:

* Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.
* Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.
* Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.
* Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.
* Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge. Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.
* Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.

Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.

Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind. Ziele können aber auch die Eigenschaften eines komplexen Typs sein. Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft. Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert. Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).

## <a name="targets"></a>Ziele

Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:

* Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.
* Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird. 
* Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.

### <a name="bindproperty-attribute"></a>[BindProperty]-Attribut

Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a>[BindProperties]-Attribut

Verfügbar in ASP.NET Core 2.1 und höher.  Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>Modellbindung für HTTP-GET-Anforderungen

Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden. In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter. Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen. Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält. In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>Quellen

Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:

1. Formularfelder
1. Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))
1. Routendaten
1. Abfragezeichenfolge-Parameter
1. Hochgeladene Dateien

Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft. Es gibt ein paar Ausnahmen:

* Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.
* Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.

Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ruft Werte aus der Abfrage Zeichenfolge ab. 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ruft Werte aus Routendaten ab.
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ruft Werte aus den Feldern für gepostete Formulare ab.
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ruft Werte aus dem Anforderungs Text ab.
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ruft Werte aus HTTP-Headern ab.

Diese Attribute:

* Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* Akzeptieren optional einen Modellnamenswert im Konstruktor. Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt. Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody]-Attribut

Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen. Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer. Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.

Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden. Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

Im vorherigen Beispiel:

* Das `[FromQuery]`-Attribut wird ignoriert.
* Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt. 

Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht. Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.

Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an. Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.

### <a name="additional-sources"></a>Zusätzliche Quellen

Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt. Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen. Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand. So rufen Sie Daten aus einer neuen Quelle ab

* Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.
* Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.
* Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.

Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie . Dies ist der Registrierungscode in `Startup.ConfigureServices`:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.  Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.

## <a name="no-source-for-a-model-property"></a>Keine Quelle für eine Modelleigenschaft

Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird. Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:

* Einfache Nullable-Typen werden auf `null` festgelegt.
* Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt. Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.
* Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.
* Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.

Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.

Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext. Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.

## <a name="type-conversion-errors"></a>Typkonvertierungsfehler

Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet. Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.

In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.

Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor . Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen. Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet. Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.

Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt. Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde. Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt. Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.

Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen. In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.

## <a name="simple-types"></a>Einfache Typen

Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:

* [Boolescher Wert](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [Decimal](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [Enumeration](xref:System.ComponentModel.EnumConverter)
* [GUID](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)
* [URI](xref:System.UriTypeConverter)
* [Version](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>Komplexe Typen

Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können. Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert. 

Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*. Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.

Beim Binden an einen Parameter ist das Präfix der Name des Parameters. Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft. Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.

Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>Präfix = Parametername

Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="prefix--property-name"></a>Präfix = Name der Eigenschaft

Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="custom-prefix"></a>Benutzerdefiniertes Präfix

Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht. Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.

### <a name="attributes-for-complex-type-targets"></a>Attribute für Ziele komplexen Typs

Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind. Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten. Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.
>
> Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).

### <a name="bindrequired-attribute"></a>[BindRequired]-Attribut

Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter. Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever]-Attribut

Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter. Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[Bind]-Attribut

Kann auf eine Klasse oder einen Methodenparameter angewendet werden. Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.

Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs*szenarien (create) verwendet werden. Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben. Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs. Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).

## <a name="collections"></a>Sammlungen

Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*. Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix. Beispiel:

* Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* Das folgende Format wird nur für Formulardaten unterstützt:

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:

  * selectedCourses[0]=1050
  * selectedCourses[1]=2000

  Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null). Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert. Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.

## <a name="dictionaries"></a>Wörterbücher

Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*. Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix. Beispiel:

* Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses["2000"]="Economics"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung

Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:

* behandeln Werte als invariante Kulturen.
* erwarten, dass URLs kulturinvariant sind.

Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung. Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.

So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:

* Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.
* Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).
* Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).
* Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>Spezielle Datentypen

Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.

### <a name="iformfile-and-iformfilecollection"></a>„IFormFile“ und „IFormFileCollection“

Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.  Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.

### <a name="cancellationtoken"></a>CancellationToken

Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.

### <a name="formcollection"></a>„FormCollection“

Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.

## <a name="input-formatters"></a>Eingabeformatierer

Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein. Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist. ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten. Sie können andere Formatierer für andere Inhaltstypen hinzufügen.

ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus. Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).

So verwenden Sie die integrierte XML-Eingabeformatierer

* Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.

* In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).

## <a name="exclude-specified-types-from-model-binding"></a>Ausschließen angegebener Typen aus der Modellbindung

Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert. Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen. Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.

Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu. Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu. Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>Benutzerdefinierte Modellbindungen

Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen. Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).

## <a name="manual-model-binding"></a>Manuelle Modellbindung

Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden. Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert. Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen. Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt. Hier sehen Sie ein Beispiel:

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices]-Attribut

Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben. Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht. Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab. Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
