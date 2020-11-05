---
title: Erste Schritte mit NSwag und ASP.NET Core
author: zuckerthoben
description: Erfahren Sie, wie Sie NSwag zum Generieren von Dokumentationen und Hilfeseiten für eine ASP.NET Core-Web-API-App verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 78d58d4d544c33862cf502ce63e83560e8009c65
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060572"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Erste Schritte mit NSwag und ASP.NET Core

Von [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) und [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag bietet die folgenden Funktionen:

* Die Möglichkeit, die Swagger-Benutzeroberfläche und den Swagger-Generator zu verwenden.
* Flexible Codegenerierung.

Mit NSwag müssen Sie keine API&mdash; besitzen, sondern können APIs von Drittanbietern verwenden, die Swagger integrieren und eine Clientimplementierung generieren. Mit NSwag können Sie den Entwicklungszyklus beschleunigen und sich problemlos an API-Änderungen anpassen.

## <a name="register-the-nswag-middleware"></a>Registrieren der NSwag-Middleware

Die NSwag-Middleware wird für folgende Zwecke registriert:

* Generieren Sie die Swagger-Spezifikation für die implementierte Web-API.
* Stellen Sie die Swagger-Benutzeroberfläche zum Durchsuchen und Testen die Web-API bereit.

Um [NSwag](https://github.com/RicoSuter/NSwag) mit ASP.NET Core-Middleware zu verwenden, installieren Sie das NuGet-Paket [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Dieses Paket enthält die Middleware zum Generieren und Bereitstellen der Swagger-Spezifikation, der Swagger-Benutzeroberfläche (v2 und v3) und der [ReDoc-Benutzeroberfläche](https://github.com/Rebilly/ReDoc).

Verwenden Sie einen der folgenden Ansätze, um das NuGet-Paket „NSwag“ zu installieren:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aus dem Fenster **Paket-Manager-Konsole** :
  * Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.
  * Navigieren Sie zu dem Verzeichnis, in dem die *TodoApi.csproj* -Datei gespeichert ist.
  * Führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* Aus dem Dialogfeld **NuGet-Pakete verwalten** :
  * Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
  * Legen Sie die **Paketquelle** auf „nuget.org“ fest.
  * Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.
  * Wählen Sie das Paket „NSwag.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...** .
* Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.
* Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.
* Wählen Sie das Paket „NSwag.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Hinzufügen und Konfigurieren von Swagger-Middleware

Sie können Swagger in Ihrer ASP.NET Core-App hinzufügen und konfigurieren, indem Sie die folgenden Schritte ausführen:

* Registrieren Sie in der `Startup.ConfigureServices`-Methode die erforderlichen Swagger-Dienste:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* Aktivieren Sie die Middleware in der `Startup.Configure`-Methode, um die generierte Swagger-Spezifikation und die Swagger-Benutzeroberfläche zu verarbeiten:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Starten Sie die App. Navigieren Sie zu:
  * `http://localhost:<port>/swagger`, um die Swagger-Benutzeroberfläche anzuzeigen.
  * `http://localhost:<port>/swagger/v1/swagger.json`, um die Swagger-Spezifikation anzuzeigen.

## <a name="code-generation"></a>Codeerzeugung

Durch die Auswahl einer der folgenden Optionen können Sie die Funktion zur Codegenerierung von NSwag nutzen:

* [NSwagStudio:](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) Dies ist eine Windows-Desktop-App zum Generieren von API-Clientcode in C# und TypeScript.
* Die NuGet-Pakete [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) oder [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/), um Code innerhalb des Projekts zu generieren.
* NSwag über die [Befehlszeile](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Das NuGet-Paket [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild).
* [Unchase OpenAPI (Swagger) Connected Service:](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) Dies ist ein mit Visual Studio verbundener Dienst zum Generieren von API-Clientcode in C# oder TypeScript. Es werden auch C#-Controller für OpenAPI-Dienste mit NSwag generiert.

### <a name="generate-code-with-nswagstudio"></a>Generieren von Code mit NSwagStudio

* Installieren Sie NSwagStudio anhand der Anweisungen im [NSwagStudio-GitHub-Repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Auf der Releaseseite von NSwag können Sie eine XCOPY-Version herunterladen, die ohne Installation und Administratorrechte gestartet werden kann.
* Starten Sie NSwagStudio, und geben Sie die Datei-URL *swagger.json* in das Textfeld **URL zur Spezifizierung des Swaggers** ein. Beispiel: *http://localhost:44354/swagger/v1/swagger.json*
* Klicken Sie auf die Schaltfläche **Lokale Kopie erstellen** , um eine JSON-Darstellung Ihrer Swagger-Spezifikation zu generieren.

  ![Erstellen einer lokalen Kopie der Swagger-Spezifikation](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* Klicken Sie im Bereich **Ausgaben** auf das Kontrollkästchen **CSharp-Client**. Je nach Ihrem Projekt können Sie auch den **TypeScript Client** oder **CSharp-Web-API-Controller** auswählen. Bei der Auswahl des **CSharp-Web-API-Controllers** erstellt eine Dienstspezifikation den Dienst neu, und dient als eine umgekehrte Generierung.
* Klicken Sie auf **Ausgaben generieren** , um eine vollständige C#-Clientimplementierung des *TodoApi.NSwag* -Projekts durchzuführen. Klicken Sie auf die Registerkarte **CSharp-Client** , damit der generierte Clientcode angezeigt wird:

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> Die C#-Clientcode wird auf Grundlage Ihrer Auswahl auf der Registerkarte **Einstellungen** generiert. Ändern Sie die Einstellungen, um Aufgaben wie das Umbenennen von Standardnamespaces und die Generierung von synchronen Methoden auszuführen.

* Kopieren Sie den generierten C#-Code in eine Datei in das Clientprojekt, das die API verwendet.
* Verwendung der Web-API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Anpassen der API-Dokumentation

Swagger umfasst Optionen zum Dokumentieren des Objektmodells, um die Verwendung der Web-API zu vereinfachen.

### <a name="api-info-and-description"></a>API-Informationen und -Beschreibung

In der `Startup.ConfigureServices`-Methode werden Informationen wie Autor, Lizenz und Beschreibung durch die Konfigurationsaktion hinzugefügt, die an die `AddSwaggerDocument`-Methode übergeben wird:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:

![Swagger-Benutzeroberfläche mit Versionsinformationen](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML-Kommentare

Um XML-Kommentaren zu aktivieren, führen Sie die folgenden Schritte aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.
* Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ* -Datei hinzu:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
* Überprüfen Sie das Feld **XML-Dokumentationsdatei** im Abschnitt **Ausgabe** der Registerkarte **Erstellen**.

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* Drücken und halten Sie im *Lösungspad* die **CONTROL** -Taste, und klicken Sie auf den Projektnamen. Navigieren Sie zu **Extras** > **Datei bearbeiten**.
* Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ* -Datei hinzu:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Öffnen Sie das Dialogfeld **Projektoptionen** , und klicken Sie dann auf **Erstellen** > **Compiler**.
* Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**.

::: moniker-end

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ* -Datei hinzu:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Datenanmerkungen

::: moniker range="<= aspnetcore-2.0"

Da NSwag [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection) verwendet, und der empfohlene Rückgabetyp ist für Web-API-Aktionen [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), kann nicht abgeleitet werden, was Ihre Aktion ausführt und was zurückgegeben wird.

Betrachten Sie das folgende Beispiel:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Die vorherige Aktion gibt `IActionResult` zurück. Innerhalb der Aktion gibt sie jedoch [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) oder [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) zurück. Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt. Markieren Sie die Aktion mit den folgenden Attributen:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Da NSwag [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) verwendet und der empfohlene Rückgabetyp für Web-API-Aktionen [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601) ist, kann nur der von `T` definierte Rückgabetyp abgeleitet werden. Sie können nicht automatisch andere mögliche Rückgabetypen ableiten.

Betrachten Sie das folgende Beispiel:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Die vorherige Aktion gibt `ActionResult<T>` zurück. In der Aktion gibt sie [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) zurück. Da der Controller das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut aufweist, ist auch eine [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)-Antwort möglich. Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses). Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt. Markieren Sie die Aktion mit den folgenden Attributen:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

In ASP.NET Core 2.2 oder höher können Sie stattdessen Konventionen verwenden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen. Weitere Informationen finden Sie unter <xref:web-api/advanced/conventions>.

::: moniker-end

Der Swagger-Generator kann diese Aktion nun genau beschreiben, und generierte Clients wissen, was sie beim Aufrufen des Endpunkts empfangen. Es wird empfohlen, alle Aktionen mit diesen Attributen zu markieren.

Weitere Informationen zu den Richtlinien dazu, welche HTTP-Antworten Ihre API-Aktionen zurückgeben sollten, finden Sie in der [RFC 7231 specification (Spezifikation von RFC 7231)](https://tools.ietf.org/html/rfc7231#section-4.3).
