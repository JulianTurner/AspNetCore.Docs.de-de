---
page_type: sample
description: In diesem Tutorial wird beschrieben, wie Sie eine ASP.NET Core-Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 95a2a6fcda0a4f7148183981f7dbacd06388329d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "84106519"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="406a1-102">Erstellen einer Web-API mit ASP.NET Core und MongoDB</span><span class="sxs-lookup"><span data-stu-id="406a1-102">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="406a1-103">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="406a1-103">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="406a1-104">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="406a1-104">In this tutorial, you learn how to:</span></span>

* <span data-ttu-id="406a1-105">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="406a1-105">Configure MongoDB</span></span>
* <span data-ttu-id="406a1-106">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="406a1-106">Create a MongoDB database</span></span>
* <span data-ttu-id="406a1-107">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="406a1-107">Define a MongoDB collection and schema</span></span>
* <span data-ttu-id="406a1-108">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="406a1-108">Perform MongoDB CRUD operations from a web API</span></span>
* <span data-ttu-id="406a1-109">Anpassen der JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="406a1-109">Customize JSON serialization</span></span>

## <a name="prerequisites"></a><span data-ttu-id="406a1-110">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="406a1-110">Prerequisites</span></span>

* [<span data-ttu-id="406a1-111">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="406a1-111">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="406a1-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="406a1-112">[Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="406a1-113">MongoDB</span><span class="sxs-lookup"><span data-stu-id="406a1-113">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a><span data-ttu-id="406a1-114">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="406a1-114">Configure MongoDB</span></span>

<span data-ttu-id="406a1-115">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="406a1-115">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="406a1-116">Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<version_number>\\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-116">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="406a1-117">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="406a1-117">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="406a1-118">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="406a1-118">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="406a1-119">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="406a1-119">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="406a1-120">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="406a1-120">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="406a1-121">Beispiel: *C:\\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="406a1-121">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="406a1-122">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="406a1-122">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="406a1-123">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="406a1-123">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="406a1-124">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="406a1-124">Open a command shell.</span></span> <span data-ttu-id="406a1-125">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="406a1-125">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="406a1-126">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="406a1-126">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="406a1-127">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="406a1-127">Open another command shell instance.</span></span> <span data-ttu-id="406a1-128">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="406a1-128">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="406a1-129">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="406a1-129">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="406a1-130">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="406a1-130">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="406a1-131">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="406a1-131">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="406a1-132">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="406a1-132">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="406a1-133">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="406a1-133">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="406a1-134">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="406a1-134">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="406a1-135">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="406a1-135">The following result is displayed:</span></span>

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
  > <span data-ttu-id="406a1-136">Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.</span><span class="sxs-lookup"><span data-stu-id="406a1-136">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="406a1-137">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="406a1-137">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="406a1-138">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="406a1-138">The following result is displayed:</span></span>

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

    <span data-ttu-id="406a1-139">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-139">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="406a1-140">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="406a1-140">The database is ready.</span></span> <span data-ttu-id="406a1-141">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="406a1-141">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="406a1-142">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="406a1-142">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="406a1-143">Wechseln Sie zu **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="406a1-143">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="406a1-144">Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="406a1-144">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="406a1-145">Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="406a1-145">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="406a1-146">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="406a1-146">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="406a1-147">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="406a1-147">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="406a1-148">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="406a1-148">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="406a1-149">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="406a1-149">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="406a1-150">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="406a1-150">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a><span data-ttu-id="406a1-151">Hinzufügen eines Entitätsmodells</span><span class="sxs-lookup"><span data-stu-id="406a1-151">Add an entity model</span></span>

1. <span data-ttu-id="406a1-152">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-152">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="406a1-153">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-153">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="406a1-154">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,</span><span class="sxs-lookup"><span data-stu-id="406a1-154">In the preceding class, the `Id` property:</span></span>

    * <span data-ttu-id="406a1-155">um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="406a1-155">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="406a1-156">Sie wird mit [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.</span><span class="sxs-lookup"><span data-stu-id="406a1-156">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="406a1-157">Sie wird mit [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, um die Übergabe des Parameters als Typ `string` anstelle einer [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="406a1-157">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="406a1-158">Mongo behandelt die Konvertierung von `string` zu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="406a1-158">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

    <span data-ttu-id="406a1-159">Die Eigenschaft `BookName` ist mit dem Attribut [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen.</span><span class="sxs-lookup"><span data-stu-id="406a1-159">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="406a1-160">Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="406a1-160">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="406a1-161">Hinzufügen eines Konfigurationsmodells</span><span class="sxs-lookup"><span data-stu-id="406a1-161">Add a configuration model</span></span>

1. <span data-ttu-id="406a1-162">Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-162">Add the following database configuration values to *appsettings.json*:</span></span>

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. <span data-ttu-id="406a1-163">Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-163">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    <span data-ttu-id="406a1-164">Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="406a1-164">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="406a1-165">Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="406a1-165">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="406a1-166">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-166">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    <span data-ttu-id="406a1-167">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="406a1-167">In the preceding code:</span></span>

    * <span data-ttu-id="406a1-168">Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Abhängigkeitsinjektionscontainer (DI, Dependency Injection) registriert.</span><span class="sxs-lookup"><span data-stu-id="406a1-168">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="406a1-169">D. h. die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts wird beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="406a1-169">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="406a1-170">Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="406a1-170">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="406a1-171">Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="406a1-171">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="406a1-172">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="406a1-172">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="406a1-173">Hinzufügen eines CRUD-Vorgangsdiensts</span><span class="sxs-lookup"><span data-stu-id="406a1-173">Add a CRUD operations service</span></span>

1. <span data-ttu-id="406a1-174">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-174">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="406a1-175">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-175">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    <span data-ttu-id="406a1-176">Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen.</span><span class="sxs-lookup"><span data-stu-id="406a1-176">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="406a1-177">Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="406a1-177">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="406a1-178">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-178">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    <span data-ttu-id="406a1-179">Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="406a1-179">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="406a1-180">Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="406a1-180">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="406a1-181">Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.</span><span class="sxs-lookup"><span data-stu-id="406a1-181">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="406a1-182">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="406a1-182">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>


    ```csharp
    using BooksApi.Services;
    ```

<span data-ttu-id="406a1-183">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="406a1-183">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="406a1-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz für das Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="406a1-184">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="406a1-185">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="406a1-185">The constructor of this class is provided the MongoDB connection string:</span></span>

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* <span data-ttu-id="406a1-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="406a1-186">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="406a1-187">In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="406a1-187">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="406a1-188">Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="406a1-188">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="406a1-189">Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="406a1-189">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="406a1-190">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="406a1-190">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="406a1-191">`TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="406a1-191">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="406a1-192">`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="406a1-192">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="406a1-193">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="406a1-193">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="406a1-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="406a1-194">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="406a1-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="406a1-195">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="406a1-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="406a1-196">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="406a1-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="406a1-197">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="406a1-198">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="406a1-198">Add a controller</span></span>

<span data-ttu-id="406a1-199">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="406a1-199">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

<span data-ttu-id="406a1-200">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="406a1-200">The preceding web API controller:</span></span>

* <span data-ttu-id="406a1-201">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="406a1-201">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="406a1-202">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="406a1-202">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="406a1-203">Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="406a1-203">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="406a1-204">Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="406a1-204">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="406a1-205">`CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-205">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="406a1-206">Der `Location`-Header gibt den URI des neu erstellten Buchs an.</span><span class="sxs-lookup"><span data-stu-id="406a1-206">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="406a1-207">Testen der Web-API</span><span class="sxs-lookup"><span data-stu-id="406a1-207">Test the web API</span></span>

1. <span data-ttu-id="406a1-208">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="406a1-208">Build and run the app.</span></span>

1. <span data-ttu-id="406a1-209">Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="406a1-209">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="406a1-210">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="406a1-210">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="406a1-211">Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="406a1-211">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="406a1-212">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="406a1-212">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="406a1-213">Konfigurieren von JSON-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="406a1-213">Configure JSON serialization options</span></span>

<span data-ttu-id="406a1-214">Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="406a1-214">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="406a1-215">Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.</span><span class="sxs-lookup"><span data-stu-id="406a1-215">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="406a1-216">Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="406a1-216">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="406a1-217">Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="406a1-217">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="406a1-218">JSON.NET wurde aus dem freigegebenen ASP.NET-Framework entfernt.</span><span class="sxs-lookup"><span data-stu-id="406a1-218">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="406a1-219">Fügen Sie [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="406a1-219">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="406a1-220">Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddMvc`-Methode:</span><span class="sxs-lookup"><span data-stu-id="406a1-220">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    <span data-ttu-id="406a1-221">Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein.</span><span class="sxs-lookup"><span data-stu-id="406a1-221">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="406a1-222">Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.</span><span class="sxs-lookup"><span data-stu-id="406a1-222">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="406a1-223">Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:</span><span class="sxs-lookup"><span data-stu-id="406a1-223">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    <span data-ttu-id="406a1-224">Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.</span><span class="sxs-lookup"><span data-stu-id="406a1-224">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="406a1-225">Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="406a1-225">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    ```csharp
    using Newtonsoft.Json;
    ```

1. <span data-ttu-id="406a1-226">Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte.</span><span class="sxs-lookup"><span data-stu-id="406a1-226">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="406a1-227">Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="406a1-227">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="406a1-228">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="406a1-228">Next steps</span></span>

<span data-ttu-id="406a1-229">Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="406a1-229">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="406a1-230">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="406a1-230">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [<span data-ttu-id="406a1-231">Erstellen von Web-APIs mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="406a1-231">Create web APIs with ASP.NET Core</span></span>](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
