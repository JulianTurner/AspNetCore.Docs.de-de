---
title: Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8
author: rick-anderson
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424280"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="ec7f6-103">Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8</span><span class="sxs-lookup"><span data-stu-id="ec7f6-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="ec7f6-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ec7f6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ec7f6-105">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ec7f6-106">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ec7f6-107">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ec7f6-108">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="ec7f6-109">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="ec7f6-110">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ec7f6-111">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ec7f6-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec7f6-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ec7f6-112">Prerequisites</span></span>

* <span data-ttu-id="ec7f6-113">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="ec7f6-116">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="ec7f6-116">Database engines</span></span>

<span data-ttu-id="ec7f6-117">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="ec7f6-118">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="ec7f6-119">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ec7f6-120">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="ec7f6-120">Troubleshooting</span></span>

<span data-ttu-id="ec7f6-121">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ec7f6-122">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="ec7f6-123">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="ec7f6-123">The sample app</span></span>

<span data-ttu-id="ec7f6-124">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="ec7f6-125">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ec7f6-126">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-126">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="ec7f6-129">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="ec7f6-130">Das Tutorial konzentriert sich auf die Verwendung von EF Core mit ASP.NET Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="ec7f6-131">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="ec7f6-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-133">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ec7f6-134">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ec7f6-135">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="ec7f6-136">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="ec7f6-137">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 5.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-139">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="ec7f6-140">Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ec7f6-141">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="ec7f6-141">Set up the site style</span></span>

<span data-ttu-id="ec7f6-142">Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *Pages/Shared/_Layout.cshtml* ein: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="ec7f6-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="ec7f6-143">Mit der Layoutdatei werden die Kopfzeile, die Fußzeile und das Menü der Website festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="ec7f6-144">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="ec7f6-145">Jedes Vorkommen von „ContosoUniversity“ wird in „Contoso University“ geändert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ec7f6-146">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-146">There are three occurrences.</span></span>
* <span data-ttu-id="ec7f6-147">Die Menüeinträge **Home** und **Privacy** werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="ec7f6-148">Es werden Einträge für **About**, **Students**, **Courses**, **Instructors** und **Departmens** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="ec7f6-149">Ersetzen Sie den Inhalt der Datei *Pages/Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="ec7f6-150">Der obige Code ersetzt den Text über ASP.NET Core durch Text über diese App.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="ec7f6-151">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="ec7f6-152">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="ec7f6-152">The data model</span></span>

<span data-ttu-id="ec7f6-153">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-153">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="ec7f6-155">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ec7f6-156">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-156">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="ec7f6-158">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="ec7f6-159">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="ec7f6-160">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="ec7f6-161">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ec7f6-162">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="ec7f6-163">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="ec7f6-164">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ec7f6-165">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="ec7f6-166">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="ec7f6-167">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="ec7f6-168">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="ec7f6-169">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="ec7f6-170">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="ec7f6-171">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="ec7f6-172">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="ec7f6-173">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="ec7f6-174">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="ec7f6-175">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-175">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="ec7f6-177">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="ec7f6-178">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="ec7f6-179">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="ec7f6-180">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="ec7f6-181">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="ec7f6-182">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ec7f6-183">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="ec7f6-184">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ec7f6-185">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ec7f6-186">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="ec7f6-187">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ec7f6-188">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ec7f6-189">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ec7f6-190">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ec7f6-191">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ec7f6-192">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ec7f6-193">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-193">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="ec7f6-195">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="ec7f6-196">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ec7f6-197">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ec7f6-198">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="ec7f6-199">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="ec7f6-200">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="ec7f6-200">Scaffold Student pages</span></span>

<span data-ttu-id="ec7f6-201">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="ec7f6-202">Eine EF Core `DbContext`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="ec7f6-203">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="ec7f6-204">Diese Klasse wird von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="ec7f6-205">Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-207">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="ec7f6-208">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ec7f6-209">Im Dialogfeld **Add New Scaffold Item** (Neues Gerüstelement hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="ec7f6-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="ec7f6-210">Wählen Sie auf der linken Registerkarte **Installiert > Häufig > Razor-Seiten** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="ec7f6-211">Wählen Sie **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="ec7f6-212">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="ec7f6-213">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="ec7f6-214">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="ec7f6-215">Ändern Sie den Datenkontextnamen so, dass er auf `SchoolContext` anstatt auf `ContosoUniversityContext` endet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="ec7f6-216">Der aktualisierte Kontextname lautet `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="ec7f6-217">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-217">Select **Add**.</span></span>

<span data-ttu-id="ec7f6-218">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-220">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="ec7f6-221">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="ec7f6-222">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="ec7f6-223">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="ec7f6-224">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="ec7f6-225">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="ec7f6-226">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="ec7f6-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="ec7f6-227">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="ec7f6-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="ec7f6-228">Wenn der vorherige Schritt zu einem Fehler führt, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="ec7f6-229">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-229">The scaffolding process:</span></span>

* <span data-ttu-id="ec7f6-230">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="ec7f6-231">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-232">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-233">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-234">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-235">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="ec7f6-236">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="ec7f6-237">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="ec7f6-238">Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="ec7f6-239">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="ec7f6-239">Database connection string</span></span>

<span data-ttu-id="ec7f6-240">Das Gerüstbautool generiert eine Verbindungszeichenfolge in der Datei *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-242">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="ec7f6-243">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ec7f6-244">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-246">Kürzen Sie die SQLite-Verbindungszeichenfolge auf *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="ec7f6-247">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="ec7f6-247">Update the database context class</span></span>

<span data-ttu-id="ec7f6-248">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="ec7f6-249">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ec7f6-250">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ec7f6-251">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ec7f6-252">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="ec7f6-253">Der obige Code ändert den Singular `DbSet<Student> Student` in den Plural `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="ec7f6-254">Nehmen Sie eine globale Änderung vor, damit der Code der Razor-Seite mit dem neuen Namen `DBSet` übereinstimmt: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="ec7f6-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="ec7f6-255">in `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="ec7f6-255">to: `_context.Students.`</span></span>

<span data-ttu-id="ec7f6-256">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-256">There are 8 occurrences.</span></span>

<span data-ttu-id="ec7f6-257">Da eine Entitätenmenge mehrere Entitäten enthält, bevorzugen viele Entwickler den Plural für `DBSet`-Eigenschaftennamen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="ec7f6-258">Der hervorgehobene Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-258">The highlighted code:</span></span>

* <span data-ttu-id="ec7f6-259">Erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ec7f6-260">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="ec7f6-261">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="ec7f6-262">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="ec7f6-263">Ruft <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="ec7f6-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="ec7f6-265">Wird aufgerufen, wenn `SchoolContext` initialisiert wurde, bevor jedoch das Modell gesperrt und zum Initialisieren des Kontexts verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="ec7f6-266">Ist erforderlich, da die `Student`-Entität später im Tutorial Verweise auf die anderen Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="ec7f6-267">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="ec7f6-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ec7f6-268">Startup.cs</span></span>

<span data-ttu-id="ec7f6-269">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ec7f6-270">Dienste (z. B. `SchoolContext`) werden per Abhängigkeitsinjektion während des App-Starts registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="ec7f6-271">Komponenten, die diese Dienste erfordern (z. B. Razor-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="ec7f6-272">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ec7f6-273">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-275">Die folgenden hervorgehobenen Zeilen wurden beim Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-277">Stellen Sie sicher, dass der beim Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="ec7f6-278">Weitere Informationen zur Verwendung einer Produktionsdatenbank finden Sie unter [Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="ec7f6-279">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ec7f6-280">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="ec7f6-281">Hinzufügen des Filters für die Datenbankausnahme</span><span class="sxs-lookup"><span data-stu-id="ec7f6-281">Add the database exception filter</span></span>

<span data-ttu-id="ec7f6-282">Fügen Sie `AddDatabaseDeveloperPageExceptionFilter` in `ConfigureServices` hinzu, wie im folgenden Code dargestellt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="ec7f6-284">Fügen Sie das NuGet-Paket [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="ec7f6-285">Geben Sie in der PMC den folgenden Befehl ein, um das NuGet-Paket hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="ec7f6-287">Das NuGet-Paket `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` stellt ASP.NET Core-Middleware für Entity Framework Core-Fehlerseiten bereit.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="ec7f6-288">Diese Middleware hilft bei der Erkennung und Diagnose von Fehlern bei Entity Framework Core-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ec7f6-289">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="ec7f6-289">Create the database</span></span>

<span data-ttu-id="ec7f6-290">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="ec7f6-291">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="ec7f6-292">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="ec7f6-293">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="ec7f6-294">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-294">Delete the database.</span></span> <span data-ttu-id="ec7f6-295">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-295">Any existing data is lost.</span></span>
* <span data-ttu-id="ec7f6-296">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-296">Change the data model.</span></span> <span data-ttu-id="ec7f6-297">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="ec7f6-298">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-298">Run the app.</span></span>
* <span data-ttu-id="ec7f6-299">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="ec7f6-300">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="ec7f6-301">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="ec7f6-302">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="ec7f6-303">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="ec7f6-304">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ec7f6-305">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ec7f6-305">Test the app</span></span>

* <span data-ttu-id="ec7f6-306">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-306">Run the app.</span></span>
* <span data-ttu-id="ec7f6-307">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ec7f6-308">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ec7f6-309">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="ec7f6-309">Seed the database</span></span>

<span data-ttu-id="ec7f6-310">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="ec7f6-311">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="ec7f6-312">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="ec7f6-313">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="ec7f6-314">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="ec7f6-315">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="ec7f6-316">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-318">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="ec7f6-319">Antworten Sie mit `Y`, um die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-321">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="ec7f6-322">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-322">Restart the app.</span></span>
* <span data-ttu-id="ec7f6-323">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="ec7f6-324">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="ec7f6-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-326">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="ec7f6-327">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="ec7f6-328">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="ec7f6-329">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="ec7f6-330">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="ec7f6-331">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-333">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="ec7f6-334">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="ec7f6-335">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-335">Asynchronous code</span></span>

<span data-ttu-id="ec7f6-336">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ec7f6-337">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ec7f6-338">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ec7f6-339">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ec7f6-340">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ec7f6-341">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="ec7f6-342">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ec7f6-343">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ec7f6-344">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="ec7f6-345">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ec7f6-346">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ec7f6-347">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="ec7f6-348">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="ec7f6-349">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ec7f6-350">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ec7f6-351">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ec7f6-352">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ec7f6-353">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ec7f6-354">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ec7f6-355">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ec7f6-356">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ec7f6-357">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ec7f6-358">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="ec7f6-359">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="ec7f6-360">Überlegungen zur Leistung</span><span class="sxs-lookup"><span data-stu-id="ec7f6-360">Performance considerations</span></span>

<span data-ttu-id="ec7f6-361">Im Allgemeinen sollte eine Webseite keine beliebige Anzahl von Zeilen laden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="ec7f6-362">Eine Abfrage sollte Paging oder ein Verfahren zum Einschränken verwenden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="ec7f6-363">Die vorherige Abfrage könnte z. B. `Take` verwenden, um die Anzahl zurückgegebener Zeilen einzuschränken:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="ec7f6-364">Durch das Auflisten einer großen Tabelle in einer Sicht kann eine teilweise konstruierte HTTP 200-Antwort zurückgegeben werden, wenn während des Durchlaufens eine Datenbankausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="ec7f6-365">Der Standardwert von <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> ist 1.024.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="ec7f6-366">Der folgende Code legt `MaxModelBindingCollectionSize` fest.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="ec7f6-367">Das Paging wird später im Tutorial beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec7f6-368">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ec7f6-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ec7f6-369">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="ec7f6-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="ec7f6-370">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ec7f6-371">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ec7f6-372">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ec7f6-373">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="ec7f6-374">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="ec7f6-375">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ec7f6-376">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ec7f6-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec7f6-377">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ec7f6-377">Prerequisites</span></span>

* <span data-ttu-id="ec7f6-378">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="ec7f6-381">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="ec7f6-381">Database engines</span></span>

<span data-ttu-id="ec7f6-382">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="ec7f6-383">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="ec7f6-384">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ec7f6-385">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="ec7f6-385">Troubleshooting</span></span>

<span data-ttu-id="ec7f6-386">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ec7f6-387">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="ec7f6-388">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="ec7f6-388">The sample app</span></span>

<span data-ttu-id="ec7f6-389">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="ec7f6-390">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ec7f6-391">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-391">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="ec7f6-394">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="ec7f6-395">Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="ec7f6-396">Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="ec7f6-397">Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="ec7f6-398">Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-400">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ec7f6-401">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-401">Build the project.</span></span>
* <span data-ttu-id="ec7f6-402">Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="ec7f6-403">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-405">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ec7f6-406">Löschen Sie *ContosoUniversity.csproj*, und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="ec7f6-407">Kommentieren Sie in *Program.cs* `#define Startup` aus, sodass `StartupSQLite` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="ec7f6-408">Löschen Sie *appSettings.json*, und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="ec7f6-409">Löschen Sie den Ordner *Migrations*, und benennen Sie *MigrationsSQL* in *Migrations* um.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="ec7f6-410">Führen Sie eine globale Suche nach `#if SQLiteVersion` aus, und entfernen Sie `#if SQLiteVersion` sowie die zugehörige `#endif`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="ec7f6-411">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-411">Build the project.</span></span>
* <span data-ttu-id="ec7f6-412">Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="ec7f6-413">Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="ec7f6-414">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="ec7f6-415">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="ec7f6-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-417">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ec7f6-418">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ec7f6-419">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="ec7f6-420">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="ec7f6-421">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-423">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="ec7f6-424">Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ec7f6-425">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="ec7f6-425">Set up the site style</span></span>

<span data-ttu-id="ec7f6-426">Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="ec7f6-427">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ec7f6-428">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-428">There are three occurrences.</span></span>

* <span data-ttu-id="ec7f6-429">Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="ec7f6-430">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="ec7f6-431">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="ec7f6-432">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="ec7f6-433">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="ec7f6-433">The data model</span></span>

<span data-ttu-id="ec7f6-434">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-434">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="ec7f6-436">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ec7f6-437">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-437">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="ec7f6-439">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="ec7f6-440">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="ec7f6-441">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="ec7f6-442">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ec7f6-443">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="ec7f6-444">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="ec7f6-445">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ec7f6-446">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="ec7f6-447">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="ec7f6-448">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="ec7f6-449">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="ec7f6-450">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="ec7f6-451">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="ec7f6-452">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="ec7f6-453">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="ec7f6-454">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="ec7f6-455">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="ec7f6-456">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-456">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="ec7f6-458">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="ec7f6-459">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="ec7f6-460">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="ec7f6-461">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="ec7f6-462">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="ec7f6-463">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ec7f6-464">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="ec7f6-465">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ec7f6-466">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ec7f6-467">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="ec7f6-468">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ec7f6-469">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ec7f6-470">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ec7f6-471">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ec7f6-472">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ec7f6-473">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ec7f6-474">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-474">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="ec7f6-476">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="ec7f6-477">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ec7f6-478">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ec7f6-479">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="ec7f6-480">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="ec7f6-481">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="ec7f6-481">Scaffold Student pages</span></span>

<span data-ttu-id="ec7f6-482">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="ec7f6-483">Eine EF Core *context*-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-483">An EF Core *context* class.</span></span> <span data-ttu-id="ec7f6-484">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="ec7f6-485">Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="ec7f6-486">Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-488">Erstellen Sie einen Ordner *Students* im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="ec7f6-489">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ec7f6-490">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="ec7f6-491">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="ec7f6-492">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="ec7f6-493">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="ec7f6-494">Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="ec7f6-495">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-495">Select **Add**.</span></span>

<span data-ttu-id="ec7f6-496">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-498">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="ec7f6-499">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="ec7f6-500">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="ec7f6-501">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="ec7f6-502">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="ec7f6-503">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="ec7f6-504">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="ec7f6-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="ec7f6-505">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="ec7f6-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="ec7f6-506">Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="ec7f6-507">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-507">The scaffolding process:</span></span>

* <span data-ttu-id="ec7f6-508">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="ec7f6-509">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-510">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-511">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-512">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="ec7f6-513">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="ec7f6-514">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="ec7f6-515">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="ec7f6-516">Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="ec7f6-517">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="ec7f6-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-519">In der Datei *appsettings.json* wird die Verbindungszeichenfolge [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) angegeben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="ec7f6-520">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ec7f6-521">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-523">Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="ec7f6-524">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="ec7f6-524">Update the database context class</span></span>

<span data-ttu-id="ec7f6-525">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="ec7f6-526">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ec7f6-527">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ec7f6-528">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ec7f6-529">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="ec7f6-530">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ec7f6-531">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-531">In EF Core terminology:</span></span>

* <span data-ttu-id="ec7f6-532">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="ec7f6-533">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ec7f6-534">Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="ec7f6-535">Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="ec7f6-536">Damit der Razor Pages-Code mit dem neuen DBSet-Namen übereinstimmt, ändern Sie `_context.Student` im gesamten Projekt global in `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="ec7f6-537">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-537">There are 8 occurrences.</span></span>

<span data-ttu-id="ec7f6-538">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="ec7f6-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ec7f6-539">Startup.cs</span></span>

<span data-ttu-id="ec7f6-540">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ec7f6-541">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ec7f6-542">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ec7f6-543">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ec7f6-544">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-546">In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-548">Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="ec7f6-549">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ec7f6-550">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ec7f6-551">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="ec7f6-551">Create the database</span></span>

<span data-ttu-id="ec7f6-552">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="ec7f6-553">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="ec7f6-554">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="ec7f6-555">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="ec7f6-556">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-556">Delete the database.</span></span> <span data-ttu-id="ec7f6-557">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-557">Any existing data is lost.</span></span>
* <span data-ttu-id="ec7f6-558">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-558">Change the data model.</span></span> <span data-ttu-id="ec7f6-559">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="ec7f6-560">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-560">Run the app.</span></span>
* <span data-ttu-id="ec7f6-561">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="ec7f6-562">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="ec7f6-563">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="ec7f6-564">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="ec7f6-565">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="ec7f6-566">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ec7f6-567">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ec7f6-567">Test the app</span></span>

* <span data-ttu-id="ec7f6-568">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-568">Run the app.</span></span>
* <span data-ttu-id="ec7f6-569">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ec7f6-570">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ec7f6-571">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="ec7f6-571">Seed the database</span></span>

<span data-ttu-id="ec7f6-572">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="ec7f6-573">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="ec7f6-574">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="ec7f6-575">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="ec7f6-576">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="ec7f6-577">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="ec7f6-578">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-580">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-582">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="ec7f6-583">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-583">Restart the app.</span></span>

* <span data-ttu-id="ec7f6-584">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="ec7f6-585">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="ec7f6-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-587">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="ec7f6-588">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="ec7f6-589">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="ec7f6-590">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="ec7f6-591">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="ec7f6-592">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-594">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="ec7f6-595">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="ec7f6-596">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-596">Asynchronous code</span></span>

<span data-ttu-id="ec7f6-597">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ec7f6-598">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ec7f6-599">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ec7f6-600">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ec7f6-601">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ec7f6-602">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="ec7f6-603">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ec7f6-604">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ec7f6-605">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="ec7f6-606">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ec7f6-607">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ec7f6-608">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="ec7f6-609">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="ec7f6-610">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ec7f6-611">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ec7f6-612">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ec7f6-613">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ec7f6-614">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ec7f6-615">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ec7f6-616">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ec7f6-617">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ec7f6-618">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ec7f6-619">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="ec7f6-620">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ec7f6-621">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ec7f6-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ec7f6-622">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="ec7f6-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ec7f6-623">Die Beispiel-Web-App der Contoso University veranschaulicht, wie Sie mit Entity Framework Core (EF Core) und ASP.NET Core eine Razor Pages-App erstellen können.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="ec7f6-624">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ec7f6-625">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ec7f6-626">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="ec7f6-627">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ec7f6-628">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="ec7f6-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ec7f6-629">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ec7f6-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="ec7f6-632">Kenntnisse zu [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="ec7f6-633">Einsteiger sollten den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit dieser Tutorialreihe beginnen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ec7f6-634">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="ec7f6-634">Troubleshooting</span></span>

<span data-ttu-id="ec7f6-635">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ec7f6-636">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="ec7f6-637">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="ec7f6-637">The Contoso University web app</span></span>

<span data-ttu-id="ec7f6-638">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="ec7f6-639">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ec7f6-640">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-640">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="ec7f6-643">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="ec7f6-644">In diesem Tutorial geht es um EF Core mit Razor Pages, nicht um die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="ec7f6-645">Erstellen der Razor Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-647">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ec7f6-648">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="ec7f6-649">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="ec7f6-650">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="ec7f6-651">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="ec7f6-652">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor Pages-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="ec7f6-653">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ec7f6-655">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="ec7f6-655">Set up the site style</span></span>

<span data-ttu-id="ec7f6-656">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="ec7f6-657">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="ec7f6-658">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ec7f6-659">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-659">There are three occurrences.</span></span>

* <span data-ttu-id="ec7f6-660">Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="ec7f6-661">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-661">The changes are highlighted.</span></span> <span data-ttu-id="ec7f6-662">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="ec7f6-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="ec7f6-663">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="ec7f6-664">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="ec7f6-664">Create the data model</span></span>

<span data-ttu-id="ec7f6-665">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="ec7f6-666">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-666">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="ec7f6-668">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="ec7f6-669">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="ec7f6-670">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="ec7f6-671">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="ec7f6-672">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="ec7f6-673">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-673">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="ec7f6-675">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-675">Create a *Models* folder.</span></span> <span data-ttu-id="ec7f6-676">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="ec7f6-677">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="ec7f6-678">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ec7f6-679">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="ec7f6-680">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="ec7f6-681">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ec7f6-682">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="ec7f6-683">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="ec7f6-684">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="ec7f6-685">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="ec7f6-686">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="ec7f6-687">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="ec7f6-688">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="ec7f6-689">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="ec7f6-690">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="ec7f6-691">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="ec7f6-692">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="ec7f6-693">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-693">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="ec7f6-695">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="ec7f6-696">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="ec7f6-697">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="ec7f6-698">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="ec7f6-699">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="ec7f6-700">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ec7f6-701">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="ec7f6-702">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ec7f6-703">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ec7f6-704">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="ec7f6-705">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="ec7f6-706">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ec7f6-707">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ec7f6-708">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ec7f6-709">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ec7f6-710">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ec7f6-711">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="ec7f6-712">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-712">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="ec7f6-714">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="ec7f6-715">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ec7f6-716">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ec7f6-717">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="ec7f6-718">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="ec7f6-718">Scaffold the student model</span></span>

<span data-ttu-id="ec7f6-719">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="ec7f6-720">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="ec7f6-721">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-721">Build the project.</span></span>
* <span data-ttu-id="ec7f6-722">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ec7f6-724">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ec7f6-725">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="ec7f6-726">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="ec7f6-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="ec7f6-727">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="ec7f6-728">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="ec7f6-729">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="ec7f6-730">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-730">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="ec7f6-732">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ec7f6-734">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="ec7f6-735">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="ec7f6-736">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="ec7f6-736">Files created</span></span>

* <span data-ttu-id="ec7f6-737">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="ec7f6-738">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="ec7f6-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="ec7f6-739">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="ec7f6-739">File updates</span></span>

* <span data-ttu-id="ec7f6-740">*Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="ec7f6-741">*appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="ec7f6-742">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="ec7f6-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="ec7f6-743">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ec7f6-744">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ec7f6-745">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ec7f6-746">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ec7f6-747">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="ec7f6-748">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="ec7f6-749">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="ec7f6-750">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ec7f6-751">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="ec7f6-752">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="ec7f6-752">Update main</span></span>

<span data-ttu-id="ec7f6-753">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="ec7f6-754">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ec7f6-755">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="ec7f6-756">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="ec7f6-757">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="ec7f6-758">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="ec7f6-759">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-759">If it exists, no action is taken.</span></span> <span data-ttu-id="ec7f6-760">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="ec7f6-761">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="ec7f6-762">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="ec7f6-763">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="ec7f6-764">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-764">Delete the DB.</span></span>
* <span data-ttu-id="ec7f6-765">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="ec7f6-766">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-766">Run the app.</span></span>
* <span data-ttu-id="ec7f6-767">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="ec7f6-768">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="ec7f6-769">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ec7f6-770">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ec7f6-770">Test the app</span></span>

<span data-ttu-id="ec7f6-771">Führen Sie die App aus, und akzeptieren Sie die cookierichtlinie.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="ec7f6-772">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="ec7f6-773">Weitere Informationen zur cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="ec7f6-774">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ec7f6-775">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="ec7f6-776">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="ec7f6-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="ec7f6-777">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="ec7f6-778">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ec7f6-779">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ec7f6-780">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ec7f6-781">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="ec7f6-782">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ec7f6-783">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-783">In EF Core terminology:</span></span>

* <span data-ttu-id="ec7f6-784">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="ec7f6-785">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ec7f6-786">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="ec7f6-787">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="ec7f6-788">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="ec7f6-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ec7f6-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ec7f6-790">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="ec7f6-791">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ec7f6-792">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ec7f6-793">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="ec7f6-794">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="ec7f6-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="ec7f6-795">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="ec7f6-796">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="ec7f6-797">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="ec7f6-798">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="ec7f6-799">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="ec7f6-800">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="ec7f6-801">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="ec7f6-802">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="ec7f6-803">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="ec7f6-804">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="ec7f6-805">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="ec7f6-806">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="ec7f6-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ec7f6-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ec7f6-808">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ec7f6-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ec7f6-810">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="ec7f6-811">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="ec7f6-811">View the DB</span></span>

<span data-ttu-id="ec7f6-812">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="ec7f6-813">Daher lautet der Name der Datenbank „SchoolContext-{GUID}“.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="ec7f6-814">Die GUID ist für jeden Benutzer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="ec7f6-815">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="ec7f6-816">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="ec7f6-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="ec7f6-817">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="ec7f6-818">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="ec7f6-819">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="ec7f6-819">Asynchronous code</span></span>

<span data-ttu-id="ec7f6-820">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ec7f6-821">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ec7f6-822">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ec7f6-823">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ec7f6-824">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ec7f6-825">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="ec7f6-826">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ec7f6-827">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ec7f6-828">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="ec7f6-829">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ec7f6-830">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ec7f6-831">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="ec7f6-832">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="ec7f6-833">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="ec7f6-834">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ec7f6-835">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ec7f6-836">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ec7f6-837">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ec7f6-838">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="ec7f6-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ec7f6-839">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="ec7f6-840">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ec7f6-841">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ec7f6-842">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ec7f6-843">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="ec7f6-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="ec7f6-844">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="ec7f6-845">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="ec7f6-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="ec7f6-846">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ec7f6-846">Additional resources</span></span>

* [<span data-ttu-id="ec7f6-847">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="ec7f6-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="ec7f6-848">Nächste</span><span class="sxs-lookup"><span data-stu-id="ec7f6-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
