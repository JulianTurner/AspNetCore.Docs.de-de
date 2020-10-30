---
title: Cache-Taghilfsprogramm im ASP.NET Core MVC
author: pkellner
description: Erfahren Sie, wie das Cache-Taghilfsprogramm verwendet wird.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: a87f91255bd1f280b1567f522423a6f4e88a6dd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060884"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a>Cache-Taghilfsprogramm im ASP.NET Core MVC

Von [Peter Kellner](https://peterkellner.net)

Durch das Cache-Taghilfsprogramm kann die Leistung Ihrer ASP.NET Core-App verbessert werden, indem deren Inhalte im internen ASP.NET Core-Cacheanbieter zwischengespeichert werden.

Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

Im folgenden Razor Markup wird das aktuelle Datum zwischengespeichert:

```cshtml
<cache>@DateTime.Now</cache>
```

Über die erste Anforderung an die Seite, die das Taghilfsprogramm enthält, wird das aktuelle Datum zurückgegeben. Über zusätzliche Anforderungen werden die zwischengespeicherten Werte angezeigt, bis der Cache abläuft (standardmäßig 20 Minuten lang) oder bis das zwischengespeicherte Datum aus dem Cache entfernt wird.

## <a name="cache-tag-helper-attributes"></a>Attribute von Cache-Taghilfsprogrammen

### <a name="enabled"></a>enabled

| Attributtyp  | Beispiele        | Standard |
| --------------- | --------------- | ------- |
| Boolesch         | `true`, `false` | `true`  |

`enabled` legt fest, ob der Inhalt zwischengespeichert wird, der vom Cache-Taghilfsprogramm eingeschlossen wird. Der Standardwert ist `true`. Wenn diese Option auf `false` festgelegt ist, wird die gerenderte Ausgabe **nicht** zwischengespeichert.

Beispiel:

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a>expires-on

| Attributtyp   | Beispiel                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

`expires-on` legt die absolute Ablaufzeit für das zwischengespeicherte Element fest.

Im folgenden Beispiel werden die Inhalte des Cache-Taghilfsprogramms bis zum 29. Januar 2025 um 17:02 Uhr zwischengespeichert:

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a>expires-after

| Attributtyp | Beispiel                      | Standard    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | 20 Minuten |

`expires-after` legt die Zeitspanne ab der ersten Anforderungszeit fest, um die Inhalte zwischenzuspeichern.

Beispiel:

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Der Razor Standardwert wird von der Ansichts-Engine `expires-after` auf 20 Minuten festgelegt.

### <a name="expires-sliding"></a>expires-sliding

| Attributtyp | Beispiel                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

Legt die Zeit fest, nach der ein Cacheeintrag gelöscht werden soll, wenn niemand auf diesen Wert zugegriffen hat.

Beispiel:

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a>vary-by-header

| Attributtyp | Beispiele                                    |
| -------------- | ------------------------------------------- |
| String         | `User-Agent`, `User-Agent,content-encoding` |

`vary-by-header` akzeptiert eine durch Trennzeichen getrennte Liste von Headerwerten, die eine Cacheaktualisierung auslösen, wenn diese geändert werden.

Im folgenden Beispiel wird der Headerwert `User-Agent` überwacht. Außerdem werden die Inhalte für alle `User-Agent` zwischengespeichert, die dem Webserver präsentiert werden:

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a>vary-by-query

| Attributtyp | Beispiele             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-query` akzeptiert eine durch Kommas getrennte Liste von <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in eine Abfragezeichenfolge (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>), die eine Cacheaktualisierung auslösen, wenn der Wert eines aufgeführten Schlüssels geändert wird.

Im folgenden Beispiel werden die Werte von `Make` und `Model` überwacht. Außerdem werden die Inhalte für alle `Make` und `Model` zwischengespeichert, die dem Webserver präsentiert werden:

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a>vary-by-route

| Attributtyp | Beispiele             |
| -------------- | -------------------- |
| String         | `Make`, `Make,Model` |

`vary-by-route` akzeptiert eine durch Trennzeichen getrennte Liste mit Namen von Routenparametern, die eine Cacheaktualisierung auslösen, wenn der Routendatenparameter geändert wird.

Beispiel:

*Startup.cs* :

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

*Index. cshtml* :

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a>variieren nachcookie

| Attributtyp | Beispiele                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| String         | `.AspNetCore.Identity.Application`, `.AspNetCore.Identity.Application,HairColor` |

`vary-by-cookie` akzeptiert eine durch Trennzeichen getrennte Liste von cookie Namen, die eine Cache Aktualisierung auslöst, wenn sich die cookie Werte ändern.

Im folgenden Beispiel wird der cookie zugeordnet, der zugeordnet ist ASP.NET Core Identity . Wenn ein Benutzer authentifiziert wird, löst eine Änderung in Identity cookie eine Cache Aktualisierung aus:

```cshtml
<cache vary-by-cookie=".AspNetCore.Identity.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a>vary-by-user

| Attributtyp  | Beispiele        | Standard |
| --------------- | --------------- | ------- |
| Boolesch         | `true`, `false` | `true`  |

`vary-by-user` gibt an, ob der Cache zurückgesetzt wird, wenn sich ein anderer Benutzer anmeldet, also der Kontextprinzipal geändert wird. Der aktuelle Benutzer wird auch als Anforderungs Kontext Prinzipal bezeichnet und kann in einer Ansicht angezeigt werden, Razor indem auf verwiesen wird `@User.Identity.Name` .

Das folgende Beispiel überwacht den derzeit angemeldeten Benutzer, um eine Cacheaktualisierung auszulösen:

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Wenn Sie dieses Attribut verwenden, werden die Inhalte im Cache über einen Anmelde- und Abmeldezyklus verwaltet. Wenn der Wert auf `true` festgelegt wird, erklärt ein Authentifizierungszyklus den Cache für den authentifizierten Benutzer als ungültig. Der Cache wird für ungültig erklärt, weil ein neuer eindeutiger cookie Wert generiert wird, wenn ein Benutzer authentifiziert wird. Der Cache wird für den anonymen Status beibehalten, wenn kein cookie vorhanden ist oder cookie abgelaufen ist. Wenn der Benutzer **nicht** authentifiziert ist, wird der Cache verwaltet.

### <a name="vary-by"></a>vary-by

| Attributtyp | Beispiel  |
| -------------- | -------- |
| String         | `@Model` |

Über `vary-by` können Sie festlegen, welche Daten zwischengespeichert werden sollen. Wenn das Objekt verändert wird, auf das der Zeichenfolgenwert des Attributs verweist, wird der Inhalt des Cache-Hilfsprogramms aktualisiert. Häufig wird eine Zeichenfolgenverkettung von Modellwerten diesem Attribut zugewiesen. Dies führt letztlich zu einem Szenario, bei dem der Cache ungültig wird, wenn ein Update an einem der verketteten Werte vorgenommen wird.

Bei dem folgenden Beispiel wird davon ausgegangen, dass die Controllermethode, die die Ansicht rendert, die ganzzahligen Werte der beiden Routenparameter `myParam1` und `myParam2` addiert, und die Summe als Modelleigenschaft zurückgibt. Wenn sich diese Summe ändert, wird auch der Inhalt des Cache-Taghilfsprogramms gerendert und erneut zwischengespeichert.  

Aktion:

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

*Index. cshtml* :

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a>priority

| Attributtyp      | Beispiele                               | Standard  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | `High`, `Low`, `NeverRemove`, `Normal` | `Normal` |

`priority` enthält Anweisungen zum Entfernen des Caches für den integrierten Cacheanbieter. Der Webserver entfernt `Low`-Cacheeinträge erst, wenn der Arbeitsspeicher ausgelastet ist.

Beispiel:

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

Das `priority`-Attribut garantiert keine festgelegte Ebene der Cachevermerkdauer. Bei `CacheItemPriority` handelt es sich nur um einen Vorschlag. Wenn Sie dieses Attribut auf `NeverRemove` festlegen, ist das noch keine Garantie dafür, dass zwischengespeicherte Elemente für immer gespeichert bleiben. Weitere Informationen finden Sie in den Themen im Abschnitt [Weitere Ressourcen](#additional-resources).

Das Cache-Taghilfsprogramm ist vom [Arbeitsspeicher Cache Service](xref:performance/caching/memory) abhängig. Das Cache-Taghilfsprogramm fügt den Dienst wenn nötig hinzu.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
