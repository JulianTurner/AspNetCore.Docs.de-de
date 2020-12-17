---
title: 'Teil 8: Hinzufügen der Validierung'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/29/2020
no-loc:
- Index
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: 9774607b641005145bdb1c98d850c9ce79a25476
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486121"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a>Dies ist Teil 8 der Tutorialreihe zu Razor Pages.

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt wird dem Modell `Movie` Validierungslogik hinzugefügt. Die Validierungsregeln werden erzwungen, wenn ein Benutzer einen Film erstellt oder bearbeitet.

## <a name="validation"></a>Validierung

Ein wesentlicher Grundsatz der Softwareentwicklung heißt [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (**D** on't **R** epeat **Y** ourself, dt. Wiederholen Sie sich nicht). Razor Pages ist für Entwicklungsaufgaben gedacht, bei denen die Funktionalität einmal angegeben und für die gesamte App übernommen wird. DRY kann Sie bei Folgendem unterstützen:

* Sie können die Codemenge in einer App reduzieren.
* Der Code wird weniger fehleranfällig und lässt sich leichter testen und verwalten.

Die von Razor Pages und Entity Framework gebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips:

* Validierungsregeln werden an zentraler Stelle (in der Modellklasse) deklarativ angegeben.
* Regeln werden überall in der App erzwungen.

## <a name="add-validation-rules-to-the-movie-model"></a>Hinzufügen von Validierungsregeln zum Modell „Movie“

Der Namespace `DataAnnotations` bietet Folgendes:

* Eine Gruppe integrierter Validierungsattribute, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.
* Formatierungsattribute wie `[DataType]`, die bei der Formatierung helfen und keine Validierung bieten.

Aktualisieren Sie die `Movie`-Klasse, um die integrierten Validierungsattribute `[Required]`, `[StringLength]`, `[RegularExpression]` und `[Range]` zu nutzen.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:

* Die Attribute `[Required]` und `[MinimumLength]` geben an, dass eine Eigenschaft über einen Wert verfügen muss. Nichts hindert einen Benutzer an der Eingabe von Leerzeichen, um diese Validierung zu erfüllen.
* Das Attribut `[RegularExpression]` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen. Im vorherigen Code, `Genre`:

  * Es dürfen nur Buchstaben enthalten sein.
  * Der erste Buchstabe muss ein Großbuchstabe sein. Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.

* Der `RegularExpression` `Rating`:

  * Das erste Zeichen muss ein Großbuchstabe sein.
  * Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig. „PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „`Genre`“.

* Das `[Range]`-Attribut schränkt einen Wert auf einen bestimmten Bereich ein.
* Mit dem Attribut `[StringLength]` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die Mindestlänge festlegt werden.
* Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.

Die oben genannten Validierungsregeln dienen der Veranschaulichung und sind für ein Produktionssystem nicht optimal. Beispielsweise verhindert die vorherige Regel die Eingabe eines Films mit nur zwei Zeichen und lässt keine Sonderzeichen in `Genre` zu.

Die automatische Erzwingung von Validierungsregeln durch ASP.NET Core dient folgenden Zwecken:

* Hilft, die App stabiler zu machen.
* Reduziert die Wahrscheinlichkeit, dass ungültige Daten in der Datenbank gespeichert werden.

### <a name="validation-error-ui-in-no-locrazor-pages"></a>Benutzeroberfläche für Validierungsfehler in Razor Pages

Führen Sie die App aus, und navigieren Sie zu „Pages/Movies“.

Klicken Sie auf den Link **Neu erstellen**. Füllen Sie das Formular mit einigen ungültigen Werten aus. Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Wie Sie sehen, hat das Formular in allen Feldern mit einem ungültigen Wert automatisch eine Validierungsfehlermeldung angezeigt. Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).

Ein entscheidender Vorteil ist, dass **keine** Codeänderungen auf den Seiten „Erstellen“ oder „Bearbeiten“ erforderlich waren. Nach Anwenden von Datenanmerkungen auf das Modell wurde die Benutzeroberflächenvalidierung aktiviert. Die in diesem Tutorial erstellten Razor Pages haben die Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`). Testen Sie die Validierung mithilfe der Seite „Bearbeiten“. Es erfolgt dieselbe Validierung.

Die Formulardaten werden erst an den Server zurückgesendet, wenn auf Clientseite keine Validierungsfehler auftreten. Überprüfen Sie mithilfe von mindestens einem der folgenden Ansätze, ob keine Formulardaten bereitgestellt werden:

* Setzen Sie einen Haltepunkt in der `OnPostAsync`-Methode. Senden Sie das Formular, indem Sie **Erstellen** oder **Speichern** auswählen. Der Haltepunkt wird niemals erreicht.
* Verwenden Sie das [Tool Fiddler](https://www.telerik.com/fiddler).
* Verwenden Sie die Browserentwicklungstools zum Überwachen des Netzwerkdatenverkehrs.

### <a name="server-side-validation"></a>Serverseitige Validierung

Wenn JavaScript im Browser deaktiviert ist, erfolgt beim Senden des Formulars mit Fehlern eine Bereitstellung auf dem Server.

Testen Sie optional die serverseitige Validierung:

1. Deaktivieren Sie JavaScript im Browser. JavaScript kann in den Entwicklertools im Browser deaktiviert werden. Wenn JavaScript nicht im Browser deaktiviert werden kann, probieren Sie einen anderen Browser.
1. Setzen Sie auf der Seite „Erstellen“ oder „Bearbeiten“ in der `OnPostAsync`-Methode einen Haltepunkt.
1. Senden Sie ein Formular mit ungültigen Daten.
1. Überprüfen Sie, ob der Modellstatus ungültig ist:

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
Alternativ können Sie [die clientseitige Validierung auf dem Server deaktivieren](xref:mvc/models/validation#disable-client-side-validation).

Der folgende Code zeigt einen Teil der Seite *Create.cshtml*, deren Gerüst Sie zuvor im Tutorial erstellt haben. Sie wird auf den Seiten „Erstellen“ und „Bearbeiten“ für Folgendes verwendet:

* Anzeigen des Ausgangsformulars
* Erneutes Anzeigen des Formulars bei einem Fehler

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler. Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).

Die Seiten „Erstellen“ und „Bearbeiten“ weisen keine Validierungsregeln auf. Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben. Diese Validierungsregeln gelten automatisch für Razor-Seiten, die das Modell `Movie` bearbeiten.

Wenn Validierungslogik geändert werden muss, erfolgt dies nur im Modell. Die Validierung erfolgt in der gesamten Anwendung einheitlich (Validierungslogik ist zentral definiert). Die zentrale Validierung unterstützt sauberen Code und erleichtert dessen Verwaltung und Aktualisierung.

## <a name="use-datatype-attributes"></a>Verwenden von DataType-Attributen

Untersuchen Sie die Klasse `Movie`. Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit. Das `[DataType]`-Attribut wird nur auf die Eigenschaften `ReleaseDate` und `Price` angewendet.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Die `[DataType]`-Attribute stellen Folgendes bereit:

* Hinweise für die Ansichts-Engine zum Formatieren der Daten
* Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail

Verwenden Sie das `[RegularExpression]`-Attribut, um das Format der Daten zu validieren. Das `[DataType]`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist. `[DataType]`-Attribute sind keine Validierungsattribute. In der Beispielanwendung wird nur das Datum ohne Uhrzeit angezeigt.

Die `DataType`-Enumeration stellt viele Datentypen bereit, u. a. `Date`, `Time`, `PhoneNumber`, `Currency` und `EmailAddress`. 

Die `[DataType]`-Attribute:

* Können der Anwendung ermöglichen, typspezifische Features bereitzustellen. Ein Link des Typs `mailto:` kann beispielsweise für `DataType.EmailAddress` erstellt werden.
* Können in Browsern mit HTML5-Unterstützung die Datumsauswahl `DataType.Date` bereitstellen.
* Können HTML5-Attribute des Typs `data-` (ausgesprochen „Datadash“) ausgeben, die HTML5-Browser nutzen.
* Bieten **keine** Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.

Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann. Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).

Das `[DisplayFormat]`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung angewendet wird, wenn der Wert zur Bearbeitung angezeigt wird. Dieses Verhalten kann für einige Felder unerwünscht sein. In Währungswerten soll beispielsweise wahrscheinlich nicht das Währungssymbol auf der Bearbeitungsoberfläche gezeigt werden.

Das `[DisplayFormat]`-Attribut kann eigenständig verwendet werden, doch meist empfiehlt es sich, das `[DataType]`-Attribut zu verwenden. Das `[DataType]`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Das `[DataType]`-Attribut bietet folgende Vorteile, die mit `[DisplayFormat]` nicht verfügbar sind:

* Der Browser kann HTML5-Features aktivieren, z. B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.
* Standardmäßig rendert der Browser Daten mit dem auf seinem Gebietsschema basierenden richtigen Format.
* Das `[DataType]`-Attribut kann dem ASP.NET Core-Framework ermöglichen, die richtige Feldvorlage zum Rendern der Daten auszuwählen. `DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage.

**Hinweis:** Die jQuery-Validierung funktioniert nicht mit den Attributen `[Range]` und `DateTime`. Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Es ist eine bewährte Methode, keine festen Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `[Range]`-Attributs und von `DateTime` abgeraten wird. Verwenden Sie den Abschnitt [Configuration](xref:fundamentals/configuration/index) für Datumsbereiche und andere Werte, die häufig geändert werden, anstatt sie im Code anzugeben.

Der folgende Code zeigt die Kombination von Attributen in einer Zeile:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

Unter [Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) werden erweiterte EF Core-Vorgänge mit Razor Pages erläutert.

### <a name="apply-migrations"></a>Anwenden von Migrationen

Durch die Anwendung von „DataAnnotations“ auf die Klasse wird das Schema geändert. Beispielsweise ergeben sich durch die Anwendung von DataAnnotations auf das `Title`-Feld folgende Änderungen:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* Die Zeichen werden auf 60 begrenzt.
* Ein `null`-Wert ist unzulässig.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die `Movie`-Tabelle verfügt zurzeit über das folgende Schema:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

Die vorstehenden Schemaänderungen bewirken nicht, dass EF eine Ausnahme auslöst. Erstellen Sie jedoch eine Migration, damit das Schema mit dem Modell konsistent ist.

Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.
Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

`Update-Database` führt die `Up`-Methoden der `New_DataAnnotations`-Klasse aus. Untersuchen Sie die `Up`-Methode.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

Die aktualisierte `Movie`-Tabelle hat das folgende Schema:

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Für SQLite sind keine Migrationen erforderlich.

---

### <a name="publish-to-azure"></a>Veröffentlichen in Azure

Informationen zum Bereitstellen in Azure finden Sie unter [Tutorial: Erstellen einer ASP.NET Core-App in Azure mit SQL-Datenbank](/azure/app-service/tutorial-dotnetcore-sqldb-app).

Vielen Dank für Ihr Interesse an dieser Einführung in Razor Pages. [Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [Zurück: Hinzufügen eines neuen Felds](xref:tutorials/razor-pages/new-field)
