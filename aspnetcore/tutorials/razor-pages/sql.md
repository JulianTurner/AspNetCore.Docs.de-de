---
title: 'Teil 4: Arbeiten mit einer Datenbank'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 01/05/2021
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 8c9d0d9c24e0ce81925ccde463bcf085531b665e
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024734"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a>Dies ist Teil 4 der Tutorialreihe zu Razor Pages.

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank. Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`. Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die generierte Verbindungszeichenfolge ähnelt dem folgenden JSON-Code:

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:\Users\<user>\`-Verzeichnis.

<a name="ssox"></a>
1. Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).

   ![Menü Ansicht](sql/_static/5/ssox.png)

1. Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:

   ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/5/design.png)

   ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

   Beachten Sie das Schlüsselsymbol neben `ID`. EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.

1. Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:

   ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Auf der [SQLite](https://www.sqlite.org/)-Website ist zu lesen:

> SQLite ist eine eigenständige, sehr zuverlässige, eingebettete, genehmigungsfreie SQL-Datenbank-Engine mit vollem Funktionsumfang. SQLite ist die weltweit am häufigsten verwendete Datenbank-Engine.

Es gibt viele Tools von Drittanbietern, die Sie herunterladen können, um eine SQLite-Datenbank zu verwalten und anzuzeigen. Die folgende Abbildung stammt aus [DB Browser for SQLite](https://sqlitebrowser.org/). Wenn Sie ein bestimmtes SQLite-Tool bevorzugen, geben Sie bitte in einem Kommentar dessen Vorteile an.

![DB Browser for SQLite mit der Filmdatenbank](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Für dieses Tutorial wird nach Möglichkeit das Entity Framework Core-Feature *Migrationen* verwendet. Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht. Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt. So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt. Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl. Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen. Wenn sich das Schema ändert, wird stattdessen die Datenbank gelöscht und neu erstellt.
>
>Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert. Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:
>
>* Erstellen einer neuen Tabelle.
>* Kopieren von Daten aus der alten Tabelle in die neue Tabelle.
>* Löschen der alten Tabelle.
>* Umbenennen der neuen Tabelle.
>
>Weitere Informationen finden Sie in den folgenden Ressourcen:
> * [SQLite EF Core-Datenbank-Anbieter-Einschränkungen](/ef/core/providers/sqlite/limitations)
> * [Anpassen des Migrationscodes](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Datenseeding](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE-Anweisung](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Hinzufügen des Initialisierers des Seedings

Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.
* Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist. Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.

Die folgende Ausnahme tritt auf, wenn `Update-Database` nicht ausgeführt wurde:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Löschen Sie alle Datensätze in der Datenbank. Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.

1. Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird. Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden. Sie müssen IIS mit einer der folgenden Vorgehensweisen beenden und neu starten:

   1. Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:

      ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Kontextmenü](sql/_static/stopIIS.png)

   1. Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.
   1. Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird). Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.

---

Die App zeigt die per Seeding hinzugefügten Daten:

![Im Browser geöffnete Movie-Anwendung mit Filmdaten](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank. Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`. Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die generierte Verbindungszeichenfolge sieht in etwa wie folgt aus:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:\Users\<user>\`-Verzeichnis.

<a name="ssox"></a>
* Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).

  ![Menü Ansicht](sql/_static/ssox.png)

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/design.png)

  ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

Beachten Sie das Schlüsselsymbol neben `ID`. EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

Auf der [SQLite](https://www.sqlite.org/)-Website ist zu lesen:

> SQLite ist eine eigenständige, sehr zuverlässige, eingebettete, genehmigungsfreie SQL-Datenbank-Engine mit vollem Funktionsumfang. SQLite ist die weltweit am häufigsten verwendete Datenbank-Engine.

Es gibt viele Tools von Drittanbietern, die Sie herunterladen können, um eine SQLite-Datenbank zu verwalten und anzuzeigen. Die folgende Abbildung stammt aus [DB Browser for SQLite](https://sqlitebrowser.org/). Wenn Sie ein bestimmtes SQLite-Tool bevorzugen, geben Sie bitte in einem Kommentar dessen Vorteile an.

![DB Browser for SQLite mit der Filmdatenbank](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Für dieses Tutorial wird nach Möglichkeit das Entity Framework Core-Feature *Migrationen* verwendet. Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht. Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt. So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt. Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl. Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen. Wenn sich das Schema ändert, wird stattdessen die Datenbank gelöscht und neu erstellt.
>
>Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert. Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:
>
>* Erstellen einer neuen Tabelle.
>* Kopieren von Daten aus der alten Tabelle in die neue Tabelle.
>* Löschen der alten Tabelle.
>* Umbenennen der neuen Tabelle.
>
>Weitere Informationen finden Sie in den folgenden Ressourcen:
> * [SQLite EF Core-Datenbank-Anbieter-Einschränkungen](/ef/core/providers/sqlite/limitations)
> * [Anpassen des Migrationscodes](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Datenseeding](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE-Anweisung](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Hinzufügen des Initialisierers des Seedings

Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.
* Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist. Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.

Die folgende Ausnahme tritt auf, wenn `Update-Database` nicht ausgeführt wurde:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Testen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Löschen Sie alle Datensätze in der Datenbank. Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.
* Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird. Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden. Sie müssen IIS mit einer der folgenden Vorgehensweisen beenden und neu starten:

  * Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.
    * Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird). Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.

---

Die App zeigt die per Seeding hinzugefügten Daten:

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55https.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank. Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Weitere Informationen über die in `ConfigureServices` verwendeten Methoden finden Sie unter:

* [Unterstützung für die Datenschutz-Grundverordnung (DSGVO) in ASP.NET Core](xref:security/gdpr) für `CookiePolicyOptions`.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`. Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die generierte Verbindungszeichenfolge sieht in etwa wie folgt aus:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.

<a name="ssox"></a>
* Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).

  ![Menü Ansicht](sql/_static/ssox.png)

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

Beachten Sie das Schlüsselsymbol neben `ID`. EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.

* Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Hinzufügen des Initialisierers des Seedings

Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.
* Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist. Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.

Eine Produktions-App würde `Database.Migrate` nicht aufrufen. Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:

SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden. Die Anmeldung ist fehlgeschlagen.
Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.

### <a name="test-the-app"></a>Testen der App

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Löschen Sie alle Datensätze in der Datenbank. Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.
* Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird. Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden. Hierzu können Sie einen der folgenden Ansätze verwenden:

  * Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.
    * Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird). Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird). Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.

---

Die App zeigt die per Seeding hinzugefügten Daten:

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55https.png)

Im nächsten Tutorial wird die Präsentation der Daten bereinigt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)

::: moniker-end
