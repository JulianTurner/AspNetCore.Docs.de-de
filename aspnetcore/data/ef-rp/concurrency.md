---
title: 'Teil 8: Razor Pages mit EF Core in ASP.NET Core – Parallelität'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053994"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="a41cc-103">Teil 8: Razor Pages mit EF Core in ASP.NET Core – Parallelität</span><span class="sxs-lookup"><span data-stu-id="a41cc-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="a41cc-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) und [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="a41cc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a41cc-105">Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="a41cc-106">Nebenläufigkeitskonflikte</span><span class="sxs-lookup"><span data-stu-id="a41cc-106">Concurrency conflicts</span></span>

<span data-ttu-id="a41cc-107">Ein Nebenläufigkeitskonflikt tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="a41cc-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="a41cc-108">Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="a41cc-109">Ein anderer Benutzer aktualisiert dieselbe Entität, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="a41cc-110">Wenn Parallelitätserkennung nicht aktiviert ist, überschreibt die letzte Aktualisierung der Datenbank die Änderungen des anderen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="a41cc-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="a41cc-111">Wenn dieses Risiko akzeptabel ist, können die Kosten für die Programmierung für Parallelitätserkennung den Vorteil überwiegen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="a41cc-112">Pessimistische Parallelität (Sperren)</span><span class="sxs-lookup"><span data-stu-id="a41cc-112">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="a41cc-113">Eine Möglichkeit, Parallelitätskonflikte zu vermeiden, ist die Verwendung von Datenbanksperren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="a41cc-114">Man bezeichnet dies als pessimistische Parallelität.</span><span class="sxs-lookup"><span data-stu-id="a41cc-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="a41cc-115">Bevor die App eine Datenbankzeile liest, die aktualisiert werden soll, wird eine Sperre angefordert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="a41cc-116">Nachdem eine Zeile für den Aktualisierungszugriff gesperrt wurde, dürfen keine anderen Benutzer die Zeile sperren, bis die erste Sperre freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="a41cc-117">Das Verwalten von Sperren hat Nachteile.</span><span class="sxs-lookup"><span data-stu-id="a41cc-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="a41cc-118">Die Programmierung kann komplex sein und Leistungsprobleme verursachen, wenn sich die Anzahl der Benutzer erhöht.</span><span class="sxs-lookup"><span data-stu-id="a41cc-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="a41cc-119">Entity Framework Core enthält keine integrierte Unterstützung, und in diesem Tutorial wird nicht gezeigt, wie die Implementierung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-119">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="a41cc-120">Optimistische Nebenläufigkeit</span><span class="sxs-lookup"><span data-stu-id="a41cc-120">Optimistic concurrency</span></span>

<span data-ttu-id="a41cc-121">Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten.</span><span class="sxs-lookup"><span data-stu-id="a41cc-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="a41cc-122">Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.</span><span class="sxs-lookup"><span data-stu-id="a41cc-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget30.png)

<span data-ttu-id="a41cc-124">Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.</span><span class="sxs-lookup"><span data-stu-id="a41cc-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Ändern des Startdatums in 2013](concurrency/_static/change-date30.png)

<span data-ttu-id="a41cc-126">Benutzer1 klickt zuerst auf **Speichern** und sieht, dass die Änderung wirksam wird, da der Browser die Indexseite mit 0 (null) als Budgetbetrag anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="a41cc-127">Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="a41cc-128">Was anschließend geschieht, ist abhängig davon, wie Sie Parallelitätskonflikte handhaben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="a41cc-129">Sie können nachverfolgen, welche Eigenschaft ein Benutzer geändert hat und nur die entsprechenden Spalten in der Datenbank aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-129">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="a41cc-130">In diesem Szenario sollten keine Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="a41cc-131">Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="a41cc-132">Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2.</span><span class="sxs-lookup"><span data-stu-id="a41cc-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="a41cc-133">Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können.</span><span class="sxs-lookup"><span data-stu-id="a41cc-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="a41cc-134">Dieser Ansatz bietet einige Nachteile:</span><span class="sxs-lookup"><span data-stu-id="a41cc-134">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="a41cc-135">Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="a41cc-136">Ist in einer Web-App in der Regel nicht praktisch.</span><span class="sxs-lookup"><span data-stu-id="a41cc-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="a41cc-137">Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="a41cc-138">Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="a41cc-139">Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="a41cc-140">Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.</span><span class="sxs-lookup"><span data-stu-id="a41cc-140">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="a41cc-141">Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="a41cc-142">Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="a41cc-143">(Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-143">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="a41cc-144">Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-144">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="a41cc-145">In der Regel würde die App:</span><span class="sxs-lookup"><span data-stu-id="a41cc-145">Typically, the app would:</span></span>

  * <span data-ttu-id="a41cc-146">Eine Fehlermeldung anzeigen</span><span class="sxs-lookup"><span data-stu-id="a41cc-146">Display an error message.</span></span>
  * <span data-ttu-id="a41cc-147">Den aktuellen Datenstatus anzeigen</span><span class="sxs-lookup"><span data-stu-id="a41cc-147">Show the current state of the data.</span></span>
  * <span data-ttu-id="a41cc-148">Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden</span><span class="sxs-lookup"><span data-stu-id="a41cc-148">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="a41cc-149">Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-149">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="a41cc-150">(Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt).</span><span class="sxs-lookup"><span data-stu-id="a41cc-150">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="a41cc-151">Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-151">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="a41cc-152">Konflikterkennung in EF Core</span><span class="sxs-lookup"><span data-stu-id="a41cc-152">Conflict detection in EF Core</span></span>

<span data-ttu-id="a41cc-153">EF Core löst `DbConcurrencyException`-Ausnahmen aus, wenn Konflikte erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-153">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="a41cc-154">Das Datenmodell muss konfiguriert werden, um die Konflikterkennung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-154">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="a41cc-155">Optionen für das Aktivieren der Konflikterkennung schließen Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="a41cc-155">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="a41cc-156">Konfigurieren Sie EF Core so, dass die ursprünglichen Werte von Spalten, die als [Parallelitätstoken](/ef/core/modeling/concurrency) in der Where-Klausel der Update- und Delete-Befehle enthalten sind, einbezogen werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-156">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="a41cc-157">Wenn `SaveChanges` aufgerufen wird, sucht die Where-Klausel nach den ursprünglichen Werten von Eigenschaften, die als Anmerkung mit dem <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute>-Attribut versehen wurden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-157">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="a41cc-158">Die Update-Anweisung findet keine zu aktualisierende Zeile, wenn sich eine der Parallelitätstokeneigenschaften geändert hat, seit die Zeile zum ersten Mal gelesen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-158">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="a41cc-159">EF Core interpretiert dies als einen Parallelitätskonflikt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-159">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="a41cc-160">Bei Datenbanktabellen mit vielen Spalten kann dieser Ansatz zu sehr großen Where-Klauseln führen, was wiederum dazu führen kann, dass eine große Anzahl von Zuständen erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a41cc-160">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="a41cc-161">Deshalb wird dieser Ansatz in der Regel nicht empfohlen, und ist nicht die Methode, die in diesem Tutorial verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-161">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="a41cc-162">Fügen Sie eine Änderungsverfolgungsspalte in die Datenbanktabelle ein, die verwendet werden kann, um zu bestimmen, wenn eine Änderung an einer Zeile vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-162">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="a41cc-163">In einer SQL Server-Datenbank ist `rowversion` der Datentyp der Änderungsverfolgungsspalte.</span><span class="sxs-lookup"><span data-stu-id="a41cc-163">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="a41cc-164">Der Wert `rowversion` ist eine sequenzielle Zahl, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-164">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a41cc-165">In einem Update- oder Delete-Befehl enthält die Where-Klausel den ursprünglichen Wert der Änderungsverfolgungsspalte (die ursprüngliche Zeilenversionsnummer).</span><span class="sxs-lookup"><span data-stu-id="a41cc-165">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="a41cc-166">Wenn die zu aktualisierende Zeile von einem anderen Benutzer geändert wurde, unterscheidet sich der Wert in der Spalte `rowversion` vom ursprünglichen Wert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-166">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="a41cc-167">In diesem Fall kann die Update- oder Delete-Anweisung die zu aktualisierende Zeile aufgrund der Where-Klausel nicht finden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-167">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="a41cc-168">EF Core löst eine Parallelitätsausnahme aus, wenn keine Zeilen von einem Update- oder Delete-Befehl betroffen sind.</span><span class="sxs-lookup"><span data-stu-id="a41cc-168">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="a41cc-169">Fügen Sie eine Nachverfolgungseigenschaft hinzu</span><span class="sxs-lookup"><span data-stu-id="a41cc-169">Add a tracking property</span></span>

<span data-ttu-id="a41cc-170">Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="a41cc-170">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="a41cc-171">Das <xref:System.ComponentModel.DataAnnotations.TimestampAttribute>-Attribut identifiziert die Spalte als Parallelitätsnachverfolgungsspalte.</span><span class="sxs-lookup"><span data-stu-id="a41cc-171">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="a41cc-172">Die Fluent-API ist eine alternative Methode, um die Nachverfolgungseigenschaft anzugeben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-172">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="a41cc-173">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a41cc-173">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a41cc-174">Für eine SQL Server-Datenbank wird das `[Timestamp]`-Attribut für eine Entitätseigenschaft als Bytearray definiert:</span><span class="sxs-lookup"><span data-stu-id="a41cc-174">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="a41cc-175">Bewirkt, dass die Spalte in DELETE- und UPDATE WHERE-Klauseln eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-175">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="a41cc-176">Legt den Spaltentyp in der Datenbank auf [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) fest.</span><span class="sxs-lookup"><span data-stu-id="a41cc-176">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="a41cc-177">Die Datenbank generiert eine sequenzielle Zeilenversionsnummer, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-177">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a41cc-178">In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Zeilenversionswert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-178">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="a41cc-179">Wenn sich die aktualisierte Zeile seit ihrem Abruf geändert hat, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-179">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="a41cc-180">Der Wert der aktuellen Zeilenversion stimmt nicht mit dem abgerufenen Wert überein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-180">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="a41cc-181">Der `Update`- oder `Delete`-Befehl findet keine Zeile, da die `Where`-Klausel nach dem abgerufenen Zeilenversionswert sucht.</span><span class="sxs-lookup"><span data-stu-id="a41cc-181">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="a41cc-182">Es wird eine `DbUpdateConcurrencyException` ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-182">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="a41cc-183">Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:</span><span class="sxs-lookup"><span data-stu-id="a41cc-183">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="a41cc-184">Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-184">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="a41cc-185">Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-185">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="a41cc-186">Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:</span><span class="sxs-lookup"><span data-stu-id="a41cc-186">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="a41cc-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück.</span><span class="sxs-lookup"><span data-stu-id="a41cc-187">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="a41cc-188">Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-188">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a41cc-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a41cc-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a41cc-190">Für eine SQLite-Datenbank wird das `[Timestamp]`-Attribut für eine Entitätseigenschaft als Bytearray definiert:</span><span class="sxs-lookup"><span data-stu-id="a41cc-190">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="a41cc-191">Bewirkt, dass die Spalte in DELETE- und UPDATE WHERE-Klauseln eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-191">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="a41cc-192">Wird einem BLOB-Spaltentyp zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a41cc-192">Maps to a BLOB column type.</span></span>

<span data-ttu-id="a41cc-193">Datenbank löst eine Aktualisierung der RowVersion-Spalte mit einem neuen zufälligen Bytearray aus, wenn eine Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-193">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="a41cc-194">In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert der RowVersion-Spalte.</span><span class="sxs-lookup"><span data-stu-id="a41cc-194">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="a41cc-195">Wenn sich die aktualisierte Zeile seit ihrem Abruf geändert hat, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-195">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="a41cc-196">Der Wert der aktuellen Zeilenversion stimmt nicht mit dem abgerufenen Wert überein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-196">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="a41cc-197">Der `Update`- oder `Delete`-Befehl findet keine Zeile, da die `Where`-Klausel nach dem ursprünglichen Zeilenversionswert sucht.</span><span class="sxs-lookup"><span data-stu-id="a41cc-197">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="a41cc-198">Es wird eine `DbUpdateConcurrencyException` ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-198">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="a41cc-199">Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="a41cc-199">Update the database</span></span>

<span data-ttu-id="a41cc-200">Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-200">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="a41cc-201">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-201">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="a41cc-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a41cc-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a41cc-203">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="a41cc-203">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a41cc-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a41cc-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a41cc-205">Führen Sie die folgenden Befehle über ein Terminal aus:</span><span class="sxs-lookup"><span data-stu-id="a41cc-205">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="a41cc-206">Dieser Befehl:</span><span class="sxs-lookup"><span data-stu-id="a41cc-206">This command:</span></span>

* <span data-ttu-id="a41cc-207">Erstellt die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs*.</span><span class="sxs-lookup"><span data-stu-id="a41cc-207">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="a41cc-208">Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-208">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="a41cc-209">Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="a41cc-209">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="a41cc-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a41cc-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a41cc-211">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="a41cc-211">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="a41cc-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a41cc-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a41cc-213">Öffnen Sie die Datei `Migrations/<timestamp>_RowVersion.cs`, und fügen Sie den hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="a41cc-213">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="a41cc-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="a41cc-214">The preceding code:</span></span>

  * <span data-ttu-id="a41cc-215">Aktualisiert vorhandene Zeilen mit zufälligen Blobwerten.</span><span class="sxs-lookup"><span data-stu-id="a41cc-215">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="a41cc-216">Fügt Datenbanktrigger hinzu, die die RowVersion-Spalte auf einen zufälligen Blobwert festlegen, wenn eine Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-216">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="a41cc-217">Führen Sie die folgenden Befehle über ein Terminal aus:</span><span class="sxs-lookup"><span data-stu-id="a41cc-217">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="a41cc-218">Gerüstbau der Department-Seiten</span><span class="sxs-lookup"><span data-stu-id="a41cc-218">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a41cc-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a41cc-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a41cc-220">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-220">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="a41cc-221">Erstellen Sie einen Ordner *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="a41cc-221">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="a41cc-222">Verwenden Sie `Department` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="a41cc-222">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="a41cc-223">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-223">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a41cc-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a41cc-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a41cc-225">Erstellen Sie einen Ordner *Pages/Departments*.</span><span class="sxs-lookup"><span data-stu-id="a41cc-225">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="a41cc-226">Führen Sie den folgenden Befehl aus, um das Gerüst für Department-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-226">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="a41cc-227">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="a41cc-227">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="a41cc-228">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="a41cc-228">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="a41cc-229">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-229">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="a41cc-230">Aktualisieren der Index-Seite</span><span class="sxs-lookup"><span data-stu-id="a41cc-230">Update the Index page</span></span>

<span data-ttu-id="a41cc-231">Das Gerüstbautool hat eine `RowVersion`-Spalte für die Index-Seite erstellt, dieses Feld würde jedoch in einer Produktions-App nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-231">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="a41cc-232">In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis für die Funktionsweise der Parallelitätsverarbeitung bekommen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-232">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="a41cc-233">Das letzte Byte ist nicht zwingend eindeutig.</span><span class="sxs-lookup"><span data-stu-id="a41cc-233">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="a41cc-234">Aktualisieren Sie die Seite *Pages\Departments\Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="a41cc-234">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="a41cc-235">Ersetzen Sie „Index“ durch „Abteilungen“.</span><span class="sxs-lookup"><span data-stu-id="a41cc-235">Replace Index with Departments.</span></span>
* <span data-ttu-id="a41cc-236">Ändern Sie den Code, der `RowVersion` enthält, um nur das letzte Byte des Bytearrays anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-236">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="a41cc-237">Ersetzen Sie „FirstMidName“durch „FullName“.</span><span class="sxs-lookup"><span data-stu-id="a41cc-237">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="a41cc-238">Der folgende Code zeigt die aktualisierte Seite an:</span><span class="sxs-lookup"><span data-stu-id="a41cc-238">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="a41cc-239">Aktualisieren des Seitenbearbeitungsmodells</span><span class="sxs-lookup"><span data-stu-id="a41cc-239">Update the Edit page model</span></span>

<span data-ttu-id="a41cc-240">Aktualisieren Sie die Datei *Pages\Departments\Edit.cshtml.cs* mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-240">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="a41cc-241"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> wird mit dem `rowVersion`-Wert aus der Entität aktualisiert, als diese in der `OnGet`-Methode abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-241">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGet` method.</span></span> <span data-ttu-id="a41cc-242">EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-242">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="a41cc-243">Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-243">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="a41cc-244">Im hervorgehobenen Code oben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-244">In the preceding highlighted code:</span></span>

* <span data-ttu-id="a41cc-245">Ist der Wert in `Department.RowVersion` der Inhalt der Entität, als diese ursprünglich in der Get-Anforderung für die Edit-Seite abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-245">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="a41cc-246">Der Wert wird für die `OnPost`-Methode durch ein ausgeblendetes Feld auf der Razor-Seite bereitgestellt, das die zu bearbeitende Entität anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-246">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="a41cc-247">Der Wert des ausgeblendeten Felds wird vom Modellbinder in `Department.RowVersion` kopiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-247">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="a41cc-248">`OriginalValue` verwendet EF Core in der Where-Klausel.</span><span class="sxs-lookup"><span data-stu-id="a41cc-248">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="a41cc-249">Bevor die hervorgehobene Codezeile ausgeführt wird, enthält `OriginalValue` den Wert, der in der Datenbank enthalten war, als `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde. Dieser kann sich möglicherweise von dem auf der Edit-Seite angezeigten Wert unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-249">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="a41cc-250">Der hervorgehobene Code stellt sicher, dass EF Core den ursprünglichen `RowVersion`-Wert aus der angezeigten `Department`-Entität in der Where-Klausel der SQL UPDATE-Anweisung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a41cc-250">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="a41cc-251">Wenn ein Parallelitätsfehler auftritt, ruft der folgende hervorgehobene Code die Clientwerte (die Werte, die für diese Methode bereitgestellt werden) und die Datenbankwerte ab.</span><span class="sxs-lookup"><span data-stu-id="a41cc-251">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="a41cc-252">Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die für `OnPostAsync` bereitgestellt wurden:</span><span class="sxs-lookup"><span data-stu-id="a41cc-252">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="a41cc-253">Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-253">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="a41cc-254">Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="a41cc-254">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="a41cc-255">Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-255">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="a41cc-256">In der Razor-Seite hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="a41cc-256">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="a41cc-257">Aktualisieren der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="a41cc-257">Update the Edit page</span></span>

<span data-ttu-id="a41cc-258">Aktualisieren Sie die Datei *Pages/Departments/Edit.cshtml* mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-258">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="a41cc-259">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="a41cc-259">The preceding code:</span></span>

* <span data-ttu-id="a41cc-260">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-260">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a41cc-261">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="a41cc-261">Adds a hidden row version.</span></span> <span data-ttu-id="a41cc-262">`RowVersion` muss hinzugefügt werden, damit ein Postback-Ereignis den Wert bindet.</span><span class="sxs-lookup"><span data-stu-id="a41cc-262">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="a41cc-263">Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-263">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="a41cc-264">Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-264">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="a41cc-265">Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="a41cc-265">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="a41cc-266">Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:</span><span class="sxs-lookup"><span data-stu-id="a41cc-266">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="a41cc-267">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-267">Run the app and select Departments.</span></span>
* <span data-ttu-id="a41cc-268">Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-268">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a41cc-269">Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.</span><span class="sxs-lookup"><span data-stu-id="a41cc-269">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="a41cc-270">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-270">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a41cc-271">Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-271">Change the name in the first browser tab and click **Save**.</span></span>

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="a41cc-273">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-273">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a41cc-274">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-274">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a41cc-275">Ändern Sie ein anderes Feld in der zweiten Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="a41cc-275">Change a different field in the second browser tab.</span></span>

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="a41cc-277">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-277">Click **Save**.</span></span> <span data-ttu-id="a41cc-278">Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-278">You see error messages for all fields that don't match the database values:</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error30.png)

<span data-ttu-id="a41cc-280">In diesem Browserfenster sollte nicht das Namensfeld geändert werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-280">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="a41cc-281">Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-281">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="a41cc-282">Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-282">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="a41cc-283">Klicken Sie erneut auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-283">Click **Save** again.</span></span> <span data-ttu-id="a41cc-284">Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-284">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="a41cc-285">Die gespeicherten Werte werden auf der Indexseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-285">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="a41cc-286">Aktualisieren des Modells für die Seite „Löschen“</span><span class="sxs-lookup"><span data-stu-id="a41cc-286">Update the Delete page model</span></span>

<span data-ttu-id="a41cc-287">Aktualisieren Sie die Datei *Pages/Departments/Delete.cshtml.cs* mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-287">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="a41cc-288">Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-288">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="a41cc-289">Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-289">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="a41cc-290">Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-290">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="a41cc-291">Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-291">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="a41cc-292">Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-292">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="a41cc-293">Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-293">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="a41cc-294">Wird `OnGetAsync` mit `concurrencyError` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-294">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="a41cc-295">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="a41cc-295">Update the Delete page</span></span>

<span data-ttu-id="a41cc-296">Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-296">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="a41cc-297">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-297">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a41cc-298">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-298">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a41cc-299">Eine Fehlermeldung wird hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-299">Adds an error message.</span></span>
* <span data-ttu-id="a41cc-300">„FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-300">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="a41cc-301">`RowVersion` wird geändert, um das letzte Byte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-301">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="a41cc-302">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="a41cc-302">Adds a hidden row version.</span></span> <span data-ttu-id="a41cc-303">`RowVersion` muss hinzugefügt werden, damit ein Postback-Ereignis den Wert bindet.</span><span class="sxs-lookup"><span data-stu-id="a41cc-303">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="a41cc-304">Testen Sie auf Parallelitätskonflikte</span><span class="sxs-lookup"><span data-stu-id="a41cc-304">Test concurrency conflicts</span></span>

<span data-ttu-id="a41cc-305">Erstellen Sie einen Fachbereich zum Testen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-305">Create a test department.</span></span>

<span data-ttu-id="a41cc-306">Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:</span><span class="sxs-lookup"><span data-stu-id="a41cc-306">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="a41cc-307">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-307">Run the app and select Departments.</span></span>
* <span data-ttu-id="a41cc-308">Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-308">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a41cc-309">Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.</span><span class="sxs-lookup"><span data-stu-id="a41cc-309">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="a41cc-310">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-310">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a41cc-311">Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-311">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="a41cc-312">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-312">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a41cc-313">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-313">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a41cc-314">Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-314">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="a41cc-315">Durch Klicken auf **Löschen** wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-315">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a41cc-316">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a41cc-316">Additional resources</span></span>

* [<span data-ttu-id="a41cc-317">Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)</span><span class="sxs-lookup"><span data-stu-id="a41cc-317">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="a41cc-318">Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)</span><span class="sxs-lookup"><span data-stu-id="a41cc-318">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="a41cc-319">Debuggen von ASP.NET Core 2.x-Quellcode</span><span class="sxs-lookup"><span data-stu-id="a41cc-319">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="a41cc-320">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="a41cc-320">Next steps</span></span>

<span data-ttu-id="a41cc-321">Dies ist das letzte Tutorial der Serie.</span><span class="sxs-lookup"><span data-stu-id="a41cc-321">This is the last tutorial in the series.</span></span> <span data-ttu-id="a41cc-322">Weitere Themen werden in der [MVC-Version dieser Tutorialreihe](xref:data/ef-mvc/index) behandelt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-322">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a41cc-323">Vorheriges Tutorial</span><span class="sxs-lookup"><span data-stu-id="a41cc-323">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a41cc-324">Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-324">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="a41cc-325">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-325">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="a41cc-326">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a41cc-326">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="a41cc-327">Nebenläufigkeitskonflikte</span><span class="sxs-lookup"><span data-stu-id="a41cc-327">Concurrency conflicts</span></span>

<span data-ttu-id="a41cc-328">Ein Nebenläufigkeitskonflikt tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="a41cc-328">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="a41cc-329">Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-329">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="a41cc-330">Ein anderer Benutzer dieselbe Entität aktualisiert, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-330">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="a41cc-331">Wenn die Parallelitätserkennung nicht aktiviert ist, während gleichzeitige Updates ausgeführt werden, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-331">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="a41cc-332">Das letzte Update gilt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-332">The last update wins.</span></span> <span data-ttu-id="a41cc-333">Das bedeutet, dass die neuesten zu aktualisierenden Werte in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-333">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="a41cc-334">Das erste der aktuellen Updates geht verloren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-334">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="a41cc-335">Optimistische Nebenläufigkeit</span><span class="sxs-lookup"><span data-stu-id="a41cc-335">Optimistic concurrency</span></span>

<span data-ttu-id="a41cc-336">Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten.</span><span class="sxs-lookup"><span data-stu-id="a41cc-336">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="a41cc-337">Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.</span><span class="sxs-lookup"><span data-stu-id="a41cc-337">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

<span data-ttu-id="a41cc-339">Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.</span><span class="sxs-lookup"><span data-stu-id="a41cc-339">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

<span data-ttu-id="a41cc-341">Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn im Browser die Indexseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-341">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

<span data-ttu-id="a41cc-343">Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-343">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="a41cc-344">Was daraufhin geschieht ist abhängig davon, wie Sie Nebenläufigkeitskonflikte handhaben.</span><span class="sxs-lookup"><span data-stu-id="a41cc-344">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="a41cc-345">Die optimistische Nebenläufigkeit umfasst die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-345">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="a41cc-346">Sie können Nachverfolgen, welche Eigenschaft ein Benutzer geändert hat, und nur die entsprechenden Spalten in der Datenbank aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-346">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="a41cc-347">In diesem Szenario sollten keine Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-347">In the scenario, no data would be lost.</span></span> <span data-ttu-id="a41cc-348">Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-348">Different properties were updated by the two users.</span></span> <span data-ttu-id="a41cc-349">Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2.</span><span class="sxs-lookup"><span data-stu-id="a41cc-349">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="a41cc-350">Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können.</span><span class="sxs-lookup"><span data-stu-id="a41cc-350">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="a41cc-351">Dieser Ansatz:</span><span class="sxs-lookup"><span data-stu-id="a41cc-351">This approach:</span></span>
 
  * <span data-ttu-id="a41cc-352">Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-352">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="a41cc-353">Ist in einer Web-App in der Regel nicht praktisch.</span><span class="sxs-lookup"><span data-stu-id="a41cc-353">Is generally not practical in a web app.</span></span> <span data-ttu-id="a41cc-354">Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-354">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="a41cc-355">Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-355">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="a41cc-356">Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-356">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="a41cc-357">Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.</span><span class="sxs-lookup"><span data-stu-id="a41cc-357">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="a41cc-358">Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-358">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="a41cc-359">Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-359">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="a41cc-360">(Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-360">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="a41cc-361">Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-361">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="a41cc-362">In der Regel würde die App:</span><span class="sxs-lookup"><span data-stu-id="a41cc-362">Typically, the app would:</span></span>

  * <span data-ttu-id="a41cc-363">Eine Fehlermeldung anzeigen</span><span class="sxs-lookup"><span data-stu-id="a41cc-363">Display an error message.</span></span>
  * <span data-ttu-id="a41cc-364">Den aktuellen Datenstatus anzeigen</span><span class="sxs-lookup"><span data-stu-id="a41cc-364">Show the current state of the data.</span></span>
  * <span data-ttu-id="a41cc-365">Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden</span><span class="sxs-lookup"><span data-stu-id="a41cc-365">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="a41cc-366">Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-366">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="a41cc-367">(Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt).</span><span class="sxs-lookup"><span data-stu-id="a41cc-367">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="a41cc-368">Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-368">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="a41cc-369">Behandlung von Parallelität</span><span class="sxs-lookup"><span data-stu-id="a41cc-369">Handling concurrency</span></span> 

<span data-ttu-id="a41cc-370">Wenn eine Eigenschaft als ein [Parallelitätstoken](/ef/core/modeling/concurrency) konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="a41cc-370">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="a41cc-371">Stellt EF Core sicher, dass die Eigenschaft nicht geändert wurde, nachdem sie abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-371">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="a41cc-372">Die Überprüfung findet statt, wenn [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) oder [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-372">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="a41cc-373">Wenn die Eigenschaft geändert wurde, nachdem sie abgerufen wurde, wird eine [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-373">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="a41cc-374">Das Datenbank- und Datenmodell müssen konfiguriert sein, um das Auslösen von `DbUpdateConcurrencyException` zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-374">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="a41cc-375">Erkennen von Nebenläufigkeitskonflikten mit Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="a41cc-375">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="a41cc-376">Nebenläufigkeitskonflikte können auf der Eigenschaftenebene über das [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute)-Attribut erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-376">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="a41cc-377">Das Attribut kann auf mehrere Eigenschaften für das Modell angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-377">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="a41cc-378">Weitere Informationen finden Sie unter [Datenanmerkungen-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span><span class="sxs-lookup"><span data-stu-id="a41cc-378">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="a41cc-379">Das Attribut `[ConcurrencyCheck]` wird in diesem Tutorial nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="a41cc-379">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="a41cc-380">Erkennen von Nebenläufigkeitskonflikten mit einer Zeile</span><span class="sxs-lookup"><span data-stu-id="a41cc-380">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="a41cc-381">Um Nebenläufigkeitskonflikte zu erkennen, wird dem Modell eine [Rowversion](/sql/t-sql/data-types/rowversion-transact-sql)-Nachverfolgungsspalte (Zeilenversion) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-381">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="a41cc-382">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="a41cc-382">`rowversion` :</span></span>

* <span data-ttu-id="a41cc-383">Ist SQL Server-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="a41cc-383">Is SQL Server specific.</span></span> <span data-ttu-id="a41cc-384">Andere Datenbanken enthalten möglicherweise keine ähnlichen Features.</span><span class="sxs-lookup"><span data-stu-id="a41cc-384">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="a41cc-385">Wird verwendet, um zu bestimmen, dass eine Entität, seit dem Abruf aus der Datenbank, nicht geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-385">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="a41cc-386">Die Datenbank generiert eine sequenzielle Anzahl von `rowversion`, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-386">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="a41cc-387">In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert von `rowversion`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-387">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="a41cc-388">Wenn sich die aktualisierte Zeile geändert hat, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-388">If the row being updated has changed:</span></span>

* <span data-ttu-id="a41cc-389">`rowversion` entspricht nicht dem abgerufenen Wert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-389">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="a41cc-390">Die Befehle `Update` oder `Delete` finden keine Zeile, da die `Where`-Klausel die abgerufene `rowversion` enthält.</span><span class="sxs-lookup"><span data-stu-id="a41cc-390">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="a41cc-391">Es wird eine `DbUpdateConcurrencyException` ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-391">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="a41cc-392">In EF Core wird eine Parallelitätsausnahme ausgelöst, wenn keine Zeilen durch einen `Update`- oder `Delete`-Befehl aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-392">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="a41cc-393">Hinzufügen einer Nachverfolgungseigenschaft zur Entität „Department“</span><span class="sxs-lookup"><span data-stu-id="a41cc-393">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="a41cc-394">Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="a41cc-394">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="a41cc-395">Das [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute)-Attribut gibt an, dass diese Spalte in der `Where`-Klausel der Befehle `Update` und `Delete` enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="a41cc-395">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="a41cc-396">Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor dieser durch SQL-`rowversion` ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-396">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="a41cc-397">Die Fluent-API kann auch die Nachverfolgungseigenschaft angeben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-397">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="a41cc-398">Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:</span><span class="sxs-lookup"><span data-stu-id="a41cc-398">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="a41cc-399">Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-399">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="a41cc-400">Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-400">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="a41cc-401">Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:</span><span class="sxs-lookup"><span data-stu-id="a41cc-401">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="a41cc-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück.</span><span class="sxs-lookup"><span data-stu-id="a41cc-402">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="a41cc-403">Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-403">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="a41cc-404">Sie können das von EF Core generierte T-SQL im Ausgabefenster von Visual Studio sehen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-404">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="a41cc-405">Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="a41cc-405">Update the DB</span></span>

<span data-ttu-id="a41cc-406">Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-406">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="a41cc-407">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-407">Build the project.</span></span> <span data-ttu-id="a41cc-408">Geben Sie Folgendes in ein Befehlsfenster ein:</span><span class="sxs-lookup"><span data-stu-id="a41cc-408">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="a41cc-409">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-409">The preceding commands:</span></span>

* <span data-ttu-id="a41cc-410">Die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs* wird hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-410">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="a41cc-411">Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-411">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="a41cc-412">Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="a41cc-412">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="a41cc-413">Migrationen werden durchgeführt, um die Datenbank zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="a41cc-413">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="a41cc-414">Erstellen des Gerüsts für das Abteilungsmodell</span><span class="sxs-lookup"><span data-stu-id="a41cc-414">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a41cc-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a41cc-415">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a41cc-416">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-student-pages) aus, und verwenden Sie `Department` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="a41cc-416">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a41cc-417">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a41cc-417">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="a41cc-418">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="a41cc-418">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="a41cc-419">Der vorherige Befehl erstellt ein Gerüst für das `Department`-Modell.</span><span class="sxs-lookup"><span data-stu-id="a41cc-419">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="a41cc-420">Öffnen Sie das Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a41cc-420">Open the project in Visual Studio.</span></span>

<span data-ttu-id="a41cc-421">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-421">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="a41cc-422">Aktualisieren der Indexseite für Abteilungen</span><span class="sxs-lookup"><span data-stu-id="a41cc-422">Update the Departments Index page</span></span>

<span data-ttu-id="a41cc-423">Die Gerüstbauengine hat eine `RowVersion`-Spalte auf der Indexseite erstellt, jedoch sollte dieses Feld nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-423">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="a41cc-424">In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis über die Parallelität erlangen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-424">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="a41cc-425">Das letzte Byte ist nicht zwingend eindeutig.</span><span class="sxs-lookup"><span data-stu-id="a41cc-425">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="a41cc-426">Eine echte App würde `RowVersion` oder das letzte Byte von `RowVersion` nicht anzeigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-426">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="a41cc-427">Aktualisieren der Indexseite:</span><span class="sxs-lookup"><span data-stu-id="a41cc-427">Update the Index page:</span></span>

* <span data-ttu-id="a41cc-428">Ersetzen Sie „Index“ durch „Abteilungen“.</span><span class="sxs-lookup"><span data-stu-id="a41cc-428">Replace Index with Departments.</span></span>
* <span data-ttu-id="a41cc-429">Ersetzen Sie das Markup mit `RowVersion` durch das letzten Byte von `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-429">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="a41cc-430">Ersetzen Sie „FirstMidName“durch „FullName“.</span><span class="sxs-lookup"><span data-stu-id="a41cc-430">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="a41cc-431">Das folgende Markup zeigt die aktualisierte Seite an:</span><span class="sxs-lookup"><span data-stu-id="a41cc-431">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="a41cc-432">Aktualisieren des Seitenbearbeitungsmodells</span><span class="sxs-lookup"><span data-stu-id="a41cc-432">Update the Edit page model</span></span>

<span data-ttu-id="a41cc-433">Aktualisieren Sie die Datei *Pages\Departments\Edit.cshtml.cs* mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-433">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="a41cc-434">Um ein Nebenläufigkeitsproblem zu erkennen, wird die [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)-Eigenschaft mit dem `rowVersion`-Wert aus der Entität aktualisiert, aus der dieser abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-434">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="a41cc-435">EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-435">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="a41cc-436">Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-436">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="a41cc-437">Im obenstehenden Code wird der Wert `Department.RowVersion` zurückgegeben, sobald die Entität abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-437">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="a41cc-438">`OriginalValue` ist der Wert in der Datenbank, wenn `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-438">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="a41cc-439">Der folgende Code ruft die Clientwerte (die für diese Methode bereitgestellten Werte) und die Datenbankwerte ab:</span><span class="sxs-lookup"><span data-stu-id="a41cc-439">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="a41cc-440">Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die auf `OnPostAsync` bereitgestellt wurden:</span><span class="sxs-lookup"><span data-stu-id="a41cc-440">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="a41cc-441">Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-441">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="a41cc-442">Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="a41cc-442">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="a41cc-443">Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-443">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="a41cc-444">In der Razor-Seite hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="a41cc-444">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="a41cc-445">Aktualisieren der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="a41cc-445">Update the Edit page</span></span>

<span data-ttu-id="a41cc-446">Aktualisieren Sie *Pages/Courses/Edit.cshtml* mithilfe des folgenden Markups:</span><span class="sxs-lookup"><span data-stu-id="a41cc-446">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="a41cc-447">Das obenstehende Markup:</span><span class="sxs-lookup"><span data-stu-id="a41cc-447">The preceding markup:</span></span>

* <span data-ttu-id="a41cc-448">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-448">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a41cc-449">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="a41cc-449">Adds a hidden row version.</span></span> <span data-ttu-id="a41cc-450">`RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="a41cc-450">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="a41cc-451">Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-451">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="a41cc-452">Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-452">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="a41cc-453">Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="a41cc-453">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="a41cc-454">Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:</span><span class="sxs-lookup"><span data-stu-id="a41cc-454">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="a41cc-455">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-455">Run the app and select Departments.</span></span>
* <span data-ttu-id="a41cc-456">Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-456">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a41cc-457">Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.</span><span class="sxs-lookup"><span data-stu-id="a41cc-457">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="a41cc-458">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-458">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a41cc-459">Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-459">Change the name in the first browser tab and click **Save**.</span></span>

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="a41cc-461">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-461">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a41cc-462">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-462">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a41cc-463">Ändern Sie ein anderes Feld in der zweiten Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="a41cc-463">Change a different field in the second browser tab.</span></span>

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="a41cc-465">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-465">Click **Save**.</span></span> <span data-ttu-id="a41cc-466">Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-466">You see error messages for all fields that don't match the DB values:</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

<span data-ttu-id="a41cc-468">In diesem Browserfenster sollte nicht das Namensfeld geändert werden.</span><span class="sxs-lookup"><span data-stu-id="a41cc-468">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="a41cc-469">Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-469">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="a41cc-470">Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-470">Tab out. Client-side validation removes the error message.</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/cv.png)

<span data-ttu-id="a41cc-472">Klicken Sie erneut auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-472">Click **Save** again.</span></span> <span data-ttu-id="a41cc-473">Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-473">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="a41cc-474">Die gespeicherten Werte werden auf der Indexseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-474">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="a41cc-475">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="a41cc-475">Update the Delete page</span></span>

<span data-ttu-id="a41cc-476">Aktualisieren Sie das Seitenmodell „Löschen“ mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="a41cc-476">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="a41cc-477">Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-477">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="a41cc-478">Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a41cc-478">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="a41cc-479">Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-479">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="a41cc-480">Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:</span><span class="sxs-lookup"><span data-stu-id="a41cc-480">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="a41cc-481">Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.</span><span class="sxs-lookup"><span data-stu-id="a41cc-481">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="a41cc-482">Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a41cc-482">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="a41cc-483">Wird `OnGetAsync` mit `concurrencyError` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-483">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="a41cc-484">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="a41cc-484">Update the Delete page</span></span>

<span data-ttu-id="a41cc-485">Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:</span><span class="sxs-lookup"><span data-stu-id="a41cc-485">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="a41cc-486">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="a41cc-486">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="a41cc-487">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="a41cc-487">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="a41cc-488">Eine Fehlermeldung wird hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-488">Adds an error message.</span></span>
* <span data-ttu-id="a41cc-489">„FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-489">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="a41cc-490">`RowVersion` wird geändert, um das letzte Byte anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-490">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="a41cc-491">Fügt eine ausgeblendete Zeilenversion hinzu.</span><span class="sxs-lookup"><span data-stu-id="a41cc-491">Adds a hidden row version.</span></span> <span data-ttu-id="a41cc-492">`RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="a41cc-492">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="a41cc-493">Überprüfen von Nebenläufigkeitskonflikten mit der Seite „Löschen“</span><span class="sxs-lookup"><span data-stu-id="a41cc-493">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="a41cc-494">Erstellen Sie einen Fachbereich zum Testen.</span><span class="sxs-lookup"><span data-stu-id="a41cc-494">Create a test department.</span></span>

<span data-ttu-id="a41cc-495">Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:</span><span class="sxs-lookup"><span data-stu-id="a41cc-495">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="a41cc-496">Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.</span><span class="sxs-lookup"><span data-stu-id="a41cc-496">Run the app and select Departments.</span></span>
* <span data-ttu-id="a41cc-497">Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-497">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="a41cc-498">Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.</span><span class="sxs-lookup"><span data-stu-id="a41cc-498">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="a41cc-499">Beide Registerkarten zeigen die gleichen Informationen an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-499">The two browser tabs display the same information.</span></span>

<span data-ttu-id="a41cc-500">Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a41cc-500">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="a41cc-501">Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an.</span><span class="sxs-lookup"><span data-stu-id="a41cc-501">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="a41cc-502">Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a41cc-502">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="a41cc-503">Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a41cc-503">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="a41cc-504">Durch Klicken auf **Löschen** wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="a41cc-504">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="a41cc-505">Informationen zum Vererben eines Datenmodells finden Sie unter [Vererbung](xref:data/ef-mvc/inheritance).</span><span class="sxs-lookup"><span data-stu-id="a41cc-505">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="a41cc-506">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a41cc-506">Additional resources</span></span>

* [<span data-ttu-id="a41cc-507">Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)</span><span class="sxs-lookup"><span data-stu-id="a41cc-507">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="a41cc-508">Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)</span><span class="sxs-lookup"><span data-stu-id="a41cc-508">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="a41cc-509">Dieses Tutorial auf YouTube (Behandlung von Parallelitätskonflikten)</span><span class="sxs-lookup"><span data-stu-id="a41cc-509">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="a41cc-510">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="a41cc-510">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="a41cc-511">Dieses Tutorial auf YouTube (Teil 3)</span><span class="sxs-lookup"><span data-stu-id="a41cc-511">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [<span data-ttu-id="a41cc-512">Vorherige</span><span class="sxs-lookup"><span data-stu-id="a41cc-512">Previous</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

