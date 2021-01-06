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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054215"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="c16a4-103">Tutorial: Implementieren von Vererbung: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="c16a4-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="c16a4-104">Im vorherigen Tutorial haben Sie Parallelitätsausnahmen behandelt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="c16a4-105">In diesem Tutorial erfahren Sie, wie Sie die Vererbung in das Datenmodell implementieren können.</span><span class="sxs-lookup"><span data-stu-id="c16a4-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="c16a4-106">Bei objektorientierter Programmierung können Sie mithilfe von Vererbung die Wiederverwendung von Code vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="c16a4-107">In diesem Tutorial ändern Sie die Klassen `Instructor` und `Student` so, dass sie von einer `Person`-Basisklasse abgeleitet werden, die Eigenschaften wie `LastName` enthält. Diese Eigenschaften sind für Dozenten und Studenten gängig.</span><span class="sxs-lookup"><span data-stu-id="c16a4-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="c16a4-108">Sie fügen keine Webseiten hinzu oder ändern diese, aber Sie werden Teile des Codes ändern. Diese Änderungen werden automatisch in der Datenbank widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="c16a4-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c16a4-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c16a4-110">Zuordnen von Vererbung zu Datenbank</span><span class="sxs-lookup"><span data-stu-id="c16a4-110">Map inheritance to database</span></span>
> * <span data-ttu-id="c16a4-111">Erstellen der Klasse „Person“</span><span class="sxs-lookup"><span data-stu-id="c16a4-111">Create the Person class</span></span>
> * <span data-ttu-id="c16a4-112">Aktualisieren von „Instructor“ und „Student“</span><span class="sxs-lookup"><span data-stu-id="c16a4-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="c16a4-113">Hinzuzufügen von „Person“ zum Modell</span><span class="sxs-lookup"><span data-stu-id="c16a4-113">Add Person to the model</span></span>
> * <span data-ttu-id="c16a4-114">Erstellen und Aktualisieren von Migrationen</span><span class="sxs-lookup"><span data-stu-id="c16a4-114">Create and update migrations</span></span>
> * <span data-ttu-id="c16a4-115">Testen der Implementierung</span><span class="sxs-lookup"><span data-stu-id="c16a4-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c16a4-116">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c16a4-116">Prerequisites</span></span>

* [<span data-ttu-id="c16a4-117">Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="c16a4-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="c16a4-118">Zuordnen von Vererbung zu Datenbank</span><span class="sxs-lookup"><span data-stu-id="c16a4-118">Map inheritance to database</span></span>

<span data-ttu-id="c16a4-119">Die Klassen `Instructor` und `Student` im Datenmodell „Schule“ weisen mehrere identische Eigenschaften auf:</span><span class="sxs-lookup"><span data-stu-id="c16a4-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Die Klassen „Student“ und „Instructor“](inheritance/_static/no-inheritance.png)

<span data-ttu-id="c16a4-121">Angenommen, Sie möchten den redundanten Code für die Eigenschaften löschen, die von den Entitäten `Instructor` und `Student` gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="c16a4-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="c16a4-122">Oder Sie möchten einen Dienst schreiben, mit dem Namen formatiert werden können, ohne dass es eine Rolle spielt, ob der Name von einem Dozenten oder von einem Studenten stammt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="c16a4-123">Dann können Sie eine `Person`-Basisklasse erstellen, die nur diese gemeinsam genutzten Eigenschaften enthält. Anschließend können Sie einstellen, dass die Klassen `Instructor` und `Student` von dieser Basisklasse erben sollen, wie in der folgenden Abbildung dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c16a4-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![Die Klassen „Student“ und „Instructor“ abgeleitet von der Klasse „Person“](inheritance/_static/inheritance.png)

<span data-ttu-id="c16a4-125">Es gibt mehrere Möglichkeiten, wie diese Vererbungsstruktur in der Datenbank dargestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c16a4-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="c16a4-126">Sie können z.B. über die Tabelle „Person“ verfügen, die Informationen zu Studenten und Dozenten in einer einzigen Tabelle enthält.</span><span class="sxs-lookup"><span data-stu-id="c16a4-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="c16a4-127">Einige Spalten können dann nur für Dozenten gelten (HireDate), einige nur für Studenten (EnrollmentDate) und einige für beide (LastName, FirstName).</span><span class="sxs-lookup"><span data-stu-id="c16a4-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="c16a4-128">Normalerweise sollten Sie über eine Unterscheidungsspalte verfügen, in der angegeben wird, welcher Typ in den jeweiligen Zeilen dargestellt wird.</span><span class="sxs-lookup"><span data-stu-id="c16a4-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="c16a4-129">So kann die Unterscheidungsspalte beispielsweise „Instructor“ für Dozenten und „Student“ für Studenten enthalten.</span><span class="sxs-lookup"><span data-stu-id="c16a4-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![Beispiel: Tabelle pro Hierarchie](inheritance/_static/tph.png)

<span data-ttu-id="c16a4-131">Dieses Muster, bei dem aus einer einzigen Datenbanktabelle eine Vererbungsstruktur für Entitäten generiert wird, wird als TPH-Vererbung (TPH = Table per Hierarchy, Tabelle pro Hierarchie) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c16a4-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="c16a4-132">Alternativ kann die Datenbank so gestaltet werden, dass sie mehr wie die Vererbungsstruktur aussieht.</span><span class="sxs-lookup"><span data-stu-id="c16a4-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="c16a4-133">Die Tabelle „Person“ könnte beispielsweise nur die Namensfelder aufweisen und über separate Tabellen mit den Namen „Instructor“ und „Student“ verfügen, in denen die Datumsfelder enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="c16a4-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="c16a4-134">Tabelle pro Typ (TPT) wird von EF Core 3.x nicht unterstützt, wurde aber trotzdem in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan) implementiert.</span><span class="sxs-lookup"><span data-stu-id="c16a4-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

!["Tabelle pro Typ"-Vererbung](inheritance/_static/tpt.png)

<span data-ttu-id="c16a4-136">Dieses Muster, bei dem für jede Entitätsklasse eine Datenbanktabelle erstellt wird, wird als TPT-Vererbung (TPT = Table per Type, Tabelle pro Typ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c16a4-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="c16a4-137">Eine weitere Möglichkeit besteht darin, individuellen Tabellen alle nicht abstrakten Typen zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="c16a4-138">Alle Eigenschaften einer Klasse, einschließlich der geerbten Eigenschaften, werden Spalten der entsprechenden Tabelle zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="c16a4-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="c16a4-139">Dieses Muster wird als TPC-Vererbung (TPC = Table per Concrete, Tabelle pro konkretem Typ) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c16a4-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="c16a4-140">Wenn Sie die TPC-Vererbung für die Klassen „Person“, „Student“ und „Instructor“ wie oben beschrieben implementieren, würden die Tabellen „Student“ und „Instructor“ nach der Implementierung unverändert aussehen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="c16a4-141">Bei den TPC- und TPH-Vererbungsmustern wird in der Regel eine bessere Leistung erzielt als bei den TPT-Vererbungsmustern, da TPT-Muster zu komplexen Joinabfrage führen können.</span><span class="sxs-lookup"><span data-stu-id="c16a4-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="c16a4-142">Dieses Tutorial veranschaulicht die Implementierung der TPH-Vererbung.</span><span class="sxs-lookup"><span data-stu-id="c16a4-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="c16a4-143">TPH ist das einzige Vererbungsmuster, das von Entity Framework Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c16a4-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="c16a4-144">Dabei erstellen Sie eine `Person`-Klasse, ändern die Klassen `Instructor` und `Student`, die von `Person` abgeleitet werden sollen, fügen die neue Klasse zum `DbContext` hinzu und erstellen eine Migration.</span><span class="sxs-lookup"><span data-stu-id="c16a4-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="c16a4-145">Sie sollten eine Kopie des Projekts speichern, bevor Sie folgende Änderungen vornehmen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="c16a4-146">Wenn Probleme auftreten und Sie von vorne beginnen müssen, ist es leichter, vom gespeicherten Projekt aus zu starten, statt für dieses Tutorial ausgeführte Schritte rückgängig zu machen oder zum Anfang der Reihe zurückkehren zu müssen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="c16a4-147">Erstellen der Klasse „Person“</span><span class="sxs-lookup"><span data-stu-id="c16a4-147">Create the Person class</span></span>

<span data-ttu-id="c16a4-148">Erstellen Sie im Ordner „Models“ (Modelle) die Datei „Person.cs“ und ersetzen Sie den Vorlagencode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c16a4-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="c16a4-149">Aktualisieren von „Instructor“ und „Student“</span><span class="sxs-lookup"><span data-stu-id="c16a4-149">Update Instructor and Student</span></span>

<span data-ttu-id="c16a4-150">Leiten Sie in der Datei *Instructor.cs* die Klasse „Instructor“ von der Klasse „Person“ ab, und entfernen Sie Schlüssel- und Namensfelder.</span><span class="sxs-lookup"><span data-stu-id="c16a4-150">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="c16a4-151">Der Code sieht aus wie im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c16a4-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="c16a4-152">Nehmen Sie an der Datei *Student.cs* die gleichen Änderungen vor.</span><span class="sxs-lookup"><span data-stu-id="c16a4-152">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="c16a4-153">Hinzuzufügen von „Person“ zum Modell</span><span class="sxs-lookup"><span data-stu-id="c16a4-153">Add Person to the model</span></span>

<span data-ttu-id="c16a4-154">Fügen Sie den Entitätstyp „Person“ zur Datei *SchoolContext.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c16a4-154">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="c16a4-155">Die neuen Zeilen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c16a4-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="c16a4-156">Das ist alles, was Entity Framework für die Konfiguration der „Tabelle pro Hierarchie“-Vererbung benötigt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="c16a4-157">Sie werden feststellen, dass die Datenbank nach ihrer Aktualisierung statt der Tabellen „Student“ und „Instructor“ eine Person-Tabelle enthält.</span><span class="sxs-lookup"><span data-stu-id="c16a4-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="c16a4-158">Erstellen und Aktualisieren von Migrationen</span><span class="sxs-lookup"><span data-stu-id="c16a4-158">Create and update migrations</span></span>

<span data-ttu-id="c16a4-159">Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-159">Save your changes and build the project.</span></span> <span data-ttu-id="c16a4-160">Öffnen Sie anschließend das Befehlsfenster im Projektordner, und geben Sie folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="c16a4-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="c16a4-161">Führen Sie noch nicht den Befehl `database update` aus.</span><span class="sxs-lookup"><span data-stu-id="c16a4-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="c16a4-162">Die Ausführung dieses Befehls führt zu einem Datenverlust, da er die Tabelle „Instructor“ löscht und die Tabelle „Student“ in „Person“ umbenennt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="c16a4-163">Sie müssen benutzerdefinierten Code angeben, damit vorhandene Daten erhalten bleiben.</span><span class="sxs-lookup"><span data-stu-id="c16a4-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="c16a4-164">Öffnen Sie *Migrations/\<timestamp>_Inheritance.cs*, und ersetzen Sie die Methode `Up` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c16a4-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="c16a4-165">Dieser Code übernimmt folgende Tasks für Datenbankaktualisierungen:</span><span class="sxs-lookup"><span data-stu-id="c16a4-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="c16a4-166">Er entfernt Fremdschlüsseleinschränkungen und -indizes, die auf die Tabelle „Student“ verweisen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="c16a4-167">Er benennt die Tabelle „Instructor“ in „Person“ um und nimmt die Änderungen vor, die für das Speichern von Studentendaten erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="c16a4-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="c16a4-168">Er fügt das EnrollmentDate für Studenten hinzu, bei dem NULL-Werte zugelassen sind.</span><span class="sxs-lookup"><span data-stu-id="c16a4-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="c16a4-169">Er fügt eine Unterscheidungsspalte hinzu, um anzugeben, ob eine Zeile für einen Studenten oder für einen Dozenten bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="c16a4-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="c16a4-170">Er legt fest, dass bei HireDate NULL-Werte zugelassen sind, da die Zeilen für Studenten keine Einstellungsdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="c16a4-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="c16a4-171">Er fügt ein temporäres Feld hinzu, über das Fremdschlüssel aktualisiert werden sollen, die auf Studenten verweisen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="c16a4-172">Wenn Sie Studenten in die Tabelle „Person“ kopieren, erhalten diese neue Primärschlüsselwerte.</span><span class="sxs-lookup"><span data-stu-id="c16a4-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="c16a4-173">Kopiert Daten aus der Tabelle „Student“ in die Tabelle „Person“.</span><span class="sxs-lookup"><span data-stu-id="c16a4-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="c16a4-174">Dadurch werden Studenten neue Primärschlüsselwerte zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="c16a4-175">Er legt Fremdschlüsselwerte fest, die auf Studenten verweisen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="c16a4-176">Er erstellt Fremdschlüsseleinschränkungen und -indizes neu, die dann auf die Tabelle „Person“ verweisen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="c16a4-177">(Hätten Sie als Primärschlüsseltyp statt einem Integer die grafische Benutzeroberfläche verwendet haben, hätten die Primärschlüsselwerte für Studenten nicht geändert werden müssen, und mehrere dieser Schritte hätten ausgelassen werden können.)</span><span class="sxs-lookup"><span data-stu-id="c16a4-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="c16a4-178">Führen Sie den Befehl `database update` aus:</span><span class="sxs-lookup"><span data-stu-id="c16a4-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="c16a4-179">(In einem Produktionssystem würden Sie entsprechende Änderungen an der Methode `Down` vornehmen, falls Sie diese jemals verwenden müssten, um zur vorherigen Datenbankversion zurückzukehren.</span><span class="sxs-lookup"><span data-stu-id="c16a4-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="c16a4-180">In diesem Tutorial wird die Methode `Down` nicht verwendet.)</span><span class="sxs-lookup"><span data-stu-id="c16a4-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="c16a4-181">Es ist möglich, dass andere Fehler auftreten, wenn Schemaänderungen in einer Datenbank durchgeführt werden, die vorhandene Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="c16a4-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="c16a4-182">Wenn Migrationsfehler auftreten, die Sie nicht beheben können, können Sie entweder den Datenbanknamen in der Verbindungszeichenfolge ändern oder die Datenbank löschen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="c16a4-183">In einer neuen Datenbank gibt es keine zu migrierenden Daten, und der Befehl „update-database“ wird wahrscheinlich ohne Fehler ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c16a4-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="c16a4-184">Verwenden Sie zum Löschen der Datenbank SSOX, oder führen Sie den CLI-Befehl `database drop` aus.</span><span class="sxs-lookup"><span data-stu-id="c16a4-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="c16a4-185">Testen der Implementierung</span><span class="sxs-lookup"><span data-stu-id="c16a4-185">Test the implementation</span></span>

<span data-ttu-id="c16a4-186">Führen Sie die App aus, und testen Sie verschiedene Seiten.</span><span class="sxs-lookup"><span data-stu-id="c16a4-186">Run the app and try various pages.</span></span> <span data-ttu-id="c16a4-187">Alles funktioniert genauso wie vorher.</span><span class="sxs-lookup"><span data-stu-id="c16a4-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="c16a4-188">Wenn Sie im **SQL Server-Objekt-Explorer** **Data Connections/SchoolContext** und anschließend **Tabellen** erweitern, können Sie sehen, dass die Tabellen „Student“ und „Instructor“ durch eine Person-Tabelle ersetzt wurden.</span><span class="sxs-lookup"><span data-stu-id="c16a4-188">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="c16a4-189">Wenn Sie den Person-Tabellen-Designer öffnen, sehen Sie, dass er alle Spalten aus den Tabellen „Student“ und „Instructor“ enthält.</span><span class="sxs-lookup"><span data-stu-id="c16a4-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![Person-Tabelle im SSOX](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="c16a4-191">Klicken Sie mit der rechten Maustaste auf die Tabelle „Person“, und klicken Sie anschließend auf **Tabellendaten anzeigen**, um die Unterscheidungsspalte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c16a4-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![Person-Tabelle im SSOX: Tabellendaten](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="c16a4-193">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="c16a4-193">Get the code</span></span>

[<span data-ttu-id="c16a4-194">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="c16a4-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="c16a4-195">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c16a4-195">Additional resources</span></span>

<span data-ttu-id="c16a4-196">Weitere Informationen zur Vererbung in Entity Framework Core finden Sie unter [Vererbung](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="c16a4-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c16a4-197">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c16a4-197">Next steps</span></span>

<span data-ttu-id="c16a4-198">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="c16a4-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c16a4-199">Vererbung wurde der Datenbank zugeordnet</span><span class="sxs-lookup"><span data-stu-id="c16a4-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="c16a4-200">Klasse „Person“ wurde erstellt</span><span class="sxs-lookup"><span data-stu-id="c16a4-200">Created the Person class</span></span>
> * <span data-ttu-id="c16a4-201">„Instructor“ und „Student“ wurden aktualisiert</span><span class="sxs-lookup"><span data-stu-id="c16a4-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="c16a4-202">„Person“ wurde dem Modell hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="c16a4-202">Added Person to the model</span></span>
> * <span data-ttu-id="c16a4-203">Migrationen wurden erstellt und aktualisiert</span><span class="sxs-lookup"><span data-stu-id="c16a4-203">Created and update migrations</span></span>
> * <span data-ttu-id="c16a4-204">Die Implementierung wurde getestet</span><span class="sxs-lookup"><span data-stu-id="c16a4-204">Tested the implementation</span></span>

<span data-ttu-id="c16a4-205">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine Vielzahl von Entity Framework-Szenarios auf fortgeschrittenem Niveau verarbeiten können.</span><span class="sxs-lookup"><span data-stu-id="c16a4-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="c16a4-206">Weiter: Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="c16a4-206">Next: Advanced topics</span></span>](advanced.md)