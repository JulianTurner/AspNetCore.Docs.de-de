---
title: Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8
author: rick-anderson
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 9dcb1c4a19e50a57f1a1918cfcf775b49fa89b11
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320147"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="b0764-103">Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8</span><span class="sxs-lookup"><span data-stu-id="b0764-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="b0764-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b0764-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b0764-105">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b0764-106">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b0764-107">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b0764-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b0764-108">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b0764-109">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="b0764-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b0764-110">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="b0764-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b0764-111">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b0764-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0764-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b0764-112">Prerequisites</span></span>

* <span data-ttu-id="b0764-113">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="b0764-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b0764-116">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="b0764-116">Database engines</span></span>

<span data-ttu-id="b0764-117">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b0764-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b0764-118">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="b0764-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b0764-119">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="b0764-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b0764-120">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b0764-120">Troubleshooting</span></span>

<span data-ttu-id="b0764-121">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b0764-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b0764-122">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0764-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b0764-123">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="b0764-123">The sample app</span></span>

<span data-ttu-id="b0764-124">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="b0764-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b0764-125">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b0764-126">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-126">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="b0764-129">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="b0764-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b0764-130">Das Tutorial konzentriert sich auf die Verwendung von EF Core mit ASP.NET Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b0764-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="b0764-131">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="b0764-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0764-133">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="b0764-134">Wählen Sie im Dialogfeld **Neues Projekt** die Option **ASP.NET Core-Webanwendung** > **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="b0764-135">Geben Sie `ContosoUniversity` im Dialogfeld **Neues Projekt konfigurieren** für **Projektname** ein.</span><span class="sxs-lookup"><span data-stu-id="b0764-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="b0764-136">Es ist wichtig, genau diesen Namen unter Berücksichtigung der Groß-/Kleinschreibung zu verwenden, sodass beim Kopieren von Code jeder `namespace` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="b0764-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="b0764-137">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-137">Select **Create**.</span></span>
1. <span data-ttu-id="b0764-138">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="b0764-139">In den Dropdownmenüs **.NET Core** und **ASP.NET Core 5.0**.</span><span class="sxs-lookup"><span data-stu-id="b0764-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="b0764-140">**ASP.NET Core-Web-App**</span><span class="sxs-lookup"><span data-stu-id="b0764-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="b0764-141">**Dialogfeld**
      ![Neues ASP.NET Core-Projekt erstellen](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="b0764-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-143">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="b0764-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="b0764-144">Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="b0764-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b0764-145">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b0764-145">Set up the site style</span></span>

<span data-ttu-id="b0764-146">Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *Pages/Shared/_Layout.cshtml* ein: </span><span class="sxs-lookup"><span data-stu-id="b0764-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b0764-147">Mit der Layoutdatei werden die Kopfzeile, die Fußzeile und das Menü der Website festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b0764-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="b0764-148">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="b0764-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="b0764-149">Jedes Vorkommen von „ContosoUniversity“ wird in „Contoso University“ geändert.</span><span class="sxs-lookup"><span data-stu-id="b0764-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b0764-150">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="b0764-150">There are three occurrences.</span></span>
* <span data-ttu-id="b0764-151">Die Menüeinträge **Home** und **Privacy** werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="b0764-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="b0764-152">Es werden Einträge für **About**, **Students**, **Courses**, **Instructors** und **Departmens** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b0764-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b0764-153">Ersetzen Sie den Inhalt der Datei *Pages/Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="b0764-154">Der obige Code ersetzt den Text über ASP.NET Core durch Text über diese App.</span><span class="sxs-lookup"><span data-stu-id="b0764-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="b0764-155">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b0764-156">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="b0764-156">The data model</span></span>

<span data-ttu-id="b0764-157">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="b0764-157">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="b0764-159">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="b0764-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b0764-160">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="b0764-160">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="b0764-162">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="b0764-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="b0764-163">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b0764-164">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="b0764-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b0764-165">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="b0764-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b0764-166">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b0764-167">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b0764-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b0764-168">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b0764-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b0764-169">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b0764-170">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b0764-171">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b0764-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b0764-172">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b0764-173">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b0764-174">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b0764-175">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="b0764-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b0764-176">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="b0764-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b0764-177">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="b0764-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b0764-178">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="b0764-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b0764-179">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="b0764-179">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="b0764-181">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b0764-182">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b0764-183">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="b0764-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b0764-184">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b0764-185">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b0764-186">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="b0764-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b0764-187">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b0764-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b0764-188">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b0764-189">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b0764-190">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b0764-191">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b0764-192">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b0764-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b0764-193">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b0764-194">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b0764-195">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b0764-196">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b0764-197">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="b0764-197">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="b0764-199">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b0764-200">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b0764-201">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b0764-202">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="b0764-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b0764-203">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b0764-204">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="b0764-204">Scaffold Student pages</span></span>

<span data-ttu-id="b0764-205">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="b0764-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b0764-206">Eine EF Core `DbContext`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="b0764-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="b0764-207">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b0764-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b0764-208">Diese Klasse wird von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b0764-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="b0764-209">Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-211">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="b0764-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="b0764-212">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b0764-213">Im Dialogfeld **Add New Scaffold Item** (Neues Gerüstelement hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="b0764-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="b0764-214">Wählen Sie auf der linken Registerkarte **Installiert > Häufig > Razor-Seiten** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="b0764-215">Wählen Sie **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b0764-216">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b0764-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b0764-217">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b0764-218">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="b0764-219">Ändern Sie den Datenkontextnamen so, dass er auf `SchoolContext` anstatt auf `ContosoUniversityContext` endet.</span><span class="sxs-lookup"><span data-stu-id="b0764-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="b0764-220">Der aktualisierte Kontextname lautet `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b0764-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="b0764-221">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-221">Select **Add**.</span></span>

<span data-ttu-id="b0764-222">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="b0764-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-224">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="b0764-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="b0764-225">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b0764-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b0764-226">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="b0764-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b0764-227">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="b0764-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b0764-228">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="b0764-229">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b0764-230">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="b0764-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="b0764-231">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="b0764-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="b0764-232">Wenn der vorherige Schritt zu einem Fehler führt, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="b0764-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b0764-233">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="b0764-233">The scaffolding process:</span></span>

* <span data-ttu-id="b0764-234">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="b0764-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b0764-235">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-236">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-237">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-238">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-239">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b0764-240">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="b0764-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b0764-241">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="b0764-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b0764-242">legt eine Datenbankverbindungszeichenfolge für *appsettings.json* fest</span><span class="sxs-lookup"><span data-stu-id="b0764-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b0764-243">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b0764-243">Database connection string</span></span>

<span data-ttu-id="b0764-244">Das Gerüstbautool generiert eine Verbindungszeichenfolge in der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b0764-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-246">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an:</span><span class="sxs-lookup"><span data-stu-id="b0764-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="b0764-247">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b0764-248">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="b0764-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-250">Kürzen Sie die SQLite-Verbindungszeichenfolge auf *CU.db*:</span><span class="sxs-lookup"><span data-stu-id="b0764-250">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b0764-251">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="b0764-251">Update the database context class</span></span>

<span data-ttu-id="b0764-252">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b0764-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b0764-253">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b0764-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b0764-254">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b0764-255">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b0764-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b0764-256">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b0764-257">Der obige Code ändert den Singular `DbSet<Student> Student` in den Plural `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="b0764-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="b0764-258">Nehmen Sie eine globale Änderung vor, damit der Code der Razor-Seite mit dem neuen Namen `DBSet` übereinstimmt: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="b0764-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="b0764-259">in `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="b0764-259">to: `_context.Students.`</span></span>

<span data-ttu-id="b0764-260">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="b0764-260">There are 8 occurrences.</span></span>

<span data-ttu-id="b0764-261">Da eine Entitätenmenge mehrere Entitäten enthält, bevorzugen viele Entwickler den Plural für `DBSet`-Eigenschaftennamen.</span><span class="sxs-lookup"><span data-stu-id="b0764-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="b0764-262">Der hervorgehobene Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-262">The highlighted code:</span></span>

* <span data-ttu-id="b0764-263">Erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="b0764-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b0764-264">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="b0764-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="b0764-265">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="b0764-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="b0764-266">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="b0764-267">Ruft <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="b0764-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="b0764-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="b0764-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="b0764-269">Wird aufgerufen, wenn `SchoolContext` initialisiert wurde, bevor jedoch das Modell gesperrt und zum Initialisieren des Kontexts verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="b0764-270">Ist erforderlich, da die `Student`-Entität später im Tutorial Verweise auf die anderen Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="b0764-271">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b0764-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b0764-272">Startup.cs</span></span>

<span data-ttu-id="b0764-273">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b0764-274">Dienste (z. B. `SchoolContext`) werden per Abhängigkeitsinjektion während des App-Starts registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="b0764-275">Komponenten, die diese Dienste erfordern (z. B. Razor-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="b0764-276">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="b0764-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b0764-277">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-279">Die folgenden hervorgehobenen Zeilen wurden beim Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b0764-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-281">Stellen Sie sicher, dass der beim Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="b0764-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="b0764-282">Weitere Informationen zur Verwendung einer Produktionsdatenbank finden Sie unter [Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="b0764-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="b0764-283">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b0764-284">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b0764-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="b0764-285">Hinzufügen des Filters für die Datenbankausnahme</span><span class="sxs-lookup"><span data-stu-id="b0764-285">Add the database exception filter</span></span>

<span data-ttu-id="b0764-286">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> in `ConfigureServices` hinzu, wie im folgenden Code dargestellt:</span><span class="sxs-lookup"><span data-stu-id="b0764-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="b0764-288">Fügen Sie das NuGet-Paket [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) hinzu.</span><span class="sxs-lookup"><span data-stu-id="b0764-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="b0764-289">Geben Sie Folgendes in die Konsole des Paket-Managers ein, um das NuGet-Paket hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="b0764-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="b0764-291">Das NuGet-Paket `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` stellt ASP.NET Core-Middleware für Entity Framework Core-Fehlerseiten bereit.</span><span class="sxs-lookup"><span data-stu-id="b0764-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="b0764-292">Diese Middleware hilft bei der Erkennung und Diagnose von Fehlern bei Entity Framework Core-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b0764-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="b0764-293">`AddDatabaseDeveloperPageExceptionFilter` bietet hilfreiche Fehlerinformationen in der [Entwicklungsumgebung](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="b0764-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b0764-294">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0764-294">Create the database</span></span>

<span data-ttu-id="b0764-295">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="b0764-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b0764-296">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b0764-297">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b0764-298">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b0764-299">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-299">Delete the database.</span></span> <span data-ttu-id="b0764-300">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="b0764-300">Any existing data is lost.</span></span>
* <span data-ttu-id="b0764-301">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="b0764-301">Change the data model.</span></span> <span data-ttu-id="b0764-302">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="b0764-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b0764-303">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-303">Run the app.</span></span>
* <span data-ttu-id="b0764-304">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="b0764-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b0764-305">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="b0764-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b0764-306">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="b0764-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b0764-307">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b0764-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="b0764-308">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b0764-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b0764-309">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b0764-310">Testen der App</span><span class="sxs-lookup"><span data-stu-id="b0764-310">Test the app</span></span>

* <span data-ttu-id="b0764-311">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-311">Run the app.</span></span>
* <span data-ttu-id="b0764-312">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b0764-313">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="b0764-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b0764-314">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0764-314">Seed the database</span></span>

<span data-ttu-id="b0764-315">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b0764-316">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="b0764-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b0764-317">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b0764-318">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b0764-319">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b0764-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b0764-320">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b0764-321">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="b0764-321">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-323">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="b0764-324">Antworten Sie mit `Y`, um die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="b0764-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-326">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="b0764-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b0764-327">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="b0764-327">Restart the app.</span></span>
* <span data-ttu-id="b0764-328">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b0764-329">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="b0764-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-331">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b0764-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b0764-332">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b0764-333">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="b0764-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b0764-334">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b0764-335">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b0764-336">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b0764-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-338">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b0764-339">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="b0764-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b0764-340">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="b0764-340">Asynchronous code</span></span>

<span data-ttu-id="b0764-341">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="b0764-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b0764-342">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b0764-343">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b0764-344">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="b0764-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b0764-345">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="b0764-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b0764-346">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b0764-347">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="b0764-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b0764-348">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b0764-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b0764-349">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b0764-350">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b0764-351">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b0764-352">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b0764-353">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="b0764-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b0764-354">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="b0764-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b0764-355">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b0764-356">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b0764-357">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b0764-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b0764-358">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="b0764-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b0764-359">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b0764-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b0764-360">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="b0764-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b0764-361">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="b0764-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b0764-362">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0764-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b0764-363">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="b0764-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b0764-364">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b0764-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="b0764-365">Überlegungen zur Leistung</span><span class="sxs-lookup"><span data-stu-id="b0764-365">Performance considerations</span></span>

<span data-ttu-id="b0764-366">Im Allgemeinen sollte eine Webseite keine beliebige Anzahl von Zeilen laden.</span><span class="sxs-lookup"><span data-stu-id="b0764-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="b0764-367">Eine Abfrage sollte Paging oder ein Verfahren zum Einschränken verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0764-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="b0764-368">Die vorherige Abfrage könnte z. B. `Take` verwenden, um die Anzahl zurückgegebener Zeilen einzuschränken:</span><span class="sxs-lookup"><span data-stu-id="b0764-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b0764-369">Durch das Auflisten einer großen Tabelle in einer Sicht kann eine teilweise konstruierte HTTP 200-Antwort zurückgegeben werden, wenn während des Durchlaufens eine Datenbankausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="b0764-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="b0764-370">Der Standardwert von <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> ist 1.024.</span><span class="sxs-lookup"><span data-stu-id="b0764-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="b0764-371">Der folgende Code legt `MaxModelBindingCollectionSize` fest.</span><span class="sxs-lookup"><span data-stu-id="b0764-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b0764-372">Das Paging wird später im Tutorial beschrieben.</span><span class="sxs-lookup"><span data-stu-id="b0764-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0764-373">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b0764-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b0764-374">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="b0764-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="b0764-375">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="b0764-376">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b0764-377">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b0764-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b0764-378">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="b0764-379">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="b0764-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="b0764-380">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="b0764-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b0764-381">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b0764-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0764-382">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b0764-382">Prerequisites</span></span>

* <span data-ttu-id="b0764-383">Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="b0764-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="b0764-386">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="b0764-386">Database engines</span></span>

<span data-ttu-id="b0764-387">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b0764-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="b0764-388">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="b0764-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="b0764-389">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="b0764-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b0764-390">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b0764-390">Troubleshooting</span></span>

<span data-ttu-id="b0764-391">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="b0764-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b0764-392">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b0764-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="b0764-393">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="b0764-393">The sample app</span></span>

<span data-ttu-id="b0764-394">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="b0764-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="b0764-395">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b0764-396">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-396">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="b0764-399">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="b0764-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="b0764-400">Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b0764-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="b0764-401">Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="b0764-402">Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="b0764-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="b0764-403">Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="b0764-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-405">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b0764-406">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b0764-406">Build the project.</span></span>
* <span data-ttu-id="b0764-407">Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="b0764-408">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0764-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-410">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="b0764-411">Löschen Sie *ContosoUniversity.csproj*, und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.</span><span class="sxs-lookup"><span data-stu-id="b0764-411">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="b0764-412">Kommentieren Sie in *Program.cs* `#define Startup` aus, sodass `StartupSQLite` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-412">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="b0764-413">Löschen Sie *appSettings.json*, und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.</span><span class="sxs-lookup"><span data-stu-id="b0764-413">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="b0764-414">Löschen Sie den Ordner *Migrations*, und benennen Sie *MigrationsSQL* in *Migrations* um.</span><span class="sxs-lookup"><span data-stu-id="b0764-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="b0764-415">Führen Sie eine globale Suche nach `#if SQLiteVersion` aus, und entfernen Sie `#if SQLiteVersion` sowie die zugehörige `#endif`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="b0764-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="b0764-416">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b0764-416">Build the project.</span></span>
* <span data-ttu-id="b0764-417">Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="b0764-418">Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="b0764-419">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0764-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="b0764-420">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="b0764-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-422">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b0764-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b0764-423">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="b0764-424">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="b0764-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="b0764-425">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="b0764-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="b0764-426">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-428">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="b0764-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="b0764-429">Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="b0764-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b0764-430">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b0764-430">Set up the site style</span></span>

<span data-ttu-id="b0764-431">Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:</span><span class="sxs-lookup"><span data-stu-id="b0764-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="b0764-432">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="b0764-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b0764-433">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="b0764-433">There are three occurrences.</span></span>

* <span data-ttu-id="b0764-434">Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.</span><span class="sxs-lookup"><span data-stu-id="b0764-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="b0764-435">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b0764-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="b0764-436">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="b0764-436">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="b0764-437">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="b0764-438">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="b0764-438">The data model</span></span>

<span data-ttu-id="b0764-439">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="b0764-439">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="b0764-441">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="b0764-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="b0764-442">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="b0764-442">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="b0764-444">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="b0764-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="b0764-445">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="b0764-446">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="b0764-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="b0764-447">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="b0764-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b0764-448">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="b0764-449">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="b0764-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="b0764-450">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b0764-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b0764-451">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="b0764-452">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="b0764-453">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b0764-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="b0764-454">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="b0764-455">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="b0764-456">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="b0764-457">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="b0764-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="b0764-458">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="b0764-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="b0764-459">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="b0764-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="b0764-460">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="b0764-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="b0764-461">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="b0764-461">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="b0764-463">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="b0764-464">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="b0764-465">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="b0764-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="b0764-466">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="b0764-467">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="b0764-468">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="b0764-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b0764-469">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="b0764-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="b0764-470">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b0764-471">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b0764-472">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="b0764-473">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b0764-474">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b0764-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b0764-475">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b0764-476">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b0764-477">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b0764-478">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="b0764-479">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="b0764-479">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="b0764-481">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="b0764-482">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b0764-483">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b0764-484">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="b0764-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="b0764-485">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="b0764-486">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="b0764-486">Scaffold Student pages</span></span>

<span data-ttu-id="b0764-487">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="b0764-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="b0764-488">Eine EF Core *context*-Klasse.</span><span class="sxs-lookup"><span data-stu-id="b0764-488">An EF Core *context* class.</span></span> <span data-ttu-id="b0764-489">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b0764-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="b0764-490">Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b0764-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="b0764-491">Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-493">Erstellen Sie einen Ordner *Students* im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="b0764-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="b0764-494">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-494">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b0764-495">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="b0764-496">Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="b0764-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="b0764-497">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="b0764-498">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="b0764-499">Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="b0764-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="b0764-500">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-500">Select **Add**.</span></span>

<span data-ttu-id="b0764-501">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="b0764-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-503">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="b0764-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="b0764-504">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b0764-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="b0764-505">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="b0764-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="b0764-506">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="b0764-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="b0764-507">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="b0764-508">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="b0764-509">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="b0764-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="b0764-510">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="b0764-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="b0764-511">Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="b0764-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="b0764-512">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="b0764-512">The scaffolding process:</span></span>

* <span data-ttu-id="b0764-513">Erstellt Razor Pages im Ordner *Pages/Students*:</span><span class="sxs-lookup"><span data-stu-id="b0764-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="b0764-514">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-515">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-516">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-517">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="b0764-518">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="b0764-519">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="b0764-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="b0764-520">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="b0764-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="b0764-521">legt eine Datenbankverbindungszeichenfolge für *appsettings.json* fest</span><span class="sxs-lookup"><span data-stu-id="b0764-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="b0764-522">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="b0764-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-524">In der Datei *appsettings.json* wird die Verbindungszeichenfolge [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) angegeben.</span><span class="sxs-lookup"><span data-stu-id="b0764-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="b0764-525">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b0764-526">Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="b0764-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-528">Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="b0764-528">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="b0764-529">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="b0764-529">Update the database context class</span></span>

<span data-ttu-id="b0764-530">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b0764-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="b0764-531">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b0764-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b0764-532">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b0764-533">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b0764-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b0764-534">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="b0764-535">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="b0764-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b0764-536">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="b0764-536">In EF Core terminology:</span></span>

* <span data-ttu-id="b0764-537">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="b0764-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="b0764-538">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b0764-539">Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen.</span><span class="sxs-lookup"><span data-stu-id="b0764-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="b0764-540">Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="b0764-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="b0764-541">Damit der Razor Pages-Code mit dem neuen DBSet-Namen übereinstimmt, ändern Sie `_context.Student` im gesamten Projekt global in `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="b0764-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="b0764-542">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="b0764-542">There are 8 occurrences.</span></span>

<span data-ttu-id="b0764-543">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="b0764-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="b0764-544">Startup.cs</span></span>

<span data-ttu-id="b0764-545">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b0764-546">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b0764-547">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b0764-548">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="b0764-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b0764-549">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-551">In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b0764-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-553">Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="b0764-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="b0764-554">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b0764-555">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b0764-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="b0764-556">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0764-556">Create the database</span></span>

<span data-ttu-id="b0764-557">Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="b0764-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="b0764-558">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="b0764-559">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="b0764-560">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="b0764-561">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-561">Delete the database.</span></span> <span data-ttu-id="b0764-562">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="b0764-562">Any existing data is lost.</span></span>
* <span data-ttu-id="b0764-563">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="b0764-563">Change the data model.</span></span> <span data-ttu-id="b0764-564">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="b0764-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="b0764-565">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-565">Run the app.</span></span>
* <span data-ttu-id="b0764-566">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="b0764-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="b0764-567">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="b0764-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="b0764-568">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="b0764-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="b0764-569">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b0764-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="b0764-570">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b0764-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="b0764-571">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b0764-572">Testen der App</span><span class="sxs-lookup"><span data-stu-id="b0764-572">Test the app</span></span>

* <span data-ttu-id="b0764-573">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-573">Run the app.</span></span>
* <span data-ttu-id="b0764-574">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b0764-575">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="b0764-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="b0764-576">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0764-576">Seed the database</span></span>

<span data-ttu-id="b0764-577">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="b0764-578">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="b0764-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="b0764-579">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="b0764-580">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="b0764-581">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b0764-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="b0764-582">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="b0764-583">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="b0764-583">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-585">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-587">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="b0764-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="b0764-588">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="b0764-588">Restart the app.</span></span>

* <span data-ttu-id="b0764-589">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="b0764-590">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="b0764-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-592">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b0764-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="b0764-593">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="b0764-594">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="b0764-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="b0764-595">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="b0764-596">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="b0764-597">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b0764-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-599">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="b0764-600">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="b0764-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="b0764-601">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="b0764-601">Asynchronous code</span></span>

<span data-ttu-id="b0764-602">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="b0764-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b0764-603">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b0764-604">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b0764-605">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="b0764-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b0764-606">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="b0764-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b0764-607">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="b0764-608">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="b0764-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b0764-609">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b0764-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b0764-610">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="b0764-611">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b0764-612">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b0764-613">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="b0764-614">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="b0764-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="b0764-615">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="b0764-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b0764-616">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b0764-617">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b0764-618">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b0764-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b0764-619">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="b0764-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b0764-620">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b0764-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="b0764-621">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="b0764-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b0764-622">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="b0764-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b0764-623">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0764-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b0764-624">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="b0764-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="b0764-625">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b0764-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="b0764-626">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b0764-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="b0764-627">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="b0764-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b0764-628">Die Beispiel-Web-App der Contoso University veranschaulicht, wie Sie mit Entity Framework Core (EF Core) und ASP.NET Core eine Razor Pages-App erstellen können.</span><span class="sxs-lookup"><span data-stu-id="b0764-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="b0764-629">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="b0764-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="b0764-630">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="b0764-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="b0764-631">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="b0764-632">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="b0764-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="b0764-633">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b0764-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b0764-634">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="b0764-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="b0764-637">Kenntnisse zu [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="b0764-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="b0764-638">Einsteiger sollten den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit dieser Tutorialreihe beginnen.</span><span class="sxs-lookup"><span data-stu-id="b0764-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="b0764-639">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="b0764-639">Troubleshooting</span></span>

<span data-ttu-id="b0764-640">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="b0764-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="b0764-641">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="b0764-642">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="b0764-642">The Contoso University web app</span></span>

<span data-ttu-id="b0764-643">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="b0764-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="b0764-644">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="b0764-645">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-645">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="b0764-648">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="b0764-649">In diesem Tutorial geht es um EF Core mit Razor Pages, nicht um die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b0764-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="b0764-650">Erstellen der Razor Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="b0764-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-652">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="b0764-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="b0764-653">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="b0764-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="b0764-654">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="b0764-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="b0764-655">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="b0764-656">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="b0764-657">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor Pages-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="b0764-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="b0764-658">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="b0764-660">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="b0764-660">Set up the site style</span></span>

<span data-ttu-id="b0764-661">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="b0764-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="b0764-662">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="b0764-663">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="b0764-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="b0764-664">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="b0764-664">There are three occurrences.</span></span>

* <span data-ttu-id="b0764-665">Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="b0764-665">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="b0764-666">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b0764-666">The changes are highlighted.</span></span> <span data-ttu-id="b0764-667">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="b0764-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="b0764-668">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="b0764-668">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="b0764-669">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="b0764-669">Create the data model</span></span>

<span data-ttu-id="b0764-670">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="b0764-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="b0764-671">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="b0764-671">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="b0764-673">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="b0764-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="b0764-674">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="b0764-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="b0764-675">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="b0764-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="b0764-676">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="b0764-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="b0764-677">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="b0764-678">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="b0764-678">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="b0764-680">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="b0764-680">Create a *Models* folder.</span></span> <span data-ttu-id="b0764-681">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0764-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="b0764-682">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="b0764-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="b0764-683">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="b0764-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="b0764-684">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="b0764-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="b0764-685">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="b0764-686">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="b0764-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="b0764-687">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="b0764-688">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="b0764-689">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="b0764-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="b0764-690">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="b0764-691">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="b0764-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="b0764-692">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="b0764-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="b0764-693">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="b0764-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="b0764-694">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="b0764-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="b0764-695">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="b0764-696">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="b0764-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="b0764-697">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="b0764-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="b0764-698">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="b0764-698">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="b0764-700">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="b0764-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="b0764-701">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="b0764-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="b0764-702">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="b0764-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="b0764-703">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="b0764-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="b0764-704">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="b0764-705">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="b0764-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="b0764-706">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="b0764-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="b0764-707">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="b0764-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="b0764-708">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="b0764-709">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="b0764-710">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="b0764-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="b0764-711">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="b0764-712">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="b0764-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="b0764-713">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="b0764-714">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="b0764-715">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="b0764-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="b0764-716">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="b0764-717">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="b0764-717">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="b0764-719">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:</span><span class="sxs-lookup"><span data-stu-id="b0764-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="b0764-720">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b0764-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="b0764-721">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="b0764-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="b0764-722">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="b0764-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="b0764-723">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="b0764-723">Scaffold the student model</span></span>

<span data-ttu-id="b0764-724">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="b0764-725">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="b0764-726">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b0764-726">Build the project.</span></span>
* <span data-ttu-id="b0764-727">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="b0764-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b0764-729">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-729">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="b0764-730">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="b0764-731">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="b0764-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="b0764-732">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="b0764-733">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="b0764-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="b0764-734">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="b0764-735">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-735">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="b0764-737">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="b0764-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b0764-739">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="b0764-740">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="b0764-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="b0764-741">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="b0764-741">Files created</span></span>

* <span data-ttu-id="b0764-742">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="b0764-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="b0764-743">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="b0764-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="b0764-744">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="b0764-744">File updates</span></span>

* <span data-ttu-id="b0764-745">*Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="b0764-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="b0764-746">*appsettings.json* : Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b0764-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="b0764-747">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="b0764-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="b0764-748">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="b0764-749">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="b0764-750">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="b0764-751">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="b0764-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="b0764-752">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="b0764-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="b0764-753">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="b0764-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="b0764-754">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="b0764-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="b0764-755">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="b0764-756">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="b0764-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="b0764-757">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="b0764-757">Update main</span></span>

<span data-ttu-id="b0764-758">Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="b0764-758">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="b0764-759">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="b0764-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="b0764-760">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="b0764-761">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="b0764-762">Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.</span><span class="sxs-lookup"><span data-stu-id="b0764-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="b0764-763">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="b0764-764">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="b0764-764">If it exists, no action is taken.</span></span> <span data-ttu-id="b0764-765">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="b0764-766">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="b0764-767">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="b0764-768">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="b0764-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="b0764-769">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-769">Delete the DB.</span></span>
* <span data-ttu-id="b0764-770">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="b0764-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="b0764-771">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="b0764-771">Run the app.</span></span>
* <span data-ttu-id="b0764-772">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="b0764-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="b0764-773">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="b0764-774">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="b0764-775">Testen der App</span><span class="sxs-lookup"><span data-stu-id="b0764-775">Test the app</span></span>

<span data-ttu-id="b0764-776">Führen Sie die App aus, und akzeptieren Sie die cookierichtlinie.</span><span class="sxs-lookup"><span data-stu-id="b0764-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="b0764-777">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="b0764-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="b0764-778">Weitere Informationen zur cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="b0764-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="b0764-779">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="b0764-780">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="b0764-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="b0764-781">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="b0764-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="b0764-782">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="b0764-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="b0764-783">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="b0764-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="b0764-784">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="b0764-785">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="b0764-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="b0764-786">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="b0764-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="b0764-787">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="b0764-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="b0764-788">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="b0764-788">In EF Core terminology:</span></span>

* <span data-ttu-id="b0764-789">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="b0764-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="b0764-790">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="b0764-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="b0764-791">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="b0764-792">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="b0764-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="b0764-793">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="b0764-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="b0764-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="b0764-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="b0764-795">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="b0764-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="b0764-796">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b0764-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="b0764-797">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="b0764-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="b0764-798">Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="b0764-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="b0764-799">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="b0764-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="b0764-800">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="b0764-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="b0764-801">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="b0764-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="b0764-802">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="b0764-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="b0764-803">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="b0764-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="b0764-804">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="b0764-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="b0764-805">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="b0764-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="b0764-806">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b0764-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="b0764-807">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="b0764-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="b0764-808">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="b0764-809">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="b0764-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="b0764-810">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="b0764-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="b0764-811">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="b0764-811">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="b0764-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0764-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b0764-813">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="b0764-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="b0764-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b0764-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b0764-815">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="b0764-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="b0764-816">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="b0764-816">View the DB</span></span>

<span data-ttu-id="b0764-817">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="b0764-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="b0764-818">Daher lautet der Name der Datenbank „SchoolContext-{GUID}“.</span><span class="sxs-lookup"><span data-stu-id="b0764-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="b0764-819">Die GUID ist für jeden Benutzer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="b0764-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="b0764-820">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="b0764-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="b0764-821">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="b0764-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="b0764-822">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="b0764-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="b0764-823">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="b0764-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="b0764-824">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="b0764-824">Asynchronous code</span></span>

<span data-ttu-id="b0764-825">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="b0764-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="b0764-826">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="b0764-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="b0764-827">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b0764-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="b0764-828">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="b0764-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="b0764-829">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="b0764-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="b0764-830">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b0764-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="b0764-831">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="b0764-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="b0764-832">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="b0764-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="b0764-833">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="b0764-834">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="b0764-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="b0764-835">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="b0764-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="b0764-836">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="b0764-837">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="b0764-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="b0764-838">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="b0764-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="b0764-839">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="b0764-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="b0764-840">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="b0764-841">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="b0764-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="b0764-842">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="b0764-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="b0764-843">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="b0764-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="b0764-844">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b0764-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="b0764-845">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="b0764-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="b0764-846">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="b0764-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="b0764-847">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b0764-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="b0764-848">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="b0764-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="b0764-849">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="b0764-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="b0764-850">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="b0764-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="b0764-851">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b0764-851">Additional resources</span></span>

* [<span data-ttu-id="b0764-852">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="b0764-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="b0764-853">Nächste</span><span class="sxs-lookup"><span data-stu-id="b0764-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
