---
title: Erstellen einer Web-API mit ASP.NET Core und MongoDB
author: prkhandelwal
description: In diesem Tutorial wird beschrieben, wie Sie eine ASP.NET Core-Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060741"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Erstellen einer Web-API mit ASP.NET Core und MongoDB

Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren von MongoDB
> * Erstellen einer MongoDB-Datenbank
> * Definieren einer MongoDB-Sammlung und eines Schemas
> * Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API
> * Anpassen der JSON-Serialisierung

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 oder höher](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 oder höher](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 oder höher](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio für Mac Version 7.7 oder höher](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurieren von MongoDB

Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert. Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<version_number>\\bin* hinzu. Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.

Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern. Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten. Beispiel: *C:\\BooksData* auf Windows. Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist. Die Mongo-Shell erstellt keine neuen Verzeichnisse.
1. Öffnen Sie eine Befehlsshell. Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen. Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Öffnen Sie eine andere Befehlsshellinstanz. Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:

   ```console
   mongo
   ```

1. Führen Sie in einer Befehlsshell folgenden Befehl aus:

   ```console
   use BookstoreDb
   ```

   Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt. Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.

1. Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:

   ```console
   db.createCollection('Books')
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   { "ok" : 1 }
   ```

1. Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.

1. Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:

   ```console
   db.Books.find({}).pretty()
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.

Die Datenbank ist bereit. Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.

## <a name="create-the-aspnet-core-web-api-project"></a>Erstellen eines ASP.NET Core-Web-API-Projektes

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.
1. Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.
1. Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.
1. Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 3.0** aus. Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.
1. Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln. Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.

1. Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'BooksApi' (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „BooksApi“ nicht vorhanden). Add them? (Sollen sie hinzugefügt werden?)** . Wählen Sie **Ja**.
1. Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln. Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in Visual Studio für Mac-Versionen vor Version 8.6 auf der Randleiste auf **Datei** > **Neue Projektmappe** >  **.NET Core** > **App**. Klicken Sie in Version 8.6 oder höher auf der Randleiste auf **Datei** > **Neue Projektmappe** > **Web and Console** > **App** (App und Konsole).
1. Wählen Sie die C#-Projektvorlage **ASP.NET Core** > **API** aus, und klicken Sie auf **Weiter**.
1. Wählen Sie aus der Dropdownliste **Zielframework** die Option **.NET Core 3.1** aus, und klicken Sie auf **Weiter**.
1. Geben Sie als **Projektnamen***BooksApi* ein, und klicken Sie auf **Erstellen**.
1. Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.
1. Geben Sie in das Suchfeld *MongoDB.Driver* ein, wählen Sie das Paket *MongoDB.Driver* aus, und klicken Sie auf **Paket hinzufügen**.
1. Klicken Sie im Dialogfeld **Zustimmung zur Lizenz** auf die Schaltfläche **Annehmen**.

---

## <a name="add-an-entity-model"></a>Hinzufügen eines Entitätsmodells

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.
1. Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,

   * um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.
   * Sie wird mit [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.
   * Sie wird mit [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, um die Übergabe des Parameters als Typ `string` anstelle einer [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur zu ermöglichen. Mongo behandelt die Konvertierung von `string` zu `ObjectId`.

   Die Eigenschaft `BookName` ist mit dem Attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen. Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.

## <a name="add-a-configuration-model"></a>Hinzufügen eines Konfigurationsmodells

1. Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert. Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.

1. Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   Für den Code oben gilt:

   * Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Dependency-Injection-Container registriert. Dies bedeutet, dass die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt wird.
   * Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert. Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.

1. Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Hinzufügen eines CRUD-Vorgangsdiensts

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.
1. Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen. Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.

1. Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen. Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist. Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.

1. Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz für das Ausführen von Datenbankvorgängen. Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar. In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten. Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde. Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:

  * `collection` steht für den Sammlungsnamen.
  * `TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.

`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt. In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Der oben aufgeführte Web-API-Controller:

* Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.
* Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.
* Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben. Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt. `CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu. Der `Location`-Header gibt den URI des neu erstellten Buchs an.

## <a name="test-the-web-api"></a>Testen der Web-API

1. Erstellen Sie die App, und führen Sie sie aus.

1. Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen. Die folgende JSON-Antwort wird angezeigt:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen. Die folgende JSON-Antwort wird angezeigt:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Konfigurieren von JSON-Serialisierungsoptionen

Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:

* Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.
* Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.

Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:

1. JSON.NET wurde aus dem freigegebenen ASP.NET-Framework entfernt. Fügen Sie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) einen Paketverweis hinzu.

1. Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddControllers`-Methode:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein. Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.

1. Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.

1. Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte. Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren von MongoDB
> * Erstellen einer MongoDB-Datenbank
> * Definieren einer MongoDB-Sammlung und eines Schemas
> * Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API
> * Anpassen der JSON-Serialisierung

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio für Mac Version 7.7 oder höher](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurieren von MongoDB

Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert. Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<version_number>\\bin* hinzu. Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.

Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern. Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten. Beispiel: *C:\\BooksData* auf Windows. Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist. Die Mongo-Shell erstellt keine neuen Verzeichnisse.
1. Öffnen Sie eine Befehlsshell. Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen. Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Öffnen Sie eine andere Befehlsshellinstanz. Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:

   ```console
   mongo
   ```

1. Führen Sie in einer Befehlsshell folgenden Befehl aus:

   ```console
   use BookstoreDb
   ```

   Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt. Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.

1. Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:

   ```console
   db.createCollection('Books')
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   { "ok" : 1 }
   ```

1. Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.

1. Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:

   ```console
   db.Books.find({}).pretty()
   ```

   Das folgende Ergebnis wird angezeigt:

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.

Die Datenbank ist bereit. Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.

## <a name="create-the-aspnet-core-web-api-project"></a>Erstellen eines ASP.NET Core-Web-API-Projektes

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.
1. Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.
1. Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.
1. Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.2**. Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.
1. Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln. Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.

1. Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'BooksApi' (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „BooksApi“ nicht vorhanden). Add them? (Sollen sie hinzugefügt werden?)** . Wählen Sie **Ja**.
1. Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln. Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in Visual Studio für Mac-Versionen vor Version 8.6 auf der Randleiste auf **Datei** > **Neue Projektmappe** >  **.NET Core** > **App**. Klicken Sie in Version 8.6 oder höher auf der Randleiste auf **Datei** > **Neue Projektmappe** > **Web and Console** > **App** (App und Konsole).
1. Wählen Sie die C#-Projektvorlage **ASP.NET Core-Web-API** aus, und klicken Sie auf **Weiter**.
1. Wählen Sie aus der Dropdownliste **Zielframework** die Option **.NET Core 2.2** aus, und klicken Sie auf **Weiter**.
1. Geben Sie als **Projektnamen***BooksApi* ein, und klicken Sie auf **Erstellen**.
1. Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.
1. Geben Sie in das Suchfeld *MongoDB.Driver* ein, wählen Sie das Paket *MongoDB.Driver* aus, und klicken Sie auf **Paket hinzufügen**.
1. Klicken Sie im Dialogfeld **Zustimmung zur Lizenz** auf die Schaltfläche **Annehmen**.

---

## <a name="add-an-entity-model"></a>Hinzufügen eines Entitätsmodells

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.
1. Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,

   * um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.
   * Sie wird mit [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.
   * Sie wird mit [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, um die Übergabe des Parameters als Typ `string` anstelle einer [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur zu ermöglichen. Mongo behandelt die Konvertierung von `string` zu `ObjectId`.

   Die Eigenschaft `BookName` ist mit dem Attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen. Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.

## <a name="add-a-configuration-model"></a>Hinzufügen eines Konfigurationsmodells

1. Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert. Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.

1. Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   Für den Code oben gilt:

   * Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Dependency-Injection-Container registriert. Dies bedeutet, dass die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt wird.
   * Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert. Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.

1. Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Hinzufügen eines CRUD-Vorgangsdiensts

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.
1. Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen. Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.

1. Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen. Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist. Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.

1. Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz für das Ausführen von Datenbankvorgängen. Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar. In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten. Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde. Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:

  * `collection` steht für den Sammlungsnamen.
  * `TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.

`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt. In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.
* [Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Der oben aufgeführte Web-API-Controller:

* Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.
* Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.
* Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben. Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt. `CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu. Der `Location`-Header gibt den URI des neu erstellten Buchs an.

## <a name="test-the-web-api"></a>Testen der Web-API

1. Erstellen Sie die App, und führen Sie sie aus.

1. Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen. Die folgende JSON-Antwort wird angezeigt:

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen. Die folgende JSON-Antwort wird angezeigt:

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>Konfigurieren von JSON-Serialisierungsoptionen

Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:

* Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.
* Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.

Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:

1. Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddMvc`-Methode:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein. Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.

1. Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.

1. Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte. Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Hinzufügen der Authentifizierungsunterstützung zu einer Web-API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:

* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
