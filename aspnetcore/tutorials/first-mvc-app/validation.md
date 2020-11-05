---
title: 'Teil 9: Hinzufügen der Validierung zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 9 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 04/13/2017
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
uid: tutorials/first-mvc-app/validation
ms.openlocfilehash: 340a66c4a561c6e00bf6f38bcf51abc795aa649c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059090"
---
# <a name="part-9-add-validation-to-an-aspnet-core-mvc-app"></a>Teil 9: Hinzufügen der Validierung zu einer ASP.NET Core MVC-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Abschnitt

* Dem Modell `Movie` wird Validierungslogik hinzugefügt.
* Sie stellen sicher, dass die Validierungsregeln immer dann erzwungen werden, wenn ein Benutzer einen Film erstellt oder bearbeitet.

## <a name="keeping-things-dry"></a>Einhalten des DRY-Prinzips

Einer der Entwurfsgrundsätze von MVC ist [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (Don't Repeat Yourself, keine Wiederholungen). ASP.NET Core MVC unterstützt Sie dabe, Funktionalität und Verhalten nur einmal anzugeben und dann auf die gesamte App zu übertragen. Dadurch reduziert sich der Umfang an Code, den Sie schreiben müssen. Und der Code, den Sie schreiben, ist weniger fehleranfällig, leichter zu testen und einfacher zu verwalten.

Die von MVC und Entity Framework Core Code First angebotene Unterstützung der Validierung ist ein gutes Beispiel für den Einsatz des DRY-Prinzips. Sie können deklarativ Validierungsregeln an zentraler Stelle (in der Modellklasse) angegeben, und die Regeln werden überall in der App erzwungen.

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

## <a name="validation-error-ui"></a>Benutzeroberflächenoption für Validierungsfehler

Führen Sie die App aus, und navigieren Sie zum Movies-Controller.

Tippen Sie auf den Link **Neu erstellen** , um einen neuen Film hinzuzufügen. Füllen Sie das Formular mit einigen ungültigen Werten aus. Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/first-mvc-app/validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

Wie Sie sehen, hat das Formular automatisch für alle Felder mit einem ungültigen Wert eine entsprechende Validierungsfehlermeldung angezeigt. Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).

Ein entscheidender Vorteil ist, dass Sie nicht eine Codezeile in der Klasse `MoviesController` oder in der Ansicht *Create.cshtml* ändern müssen, um diese Benutzeroberfläche für die Validierung zu aktivieren. Die Controller und Ansichten, die Sie zuvor in diesem Tutorial erstellt haben, haben die angegebenen Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`). Testen Sie die Validierung mithilfe der Aktionsmethode `Edit`, und es folgt die gleiche Validierung.

Die Formulardaten werden erst an den Server gesendet, wenn auf Clientseite keine Validierungsfehler mehr auftreten. Sie können dies überprüfen, indem Sie einen Haltepunkt in die Methode `HTTP Post` einfügen. Verwenden Sie dazu das [Fiddler-Tool](https://www.telerik.com/fiddler) oder die [F12-Entwicklertools](/microsoft-edge/devtools-guide).

## <a name="how-validation-works"></a>Funktionsweise der Validierung

Sie fragen sich vielleicht, wie die Benutzeroberfläche für die Validierung ohne Aktualisierungen von Code im Controller oder in Ansichten generiert wurde. Der folgende Code zeigt die beiden `Create`-Methoden.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippetCreate)]

Die erste `Create`-Aktionsmethode (HTTP GET) zeigt das erste Formular „Create“ an. Die zweite Version (`[HttpPost]`) verarbeitet die Formularbereitstellung. Die zweite `Create`-Methode (die `[HttpPost]`-Version) ruft `ModelState.IsValid` auf, um zu überprüfen, ob der Film Validierungsfehler aufweist. Beim Aufrufen dieser Methode werden alle Validierungsattribute ausgewertet, die auf das Objekt angewendet wurden. Wenn das Objekt Validierungsfehler enthält, zeigt die `Create`-Methode das Formular erneut an. Wenn keine Fehler vorliegen, speichert die Methode den neuen Film in der Datenbank. In unserem Movie-Beispiel wird das Formular nicht an den Server gesendet, wenn Validierungsfehler auf der Clientseite erkannt werden. Die zweite `Create`-Methode wird nicht aufgerufen, wenn clientseitige Validierungsfehler vorhanden sind. Wenn Sie JavaScript in Ihrem Browser deaktivieren, wird die Clientvalidierung deaktiviert, und Sie können die HTTP-POST-`Create`-Methode testen und mit `ModelState.IsValid` Validierungsfehler finden.

Sie können einen Haltepunkt in der `[HttpPost] Create`-Methode festlegen und überprüfen, ob die Methode tatsächlich niemals aufgerufen wird und die clientseitige Validierung die Formulardaten nicht sendet, wenn Validierungsfehler gefunden werden. Wenn Sie JavaScript in Ihrem Browser deaktivieren und dann das Formular mit Fehlern senden, wird der Haltepunkt erreicht. Sie erhalten auch ohne JavaScript weiterhin eine vollständige Validierung. 

Die folgende Abbildung zeigt, wie JavaScript im Firefox-Browser deaktiviert wird.

![Firefox: Deaktivieren Sie auf der Registerkarte „Inhalt“ unter „Optionen“ das Kontrollkästchen „Javascript aktivieren“.](~/tutorials/first-mvc-app/validation/_static/ff.png)

Die folgende Abbildung zeigt, wie JavaScript im Chrome-Browser deaktiviert wird.

![Google Chrome: Wählen Sie im JavaScript-Abschnitt der Inhaltseinstellungen die Option „Ausführung von JavaScript für keine Webseite zulassen“ aus.](~/tutorials/first-mvc-app/validation/_static/chrome.png)

Nachdem Sie JavaScript deaktiviert haben, senden Sie ungültige Daten, und gehen Sie den Debugger durch.

![Während des Debuggens von ungültigen Daten zeigt IntelliSense für „ModelState.IsValid“ an, dass der Wert falsch ist.](~/tutorials/first-mvc-app/validation/_static/ms.png)

Der Teil der *Create.cshtml* -Ansichtsvorlage wird im folgenden Markup gezeigt:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/CreateRatingBrevity.html)]

Das obige Markup wird von den Aktionsmethoden verwendet, um das Anfangsformulars anzuzeigen und es im Fall eines Fehlers erneut anzuzeigen.

Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind. Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler. Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).

Wirklich nützlich an diesem Ansatz ist, dass weder der Controller noch die Ansichtsvorlage `Create` an den eigentlichen Validierungsregeln, die erzwungen werden, oder den spezifischen Fehlermeldungen, die angezeigt werden, beteiligt sind. Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben. Diese gleichen Validierungsregeln werden automatisch auf die Ansicht `Edit` und alle anderen Ansichtsvorlagen angewendet, die Sie erstellen und die das Modell bearbeiten.

Wenn Sie Validierungslogik ändern müssen, können Sie dies auch an genau einer Stelle tun, indem Sie Validierungsattribute zum Modell hinzufügen (in diesem Beispiel zur Klasse `Movie`). Sie müssen sich keine Gedanken darüber machen, ob die verschiedenen Teile der Anwendung inkonsistent sind und wie Regeln erzwungen werden: Die gesamte Validierungslogik wird zentral definiert und überall verwendet. Dies hält den Code sehr übersichtlich und vereinfacht die Verwaltung und Entwicklung. Und dies bedeutet, dass Sie das DRY-Prinzip vollständig einhalten.

## <a name="using-datatype-attributes"></a>Verwenden von „DataType“-Attributen

Öffnen Sie die Datei *Movie.cs* , und überprüfen Sie die Klasse `Movie`. Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit. Wir haben bereits einen `DataType`-Enumerationswert auf die Felder mit dem Veröffentlichungsdatum und dem Preis angewendet. Der folgende Code zeigt die Eigenschaften `ReleaseDate` und `Price` mit dem entsprechenden `DataType`-Attribut.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

Die `DataType`-Attribute geben der Anzeige-Engine nur Hinweise zum Formatieren der Daten (und liefern Elemente bzw. Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail). Sie können das `RegularExpression`-Attribut verwenden, um das Format der Daten zu validieren. Das `DataType`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist. Dabei handelt es sich nicht um Validierungsattribute. In diesem Fall möchten wir nur das Datum verfolgen, nicht die Zeit. Die `DataType`-Enumeration stellt viele Datentypen bereit, wie z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“ usw. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Beispielsweise kann ein `mailto:`-Link für `DataType.EmailAddress` erstellt werden, und eine Datumsauswahl kann in Browsern mit Unterstützung für HTML5 für `DataType.Date` bereitgestellt werden. Die `DataType`-Attribute geben `data-`-Attribute (ausgesprochen „Datadash“) von HTML5 aus, die HTML5-Browser nutzen können. Die `DataType`-Attribute bieten **keine** Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung auch angewendet werden soll, wenn der Wert in einem Textfeld zur Bearbeitung angezeigt wird. (Es kann sein, dass Sie dies für einige Felder nicht benötigen. Z.B. ist es sinnvoller, dass für Währungsangaben das Währungssymbol im Textfeld für die Bearbeitung nicht enthalten ist.)

Sie können das `DisplayFormat`-Attribut eigenständig verwenden, doch meist empfiehlt es sich, das `DataType`-Attribut zu verwenden. Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Es bietet die folgenden Vorteile, die „DisplayFormat“ nicht bietet:

* Der Browser kann HTML5-Features aktivieren (z.B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.).

* Standardmäßig rendert der Browser Daten mit dem ordnungsgemäßen auf Ihrem Gebietsschema basierenden Format.

* Mit dem `DataType`-Attribut kann MVC die richtige Feldvorlage zum Rendern der Daten auswählen (`DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage).

> [!NOTE]
> Die jQuery-Validierung funktioniert mit den Attributen `Range` und `DateTime` nicht. Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:
>
> `[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]`

Sie müssen die jQuery-Datumsvalidierung deaktivieren, um das `Range`-Attribut mit `DateTime` zu verwenden. Es wird allgemein nicht empfohlen, feste Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `Range`-Attributs und von `DateTime` abgeraten wird.

Der folgende Code zeigt die Kombination von Attributen in einer Zeile:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDAmult.cs?name=snippet1)]

Im nächsten Teil der Reihe überprüfen wir die App und nehmen einige Verbesserungen an den automatisch generierten Methoden `Details` und `Delete` vor.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Arbeiten mit Formularen](xref:mvc/views/working-with-forms)
* [Globalisierung und Lokalisierung](xref:fundamentals/localization)
* [Einführung in Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Erstellen von Taghilfsprogrammen](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Zurück](new-field.md)
> [Weiter](details.md)  
