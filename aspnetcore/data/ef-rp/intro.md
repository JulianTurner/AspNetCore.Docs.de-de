---
title: ':::no-loc(Razor)::: Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8'
author: rick-anderson
description: 'Informationen zum Erstellen einer :::no-loc(Razor)::: Pages-App mit Entity Framework Core'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365417"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="f74ef-103">:::no-loc(Razor)::: Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8</span><span class="sxs-lookup"><span data-stu-id="f74ef-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="f74ef-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f74ef-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f74ef-105">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [:::no-loc(Razor)::: Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="f74ef-106">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f74ef-107">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f74ef-108">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="f74ef-109">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="f74ef-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="f74ef-110">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="f74ef-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f74ef-111">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f74ef-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f74ef-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f74ef-112">Prerequisites</span></span>

* <span data-ttu-id="f74ef-113">Wenn Sie noch nicht mit :::no-loc(Razor)::: Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="f74ef-116">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="f74ef-116">Database engines</span></span>

<span data-ttu-id="f74ef-117">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f74ef-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="f74ef-118">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="f74ef-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="f74ef-119">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="f74ef-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f74ef-120">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f74ef-120">Troubleshooting</span></span>

<span data-ttu-id="f74ef-121">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f74ef-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f74ef-122">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="f74ef-123">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="f74ef-123">The sample app</span></span>

<span data-ttu-id="f74ef-124">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="f74ef-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="f74ef-125">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f74ef-126">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-126">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="f74ef-129">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="f74ef-130">Das Tutorial konzentriert sich auf die Verwendung von EF Core mit ASP.NET Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f74ef-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="f74ef-131">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f74ef-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-133">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="f74ef-134">Klicken Sie auf **ASP.NET Core-Webanwendung**  > **WEITER**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="f74ef-135">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="f74ef-136">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="f74ef-137">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-137">Select **Create**.</span></span>
* <span data-ttu-id="f74ef-138">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 5.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-140">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="f74ef-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="f74ef-141">Führen Sie die folgenden Befehle aus, um ein :::no-loc(Razor)::: Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="f74ef-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f74ef-142">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="f74ef-142">Set up the site style</span></span>

<span data-ttu-id="f74ef-143">Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *Pages/Shared/_Layout.cshtml* ein: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="f74ef-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="f74ef-144">Mit der Layoutdatei werden die Kopfzeile, die Fußzeile und das Menü der Website festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="f74ef-145">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="f74ef-146">Jedes Vorkommen von „ContosoUniversity“ wird in „Contoso University“ geändert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f74ef-147">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="f74ef-147">There are three occurrences.</span></span>
* <span data-ttu-id="f74ef-148">Die Menüeinträge **Home** und **Privacy** werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="f74ef-149">Es werden Einträge für **About** , **Students** , **Courses** , **Instructors** und **Departmens** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="f74ef-150">Ersetzen Sie den Inhalt der Datei *Pages/Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="f74ef-151">Der obige Code ersetzt den Text über ASP.NET Core durch Text über diese App.</span><span class="sxs-lookup"><span data-stu-id="f74ef-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="f74ef-152">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="f74ef-153">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="f74ef-153">The data model</span></span>

<span data-ttu-id="f74ef-154">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-154">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="f74ef-156">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="f74ef-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="f74ef-157">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="f74ef-157">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="f74ef-159">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="f74ef-160">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="f74ef-161">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="f74ef-162">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="f74ef-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f74ef-163">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="f74ef-164">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="f74ef-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="f74ef-165">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f74ef-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f74ef-166">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="f74ef-167">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="f74ef-168">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="f74ef-169">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="f74ef-170">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="f74ef-171">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="f74ef-172">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="f74ef-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="f74ef-173">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="f74ef-174">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="f74ef-175">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="f74ef-176">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="f74ef-176">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="f74ef-178">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="f74ef-179">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="f74ef-180">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="f74ef-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="f74ef-181">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="f74ef-182">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="f74ef-183">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f74ef-184">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="f74ef-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="f74ef-185">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f74ef-186">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f74ef-187">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="f74ef-188">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f74ef-189">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f74ef-190">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f74ef-191">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f74ef-192">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f74ef-193">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="f74ef-194">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="f74ef-194">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="f74ef-196">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="f74ef-197">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f74ef-198">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f74ef-199">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="f74ef-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="f74ef-200">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="f74ef-201">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="f74ef-201">Scaffold Student pages</span></span>

<span data-ttu-id="f74ef-202">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="f74ef-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="f74ef-203">Eine EF Core `DbContext`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f74ef-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="f74ef-204">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="f74ef-205">Diese Klasse wird von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="f74ef-206">:::no-loc(Razor)::: Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-208">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="f74ef-209">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students* , und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f74ef-210">Im Dialogfeld **Add New Scaffold Item** (Neues Gerüstelement hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="f74ef-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="f74ef-211">Wählen Sie auf der linken Registerkarte **Installiert > Häufig > :::no-loc(Razor):::-Seiten** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="f74ef-212">Wählen Sie **:::no-loc(Razor):::-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="f74ef-213">Gehen Sie im Dialogfeld **:::no-loc(Razor)::: Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="f74ef-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="f74ef-214">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="f74ef-215">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="f74ef-216">Ändern Sie den Datenkontextnamen so, dass er auf `SchoolContext` anstatt auf `ContosoUniversityContext` endet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="f74ef-217">Der aktualisierte Kontextname lautet `ContosoUniversity.Data.SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="f74ef-218">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-218">Select **Add**.</span></span>

<span data-ttu-id="f74ef-219">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="f74ef-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-221">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="f74ef-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="f74ef-222">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f74ef-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="f74ef-223">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="f74ef-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="f74ef-224">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="f74ef-225">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="f74ef-226">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="f74ef-227">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="f74ef-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="f74ef-228">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="f74ef-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="f74ef-229">Wenn der vorherige Schritt zu einem Fehler führt, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="f74ef-230">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="f74ef-230">The scaffolding process:</span></span>

* <span data-ttu-id="f74ef-231">Erstellt :::no-loc(Razor)::: Pages im Ordner *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="f74ef-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="f74ef-232">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-233">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-234">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-235">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-236">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="f74ef-237">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="f74ef-238">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="f74ef-239">legt eine Datenbankverbindungszeichenfolge für *:::no-loc(appsettings.json):::* fest</span><span class="sxs-lookup"><span data-stu-id="f74ef-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="f74ef-240">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="f74ef-240">Database connection string</span></span>

<span data-ttu-id="f74ef-241">Das Gerüstbautool generiert eine Verbindungszeichenfolge in der Datei *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f74ef-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-243">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an:</span><span class="sxs-lookup"><span data-stu-id="f74ef-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="f74ef-244">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f74ef-245">Standardmäßig erstellt LocalDB *MDF* -Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-247">Kürzen Sie die SQLite-Verbindungszeichenfolge auf *CU.db* :</span><span class="sxs-lookup"><span data-stu-id="f74ef-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="f74ef-248">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="f74ef-248">Update the database context class</span></span>

<span data-ttu-id="f74ef-249">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="f74ef-250">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f74ef-251">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f74ef-252">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f74ef-253">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="f74ef-254">Der obige Code ändert den Singular `DbSet<Student> Student` in den Plural `DbSet<Student> Students`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="f74ef-255">Nehmen Sie eine globale Änderung vor, damit der Code der :::no-loc(Razor):::-Seite mit dem neuen Namen `DBSet` übereinstimmt: `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="f74ef-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="f74ef-256">in `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="f74ef-256">to: `_context.Students.`</span></span>

<span data-ttu-id="f74ef-257">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-257">There are 8 occurrences.</span></span>

<span data-ttu-id="f74ef-258">Da eine Entitätenmenge mehrere Entitäten enthält, bevorzugen viele Entwickler den Plural für `DBSet`-Eigenschaftennamen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="f74ef-259">Der hervorgehobene Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-259">The highlighted code:</span></span>

* <span data-ttu-id="f74ef-260">Erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="f74ef-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f74ef-261">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="f74ef-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="f74ef-262">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="f74ef-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="f74ef-263">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="f74ef-264">Ruft <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span><span class="sxs-lookup"><span data-stu-id="f74ef-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="f74ef-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="f74ef-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="f74ef-266">Wird aufgerufen, wenn `SchoolContext` initialisiert wurde, bevor jedoch das Modell gesperrt und zum Initialisieren des Kontexts verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="f74ef-267">Ist erforderlich, da die `Student`-Entität später im Tutorial Verweise auf die anderen Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="f74ef-268">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="f74ef-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f74ef-269">Startup.cs</span></span>

<span data-ttu-id="f74ef-270">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f74ef-271">Dienste (z. B. `SchoolContext`) werden per Abhängigkeitsinjektion während des App-Starts registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="f74ef-272">Komponenten, die diese Dienste erfordern (z. B. :::no-loc(Razor):::-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="f74ef-273">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f74ef-274">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-276">Die folgenden hervorgehobenen Zeilen wurden beim Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-278">Stellen Sie sicher, dass der beim Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="f74ef-279">Weitere Informationen zur Verwendung einer Produktionsdatenbank finden Sie unter [Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).</span><span class="sxs-lookup"><span data-stu-id="f74ef-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="f74ef-280">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f74ef-281">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f74ef-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="f74ef-282">Hinzufügen des Filters für die Datenbankausnahme</span><span class="sxs-lookup"><span data-stu-id="f74ef-282">Add the database exception filter</span></span>

<span data-ttu-id="f74ef-283">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> in `ConfigureServices` hinzu, wie im folgenden Code dargestellt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="f74ef-285">Fügen Sie das NuGet-Paket [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) hinzu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="f74ef-286">Geben Sie Folgendes in die Konsole des Paket-Managers ein, um das NuGet-Paket hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="f74ef-288">Das NuGet-Paket `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` stellt ASP.NET Core-Middleware für Entity Framework Core-Fehlerseiten bereit.</span><span class="sxs-lookup"><span data-stu-id="f74ef-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="f74ef-289">Diese Middleware hilft bei der Erkennung und Diagnose von Fehlern bei Entity Framework Core-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="f74ef-290">`AddDatabaseDeveloperPageExceptionFilter` bietet hilfreiche Fehlerinformationen in der [Entwicklungsumgebung](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="f74ef-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f74ef-291">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="f74ef-291">Create the database</span></span>

<span data-ttu-id="f74ef-292">Aktualisieren Sie *Program.cs* , um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="f74ef-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="f74ef-293">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="f74ef-294">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="f74ef-295">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="f74ef-296">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-296">Delete the database.</span></span> <span data-ttu-id="f74ef-297">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-297">Any existing data is lost.</span></span>
* <span data-ttu-id="f74ef-298">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="f74ef-298">Change the data model.</span></span> <span data-ttu-id="f74ef-299">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="f74ef-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="f74ef-300">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-300">Run the app.</span></span>
* <span data-ttu-id="f74ef-301">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="f74ef-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="f74ef-302">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="f74ef-303">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="f74ef-304">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="f74ef-305">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="f74ef-306">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f74ef-307">Testen der App</span><span class="sxs-lookup"><span data-stu-id="f74ef-307">Test the app</span></span>

* <span data-ttu-id="f74ef-308">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-308">Run the app.</span></span>
* <span data-ttu-id="f74ef-309">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f74ef-310">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="f74ef-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="f74ef-311">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="f74ef-311">Seed the database</span></span>

<span data-ttu-id="f74ef-312">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="f74ef-313">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="f74ef-314">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="f74ef-315">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="f74ef-316">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="f74ef-317">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="f74ef-318">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="f74ef-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-320">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="f74ef-321">Antworten Sie mit `Y`, um die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-323">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="f74ef-324">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-324">Restart the app.</span></span>
* <span data-ttu-id="f74ef-325">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="f74ef-326">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="f74ef-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-328">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f74ef-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="f74ef-329">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="f74ef-330">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="f74ef-331">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="f74ef-332">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student** , und klicken Sie auf **Daten anzeigen** , um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="f74ef-333">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student** , und klicken Sie auf **Code anzeigen** , um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-335">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="f74ef-336">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f74ef-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="f74ef-337">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-337">Asynchronous code</span></span>

<span data-ttu-id="f74ef-338">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="f74ef-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f74ef-339">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f74ef-340">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f74ef-341">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f74ef-342">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="f74ef-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f74ef-343">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="f74ef-344">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f74ef-345">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f74ef-346">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="f74ef-347">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f74ef-348">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f74ef-349">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="f74ef-350">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="f74ef-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="f74ef-351">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f74ef-352">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f74ef-353">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f74ef-354">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="f74ef-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f74ef-355">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="f74ef-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f74ef-356">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="f74ef-357">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f74ef-358">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f74ef-359">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f74ef-360">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="f74ef-361">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f74ef-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="f74ef-362">Überlegungen zur Leistung</span><span class="sxs-lookup"><span data-stu-id="f74ef-362">Performance considerations</span></span>

<span data-ttu-id="f74ef-363">Im Allgemeinen sollte eine Webseite keine beliebige Anzahl von Zeilen laden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="f74ef-364">Eine Abfrage sollte Paging oder ein Verfahren zum Einschränken verwenden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="f74ef-365">Die vorherige Abfrage könnte z. B. `Take` verwenden, um die Anzahl zurückgegebener Zeilen einzuschränken:</span><span class="sxs-lookup"><span data-stu-id="f74ef-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f74ef-366">Durch das Auflisten einer großen Tabelle in einer Sicht kann eine teilweise konstruierte HTTP 200-Antwort zurückgegeben werden, wenn während des Durchlaufens eine Datenbankausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="f74ef-367">Der Standardwert von <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> ist 1.024.</span><span class="sxs-lookup"><span data-stu-id="f74ef-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="f74ef-368">Der folgende Code legt `MaxModelBindingCollectionSize` fest.</span><span class="sxs-lookup"><span data-stu-id="f74ef-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f74ef-369">Das Paging wird später im Tutorial beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f74ef-370">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f74ef-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f74ef-371">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="f74ef-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="f74ef-372">Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [:::no-loc(Razor)::: Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="f74ef-373">In den Tutorials wird eine Website für eine fiktive Contoso University erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f74ef-374">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f74ef-375">In diesem Tutorial wird der Code-First-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="f74ef-376">Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="f74ef-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="f74ef-377">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="f74ef-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f74ef-378">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f74ef-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f74ef-379">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f74ef-379">Prerequisites</span></span>

* <span data-ttu-id="f74ef-380">Wenn Sie noch nicht mit :::no-loc(Razor)::: Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="f74ef-383">Datenbank-Engines</span><span class="sxs-lookup"><span data-stu-id="f74ef-383">Database engines</span></span>

<span data-ttu-id="f74ef-384">Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f74ef-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="f74ef-385">In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="f74ef-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="f74ef-386">Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="f74ef-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f74ef-387">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f74ef-387">Troubleshooting</span></span>

<span data-ttu-id="f74ef-388">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="f74ef-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f74ef-389">Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="f74ef-390">Die Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="f74ef-390">The sample app</span></span>

<span data-ttu-id="f74ef-391">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="f74ef-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="f74ef-392">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f74ef-393">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-393">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

<span data-ttu-id="f74ef-396">Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="f74ef-397">Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f74ef-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="f74ef-398">Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="f74ef-399">Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials.</span><span class="sxs-lookup"><span data-stu-id="f74ef-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="f74ef-400">Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-402">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f74ef-403">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-403">Build the project.</span></span>
* <span data-ttu-id="f74ef-404">Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="f74ef-405">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-407">So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="f74ef-408">Löschen Sie *ContosoUniversity.csproj* , und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.</span><span class="sxs-lookup"><span data-stu-id="f74ef-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="f74ef-409">Kommentieren Sie in *Program.cs* `#define Startup` aus, sodass `StartupSQLite` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="f74ef-410">Löschen Sie *appSettings.json* , und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.</span><span class="sxs-lookup"><span data-stu-id="f74ef-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="f74ef-411">Löschen Sie den Ordner *Migrations* , und benennen Sie *MigrationsSQL* in *Migrations* um.</span><span class="sxs-lookup"><span data-stu-id="f74ef-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="f74ef-412">Führen Sie eine globale Suche nach `#if SQLiteVersion` aus, und entfernen Sie `#if SQLiteVersion` sowie die zugehörige `#endif`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="f74ef-413">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-413">Build the project.</span></span>
* <span data-ttu-id="f74ef-414">Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="f74ef-415">Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="f74ef-416">Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="f74ef-417">Erstellen des Web-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f74ef-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-419">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f74ef-420">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="f74ef-421">Geben Sie dem Projekt den Namen *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="f74ef-422">Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="f74ef-423">Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-425">Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="f74ef-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="f74ef-426">Führen Sie die folgenden Befehle aus, um ein :::no-loc(Razor)::: Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:</span><span class="sxs-lookup"><span data-stu-id="f74ef-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f74ef-427">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="f74ef-427">Set up the site style</span></span>

<span data-ttu-id="f74ef-428">Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:</span><span class="sxs-lookup"><span data-stu-id="f74ef-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="f74ef-429">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="f74ef-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f74ef-430">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="f74ef-430">There are three occurrences.</span></span>

* <span data-ttu-id="f74ef-431">Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="f74ef-432">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="f74ef-433">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="f74ef-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="f74ef-434">Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="f74ef-435">Das Datenmodell</span><span class="sxs-lookup"><span data-stu-id="f74ef-435">The data model</span></span>

<span data-ttu-id="f74ef-436">In den folgenden Abschnitten wird ein Datenmodell erstellt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-436">The following sections create a data model:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="f74ef-438">Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.</span><span class="sxs-lookup"><span data-stu-id="f74ef-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="f74ef-439">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="f74ef-439">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* <span data-ttu-id="f74ef-441">Erstellen Sie im Projektordner den Ordner *Models*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="f74ef-442">Erstellen Sie *Models/Student.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="f74ef-443">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="f74ef-444">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="f74ef-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f74ef-445">Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="f74ef-446">Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="f74ef-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="f74ef-447">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f74ef-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f74ef-448">Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="f74ef-449">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="f74ef-450">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="f74ef-451">In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="f74ef-452">Angenommen, eine Student-Zeile weist die ID=1 auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="f74ef-453">Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="f74ef-454">StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle.</span><span class="sxs-lookup"><span data-stu-id="f74ef-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="f74ef-455">Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="f74ef-456">Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="f74ef-457">Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="f74ef-458">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="f74ef-458">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="f74ef-460">Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="f74ef-461">Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="f74ef-462">Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich.</span><span class="sxs-lookup"><span data-stu-id="f74ef-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="f74ef-463">In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="f74ef-464">Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="f74ef-465">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f74ef-466">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="f74ef-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="f74ef-467">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f74ef-468">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f74ef-469">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="f74ef-470">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f74ef-471">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f74ef-472">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f74ef-473">Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f74ef-474">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f74ef-475">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="f74ef-476">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="f74ef-476">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="f74ef-478">Erstellen Sie *Models/Course.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="f74ef-479">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f74ef-480">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f74ef-481">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="f74ef-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="f74ef-482">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="f74ef-483">Gerüstbau der Student-Seiten</span><span class="sxs-lookup"><span data-stu-id="f74ef-483">Scaffold Student pages</span></span>

<span data-ttu-id="f74ef-484">In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:</span><span class="sxs-lookup"><span data-stu-id="f74ef-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="f74ef-485">Eine EF Core *context* -Klasse.</span><span class="sxs-lookup"><span data-stu-id="f74ef-485">An EF Core *context* class.</span></span> <span data-ttu-id="f74ef-486">„context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="f74ef-487">Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="f74ef-488">:::no-loc(Razor)::: Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-490">Erstellen Sie einen Ordner *Students* im Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="f74ef-491">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students* , und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f74ef-492">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **:::no-loc(Razor)::: Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="f74ef-493">Gehen Sie im Dialogfeld **:::no-loc(Razor)::: Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="f74ef-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="f74ef-494">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="f74ef-495">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="f74ef-496">Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="f74ef-497">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-497">Select **Add**.</span></span>

<span data-ttu-id="f74ef-498">Die folgenden Pakete werden automatisch installiert:</span><span class="sxs-lookup"><span data-stu-id="f74ef-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-500">Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="f74ef-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="f74ef-501">Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f74ef-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="f74ef-502">Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.</span><span class="sxs-lookup"><span data-stu-id="f74ef-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="f74ef-503">Erstellen Sie einen Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="f74ef-504">Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="f74ef-505">Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="f74ef-506">**Unter Windows**</span><span class="sxs-lookup"><span data-stu-id="f74ef-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="f74ef-507">**Unter macOS oder Linux**</span><span class="sxs-lookup"><span data-stu-id="f74ef-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="f74ef-508">Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="f74ef-509">Der Gerüstbauprozess:</span><span class="sxs-lookup"><span data-stu-id="f74ef-509">The scaffolding process:</span></span>

* <span data-ttu-id="f74ef-510">Erstellt :::no-loc(Razor)::: Pages im Ordner *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="f74ef-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="f74ef-511">*Create.cshtml* und *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-512">*Create.cshtml* und *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-513">*Details.cshtml* und *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-514">*Edit.cshtml* und *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="f74ef-515">*Index.cshtml* und *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="f74ef-516">Erstellt *Data/SchoolContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="f74ef-517">Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="f74ef-518">legt eine Datenbankverbindungszeichenfolge für *:::no-loc(appsettings.json):::* fest</span><span class="sxs-lookup"><span data-stu-id="f74ef-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="f74ef-519">Datenbankverbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="f74ef-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-521">In der Datei *:::no-loc(appsettings.json):::* wird die Verbindungszeichenfolge [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) angegeben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="f74ef-522">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f74ef-523">Standardmäßig erstellt LocalDB *MDF* -Dateien im Verzeichnis `C:/Users/<user>`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-525">Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="f74ef-526">Aktualisieren der Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="f74ef-526">Update the database context class</span></span>

<span data-ttu-id="f74ef-527">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="f74ef-528">Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f74ef-529">Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f74ef-530">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f74ef-531">Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="f74ef-532">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="f74ef-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f74ef-533">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="f74ef-533">In EF Core terminology:</span></span>

* <span data-ttu-id="f74ef-534">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="f74ef-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="f74ef-535">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f74ef-536">Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="f74ef-537">Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="f74ef-538">Damit der :::no-loc(Razor)::: Pages-Code mit dem neuen DBSet-Namen übereinstimmt, ändern Sie `_context.Student` im gesamten Projekt global in `_context.Students`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="f74ef-539">Es gibt 8 Vorkommen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-539">There are 8 occurrences.</span></span>

<span data-ttu-id="f74ef-540">Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="f74ef-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="f74ef-541">Startup.cs</span></span>

<span data-ttu-id="f74ef-542">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f74ef-543">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f74ef-544">Komponenten, die diese Dienste erfordern (z. B. :::no-loc(Razor)::: Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f74ef-545">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f74ef-546">Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-548">In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-550">Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="f74ef-551">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f74ef-552">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f74ef-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="f74ef-553">Erstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="f74ef-553">Create the database</span></span>

<span data-ttu-id="f74ef-554">Aktualisieren Sie *Program.cs* , um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="f74ef-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="f74ef-555">Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="f74ef-556">Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="f74ef-557">`EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="f74ef-558">Löschen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-558">Delete the database.</span></span> <span data-ttu-id="f74ef-559">Alle vorhandenen Daten gehen verloren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-559">Any existing data is lost.</span></span>
* <span data-ttu-id="f74ef-560">Ändern des Datenmodells.</span><span class="sxs-lookup"><span data-stu-id="f74ef-560">Change the data model.</span></span> <span data-ttu-id="f74ef-561">Beispielsweise Hinzufügen eines `EmailAddress`-Felds.</span><span class="sxs-lookup"><span data-stu-id="f74ef-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="f74ef-562">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-562">Run the app.</span></span>
* <span data-ttu-id="f74ef-563">`EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.</span><span class="sxs-lookup"><span data-stu-id="f74ef-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="f74ef-564">Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="f74ef-565">Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="f74ef-566">Wenn dies der Fall ist, verwenden Sie Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="f74ef-567">Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="f74ef-568">Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f74ef-569">Testen der App</span><span class="sxs-lookup"><span data-stu-id="f74ef-569">Test the app</span></span>

* <span data-ttu-id="f74ef-570">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-570">Run the app.</span></span>
* <span data-ttu-id="f74ef-571">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f74ef-572">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="f74ef-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="f74ef-573">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="f74ef-573">Seed the database</span></span>

<span data-ttu-id="f74ef-574">Die `EnsureCreated`-Methode erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="f74ef-575">In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="f74ef-576">Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="f74ef-577">Der Code überprüft, ob Studenten in der Datenbank vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="f74ef-578">Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="f74ef-579">Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="f74ef-580">Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:</span><span class="sxs-lookup"><span data-stu-id="f74ef-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-582">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-584">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="f74ef-585">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-585">Restart the app.</span></span>

* <span data-ttu-id="f74ef-586">Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="f74ef-587">Zeigen Sie die Datenbank an</span><span class="sxs-lookup"><span data-stu-id="f74ef-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-589">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f74ef-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="f74ef-590">Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="f74ef-591">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="f74ef-592">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="f74ef-593">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student** , und klicken Sie auf **Daten anzeigen** , um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="f74ef-594">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student** , und klicken Sie auf **Code anzeigen** , um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-596">Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="f74ef-597">Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f74ef-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="f74ef-598">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-598">Asynchronous code</span></span>

<span data-ttu-id="f74ef-599">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="f74ef-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f74ef-600">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f74ef-601">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f74ef-602">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f74ef-603">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="f74ef-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f74ef-604">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="f74ef-605">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f74ef-606">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f74ef-607">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="f74ef-608">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f74ef-609">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f74ef-610">Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="f74ef-611">Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.</span><span class="sxs-lookup"><span data-stu-id="f74ef-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="f74ef-612">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f74ef-613">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f74ef-614">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f74ef-615">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="f74ef-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f74ef-616">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="f74ef-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f74ef-617">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="f74ef-618">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f74ef-619">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f74ef-620">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f74ef-621">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="f74ef-622">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f74ef-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f74ef-623">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f74ef-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="f74ef-624">Nächstes Tutorial</span><span class="sxs-lookup"><span data-stu-id="f74ef-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f74ef-625">Die Beispiel-Web-App der Contoso University veranschaulicht, wie Sie mit Entity Framework Core (EF Core) und ASP.NET Core eine :::no-loc(Razor)::: Pages-App erstellen können.</span><span class="sxs-lookup"><span data-stu-id="f74ef-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="f74ef-626">Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University.</span><span class="sxs-lookup"><span data-stu-id="f74ef-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="f74ef-627">Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="f74ef-628">Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="f74ef-629">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="f74ef-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="f74ef-630">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f74ef-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f74ef-631">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f74ef-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="f74ef-634">Kenntnisse zu [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="f74ef-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="f74ef-635">Einsteiger sollten den Artikel [Erste Schritte mit :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit dieser Tutorialreihe beginnen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f74ef-636">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="f74ef-636">Troubleshooting</span></span>

<span data-ttu-id="f74ef-637">Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="f74ef-638">Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="f74ef-639">Die Web-App der Contoso University</span><span class="sxs-lookup"><span data-stu-id="f74ef-639">The Contoso University web app</span></span>

<span data-ttu-id="f74ef-640">Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.</span><span class="sxs-lookup"><span data-stu-id="f74ef-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="f74ef-641">Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="f74ef-642">Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-642">Here are a few of the screens created in the tutorial.</span></span>

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

<span data-ttu-id="f74ef-645">Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="f74ef-646">In diesem Tutorial geht es um EF Core mit :::no-loc(Razor)::: Pages, nicht um die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="f74ef-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="f74ef-647">Erstellen der :::no-loc(Razor)::: Pages-Web-App „ContosoUniversity“</span><span class="sxs-lookup"><span data-stu-id="f74ef-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-649">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="f74ef-650">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="f74ef-651">Geben Sie dem Projekt den Namen **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="f74ef-652">Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="f74ef-653">Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="f74ef-654">Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer :::no-loc(Razor)::: Pages-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="f74ef-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="f74ef-655">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="f74ef-657">Einrichten des Websitestils</span><span class="sxs-lookup"><span data-stu-id="f74ef-657">Set up the site style</span></span>

<span data-ttu-id="f74ef-658">Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="f74ef-659">Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="f74ef-660">Ändern Sie jedes „ContosoUniversity“ in „Contoso University“.</span><span class="sxs-lookup"><span data-stu-id="f74ef-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="f74ef-661">Diese Begriffskombination kommt dreimal vor.</span><span class="sxs-lookup"><span data-stu-id="f74ef-661">There are three occurrences.</span></span>

* <span data-ttu-id="f74ef-662">Fügen Sie Menüeinträge für **Studenten** , **Kurse** , **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="f74ef-663">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-663">The changes are highlighted.</span></span> <span data-ttu-id="f74ef-664">(Es wird *nicht* das gesamte Markup angezeigt.)</span><span class="sxs-lookup"><span data-stu-id="f74ef-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="f74ef-665">Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:</span><span class="sxs-lookup"><span data-stu-id="f74ef-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="f74ef-666">Erstellen des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="f74ef-666">Create the data model</span></span>

<span data-ttu-id="f74ef-667">Erstellen Sie Entitätsklassen für die Contoso University-App.</span><span class="sxs-lookup"><span data-stu-id="f74ef-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="f74ef-668">Beginnen Sie mit dem folgenden drei Entitäten:</span><span class="sxs-lookup"><span data-stu-id="f74ef-668">Start with the following three entities:</span></span>

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

<span data-ttu-id="f74ef-670">Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="f74ef-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="f74ef-671">Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität.</span><span class="sxs-lookup"><span data-stu-id="f74ef-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="f74ef-672">Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="f74ef-673">Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="f74ef-674">In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="f74ef-675">Die Entität „Student“</span><span class="sxs-lookup"><span data-stu-id="f74ef-675">The Student entity</span></span>

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

<span data-ttu-id="f74ef-677">Erstellen Sie einen Ordner *Models* (Modelle).</span><span class="sxs-lookup"><span data-stu-id="f74ef-677">Create a *Models* folder.</span></span> <span data-ttu-id="f74ef-678">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs* :</span><span class="sxs-lookup"><span data-stu-id="f74ef-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="f74ef-679">Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="f74ef-680">Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="f74ef-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="f74ef-681">In `classnameID` ist `classname` der Name der Klasse.</span><span class="sxs-lookup"><span data-stu-id="f74ef-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="f74ef-682">Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="f74ef-683">Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="f74ef-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="f74ef-684">Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="f74ef-685">In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="f74ef-686">Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="f74ef-687">Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="f74ef-688">Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="f74ef-689">Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1.</span><span class="sxs-lookup"><span data-stu-id="f74ef-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="f74ef-690">Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="f74ef-691">Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln.</span><span class="sxs-lookup"><span data-stu-id="f74ef-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="f74ef-692">`ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="f74ef-693">Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="f74ef-694">Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="f74ef-695">Die Entität „Enrollment“</span><span class="sxs-lookup"><span data-stu-id="f74ef-695">The Enrollment entity</span></span>

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

<span data-ttu-id="f74ef-697">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs* -Datei:</span><span class="sxs-lookup"><span data-stu-id="f74ef-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="f74ef-698">Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="f74ef-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="f74ef-699">Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="f74ef-700">In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell.</span><span class="sxs-lookup"><span data-stu-id="f74ef-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="f74ef-701">In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="f74ef-702">Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="f74ef-703">Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="f74ef-704">Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.</span><span class="sxs-lookup"><span data-stu-id="f74ef-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="f74ef-705">Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="f74ef-706">Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="f74ef-707">Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.</span><span class="sxs-lookup"><span data-stu-id="f74ef-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="f74ef-708">Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="f74ef-709">Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="f74ef-710">Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="f74ef-711">Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="f74ef-712">Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben.</span><span class="sxs-lookup"><span data-stu-id="f74ef-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="f74ef-713">Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="f74ef-714">Die Entität „Course“</span><span class="sxs-lookup"><span data-stu-id="f74ef-714">The Course entity</span></span>

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

<span data-ttu-id="f74ef-716">Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs* -Datei:</span><span class="sxs-lookup"><span data-stu-id="f74ef-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="f74ef-717">Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f74ef-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="f74ef-718">`Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="f74ef-719">Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.</span><span class="sxs-lookup"><span data-stu-id="f74ef-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="f74ef-720">Erstellen des Gerüsts für das Studentenmodell</span><span class="sxs-lookup"><span data-stu-id="f74ef-720">Scaffold the student model</span></span>

<span data-ttu-id="f74ef-721">In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="f74ef-722">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="f74ef-723">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-723">Build the project.</span></span>
* <span data-ttu-id="f74ef-724">Erstellen Sie den Ordner *Pages/Students*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f74ef-726">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students* , und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f74ef-727">Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **:::no-loc(Razor)::: Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="f74ef-728">Vervollständigen Sie das Dialogfeld **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="f74ef-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="f74ef-729">Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="f74ef-730">Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="f74ef-731">Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="f74ef-732">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-732">Select **Add**.</span></span>

![CRUD-Dialogfeld](intro/_static/s1.png)

<span data-ttu-id="f74ef-734">Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).</span><span class="sxs-lookup"><span data-stu-id="f74ef-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f74ef-736">Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="f74ef-737">Der Gerüstprozess hat folgende Dateien erstellt und geändert:</span><span class="sxs-lookup"><span data-stu-id="f74ef-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="f74ef-738">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="f74ef-738">Files created</span></span>

* <span data-ttu-id="f74ef-739">*Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).</span><span class="sxs-lookup"><span data-stu-id="f74ef-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="f74ef-740">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="f74ef-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="f74ef-741">Dateiupdates</span><span class="sxs-lookup"><span data-stu-id="f74ef-741">File updates</span></span>

* <span data-ttu-id="f74ef-742">*Startup.cs* : Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="f74ef-743">*:::no-loc(appsettings.json):::* : Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="f74ef-744">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="f74ef-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="f74ef-745">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f74ef-746">Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="f74ef-747">Komponenten, die diese Dienste erfordern (z. B. :::no-loc(Razor)::: Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="f74ef-748">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="f74ef-749">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="f74ef-750">Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="f74ef-751">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="f74ef-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="f74ef-752">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="f74ef-753">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="f74ef-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="f74ef-754">Aktualisieren der Main-Methode</span><span class="sxs-lookup"><span data-stu-id="f74ef-754">Update main</span></span>

<span data-ttu-id="f74ef-755">Ändern Sie in der *Program.cs* -Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="f74ef-756">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="f74ef-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="f74ef-757">Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="f74ef-758">Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="f74ef-759">Der folgende Code zeigt die aktualisierte *Program.cs* -Datei.</span><span class="sxs-lookup"><span data-stu-id="f74ef-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="f74ef-760">`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="f74ef-761">Wenn sie vorhanden ist, werden keine Aktionen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-761">If it exists, no action is taken.</span></span> <span data-ttu-id="f74ef-762">Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="f74ef-763">`EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="f74ef-764">Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="f74ef-765">`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:</span><span class="sxs-lookup"><span data-stu-id="f74ef-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="f74ef-766">Löschen Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-766">Delete the DB.</span></span>
* <span data-ttu-id="f74ef-767">Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.</span><span class="sxs-lookup"><span data-stu-id="f74ef-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="f74ef-768">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="f74ef-768">Run the app.</span></span>
* <span data-ttu-id="f74ef-769">Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="f74ef-770">`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="f74ef-771">Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="f74ef-772">Testen der App</span><span class="sxs-lookup"><span data-stu-id="f74ef-772">Test the app</span></span>

<span data-ttu-id="f74ef-773">Führen Sie die App aus, und akzeptieren Sie die :::no-loc(cookie):::richtlinie.</span><span class="sxs-lookup"><span data-stu-id="f74ef-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="f74ef-774">Diese App bewahrt keine personenbezogenen Informationen auf.</span><span class="sxs-lookup"><span data-stu-id="f74ef-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="f74ef-775">Weitere Informationen zur :::no-loc(cookie):::richtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="f74ef-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="f74ef-776">Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="f74ef-777">Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).</span><span class="sxs-lookup"><span data-stu-id="f74ef-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="f74ef-778">Untersuchen des Datenbankkontexts „SchoolContext“</span><span class="sxs-lookup"><span data-stu-id="f74ef-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="f74ef-779">Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="f74ef-780">Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="f74ef-781">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="f74ef-782">In diesem Projekt heißt die Klasse `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="f74ef-783">Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="f74ef-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="f74ef-784">Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="f74ef-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="f74ef-785">Das heißt für Entity Framework Core:</span><span class="sxs-lookup"><span data-stu-id="f74ef-785">In EF Core terminology:</span></span>

* <span data-ttu-id="f74ef-786">Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="f74ef-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="f74ef-787">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="f74ef-788">`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="f74ef-789">Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="f74ef-790">Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.</span><span class="sxs-lookup"><span data-stu-id="f74ef-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="f74ef-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="f74ef-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="f74ef-792">Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an.</span><span class="sxs-lookup"><span data-stu-id="f74ef-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="f74ef-793">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="f74ef-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="f74ef-794">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="f74ef-795">Standardmäßig erstellt LocalDB *.mdf* -Datenbankdateien im `C:/Users/<user>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="f74ef-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="f74ef-796">Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten</span><span class="sxs-lookup"><span data-stu-id="f74ef-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="f74ef-797">Entity Framework Core erstellt eine leere Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f74ef-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="f74ef-798">In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="f74ef-799">Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs* , und fügen Sie den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="f74ef-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="f74ef-800">Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="f74ef-801">`Models` entspricht dem vom Gerüst generierten Code.</span><span class="sxs-lookup"><span data-stu-id="f74ef-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="f74ef-802">Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="f74ef-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="f74ef-803">Der Code überprüft, ob Studenten in der Datenbank enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="f74ef-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="f74ef-804">Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="f74ef-805">Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="f74ef-806">Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="f74ef-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="f74ef-807">Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="f74ef-808">Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="f74ef-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f74ef-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f74ef-810">Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="f74ef-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="f74ef-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="f74ef-812">Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.</span><span class="sxs-lookup"><span data-stu-id="f74ef-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="f74ef-813">Abrufen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="f74ef-813">View the DB</span></span>

<span data-ttu-id="f74ef-814">Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.</span><span class="sxs-lookup"><span data-stu-id="f74ef-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="f74ef-815">Daher lautet der Name der Datenbank „SchoolContext-{GUID}“.</span><span class="sxs-lookup"><span data-stu-id="f74ef-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="f74ef-816">Die GUID ist für jeden Benutzer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="f74ef-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="f74ef-817">Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="f74ef-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="f74ef-818">Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .</span><span class="sxs-lookup"><span data-stu-id="f74ef-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="f74ef-819">Erweitern Sie den Knoten **Tabellen**.</span><span class="sxs-lookup"><span data-stu-id="f74ef-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="f74ef-820">Klicken Sie mit der rechten Maustaste auf die Tabelle **Student** , und klicken Sie auf **Daten anzeigen** , um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="f74ef-821">Asynchroner Code</span><span class="sxs-lookup"><span data-stu-id="f74ef-821">Asynchronous code</span></span>

<span data-ttu-id="f74ef-822">Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.</span><span class="sxs-lookup"><span data-stu-id="f74ef-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="f74ef-823">Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet.</span><span class="sxs-lookup"><span data-stu-id="f74ef-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="f74ef-824">Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="f74ef-825">Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="f74ef-826">Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können.</span><span class="sxs-lookup"><span data-stu-id="f74ef-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="f74ef-827">Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f74ef-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="f74ef-828">Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht.</span><span class="sxs-lookup"><span data-stu-id="f74ef-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="f74ef-829">Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="f74ef-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="f74ef-830">Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="f74ef-831">Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="f74ef-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="f74ef-832">Er soll Rückrufe für Teile des Methodentexts generieren.</span><span class="sxs-lookup"><span data-stu-id="f74ef-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="f74ef-833">Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="f74ef-834">Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="f74ef-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="f74ef-835">Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.</span><span class="sxs-lookup"><span data-stu-id="f74ef-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="f74ef-836">Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt.</span><span class="sxs-lookup"><span data-stu-id="f74ef-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="f74ef-837">Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="f74ef-838">Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="f74ef-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="f74ef-839">Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="f74ef-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="f74ef-840">Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:</span><span class="sxs-lookup"><span data-stu-id="f74ef-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="f74ef-841">Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="f74ef-842">Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="f74ef-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="f74ef-843">Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="f74ef-844">Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f74ef-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="f74ef-845">Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.</span><span class="sxs-lookup"><span data-stu-id="f74ef-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="f74ef-846">Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="f74ef-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="f74ef-847">Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="f74ef-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="f74ef-848">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f74ef-848">Additional resources</span></span>

* [<span data-ttu-id="f74ef-849">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="f74ef-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="f74ef-850">Nächste</span><span class="sxs-lookup"><span data-stu-id="f74ef-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
