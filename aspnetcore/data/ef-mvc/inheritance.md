---
title: 'Tutorial: Implementieren von Vererbung: ASP.NET MVC mit EF Core'
description: In diesem Tutorial erfahren Sie, wie Sie die Vererbung mithilfe von Entity Framework Core in einer ASP.NET Core-App implementieren.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 581a31bad4069523699fbbac63862c9dff12034d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054215"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a>Tutorial: Implementieren von Vererbung: ASP.NET MVC mit EF Core

Im vorherigen Tutorial haben Sie Parallelitätsausnahmen behandelt. In diesem Tutorial erfahren Sie, wie Sie die Vererbung in das Datenmodell implementieren können.

Bei objektorientierter Programmierung können Sie mithilfe von Vererbung die Wiederverwendung von Code vereinfachen. In diesem Tutorial ändern Sie die Klassen `Instructor` und `Student` so, dass sie von einer `Person`-Basisklasse abgeleitet werden, die Eigenschaften wie `LastName` enthält. Diese Eigenschaften sind für Dozenten und Studenten gängig. Sie fügen keine Webseiten hinzu oder ändern diese, aber Sie werden Teile des Codes ändern. Diese Änderungen werden automatisch in der Datenbank widergespiegelt.

In diesem Tutorial:

> [!div class="checklist"]
> * Zuordnen von Vererbung zu Datenbank
> * Erstellen der Klasse „Person“
> * Aktualisieren von „Instructor“ und „Student“
> * Hinzuzufügen von „Person“ zum Modell
> * Erstellen und Aktualisieren von Migrationen
> * Testen der Implementierung

## <a name="prerequisites"></a>Voraussetzungen

* [Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core](concurrency.md)

## <a name="map-inheritance-to-database"></a>Zuordnen von Vererbung zu Datenbank

Die Klassen `Instructor` und `Student` im Datenmodell „Schule“ weisen mehrere identische Eigenschaften auf:

![Die Klassen „Student“ und „Instructor“](inheritance/_static/no-inheritance.png)

Angenommen, Sie möchten den redundanten Code für die Eigenschaften löschen, die von den Entitäten `Instructor` und `Student` gemeinsam genutzt werden. Oder Sie möchten einen Dienst schreiben, mit dem Namen formatiert werden können, ohne dass es eine Rolle spielt, ob der Name von einem Dozenten oder von einem Studenten stammt. Dann können Sie eine `Person`-Basisklasse erstellen, die nur diese gemeinsam genutzten Eigenschaften enthält. Anschließend können Sie einstellen, dass die Klassen `Instructor` und `Student` von dieser Basisklasse erben sollen, wie in der folgenden Abbildung dargestellt wird:

![Die Klassen „Student“ und „Instructor“ abgeleitet von der Klasse „Person“](inheritance/_static/inheritance.png)

Es gibt mehrere Möglichkeiten, wie diese Vererbungsstruktur in der Datenbank dargestellt werden kann. Sie können z.B. über die Tabelle „Person“ verfügen, die Informationen zu Studenten und Dozenten in einer einzigen Tabelle enthält. Einige Spalten können dann nur für Dozenten gelten (HireDate), einige nur für Studenten (EnrollmentDate) und einige für beide (LastName, FirstName). Normalerweise sollten Sie über eine Unterscheidungsspalte verfügen, in der angegeben wird, welcher Typ in den jeweiligen Zeilen dargestellt wird. So kann die Unterscheidungsspalte beispielsweise „Instructor“ für Dozenten und „Student“ für Studenten enthalten.

![Beispiel: Tabelle pro Hierarchie](inheritance/_static/tph.png)

Dieses Muster, bei dem aus einer einzigen Datenbanktabelle eine Vererbungsstruktur für Entitäten generiert wird, wird als TPH-Vererbung (TPH = Table per Hierarchy, Tabelle pro Hierarchie) bezeichnet.

Alternativ kann die Datenbank so gestaltet werden, dass sie mehr wie die Vererbungsstruktur aussieht. Die Tabelle „Person“ könnte beispielsweise nur die Namensfelder aufweisen und über separate Tabellen mit den Namen „Instructor“ und „Student“ verfügen, in denen die Datumsfelder enthalten sind.

> [!WARNING]
> Tabelle pro Typ (TPT) wird von EF Core 3.x nicht unterstützt, wurde aber trotzdem in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan) implementiert.

!["Tabelle pro Typ"-Vererbung](inheritance/_static/tpt.png)

Dieses Muster, bei dem für jede Entitätsklasse eine Datenbanktabelle erstellt wird, wird als TPT-Vererbung (TPT = Table per Type, Tabelle pro Typ) bezeichnet.

Eine weitere Möglichkeit besteht darin, individuellen Tabellen alle nicht abstrakten Typen zuzuordnen. Alle Eigenschaften einer Klasse, einschließlich der geerbten Eigenschaften, werden Spalten der entsprechenden Tabelle zugeordnet. Dieses Muster wird als TPC-Vererbung (TPC = Table per Concrete, Tabelle pro konkretem Typ) bezeichnet. Wenn Sie die TPC-Vererbung für die Klassen „Person“, „Student“ und „Instructor“ wie oben beschrieben implementieren, würden die Tabellen „Student“ und „Instructor“ nach der Implementierung unverändert aussehen.

Bei den TPC- und TPH-Vererbungsmustern wird in der Regel eine bessere Leistung erzielt als bei den TPT-Vererbungsmustern, da TPT-Muster zu komplexen Joinabfrage führen können.

Dieses Tutorial veranschaulicht die Implementierung der TPH-Vererbung. TPH ist das einzige Vererbungsmuster, das von Entity Framework Core unterstützt wird.  Dabei erstellen Sie eine `Person`-Klasse, ändern die Klassen `Instructor` und `Student`, die von `Person` abgeleitet werden sollen, fügen die neue Klasse zum `DbContext` hinzu und erstellen eine Migration.

> [!TIP]
> Sie sollten eine Kopie des Projekts speichern, bevor Sie folgende Änderungen vornehmen.  Wenn Probleme auftreten und Sie von vorne beginnen müssen, ist es leichter, vom gespeicherten Projekt aus zu starten, statt für dieses Tutorial ausgeführte Schritte rückgängig zu machen oder zum Anfang der Reihe zurückkehren zu müssen.

## <a name="create-the-person-class"></a>Erstellen der Klasse „Person“

Erstellen Sie im Ordner „Models“ (Modelle) die Datei „Person.cs“ und ersetzen Sie den Vorlagencode durch folgenden Code:

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a>Aktualisieren von „Instructor“ und „Student“

Leiten Sie in der Datei *Instructor.cs* die Klasse „Instructor“ von der Klasse „Person“ ab, und entfernen Sie Schlüssel- und Namensfelder. Der Code sieht aus wie im folgenden Beispiel:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

Nehmen Sie an der Datei *Student.cs* die gleichen Änderungen vor.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a>Hinzuzufügen von „Person“ zum Modell

Fügen Sie den Entitätstyp „Person“ zur Datei *SchoolContext.cs* hinzu. Die neuen Zeilen werden hervorgehoben.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

Das ist alles, was Entity Framework für die Konfiguration der „Tabelle pro Hierarchie“-Vererbung benötigt. Sie werden feststellen, dass die Datenbank nach ihrer Aktualisierung statt der Tabellen „Student“ und „Instructor“ eine Person-Tabelle enthält.

## <a name="create-and-update-migrations"></a>Erstellen und Aktualisieren von Migrationen

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt. Öffnen Sie anschließend das Befehlsfenster im Projektordner, und geben Sie folgenden Befehl ein:

```dotnetcli
dotnet ef migrations add Inheritance
```

Führen Sie noch nicht den Befehl `database update` aus. Die Ausführung dieses Befehls führt zu einem Datenverlust, da er die Tabelle „Instructor“ löscht und die Tabelle „Student“ in „Person“ umbenennt. Sie müssen benutzerdefinierten Code angeben, damit vorhandene Daten erhalten bleiben.

Öffnen Sie *Migrations/\<timestamp>_Inheritance.cs* , und ersetzen Sie die Methode `Up` durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

Dieser Code übernimmt folgende Tasks für Datenbankaktualisierungen:

* Er entfernt Fremdschlüsseleinschränkungen und -indizes, die auf die Tabelle „Student“ verweisen.

* Er benennt die Tabelle „Instructor“ in „Person“ um und nimmt die Änderungen vor, die für das Speichern von Studentendaten erforderlich sind:

* Er fügt das EnrollmentDate für Studenten hinzu, bei dem NULL-Werte zugelassen sind.

* Er fügt eine Unterscheidungsspalte hinzu, um anzugeben, ob eine Zeile für einen Studenten oder für einen Dozenten bestimmt ist.

* Er legt fest, dass bei HireDate NULL-Werte zugelassen sind, da die Zeilen für Studenten keine Einstellungsdaten enthalten.

* Er fügt ein temporäres Feld hinzu, über das Fremdschlüssel aktualisiert werden sollen, die auf Studenten verweisen. Wenn Sie Studenten in die Tabelle „Person“ kopieren, erhalten diese neue Primärschlüsselwerte.

* Kopiert Daten aus der Tabelle „Student“ in die Tabelle „Person“. Dadurch werden Studenten neue Primärschlüsselwerte zugewiesen.

* Er legt Fremdschlüsselwerte fest, die auf Studenten verweisen.

* Er erstellt Fremdschlüsseleinschränkungen und -indizes neu, die dann auf die Tabelle „Person“ verweisen.

(Hätten Sie als Primärschlüsseltyp statt einem Integer die grafische Benutzeroberfläche verwendet haben, hätten die Primärschlüsselwerte für Studenten nicht geändert werden müssen, und mehrere dieser Schritte hätten ausgelassen werden können.)

Führen Sie den Befehl `database update` aus:

```dotnetcli
dotnet ef database update
```

(In einem Produktionssystem würden Sie entsprechende Änderungen an der Methode `Down` vornehmen, falls Sie diese jemals verwenden müssten, um zur vorherigen Datenbankversion zurückzukehren. In diesem Tutorial wird die Methode `Down` nicht verwendet.)

> [!NOTE]
> Es ist möglich, dass andere Fehler auftreten, wenn Schemaänderungen in einer Datenbank durchgeführt werden, die vorhandene Daten enthält. Wenn Migrationsfehler auftreten, die Sie nicht beheben können, können Sie entweder den Datenbanknamen in der Verbindungszeichenfolge ändern oder die Datenbank löschen. In einer neuen Datenbank gibt es keine zu migrierenden Daten, und der Befehl „update-database“ wird wahrscheinlich ohne Fehler ausgeführt. Verwenden Sie zum Löschen der Datenbank SSOX, oder führen Sie den CLI-Befehl `database drop` aus.

## <a name="test-the-implementation"></a>Testen der Implementierung

Führen Sie die App aus, und testen Sie verschiedene Seiten. Alles funktioniert genauso wie vorher.

Wenn Sie im **SQL Server-Objekt-Explorer** **Data Connections/SchoolContext** und anschließend **Tabellen** erweitern, können Sie sehen, dass die Tabellen „Student“ und „Instructor“ durch eine Person-Tabelle ersetzt wurden. Wenn Sie den Person-Tabellen-Designer öffnen, sehen Sie, dass er alle Spalten aus den Tabellen „Student“ und „Instructor“ enthält.

![Person-Tabelle im SSOX](inheritance/_static/ssox-person-table.png)

Klicken Sie mit der rechten Maustaste auf die Tabelle „Person“, und klicken Sie anschließend auf **Tabellendaten anzeigen** , um die Unterscheidungsspalte anzuzeigen.

![Person-Tabelle im SSOX: Tabellendaten](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zur Vererbung in Entity Framework Core finden Sie unter [Vererbung](/ef/core/modeling/inheritance).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Vererbung wurde der Datenbank zugeordnet
> * Klasse „Person“ wurde erstellt
> * „Instructor“ und „Student“ wurden aktualisiert
> * „Person“ wurde dem Modell hinzugefügt
> * Migrationen wurden erstellt und aktualisiert
> * Die Implementierung wurde getestet

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine Vielzahl von Entity Framework-Szenarios auf fortgeschrittenem Niveau verarbeiten können.

> [!div class="nextstepaction"]
> [Weiter: Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core](advanced.md)