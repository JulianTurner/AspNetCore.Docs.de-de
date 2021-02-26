---
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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552650"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Hinzufügen von Validierungsregeln zum Modell „Movie“

Der Namespace „DataAnnotations“ bietet eine Gruppe integrierter Validierungsattribute, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden. „DataAnnotations“ enthält auch Formatierungsattribute wie `DataType`, die bei der Formatierung helfen und keinerlei Validierung bereitstellen.

Aktualisieren Sie die `Movie`-Klasse, um die integrierten Validierungsattribute `Required`, `StringLength`, `RegularExpression` und `Range` zu nutzen.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Die Validierungsattribute geben das Verhalten an, das Sie in den Modelleigenschaften erzwingen möchten, auf die sie angewendet werden:

* Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.
* Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen. Für „Genre“ im Code oben gilt Folgendes:

  * Es dürfen nur Buchstaben enthalten sein.
  * Der erste Buchstabe muss ein Großbuchstabe sein. Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.

* Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:

  * Das erste Zeichen muss ein Großbuchstabe sein.
  * Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig. „PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.

* Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.
* Mit dem Attribut `StringLength` können Sie die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegen.
* Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.

Indem Validierungsregeln von ASP.NET Core automatisch erzwungen werden, wird Ihre App stabiler. Darüber hinaus wird sichergestellt, dass Sie die Validierung nicht vergessen und nicht versehentlich falsche Daten in die Datenbank übernehmen.
