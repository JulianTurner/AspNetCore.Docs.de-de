---
page_type: sample
description: Erfahren Sie, wie Sie Ihren ASP.NET Core-Web-API-Projekten Swashbuckle hinzufügen, um die Swagger-Benutzeroberfläche zu integrieren.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "78647587"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="456ea-102">Erste Schritte mit Swashbuckle und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="456ea-102">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="456ea-103">Wenn eine Web-API verwendet wird, fällt es dem Entwickler oft nicht leicht, die zahlreichen Methoden zu verstehen.</span><span class="sxs-lookup"><span data-stu-id="456ea-103">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="456ea-104">[Swagger](https://swagger.io/), auch als [OpenAPI](https://www.openapis.org/) bekannt, löst das Problem, das das Generieren von nützlichen Dokumentationen und Hilfeseiten für Web-APIs darstellen kann.</span><span class="sxs-lookup"><span data-stu-id="456ea-104">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="456ea-105">Das Programm bietet Vorteile wie die interaktive Dokumentation, die Generierung von Client SDKs und die Erkennbarkeit von APIs.</span><span class="sxs-lookup"><span data-stu-id="456ea-105">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="456ea-106">In diesem Beispiel – [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) – wird die .NET-Implementierung gezeigt.</span><span class="sxs-lookup"><span data-stu-id="456ea-106">In this sample, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) the .NET implementation is shown.</span></span>

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="456ea-107">Hinzufügen und Konfigurieren von Swagger-Middleware</span><span class="sxs-lookup"><span data-stu-id="456ea-107">Add and configure Swagger middleware</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

<span data-ttu-id="456ea-108">Aktivieren Sie die Middleware in der `Startup.Configure`-Methode, um das generierte JSON-Dokument und die Swagger-Benutzeroberfläche bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="456ea-108">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="456ea-109">Der obige `UseSwaggerUI`-Methodenaufruf aktiviert die [Middleware für statische Dateien](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="456ea-109">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](https://docs.microsoft.com/aspnet/core/fundamentals/static-files).</span></span> <span data-ttu-id="456ea-110">Wenn .NET Framework oder .NET Core 1.x die Zielkomponente ist, müssen Sie Ihrem Projekt das NuGet-Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="456ea-110">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="456ea-111">Starten Sie die App, und navigieren Sie zu `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="456ea-111">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="456ea-112">Das generierte Dokument mit der Beschreibung der Endpunkte wird entsprechend der [Swagger-Spezifikation (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="456ea-112">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="456ea-113">Die Swagger-Benutzeroberfläche ist unter `http://localhost:<port>/swagger` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="456ea-113">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="456ea-114">Mit der Swagger-Benutzeroberfläche können Sie die API kennenlernen und sie in andere Programme integrieren.</span><span class="sxs-lookup"><span data-stu-id="456ea-114">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="456ea-115">Legen Sie für die Eigenschaft `RoutePrefix` eine leere Zeichenfolge fest, um die Swagger-Benutzeroberfläche im App-Stamm (`http://localhost:<port>/`) bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="456ea-115">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

<span data-ttu-id="456ea-116">Wenn Sie Verzeichnisse mit ISS oder einem Reverseproxy verwenden, legen Sie den Swagger-Endpunkt mit dem Präfix `./` auf einen relativen Pfad fest.</span><span class="sxs-lookup"><span data-stu-id="456ea-116">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="456ea-117">Beispielsweise `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="456ea-117">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="456ea-118">`/swagger/v1/swagger.json` weist die App an, am ursprünglichen Stamm der URL nach einer JSON-Datei (und ggf. nach einem Routenpräfix) zu suchen.</span><span class="sxs-lookup"><span data-stu-id="456ea-118">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="456ea-119">Verwenden Sie z. B. `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` statt `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="456ea-119">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="456ea-120">Anpassen und Erweitern</span><span class="sxs-lookup"><span data-stu-id="456ea-120">Customize and extend</span></span>

<span data-ttu-id="456ea-121">Swagger stellt Optionen für das Dokumentieren des Objektmodells und das Anpassen der Benutzeroberfläche bereit, damit diese mit Ihrem Design übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="456ea-121">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="456ea-122">Fügen Sie in der `Startup`-Klasse die folgenden Namespaces hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-122">In the `Startup` class, add the following namespaces:</span></span>
```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="456ea-123">API-Informationen und -Beschreibung</span><span class="sxs-lookup"><span data-stu-id="456ea-123">API info and description</span></span>

<span data-ttu-id="456ea-124">Durch die Konfigurationsaktion, die an die `AddSwaggerGen`-Methode übergeben wird, werden Informationen wie Autor, Lizenz und Beschreibung hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="456ea-124">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

<span data-ttu-id="456ea-125">Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="456ea-125">The Swagger UI displays the version's information:</span></span>

![Swagger-Benutzeroberfläche mit Versionsinformationen: Beschreibung, Autor, Link „Mehr anzeigen“](sample_images/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="456ea-127">XML-Kommentare</span><span class="sxs-lookup"><span data-stu-id="456ea-127">XML comments</span></span>

<span data-ttu-id="456ea-128">XML-Kommentare können mithilfe der folgenden Ansätze aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="456ea-128">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="456ea-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="456ea-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="456ea-130">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="456ea-130">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="456ea-131">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-131">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="456ea-132">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="456ea-132">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="456ea-133">Drücken und halten Sie im *Lösungspad* die **CONTROL**-Taste, und klicken Sie auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="456ea-133">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="456ea-134">Navigieren Sie zu **Extras** > **Datei bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="456ea-134">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="456ea-135">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-135">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[<span data-ttu-id="456ea-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="456ea-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="456ea-137">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-137">Manually add the highlighted lines to the *.csproj* file:</span></span>

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

<span data-ttu-id="456ea-138">Das Aktivieren von XML-Kommentaren stellt Debuginformationen zu nicht-dokumentierten öffentlichen Typen und Members bereit.</span><span class="sxs-lookup"><span data-stu-id="456ea-138">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="456ea-139">Auf nicht dokumentierte Typen und Member wird durch die Warnmeldung verwiesen.</span><span class="sxs-lookup"><span data-stu-id="456ea-139">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="456ea-140">Die folgende Meldung macht z.B. durch den Warnungscode 1591 auf einen Verstoß aufmerksam:</span><span class="sxs-lookup"><span data-stu-id="456ea-140">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="456ea-141">Um Warnungen projektübergreifend zu unterdrücken, definieren Sie eine Liste der zu ignorierenden Warnungscodes (mit Semikolon als Trennzeichen).</span><span class="sxs-lookup"><span data-stu-id="456ea-141">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="456ea-142">Das Anfügen von Warnungscodes an `$(NoWarn);` gilt auch für die [C#-Standardwerte](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16).</span><span class="sxs-lookup"><span data-stu-id="456ea-142">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

<span data-ttu-id="456ea-143">Um Warnungen nur für bestimmte Member zu unterdrücken, schließen Sie den Code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning)-Präprozessordirektiven ein.</span><span class="sxs-lookup"><span data-stu-id="456ea-143">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="456ea-144">Dieser Ansatz eignet sich für Code, der nicht über die API-Dokumentation verfügbar gemacht werden soll. Im folgenden Beispiel wird der Warnungscode CS1591 für die gesamte Klasse `Program` ignoriert.</span><span class="sxs-lookup"><span data-stu-id="456ea-144">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="456ea-145">Das Erzwingen des Warnungscodes wird am Ende der Klassendefinition wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="456ea-145">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="456ea-146">Geben Sie mehrere Warnungscodes in einer kommagetrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="456ea-146">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="456ea-147">Konfigurieren Sie Swagger so, dass die XML-Datei verwendet wird, die nach Befolgen der vorherigen Anweisungen generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="456ea-147">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="456ea-148">Bei Linux oder anderen Betriebssystemen als Windows können bei Dateinamen und -pfaden Groß-/Kleinbuchstaben berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="456ea-148">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="456ea-149">Die Datei *TodoApi.XML* ist beispielsweise unter Windows, nicht aber unter CentOS gültig.</span><span class="sxs-lookup"><span data-stu-id="456ea-149">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

<span data-ttu-id="456ea-150">Im vorangehenden Codeausschnitt wurde durch [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection) ein XML-Dateiname erstellt, der dem Namen des Web-API-Projekts entspricht.</span><span class="sxs-lookup"><span data-stu-id="456ea-150">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="456ea-151">Die [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory)-Eigenschaft wird verwendet, um einen Pfad zu der XML-Datei zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="456ea-151">The [AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="456ea-152">Einige Swagger-Features (z.B. Schemas von Eingabeparametern oder HTTP-Methoden und Antwortcodes aus den jeweiligen Attributen) funktionieren ohne XML-Dokumentationsdatei.</span><span class="sxs-lookup"><span data-stu-id="456ea-152">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="456ea-153">Für die meisten Funktionen, nämlich Methodenzusammenfassungen und Beschreibungen von Parametern und Antwortcodes, ist die Verwendung einer XML-Datei unerlässlich.</span><span class="sxs-lookup"><span data-stu-id="456ea-153">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="456ea-154">Das Hinzufügen von Kommentaren mit drei Schrägstrichen zu einer Aktion erweitert die Swagger-Benutzeroberfläche, indem die Beschreibung zum Header des Abschnitts hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="456ea-154">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="456ea-155">Fügen Sie nun über der `Delete`-Aktion ein [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary)-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-155">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
<span data-ttu-id="456ea-156">Auf der Swagger-Benutzeroberfläche wird der innere Text des `<summary>`-Elements angezeigt, das im letzten Codeausschnitt angegeben wurde:</span><span class="sxs-lookup"><span data-stu-id="456ea-156">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Swagger-Benutzeroberfläche mit dem XML-Kommentar „Löscht ein bestimmtes TodoItem.“](sample_images/triple-slash-comments.png)

<span data-ttu-id="456ea-159">Die Benutzeroberfläche wird durch das generierte JSON-Schema gesteuert:</span><span class="sxs-lookup"><span data-stu-id="456ea-159">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```
<span data-ttu-id="456ea-160">Fügen Sie der Dokumentation der `Create`-Aktionsmethode ein [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks)-Element hinzu.</span><span class="sxs-lookup"><span data-stu-id="456ea-160">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="456ea-161">Dieses ergänzt die Informationen, die im `<summary>`-Element angegeben wurden, und sorgt für eine robustere Swagger-Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="456ea-161">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="456ea-162">Das `<remarks>`-Element kann aus Text, JSON-Code oder XML-Code bestehen.</span><span class="sxs-lookup"><span data-stu-id="456ea-162">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
<span data-ttu-id="456ea-163">Durch die zusätzlichen Kommentare wird die Benutzeroberfläche wie unten gezeigt erweitert:</span><span class="sxs-lookup"><span data-stu-id="456ea-163">Notice the UI enhancements with these additional comments:</span></span>

![Swagger-Benutzeroberfläche mit zusätzlichen Kommentaren](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="456ea-165">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="456ea-165">Data annotations</span></span>

<span data-ttu-id="456ea-166">Markieren Sie das Modell mit Attributen aus dem Namespace [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations), um die Komponenten der Swagger-Benutzeroberfläche zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="456ea-166">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="456ea-167">Fügen Sie das `[Required]`-Attribut der `Name`-Eigenschaft der `TodoItem`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="456ea-167">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

<span data-ttu-id="456ea-168">Das Vorhandensein dieses Attributs ändert das Verhalten der Benutzeroberfläche und des zugrunde liegenden JSON-Schemas:</span><span class="sxs-lookup"><span data-stu-id="456ea-168">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="456ea-169">Fügen Sie das `[Produces("application/json")]`-Attribut zum API-Controller hinzu.</span><span class="sxs-lookup"><span data-stu-id="456ea-169">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="456ea-170">Dadurch kann deklariert werden, dass die Aktionen des Controllers den Inhaltstyp *application/json* für Antworten unterstützen:</span><span class="sxs-lookup"><span data-stu-id="456ea-170">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
<span data-ttu-id="456ea-171">Im Dropdownmenü des **Anforderungsinhaltstyps** ist dieser Inhaltstyp als Standard für die GET-Aktionen des Controllers ausgewählt:</span><span class="sxs-lookup"><span data-stu-id="456ea-171">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Swagger UI mit Standardinhaltstyp für die Antwort](sample_images/json-response-content-type.png)

<span data-ttu-id="456ea-173">Mit zunehmender Verwendung von Datenanmerkungen in der Web-API werden die UI- und API-Hilfeseiten beschreibender und nützlicher.</span><span class="sxs-lookup"><span data-stu-id="456ea-173">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="456ea-174">Beschreiben von Antworttypen</span><span class="sxs-lookup"><span data-stu-id="456ea-174">Describe response types</span></span>

<span data-ttu-id="456ea-175">Entwickler, die eine Web-API nutzen, interessieren sich vor allem dafür, welche Antworttypen und Fehlercodes zurückgegeben werden (wenn diese nicht dem Standard entsprechen).</span><span class="sxs-lookup"><span data-stu-id="456ea-175">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="456ea-176">Die Antworttypen und Fehlercodes sind in den XML-Kommentaren und Datenanmerkungen gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="456ea-176">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="456ea-177">Die Aktion `Create` gibt bei einer erfolgreichen Anforderung den Statuscode „HTTP 201“ zurück.</span><span class="sxs-lookup"><span data-stu-id="456ea-177">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="456ea-178">Der Statuscode „HTTP 400“ wird zurückgegeben, wenn der gesendete Anforderungstext NULL ist.</span><span class="sxs-lookup"><span data-stu-id="456ea-178">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="456ea-179">Ohne richtige Dokumentation in der Swagger-Benutzeroberfläche fehlt dem Consumer das Wissen über diese erwarteten Ergebnisse.</span><span class="sxs-lookup"><span data-stu-id="456ea-179">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="456ea-180">Sie können dieses Problem beheben, indem Sie die hervorgehobenen Zeilen im folgenden Beispiel hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="456ea-180">Fix that problem by adding the highlighted lines in the following example:</span></span>

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

<span data-ttu-id="456ea-181">Die Swagger-Benutzeroberfläche dokumentiert nun deutlich die erwarteten HTTP-Antwortcodes:</span><span class="sxs-lookup"><span data-stu-id="456ea-181">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Swagger-Benutzeroberfläche, die die Klassenbeschreibung „Gibt das neu erstellte Todo-Element zurück“ nach der Antwort anzeigt sowie „400“ für den Statuscode und „Wenn das Element NULL ist“ als Grund unter den Antwortnachrichten](sample_images/data-annotations-response-types.png)

<span data-ttu-id="456ea-183">In ASP.NET Core 2.2 oder höher können Konventionen als Alternative verwendet werden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="456ea-183">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="456ea-184">Weitere Informationen finden Sie unter [Verwenden von Web-API-Konventionen](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="456ea-184">For more information, see [Use web API conventions](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).</span></span>

<span data-ttu-id="456ea-185">Informationen zum Anpassen der Benutzeroberfläche finden Sie hier: [Anpassen der Benutzeroberfläche](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span><span class="sxs-lookup"><span data-stu-id="456ea-185">For information on customizing the UI see: [Customize the UI](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)</span></span>
