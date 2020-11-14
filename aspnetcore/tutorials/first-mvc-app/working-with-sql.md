---
title: 'Teil 5: Arbeiten mit einer Datenbank in einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 5 der Tutorialreihe zum Hinzufügen eines Modells zu einer ASP.NET Core MVC-App.
ms.author: riande
ms.date: 11/10/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: c082457258346637499b8ea8000dd8792931112f
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422651"
---
# <a name="part-5-work-with-a-database-in-an-aspnet-core-mvc-app"></a><span data-ttu-id="bc73f-103">Teil 5: Arbeiten mit einer Datenbank in einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="bc73f-103">Part 5, work with a database in an ASP.NET Core MVC app</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bc73f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bc73f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bc73f-105">Das `MvcMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc73f-105">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="bc73f-106">Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="bc73f-106">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-107">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="bc73f-108">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-108">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bc73f-109">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="bc73f-109">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-110">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-110">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

<span data-ttu-id="bc73f-111">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-111">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bc73f-112">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="bc73f-112">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="bc73f-113">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einer SQL Server-Produktionsinstanz festzulegen.</span><span class="sxs-lookup"><span data-stu-id="bc73f-113">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a production SQL Server.</span></span> <span data-ttu-id="bc73f-114">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bc73f-114">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-115">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="bc73f-116">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="bc73f-116">SQL Server Express LocalDB</span></span>

<span data-ttu-id="bc73f-117">LocalDB ist eine Lightweightversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="bc73f-117">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="bc73f-118">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="bc73f-118">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="bc73f-119">Die LocalDB-Datenbank erstellt standardmäßig *MDF* -Dateien im Verzeichnis *C:/Benutzer/{Benutzer}* .</span><span class="sxs-lookup"><span data-stu-id="bc73f-119">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="bc73f-120">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="bc73f-120">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](working-with-sql/_static/ssox.png)

* <span data-ttu-id="bc73f-122">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Ansicht-Designer**.</span><span class="sxs-lookup"><span data-stu-id="bc73f-122">Right click on the `Movie` table **> View Designer**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](working-with-sql/_static/dv.png)

<span data-ttu-id="bc73f-125">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-125">Note the key icon next to `ID`.</span></span> <span data-ttu-id="bc73f-126">EF macht die Eigenschaft `ID` standardmäßig zum Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="bc73f-126">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="bc73f-127">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="bc73f-127">Right click on the `Movie` table **> View Data**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/ssox2.png)

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-130">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-130">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="bc73f-131">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="bc73f-131">Seed the database</span></span>

<span data-ttu-id="bc73f-132">Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-132">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="bc73f-133">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="bc73f-133">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="bc73f-134">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc73f-134">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="bc73f-135">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="bc73f-135">Add the seed initializer</span></span>

<span data-ttu-id="bc73f-136">Ersetzen Sie den Inhalt von *Program.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc73f-136">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

<span data-ttu-id="bc73f-137">Testen der App</span><span class="sxs-lookup"><span data-stu-id="bc73f-137">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-138">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc73f-139">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc73f-139">Delete all the records in the DB.</span></span> <span data-ttu-id="bc73f-140">Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.</span><span class="sxs-lookup"><span data-stu-id="bc73f-140">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="bc73f-141">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc73f-141">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="bc73f-142">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="bc73f-142">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="bc73f-143">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="bc73f-143">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="bc73f-144">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus.</span><span class="sxs-lookup"><span data-stu-id="bc73f-144">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express-Symbol auf der Taskleiste](working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="bc73f-147">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc73f-147">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="bc73f-148">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="bc73f-148">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-149">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-149">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bc73f-150">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="bc73f-150">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="bc73f-151">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc73f-151">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="bc73f-152">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="bc73f-152">The app shows the seeded data.</span></span>

![In Microsoft Edge geöffnete MVC Movie-Anwendung mit Filmdaten](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> <span data-ttu-id="bc73f-154">[Zurück](adding-model.md)
> [Weiter](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="bc73f-154">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bc73f-155">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bc73f-155">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bc73f-156">Das `MvcMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc73f-156">The `MvcMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="bc73f-157">Der Datenbankkontext wird mit dem Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der Methode `ConfigureServices` in der Datei *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="bc73f-157">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-158">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="bc73f-159">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-159">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bc73f-160">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="bc73f-160">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-161">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-161">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="bc73f-162">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest die `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-162">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString`.</span></span> <span data-ttu-id="bc73f-163">Für die lokale Entwicklung wird die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen:</span><span class="sxs-lookup"><span data-stu-id="bc73f-163">For local development, it gets the connection string from the *appsettings.json* file:</span></span>

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

<span data-ttu-id="bc73f-164">Wenn Sie die App auf einem Test- oder Produktionsserver bereitstellen, können Sie eine Umgebungsvariable oder eine andere Vorgehensweise zum Festlegen der Verbindungszeichenfolge für einen tatsächlichen SQL Server-Computer verwenden.</span><span class="sxs-lookup"><span data-stu-id="bc73f-164">When you deploy the app to a test or production server, you can use an environment variable or another approach to set the connection string to a real SQL Server.</span></span> <span data-ttu-id="bc73f-165">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="bc73f-165">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-166">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="bc73f-167">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="bc73f-167">SQL Server Express LocalDB</span></span>

<span data-ttu-id="bc73f-168">LocalDB ist eine Lightweightversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="bc73f-168">LocalDB is a lightweight version of the SQL Server Express Database Engine that's targeted for program development.</span></span> <span data-ttu-id="bc73f-169">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="bc73f-169">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="bc73f-170">Die LocalDB-Datenbank erstellt standardmäßig *MDF* -Dateien im Verzeichnis *C:/Benutzer/{Benutzer}* .</span><span class="sxs-lookup"><span data-stu-id="bc73f-170">By default, LocalDB database creates *.mdf* files in the *C:/Users/{user}* directory.</span></span>

* <span data-ttu-id="bc73f-171">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="bc73f-171">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](working-with-sql/_static/ssox.png)

* <span data-ttu-id="bc73f-173">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Ansicht-Designer**.</span><span class="sxs-lookup"><span data-stu-id="bc73f-173">Right click on the `Movie` table **> View Designer**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](working-with-sql/_static/dv.png)

<span data-ttu-id="bc73f-176">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-176">Note the key icon next to `ID`.</span></span> <span data-ttu-id="bc73f-177">EF macht die Eigenschaft `ID` standardmäßig zum Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="bc73f-177">By default, EF will make a property named `ID` the primary key.</span></span>

* <span data-ttu-id="bc73f-178">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie` **> Daten anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="bc73f-178">Right click on the `Movie` table **> View Data**</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](working-with-sql/_static/ssox2.png)

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-181">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a><span data-ttu-id="bc73f-182">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="bc73f-182">Seed the database</span></span>

<span data-ttu-id="bc73f-183">Erstellen Sie im Ordner *Models* die neue Klasse `SeedData`.</span><span class="sxs-lookup"><span data-stu-id="bc73f-183">Create a new class named `SeedData` in the *Models* folder.</span></span> <span data-ttu-id="bc73f-184">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="bc73f-184">Replace the generated code with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="bc73f-185">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bc73f-185">If there are any movies in the DB, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="bc73f-186">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="bc73f-186">Add the seed initializer</span></span>

<span data-ttu-id="bc73f-187">Ersetzen Sie den Inhalt von *Program.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bc73f-187">Replace the contents of *Program.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

<span data-ttu-id="bc73f-188">Testen der App</span><span class="sxs-lookup"><span data-stu-id="bc73f-188">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="bc73f-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bc73f-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bc73f-190">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="bc73f-190">Delete all the records in the DB.</span></span> <span data-ttu-id="bc73f-191">Dies ist über die Links „Löschen“ im Browser oder SSOX möglich.</span><span class="sxs-lookup"><span data-stu-id="bc73f-191">You can do this with the delete links in the browser or from SSOX.</span></span>
* <span data-ttu-id="bc73f-192">Zwingen Sie die App zur Initialisierung (rufen Sie die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bc73f-192">Force the app to initialize (call the methods in the `Startup` class) so the seed method runs.</span></span> <span data-ttu-id="bc73f-193">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="bc73f-193">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="bc73f-194">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="bc73f-194">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="bc73f-195">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus.</span><span class="sxs-lookup"><span data-stu-id="bc73f-195">Right click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**</span></span>

    ![IIS Express-Symbol auf der Taskleiste](working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](working-with-sql/_static/stopIIS.png)

    * <span data-ttu-id="bc73f-198">Wenn Sie Visual Studio im Nicht-Debugmodus ausgeführt haben, drücken Sie F5, um den Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc73f-198">If you were running VS in non-debug mode, press F5 to run in debug mode</span></span>
    * <span data-ttu-id="bc73f-199">Wenn Sie Visual Studio im Debugmodus ausgeführt haben, beenden Sie den Debugger, und drücken Sie F5.</span><span class="sxs-lookup"><span data-stu-id="bc73f-199">If you were running VS in debug mode, stop the debugger and press F5</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="bc73f-200">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="bc73f-200">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="bc73f-201">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="bc73f-201">Delete all the records in the DB (So the seed method will run).</span></span> <span data-ttu-id="bc73f-202">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bc73f-202">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="bc73f-203">Die App zeigt die per Seeding hinzugefügten Daten.</span><span class="sxs-lookup"><span data-stu-id="bc73f-203">The app shows the seeded data.</span></span>

![In Microsoft Edge geöffnete MVC Movie-Anwendung mit Filmdaten](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> <span data-ttu-id="bc73f-205">[Zurück](adding-model.md)
> [Weiter](controller-methods-views.md)</span><span class="sxs-lookup"><span data-stu-id="bc73f-205">[Previous](adding-model.md)
[Next](controller-methods-views.md)</span></span>

::: moniker-end
