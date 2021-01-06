---
title: 'Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core'
description: In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d476c836e8d497ca1291992dda38da1fc9f59ed2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054371"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a><span data-ttu-id="3c7e2-103">Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="3c7e2-103">Tutorial: Handle concurrency - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="3c7e2-104">In den vorherigen Tutorials haben Sie gelernt, wie Sie Daten aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-104">In earlier tutorials, you learned how to update data.</span></span> <span data-ttu-id="3c7e2-105">In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-105">This tutorial shows how to handle conflicts when multiple users update the same entity at the same time.</span></span>

<span data-ttu-id="3c7e2-106">Sie erstellen Webseiten, die mit der Fachbereichsentität arbeiten und behandeln Parallelitätsfehler.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-106">You'll create web pages that work with the Department entity and handle concurrency errors.</span></span> <span data-ttu-id="3c7e2-107">In der nachfolgenden Abbildung sehen Sie die Seiten „Bearbeiten“ und „Löschen“, einschließlich einiger Meldungen, die angezeigt werden, wenn ein Parallelitätskonflikt auftritt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-107">The following illustrations show the Edit and Delete pages, including some messages that are displayed if a concurrency conflict occurs.</span></span>

![Seite „Fachbereich bearbeiten“](concurrency/_static/edit-error.png)

![Seite „Fachbereich löschen“](concurrency/_static/delete-error.png)

<span data-ttu-id="3c7e2-110">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c7e2-111">Erhalten Sie Informationen über Parallelitätskonflikte</span><span class="sxs-lookup"><span data-stu-id="3c7e2-111">Learn about concurrency conflicts</span></span>
> * <span data-ttu-id="3c7e2-112">Fügen Sie eine Nachverfolgungseigenschaft hinzu</span><span class="sxs-lookup"><span data-stu-id="3c7e2-112">Add a tracking property</span></span>
> * <span data-ttu-id="3c7e2-113">Erstellen Sie Abteilungscontroller und -ansichten</span><span class="sxs-lookup"><span data-stu-id="3c7e2-113">Create Departments controller and views</span></span>
> * <span data-ttu-id="3c7e2-114">Aktualisieren Sie die Ansicht „Index“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-114">Update Index view</span></span>
> * <span data-ttu-id="3c7e2-115">Aktualisieren Sie „Bearbeiten“-Methoden</span><span class="sxs-lookup"><span data-stu-id="3c7e2-115">Update Edit methods</span></span>
> * <span data-ttu-id="3c7e2-116">Aktualisieren Sie die Ansicht „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-116">Update Edit view</span></span>
> * <span data-ttu-id="3c7e2-117">Testen Sie auf Parallelitätskonflikte</span><span class="sxs-lookup"><span data-stu-id="3c7e2-117">Test concurrency conflicts</span></span>
> * <span data-ttu-id="3c7e2-118">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="3c7e2-118">Update the Delete page</span></span>
> * <span data-ttu-id="3c7e2-119">Aktualisieren der Ansichten „Details“ und „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-119">Update Details and Create views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c7e2-120">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3c7e2-120">Prerequisites</span></span>

* [<span data-ttu-id="3c7e2-121">Aktualisieren relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="3c7e2-121">Update related data</span></span>](update-related-data.md)

## <a name="concurrency-conflicts"></a><span data-ttu-id="3c7e2-122">Nebenläufigkeitskonflikte</span><span class="sxs-lookup"><span data-stu-id="3c7e2-122">Concurrency conflicts</span></span>

<span data-ttu-id="3c7e2-123">Ein Parallelitätskonflikt tritt auf, wenn ein Benutzer die Daten einer Entität anzeigt, um diese zu bearbeiten, und ein anderer Benutzer eben diese Entitätsdaten aktualisiert, bevor die Änderungen des ersten Benutzers in die Datenbank geschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-123">A concurrency conflict occurs when one user displays an entity's data in order to edit it, and then another user updates the same entity's data before the first user's change is written to the database.</span></span> <span data-ttu-id="3c7e2-124">Wenn Sie die Erkennung solcher Konflikte nicht aktivieren, überschreibt das letzte Update der Datenbank die Änderungen des anderen Benutzers.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-124">If you don't enable the detection of such conflicts, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="3c7e2-125">In vielen Anwendungen ist dieses Risiko akzeptabel: Wenn es nur wenige Benutzer bzw. wenige Updates gibt, oder wenn es nicht schlimm ist, dass Änderungen überschrieben werden können, ist es den Aufwand, für die Parallelität zu programmieren, möglicherweise nicht wert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-125">In many applications, this risk is acceptable: if there are few users, or few updates, or if isn't really critical if some changes are overwritten, the cost of programming for concurrency might outweigh the benefit.</span></span> <span data-ttu-id="3c7e2-126">In diesem Fall müssen Sie für die Anwendung keine Behandlung von Nebenläufigkeitskonflikten konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-126">In that case, you don't have to configure the application to handle concurrency conflicts.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="3c7e2-127">Pessimistische Parallelität (Sperren)</span><span class="sxs-lookup"><span data-stu-id="3c7e2-127">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="3c7e2-128">Wenn Ihre Anwendung versehentliche Datenverluste in Parallelitätsszenarios verhindern muss, ist die Verwendung von Datenbanksperren eine Möglichkeit.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-128">If your application does need to prevent accidental data loss in concurrency scenarios, one way to do that is to use database locks.</span></span> <span data-ttu-id="3c7e2-129">Man bezeichnet dies als pessimistische Parallelität.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-129">This is called pessimistic concurrency.</span></span> <span data-ttu-id="3c7e2-130">Bevor Sie zum Beispiel eine Zeile aus einer Datenbank lesen, fordern Sie eine Sperre für den schreibgeschützten Zugriff oder den Aktualisierungszugriff an.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-130">For example, before you read a row from a database, you request a lock for read-only or for update access.</span></span> <span data-ttu-id="3c7e2-131">Wenn Sie eine Zeile für den Aktualisierungszugriff sperren, kann kein anderer Benutzer diese Zeile für den schreibgeschützten Zugriff oder den Aktualisierungszugriff sperren, da er eine Kopie der Daten erhalten würde, die gerade geändert werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-131">If you lock a row for update access, no other users are allowed to lock the row either for read-only or update access, because they would get a copy of data that's in the process of being changed.</span></span> <span data-ttu-id="3c7e2-132">Wenn Sie eine Zeile für den schreibgeschützten Zugriff sperren, können andere diese Zeile ebenfalls für den schreibgeschützten Zugriff sperren, aber nicht für den Aktualisierungszugriff.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-132">If you lock a row for read-only access, others can also lock it for read-only access but not for update.</span></span>

<span data-ttu-id="3c7e2-133">Das Verwalten von Sperren hat Nachteile.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-133">Managing locks has disadvantages.</span></span> <span data-ttu-id="3c7e2-134">Es kann komplex sein, sie zu programmieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-134">It can be complex to program.</span></span> <span data-ttu-id="3c7e2-135">Dies erfordert erhebliche Datenbankverwaltungsressourcen und kann mit steigender Anzahl der Benutzer einer Anwendung zu Leistungsproblemen führen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-135">It requires significant database management resources, and it can cause performance problems as the number of users of an application increases.</span></span> <span data-ttu-id="3c7e2-136">Aus diesen Gründen unterstützen nicht alle Datenbankverwaltungssysteme die pessimistische Parallelität.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-136">For these reasons, not all database management systems support pessimistic concurrency.</span></span> <span data-ttu-id="3c7e2-137">Entity Framework Core enthält keine integrierte Unterstützung, und in diesem Tutorial wird nicht gezeigt, wie Sie sie implementieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-137">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show you how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="3c7e2-138">Optimistische Nebenläufigkeit</span><span class="sxs-lookup"><span data-stu-id="3c7e2-138">Optimistic Concurrency</span></span>

<span data-ttu-id="3c7e2-139">Die optimistische Parallelität ist die Alternative zur pessimistischen Parallelität.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-139">The alternative to pessimistic concurrency is optimistic concurrency.</span></span> <span data-ttu-id="3c7e2-140">Die Verwendung der optimistischen Parallelität bedeutet, Nebenläufigkeitskonflikte zu erlauben und entsprechend zu reagieren, wenn diese auftreten.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-140">Optimistic concurrency means allowing concurrency conflicts to happen, and then reacting appropriately if they do.</span></span> <span data-ttu-id="3c7e2-141">Benutzer1 besucht z.B. die Seite „Abteilung bearbeiten“ und ändert das Budget für die englische Abteilung von $350.000,00 in $0,00.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-141">For example, Jane visits the Department Edit page and changes the Budget amount for the English department from $350,000.00 to $0.00.</span></span>

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

<span data-ttu-id="3c7e2-143">Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-143">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

<span data-ttu-id="3c7e2-145">Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn der Browser die Indexseite anzeigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-145">Jane clicks **Save** first and sees her change when the browser returns to the Index page.</span></span>

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

<span data-ttu-id="3c7e2-147">Dann klickt Benutzer2 auf einer Bearbeitungsseite, die weiterhin ein Budget von $350.000,00 angibt, auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-147">Then John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="3c7e2-148">Was daraufhin geschieht, ist abhängig davon, wie Sie Nebenläufigkeitskonflikte behandeln.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-148">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="3c7e2-149">Einige der Optionen schließen Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-149">Some of the options include the following:</span></span>

* <span data-ttu-id="3c7e2-150">Sie können nachverfolgen, welche Eigenschaft ein Benutzer geändert hat und nur die entsprechenden Spalten in der Datenbank aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-150">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

     <span data-ttu-id="3c7e2-151">Im Beispielszenario würden keine Daten verloren gehen, da verschiedene Eigenschaften von zwei Benutzern aktualisiert wurden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-151">In the example scenario, no data would be lost, because different properties were updated by the two users.</span></span> <span data-ttu-id="3c7e2-152">Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, wird sie die Änderungen von Benutzer1 und Benutzer2 sehen – das Startdatum 1.9.2013 und ein Budget von 0 Dollar.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-152">The next time someone browses the English department, they will see both Jane's and John's changes -- a start date of 9/1/2013 and a budget of zero dollars.</span></span> <span data-ttu-id="3c7e2-153">Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlusten führen können. Sie kann Datenverluste jedoch nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft einer Entität vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-153">This method of updating can reduce the number of conflicts that could result in data loss, but it can't avoid data loss if competing changes are made to the same property of an entity.</span></span> <span data-ttu-id="3c7e2-154">Ob Entity Framework auf diese Weise funktioniert, hängt davon ab, wie Sie Ihren Aktualisierungscode implementieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-154">Whether the Entity Framework works this way depends on how you implement your update code.</span></span> <span data-ttu-id="3c7e2-155">Oft ist dies in Webanwendungen nicht praktikabel, da es erforderlich sein kann, viele Zustände zu verwalten, um alle ursprünglichen Eigenschaftswerte einer Entität und die neuen Werte im Auge zu behalten.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-155">It's often not practical in a web application, because it can require that you maintain large amounts of state in order to keep track of all original property values for an entity as well as new values.</span></span> <span data-ttu-id="3c7e2-156">Das Verwalten vieler Zuständen kann sich auf die Leistung der Anwendung auswirken, da es entweder Serverressourcen beansprucht oder in der Webseite selbst (z. B. in ausgeblendeten Feldern) oder in einem cookie enthalten sein muss.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-156">Maintaining large amounts of state can affect application performance because it either requires server resources or must be included in the web page itself (for example, in hidden fields) or in a cookie.</span></span>

* <span data-ttu-id="3c7e2-157">Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-157">You can let John's change overwrite Jane's change.</span></span>

     <span data-ttu-id="3c7e2-158">Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 1.9.2013 und der wiederhergestellte Wert $350.000,00 angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-158">The next time someone browses the English department, they will see 9/1/2013 and the restored $350,000.00 value.</span></span> <span data-ttu-id="3c7e2-159">Das ist entweder ein *Client gewinnt*- oder ein *Letzter Schreiber gewinnt*-Szenario.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-159">This is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="3c7e2-160">(Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wie in der Einführung dieses Abschnitts beschrieben, geschieht dies automatisch, wenn Sie für die Behandlung der Parallelität keinen Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-160">(All values from the client take precedence over what's in the data store.) As noted in the introduction to this section, if you don't do any coding for concurrency handling, this will happen automatically.</span></span>

* <span data-ttu-id="3c7e2-161">Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-161">You can prevent John's change from being updated in the database.</span></span>

     <span data-ttu-id="3c7e2-162">In der Regel würden Sie eine Fehlermeldung ausgeben, ihm den aktuellen Zustand der Daten anzeigen und ihm erlauben, seine Änderungen erneut anzuwenden, sofern er dies immer noch machen möchte.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-162">Typically, you would display an error message, show him the current state of the data, and allow him to reapply his changes if he still wants to make them.</span></span> <span data-ttu-id="3c7e2-163">Dieses Szenario wird *Speicher gewinnt* genannt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-163">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="3c7e2-164">(Die Werte des Datenspeichers haben Vorrang vor den Werten, die vom Client gesendet werden.) In diesem Tutorial implementieren Sie das Szenario „Speicher gewinnt“.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-164">(The data-store values take precedence over the values submitted by the client.) You'll implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="3c7e2-165">Diese Methode stellt sicher, dass keine Änderung überschrieben wird, ohne dass ein Benutzer darüber benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-165">This method ensures that no changes are overwritten without a user being alerted to what's happening.</span></span>

### <a name="detecting-concurrency-conflicts"></a><span data-ttu-id="3c7e2-166">Erkennen von Nebenläufigkeitskonflikten</span><span class="sxs-lookup"><span data-stu-id="3c7e2-166">Detecting concurrency conflicts</span></span>

<span data-ttu-id="3c7e2-167">Sie können Konflikte auflösen, indem Sie die `DbConcurrencyException`-Ausnahmen behandeln, die vom Entity Framework ausgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-167">You can resolve conflicts by handling `DbConcurrencyException` exceptions that the Entity Framework throws.</span></span> <span data-ttu-id="3c7e2-168">Entity Framework muss dazu in der Lage sein, Konflikte zu erkennen, damit es weiß, wann diese Ausnahmen ausgelöst werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-168">In order to know when to throw these exceptions, the Entity Framework must be able to detect conflicts.</span></span> <span data-ttu-id="3c7e2-169">Aus diesem Grund müssen Sie die Datenbank und das Datenmodell entsprechend konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-169">Therefore, you must configure the database and the data model appropriately.</span></span> <span data-ttu-id="3c7e2-170">Einige der Optionen für das Aktivieren der Konflikterkennung schließen Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-170">Some options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="3c7e2-171">Fügen Sie eine Änderungsverfolgungsspalte in die Datenbanktabelle ein, die verwendet werden kann, um zu bestimmen, wenn eine Änderung an einer Zeile vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-171">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="3c7e2-172">Anschließend können Sie Entity Framework so konfigurieren, dass die Spalte in der Where-Klausel eines SQL-Updates oder eines Delete-Befehls enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-172">You can then configure the Entity Framework to include that column in the Where clause of SQL Update or Delete commands.</span></span>

     <span data-ttu-id="3c7e2-173">In der Regel ist `rowversion` der Datentyp der Änderungsverfolgungsspalte.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-173">The data type of the tracking column is typically `rowversion`.</span></span> <span data-ttu-id="3c7e2-174">Der Wert `rowversion` ist eine sequenzielle Zahl, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-174">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="3c7e2-175">In einem Update- oder Delete-Befehl enthält die Where-Klausel den ursprünglichen Wert der Änderungsverfolgungsspalte (die ursprüngliche Zeilenversion).</span><span class="sxs-lookup"><span data-stu-id="3c7e2-175">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version) .</span></span> <span data-ttu-id="3c7e2-176">Wenn die Zeile, die aktualisiert wird, durch einen anderen Benutzer geändert wurde, unterscheidet sich der Wert in der Spalte `rowversion` vom ursprünglichen Wert, sodass die Anweisungen „Update“ oder „Delete“ die Zeile, die aktualisiert werden soll, aufgrund der Where-Klausel nicht finden können.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-176">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value, so the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="3c7e2-177">Wenn Entity Framework feststellt, dass das Update oder der Delete-Befehl keine Zeilen aktualisiert hat (d.h., wenn die Anzahl der betroffenen Zeilen 0 (null) ist), wird dies als Parallelitätskonflikt interpretiert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-177">When the Entity Framework finds that no rows have been updated by the Update or Delete command (that is, when the number of affected rows is zero), it interprets that as a concurrency conflict.</span></span>

* <span data-ttu-id="3c7e2-178">Konfigurieren Sie Entity Framework so, dass die ursprünglichen Werte jeder Spalte in der Tabelle in den Where-Klauseln der Update- und Delete-Befehle enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-178">Configure the Entity Framework to include the original values of every column in the table in the Where clause of Update and Delete commands.</span></span>

     <span data-ttu-id="3c7e2-179">Wie in der ersten Option gibt die Where-Klausel keine Zeile zum Aktualisieren zurück, wenn etwas in der Zeile geändert wurde, da die Zeile zuerst gelesen wurde, was vom Entity Framework als Parallelitätskonflikt interpretiert wird.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-179">As in the first option, if anything in the row has changed since the row was first read, the Where clause won't return a row to update, which the Entity Framework interprets as a concurrency conflict.</span></span> <span data-ttu-id="3c7e2-180">Bei Datenbanktabellen mit vielen Spalten kann dieser Ansatz zu sehr großen Where-Klauseln führen, was wiederum dazu führen kann, dass Sie eine große Anzahl von Zuständen verwalten müssen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-180">For database tables that have many columns, this approach can result in very large Where clauses, and can require that you maintain large amounts of state.</span></span> <span data-ttu-id="3c7e2-181">Wie bereits erwähnt, kann das Verwalten großer Mengen von Zuständen die Anwendungsleistung beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-181">As noted earlier, maintaining large amounts of state can affect application performance.</span></span> <span data-ttu-id="3c7e2-182">Deshalb wird dieser Ansatz in der Regel nicht empfohlen, und ist nicht die Methode, die in diesem Tutorial verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-182">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

     <span data-ttu-id="3c7e2-183">Wenn Sie diesen Ansatz für die Parallelität implementieren wollen, müssen Sie alle nicht primären Schlüsseleigenschaften in der Entität markieren, für die Sie die Parallelität nachverfolgen wollen, indem Sie ihnen das Attribut `ConcurrencyCheck` hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-183">If you do want to implement this approach to concurrency, you have to mark all non-primary-key properties in the entity you want to track concurrency for by adding the `ConcurrencyCheck` attribute to them.</span></span> <span data-ttu-id="3c7e2-184">Diese Änderung ermöglicht dem Entity Framework, alle Spalten in der SQL-Where-Klausel der Update- und Delete-Anweisungen einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-184">That change enables the Entity Framework to include all columns in the SQL Where clause of Update and Delete statements.</span></span>

<span data-ttu-id="3c7e2-185">Im weiteren Verlauf dieses Tutorials fügen Sie der Fachbereichsentität die Änderungsverfolgungseigenschaft `rowversion` hinzu, erstellen einen Controller und Ansichten und überprüfen, ob alles ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-185">In the remainder of this tutorial you'll add a `rowversion` tracking property to the Department entity, create a controller and views, and test to verify that everything works correctly.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="3c7e2-186">Fügen Sie eine Nachverfolgungseigenschaft hinzu</span><span class="sxs-lookup"><span data-stu-id="3c7e2-186">Add a tracking property</span></span>

<span data-ttu-id="3c7e2-187">Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-187">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="3c7e2-188">Das Attribut `Timestamp` gibt an, dass diese Spalte in die Where-Klausel der Befehle „Update“ und „Delete“ einbezogen wird, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-188">The `Timestamp` attribute specifies that this column will be included in the Where clause of Update and Delete commands sent to the database.</span></span> <span data-ttu-id="3c7e2-189">Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor er durch SQL-`rowversion` ersetzt wurde.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-189">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` replaced it.</span></span> <span data-ttu-id="3c7e2-190">Der .NET-Typ für `rowversion` ist ein Bytearray.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-190">The .NET type for `rowversion` is a byte array.</span></span>

<span data-ttu-id="3c7e2-191">Wenn Sie die Verwendung der Fluent-API bevorzugen, können Sie die Methode `IsConcurrencyToken` (in *Data/SchoolContext.cs*) verwenden, um die Änderungsverfolgungseigenschaft anzugeben, wie im folgenden Beispiel dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-191">If you prefer to use the fluent API, you can use the `IsConcurrencyToken` method (in *Data/SchoolContext.cs*) to specify the tracking property, as shown in the following example:</span></span>

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

<span data-ttu-id="3c7e2-192">Durch das Hinzufügen einer Eigenschaft ändern Sie das Datenbankmodell, daher müssen Sie eine weitere Migration durchführen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-192">By adding a property you changed the database model, so you need to do another migration.</span></span>

<span data-ttu-id="3c7e2-193">Speichern Sie ihre Änderungen, erstellen Sie das Projekt, und geben Sie dann die folgenden Befehle in das Befehlsfenster ein:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-193">Save your changes and build the project, and then enter the following commands in the command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a><span data-ttu-id="3c7e2-194">Erstellen Sie Abteilungscontroller und -ansichten</span><span class="sxs-lookup"><span data-stu-id="3c7e2-194">Create Departments controller and views</span></span>

<span data-ttu-id="3c7e2-195">Erstellen Sie einen Abteilungscontroller und Ansichten, wie Sie es vorher bereits für Studenten, Kurse und Dozenten getan haben.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-195">Scaffold a Departments controller and views as you did earlier for Students, Courses, and Instructors.</span></span>

![Erstellen des Fachbereichs](concurrency/_static/add-departments-controller.png)

<span data-ttu-id="3c7e2-197">Ändern Sie in der Datei *DepartmentsController.cs* „FirstMidName“ an jeder Stelle in „FullName“, damit die Dropdownliste für Abteilungsadministratoren den vollen Namen des Dozenten enthält und nicht nur den Nachnamen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-197">In the *DepartmentsController.cs* file, change all four occurrences of "FirstMidName" to "FullName" so that the department administrator drop-down lists will contain the full name of the instructor rather than just the last name.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a><span data-ttu-id="3c7e2-198">Aktualisieren Sie die Ansicht „Index“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-198">Update Index view</span></span>

<span data-ttu-id="3c7e2-199">Die Engine für den Gerüstbau hat eine RowVersion-Spalte in der Indexansicht erstellt, dieses Feld soll jedoch nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-199">The scaffolding engine created a RowVersion column in the Index view, but that field shouldn't be displayed.</span></span>

<span data-ttu-id="3c7e2-200">Ersetzen Sie den Code in der Datei *Views/Departments/Index.cshtml* durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-200">Replace the code in *Views/Departments/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

<span data-ttu-id="3c7e2-201">Damit wird die Überschrift in „Abteilungen“ geändert, die Spalte „RowVersion“ gelöscht und der vollständige Name des Administrators wird anstelle des Vornamens angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-201">This changes the heading to "Departments", deletes the RowVersion column, and shows full name instead of first name for the administrator.</span></span>

## <a name="update-edit-methods"></a><span data-ttu-id="3c7e2-202">Aktualisieren Sie „Bearbeiten“-Methoden</span><span class="sxs-lookup"><span data-stu-id="3c7e2-202">Update Edit methods</span></span>

<span data-ttu-id="3c7e2-203">Fügen Sie in den HttpGet-Methoden `Edit` und `Details``AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-203">In both the HttpGet `Edit` method and the `Details` method, add `AsNoTracking`.</span></span> <span data-ttu-id="3c7e2-204">Fügen Sie in der HttpGet-Methode `Edit` für den Administrator Eager Loading hinzu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-204">In the HttpGet `Edit` method, add eager loading for the Administrator.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="3c7e2-205">Ersetzen Sie den vorhandenen Code für die HttpPost-Methode `Edit` durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-205">Replace the existing code for the HttpPost `Edit` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

<span data-ttu-id="3c7e2-206">Der Code versucht zunächst die Abteilung zu lesen, die aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-206">The code begins by trying to read the department to be updated.</span></span> <span data-ttu-id="3c7e2-207">Wenn die Methode `FirstOrDefaultAsync` NULL zurückgibt, wurde die Abteilung von einem anderen Benutzer gelöscht.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-207">If the `FirstOrDefaultAsync` method returns null, the department was deleted by another user.</span></span> <span data-ttu-id="3c7e2-208">In diesem Fall verwendet der Code die bereitgestellten Formularwerte zum Erstellen einer Abteilungsentität, damit die Seite „Bearbeiten“ mit einer Fehlermeldung erneut angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-208">In that case the code uses the posted form values to create a department entity so that the Edit page can be redisplayed with an error message.</span></span> <span data-ttu-id="3c7e2-209">Alternativ müssen Sie die Abteilungsentität nicht erneut erstellen, wenn Sie nur eine Fehlermeldung anzeigen, ohne die Abteilungsfelder erneut anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-209">As an alternative, you wouldn't have to re-create the department entity if you display only an error message without redisplaying the department fields.</span></span>

<span data-ttu-id="3c7e2-210">Die Ansicht speichert den ursprünglichen Wert von `RowVersion` in einem ausgeblendeten Feld, und diese Methode erhält diesen Wert über den Parameter `rowVersion`.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-210">The view stores the original `RowVersion` value in a hidden field, and this method receives that value in the `rowVersion` parameter.</span></span> <span data-ttu-id="3c7e2-211">Bevor Sie `SaveChanges` aufrufen, müssen Sie diesen ursprünglichen Eigenschaftswert von `RowVersion` in die Auflistung `OriginalValues` für die Entität einfügen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-211">Before you call `SaveChanges`, you have to put that original `RowVersion` property value in the `OriginalValues` collection for the entity.</span></span>

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

<span data-ttu-id="3c7e2-212">Wenn Entity Framework dann den SQL-Befehl „Update“ erstellt, enthält dieser Befehl eine Where-Klausel, die nach einer Zeile mit dem ursprünglichen Wert `RowVersion` sucht.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-212">Then when the Entity Framework creates a SQL UPDATE command, that command will include a WHERE clause that looks for a row that has the original `RowVersion` value.</span></span> <span data-ttu-id="3c7e2-213">Wenn keine Zeile durch den Befehl „Update“ betroffen ist (keine Zeile enthält den ursprünglichen Wert `RowVersion`), löst Entity Framework die Ausnahme `DbUpdateConcurrencyException` aus.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-213">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value),  the Entity Framework throws a `DbUpdateConcurrencyException` exception.</span></span>

<span data-ttu-id="3c7e2-214">Der Code im Catch-Block für diese Ausnahme ruft die betroffene Abteilungsentität ab, die die aktualisierten Werte der Eigenschaft `Entries` auf dem Ausnahmeobjekt enthält.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-214">The code in the catch block for that exception gets the affected Department entity that has the updated values from the `Entries` property on the exception object.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

<span data-ttu-id="3c7e2-215">Die Auflistung `Entries` hat nur ein `EntityEntry`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-215">The `Entries` collection will have just one `EntityEntry` object.</span></span>  <span data-ttu-id="3c7e2-216">Sie können dieses Objekt verwenden, um die aktuellen Datenbankwerte und die neuen Werte abzurufen, die von dem Benutzer eingegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-216">You can use that object to get the new values entered by the user and the current database values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

<span data-ttu-id="3c7e2-217">Der Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte mit Datenbankwerten hinzu, die von den Werten abweichen, die der Benutzer auf der Seite „Bearbeiten“ eingegeben hat (zugunsten der Übersichtlichkeit wird hier nur ein Feld angezeigt).</span><span class="sxs-lookup"><span data-stu-id="3c7e2-217">The code adds a custom error message for each column that has database values different from what the user entered on the Edit page (only one field is shown here for brevity).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

<span data-ttu-id="3c7e2-218">Schließlich legt der Code den Wert `RowVersion` von `departmentToUpdate` auf den neuen Wert fest, der aus der Datenbank abgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-218">Finally, the code sets the `RowVersion` value of the `departmentToUpdate` to the new value retrieved from the database.</span></span> <span data-ttu-id="3c7e2-219">Dieser neue `RowVersion`-Wert wird in dem ausgeblendeten Feld gespeichert, wenn die Seite „Bearbeiten“ erneut angezeigt wird. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die nach dem erneuten Anzeigen der Seite „Bearbeiten“ aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-219">This new `RowVersion` value will be stored in the hidden field when the Edit page is redisplayed, and the next time the user clicks **Save**, only concurrency errors that happen since the redisplay of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

<span data-ttu-id="3c7e2-220">Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-220">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="3c7e2-221">In der Ansicht hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-221">In the view, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-edit-view"></a><span data-ttu-id="3c7e2-222">Aktualisieren Sie die Ansicht „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-222">Update Edit view</span></span>

<span data-ttu-id="3c7e2-223">Nehmen Sie folgende Änderungen in der Datei *Views/Departments/Edit.cshtml* vor:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-223">In *Views/Departments/Edit.cshtml*, make the following changes:</span></span>

* <span data-ttu-id="3c7e2-224">Fügen Sie ein ausgeblendetes Feld zum Speichern des Eigenschaftswerts `RowVersion`, direkt nach dem ausgeblendeten Feld für die Eigenschaft `DepartmentID` hinzu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-224">Add a hidden field to save the `RowVersion` property value, immediately following the hidden field for the `DepartmentID` property.</span></span>

* <span data-ttu-id="3c7e2-225">Fügen Sie der Dropdownliste die Option „Select Administrator“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-225">Add a "Select Administrator" option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a><span data-ttu-id="3c7e2-226">Testen Sie auf Parallelitätskonflikte</span><span class="sxs-lookup"><span data-stu-id="3c7e2-226">Test concurrency conflicts</span></span>

<span data-ttu-id="3c7e2-227">Führen Sie die App aus, und navigieren Sie zur Indexseite „Abteilungen“.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-227">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="3c7e2-228">Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für die englische Abteilung und wählen **In neuer Registerkarte öffnen** aus, klicken Sie dann auf den Link **Bearbeiten** für die englische Abteilung.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-228">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**, then click the **Edit** hyperlink for the English department.</span></span> <span data-ttu-id="3c7e2-229">Beide Registerkarten zeigen nun die gleichen Informationen im Browser an.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-229">The two browser tabs now display the same information.</span></span>

<span data-ttu-id="3c7e2-230">Ändern Sie ein Feld in der ersten Registerkarte, und klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-230">Change a field in the first browser tab and click **Save**.</span></span>

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="3c7e2-232">Der Browser zeigt die Indexseite mit dem geänderten Wert an.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-232">The browser shows the Index page with the changed value.</span></span>

<span data-ttu-id="3c7e2-233">Ändern Sie ein Feld in der zweiten Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-233">Change a field in the second browser tab.</span></span>

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="3c7e2-235">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-235">Click **Save**.</span></span> <span data-ttu-id="3c7e2-236">Folgende Fehlermeldung wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-236">You see an error message:</span></span>

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

<span data-ttu-id="3c7e2-238">Klicken Sie erneut auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-238">Click **Save** again.</span></span> <span data-ttu-id="3c7e2-239">Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-239">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="3c7e2-240">Die gespeicherten Werte werden Ihnen auf der Indexseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-240">You see the saved values when the Index page appears.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="3c7e2-241">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="3c7e2-241">Update the Delete page</span></span>

<span data-ttu-id="3c7e2-242">Bei der Seite „Löschen“ entdeckt Entity Framework Nebenläufigkeitskonflikte, die durch die Bearbeitung einer Abteilung ausgelöst wurden, auf ähnliche Weise.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-242">For the Delete page, the Entity Framework detects concurrency conflicts caused by someone else editing the department in a similar manner.</span></span> <span data-ttu-id="3c7e2-243">Wenn die HttpGet-Methode `Delete` die Bestätigungsansicht anzeigt, enthält diese Ansicht den ursprünglichen Wert von `RowVersion` in einem ausgeblendeten Feld.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-243">When the HttpGet `Delete` method displays the confirmation view, the view includes the original `RowVersion` value in a hidden field.</span></span> <span data-ttu-id="3c7e2-244">Dieser Wert steht dann der HttpPost-Methode `Delete` zur Verfügung, die aufgerufen wird, wenn der Benutzer das Löschen bestätigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-244">That value is then available to the HttpPost `Delete` method that's called when the user confirms the deletion.</span></span> <span data-ttu-id="3c7e2-245">Wenn Entity Framework den SQL-Befehl „Delete“ erstellt, enthält dieser Befehl eine Where-Klausel mit dem ursprünglichen Wert `RowVersion`.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-245">When the Entity Framework creates the SQL DELETE command, it includes a WHERE clause with the original `RowVersion` value.</span></span> <span data-ttu-id="3c7e2-246">Wenn der Befehl in 0 (null) betroffenen Zeilen resultiert (d.h. die Zeile wurde geändert, nachdem die Bestätigungsseite „Löschen“ angezeigt wurde), wird eine Parallelitätsausnahme ausgelöst, und die HttpGet-Methode `Delete` wird mit einem auf TRUE festgelegten Fehlerflag aufgerufen, um die Bestätigungsseite erneut mit einer Fehlermeldung anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-246">If the command results in zero rows affected (meaning the row was changed after the Delete confirmation page was displayed), a concurrency exception is thrown, and the HttpGet `Delete` method is called with an error flag set to true in order to redisplay the confirmation page with an error message.</span></span> <span data-ttu-id="3c7e2-247">Es ist auch möglich, dass 0 (null) Zeilen betroffen sind, weil die Zeile von einem anderen Benutzer gelöscht wurde. In diesem Fall wird keine Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-247">It's also possible that zero rows were affected because the row was deleted by another user, so in that case no error message is displayed.</span></span>

### <a name="update-the-delete-methods-in-the-departments-controller"></a><span data-ttu-id="3c7e2-248">Aktualisieren der Delete-Methoden im Abteilungscontroller</span><span class="sxs-lookup"><span data-stu-id="3c7e2-248">Update the Delete methods in the Departments controller</span></span>

<span data-ttu-id="3c7e2-249">Ersetzen Sie in der Datei *DepartmentsController.cs* die HttpGet-Methode `Delete` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-249">In *DepartmentsController.cs*, replace the HttpGet `Delete` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

<span data-ttu-id="3c7e2-250">Die Methode akzeptiert einen optionalen Parameter, der angibt, ob die Seite nach einem Parallelitätsfehler erneut angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-250">The method accepts an optional parameter that indicates whether the page is being redisplayed after a concurrency error.</span></span> <span data-ttu-id="3c7e2-251">Wenn dieses Flag auf TRUE festgelegt ist, und die angegebene Abteilung nicht mehr vorhanden ist, wurde sie von einem anderen Benutzer gelöscht.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-251">If this flag is true and the department specified no longer exists, it was deleted by another user.</span></span> <span data-ttu-id="3c7e2-252">In diesem Fall leitet der Code an eine Indexseite weiter.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-252">In that case, the code redirects to the Index page.</span></span>  <span data-ttu-id="3c7e2-253">Wenn dieses Flag auf TRUE festgelegt ist, und die Abteilung vorhanden ist, wurde sie von einem anderen Benutzer geändert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-253">If this flag is true and the Department does exist, it was changed by another user.</span></span> <span data-ttu-id="3c7e2-254">In diesem Fall sendet der Code mithilfe von `ViewData` eine Fehlermeldung an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-254">In that case, the code sends an error message to the view using `ViewData`.</span></span>

<span data-ttu-id="3c7e2-255">Ersetzen Sie den Code in der HttpPost-Methode `Delete` (namens `DeleteConfirmed`) durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-255">Replace the code in the HttpPost `Delete` method (named `DeleteConfirmed`) with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

<span data-ttu-id="3c7e2-256">In dem eingerüsteten Code, den Sie soeben ersetzt haben, akzeptiert diese Methode nur eine Datensatz-ID:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-256">In the scaffolded code that you just replaced, this method accepted only a record ID:</span></span>

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

<span data-ttu-id="3c7e2-257">Diesen Parameter haben Sie in eine Abteilungsentitätsinstanz geändert, die durch die Modellbindung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-257">You've changed this parameter to a Department entity instance created by the model binder.</span></span> <span data-ttu-id="3c7e2-258">Dadurch erhält Entity Framework neben dem Datensatzschlüssel auch Zugriff auf den Eigenschaftswert „RowVersion“.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-258">This gives EF access to the RowVersion property value in addition to the record key.</span></span>

```csharp
public async Task<IActionResult> Delete(Department department)
```

<span data-ttu-id="3c7e2-259">Ebenfalls haben Sie den Namen der Aktionsmethode `DeleteConfirmed` auf `Delete` geändert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-259">You have also changed the action method name from `DeleteConfirmed` to `Delete`.</span></span> <span data-ttu-id="3c7e2-260">Der eingerüstete Code verwendet den Namen `DeleteConfirmed`, um der HttpPost-Methode eine eindeutige Signatur zuzuweisen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-260">The scaffolded code used the name `DeleteConfirmed` to give the HttpPost method a unique signature.</span></span> <span data-ttu-id="3c7e2-261">(Die CLR erfordert überladene Methoden, um verschiedene Methodenparameter zu enthalten.) Da die Signaturen nun eindeutig sind, können Sie weiterhin die MVC-Konventionen verwenden, und den gleichen Namen für die HttpPost- und HttpGet-Delete-Methoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-261">(The CLR requires overloaded methods to have different method parameters.) Now that the signatures are unique, you can stick with the MVC convention and use the same name for the HttpPost and HttpGet delete methods.</span></span>

<span data-ttu-id="3c7e2-262">Wenn die Abteilung bereits gelöscht ist, gibt die Methode `AnyAsync` FALSE zurück, und die Anwendung kehrt zu der Indexmethode zurück.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-262">If the department is already deleted, the `AnyAsync` method returns false and the application just goes back to the Index method.</span></span>

<span data-ttu-id="3c7e2-263">Wenn ein Parallelitätsfehler abgefangen wird, zeigt der Code erneut die Bestätigungsseite „Löschen“ an, und stellt ein Flag bereit, das angibt, dass eine Fehlermeldung für die Parallelität angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-263">If a concurrency error is caught, the code redisplays the Delete confirmation page and provides a flag that indicates it should display a concurrency error message.</span></span>

### <a name="update-the-delete-view"></a><span data-ttu-id="3c7e2-264">Aktualisieren der Ansicht „Löschen“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-264">Update the Delete view</span></span>

<span data-ttu-id="3c7e2-265">Ersetzen Sie den eingerüsteten Code in der Datei *Views/Departments/Delete.cshtml* durch den folgenden Code, der ein Feld für die Fehlermeldung und ausgeblendete Felder für die Eigenschaften „DepartmentID“ und „RowVersion“ hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-265">In *Views/Departments/Delete.cshtml*, replace the scaffolded code with the following code that adds an error message field and hidden fields for the DepartmentID and RowVersion properties.</span></span> <span data-ttu-id="3c7e2-266">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-266">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

<span data-ttu-id="3c7e2-267">Dadurch werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-267">This makes the following changes:</span></span>

* <span data-ttu-id="3c7e2-268">Fügt eine Fehlermeldung zwischen den Überschriften `h2` und `h3` hinzu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-268">Adds an error message between the `h2` and `h3` headings.</span></span>

* <span data-ttu-id="3c7e2-269">Ersetzt „FirstMidName“ durch „FullName“ im Feld **Administrator**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-269">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>

* <span data-ttu-id="3c7e2-270">Entfernt das Feld „RowVersion“.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-270">Removes the RowVersion field.</span></span>

* <span data-ttu-id="3c7e2-271">Fügt der Eigenschaft `RowVersion` ein ausgeblendetes Feld zu.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-271">Adds a hidden field for the `RowVersion` property.</span></span>

<span data-ttu-id="3c7e2-272">Führen Sie die App aus, und navigieren Sie zur Indexseite „Abteilungen“.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-272">Run the app and go to the Departments Index page.</span></span> <span data-ttu-id="3c7e2-273">Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für die englische Abteilung, und wählen Sie **In neuer Registerkarte öffnen** aus. Klicken Sie in der ersten Registerkarte dann auf den Link **Bearbeiten** für die englische Abteilung.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-273">Right-click the **Delete** hyperlink for the English department and select **Open in new tab**, then in the first tab click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="3c7e2-274">Ändern Sie im ersten Fenster einen der Werte, und klicken Sie auf **Speichern**:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-274">In the first window, change one of the values, and click **Save**:</span></span>

![Die Seite „Abteilung bearbeiten“ nach der Änderung vor dem Löschen](concurrency/_static/edit-after-change-for-delete.png)

<span data-ttu-id="3c7e2-276">Klicken Sie in der zweiten Registerkarte auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-276">In the second tab, click **Delete**.</span></span> <span data-ttu-id="3c7e2-277">Ihnen wird eine Fehlermeldung zur Parallelität angezeigt, und die Abteilungswerte werden mit den aktuellen Werten der Datenbank aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-277">You see the concurrency error message, and the Department values are refreshed with what's currently in the database.</span></span>

![Die Bestätigungsseite „Abteilung löschen“ mit Parallelitätsfehler](concurrency/_static/delete-error.png)

<span data-ttu-id="3c7e2-279">Wenn Sie erneut auf **Löschen** klicken, werden Sie auf die Indexseite weitergeleitet, die anzeigt, dass die Abteilung gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-279">If you click **Delete** again, you're redirected to the Index page, which shows that the department has been deleted.</span></span>

## <a name="update-details-and-create-views"></a><span data-ttu-id="3c7e2-280">Aktualisieren der Ansichten „Details“ und „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="3c7e2-280">Update Details and Create views</span></span>

<span data-ttu-id="3c7e2-281">Optional können Sie den eingerüsteten Code in den Ansichten „Details“ und „Erstellen“ bereinigen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-281">You can optionally clean up scaffolded code in the Details and Create views.</span></span>

<span data-ttu-id="3c7e2-282">Ersetzen Sie den Code in der Datei *Views/Departments/Details.cshtml*, um die Spalte „RowVersion“ zu löschen und den vollständigen Namen des Administrators anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-282">Replace the code in *Views/Departments/Details.cshtml* to delete the RowVersion column and show the full name of the Administrator.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

<span data-ttu-id="3c7e2-283">Ersetzen Sie den Code in der Datei *Views/Departments/Create.cshtml*, um der Dropdownliste eine Select-Option hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-283">Replace the code in *Views/Departments/Create.cshtml* to add a Select option to the drop-down list.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a><span data-ttu-id="3c7e2-284">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="3c7e2-284">Get the code</span></span>

[<span data-ttu-id="3c7e2-285">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="3c7e2-285">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="3c7e2-286">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3c7e2-286">Additional resources</span></span>

 <span data-ttu-id="3c7e2-287">Weitere Informationen zum Behandeln der Parallelität in EF Core finden Sie unter [Concurrency conflicts (Nebenläufigkeitskonflikte)](/ef/core/saving/concurrency).</span><span class="sxs-lookup"><span data-stu-id="3c7e2-287">For more information about how to handle concurrency in EF Core, see [Concurrency conflicts](/ef/core/saving/concurrency).</span></span>

## <a name="next-steps"></a><span data-ttu-id="3c7e2-288">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3c7e2-288">Next steps</span></span>

<span data-ttu-id="3c7e2-289">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="3c7e2-289">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c7e2-290">Haben Sie Informationen über Parallelitätskonflikte erhalten</span><span class="sxs-lookup"><span data-stu-id="3c7e2-290">Learned about concurrency conflicts</span></span>
> * <span data-ttu-id="3c7e2-291">Haben Sie eine Nachverfolgungseigenschaft hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="3c7e2-291">Added a tracking property</span></span>
> * <span data-ttu-id="3c7e2-292">Haben Sie Abteilungscontroller und Ansichten erstellt</span><span class="sxs-lookup"><span data-stu-id="3c7e2-292">Created Departments controller and views</span></span>
> * <span data-ttu-id="3c7e2-293">Haben Sie die Ansicht „Index“ aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3c7e2-293">Updated Index view</span></span>
> * <span data-ttu-id="3c7e2-294">Haben Sie „Bearbeiten“-Methoden aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3c7e2-294">Updated Edit methods</span></span>
> * <span data-ttu-id="3c7e2-295">Haben Sie die Ansicht „Bearbeiten“ aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3c7e2-295">Updated Edit view</span></span>
> * <span data-ttu-id="3c7e2-296">Haben Sie auf Parallelitätskonflikte getestet</span><span class="sxs-lookup"><span data-stu-id="3c7e2-296">Tested concurrency conflicts</span></span>
> * <span data-ttu-id="3c7e2-297">Haben Sie die Seite „Löschen“ aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3c7e2-297">Updated the Delete page</span></span>
> * <span data-ttu-id="3c7e2-298">Haben Sie die Ansichten „Details“ und „Erstellen“ aktualisiert</span><span class="sxs-lookup"><span data-stu-id="3c7e2-298">Updated Details and Create views</span></span>

<span data-ttu-id="3c7e2-299">Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die TPH-Vererbung (TPH = Table per Hierarchy, Tabelle pro Hierarchie) für die Entitäten „Instructor“ und „Student“ implementieren.</span><span class="sxs-lookup"><span data-stu-id="3c7e2-299">Advance to the next tutorial to learn how to implement table-per-hierarchy inheritance for the Instructor and Student entities.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3c7e2-300">Weiter: Tutorial: Implementieren von Vererbung: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="3c7e2-300">Next: Implement table-per-hierarchy inheritance</span></span>](inheritance.md)
