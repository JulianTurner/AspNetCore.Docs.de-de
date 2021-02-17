---
title: Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Back-End-Dienste mit ASP.NET Core MVC zur Unterstützung nativer mobiler Apps erstellt werden.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564022"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="fb0fe-103">Erstellen von Back-End-Diensten für native mobile Apps mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb0fe-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="fb0fe-104">Von [James Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="fb0fe-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="fb0fe-105">Mobile Apps können mit Back-End-Diensten von ASP.NET Core kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="fb0fe-106">Anweisungen zum Herstellen einer Verbindung zwischen lokalen Webdienste von iOS-Simulatoren und Android-Emulatoren finden Sie unter [Herstellen von Verbindungen mit lokalen Webdiensten von iOS-Simulatoren und Android-Emulatoren](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="fb0fe-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="fb0fe-107">Anzeigen oder Herunterladen von Beispielcode für Back-End-Dienste</span><span class="sxs-lookup"><span data-stu-id="fb0fe-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="fb0fe-108">Die native mobile Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="fb0fe-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="fb0fe-109">In diesem Tutorial wird veranschaulicht, wie Back-End-Dienste mithilfe von ASP.net Core erstellt werden, um systemeigene Mobile Apps</span><span class="sxs-lookup"><span data-stu-id="fb0fe-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="fb0fe-110">Dabei wird die [xamarin. Forms-App](/xamarin/xamarin-forms/data-cloud/consuming/rest) mit dem nativen Client verwendet, die separate native Clients für Android, IOS und Windows umfasst.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="fb0fe-111">Sie können das verlinkten Tutorial befolgen, um die native App zu erstellen (und die erforderlichen kostenfreien Xamarin-Tools zu installieren) und um die Xamarin-Beispielprojektmappe herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="fb0fe-112">Das xamarin-Beispiel enthält ein ASP.net Core Web-API-Dienste-Projekt, das in der ASP.net Core-APP dieses Artikels ersetzt wird (ohne dass vom Client erforderliche Änderungen vorgenommen werden müssen).</span><span class="sxs-lookup"><span data-stu-id="fb0fe-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Ausführen der ToDoRest-App auf einem Android-Smartphone](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="fb0fe-114">Features</span><span class="sxs-lookup"><span data-stu-id="fb0fe-114">Features</span></span>

<span data-ttu-id="fb0fe-115">Die Anwendung "um [" unterstützt](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) das auflisten, hinzufügen, löschen und Aktualisieren von To-Do Elementen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="fb0fe-116">Jedes Element verfügt über eine ID, einen Namen, Hinweise und über eine Eigenschaft, die angibt, ob ein Element abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="fb0fe-117">In der Hauptansicht der Elemente werden, wie oben dargestellt, die Namen der einzelnen Elemente aufgeführt. Sie sind mit einem Häkchen versehen, wenn sie abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="fb0fe-118">Durch Tippen auf das Symbol `+` wird ein Dialogfeld zum Hinzufügen eines Elements geöffnet:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Dialogfeld „Element hinzufügen“](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="fb0fe-120">Durch Tippen auf ein Element auf dem Bildschirm mit der Hauptliste wird ein Dialogfeld zur Bearbeitung geöffnet, in dem die Einstellungen „Name“, „Hinweise“, und „Fertig“ für das Element geändert oder das Element gelöscht werden kann:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Dialogfeld „Element bearbeiten“](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="fb0fe-122">Aktualisieren Sie die APP-Konstante, um Sie selbst für ASP.net Core die APP zu testen, die im nächsten Abschnitt des Computers erstellt wurde [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) .</span><span class="sxs-lookup"><span data-stu-id="fb0fe-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="fb0fe-123">Android-Emulatoren werden nicht auf dem lokalen Computer ausgeführt und verwenden eine Loopback-IP (10.0.2.2), um mit dem lokalen Computer zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="fb0fe-124">Verwenden Sie [xamarin. Essentials](/xamarin/essentials/device-information/) , um zu ermitteln, welcher Betriebssystem ausgeführt wird, um die richtige URL zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="fb0fe-125">Navigieren Sie zum [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) Projekt, und öffnen Sie die [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) Datei.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="fb0fe-126">Die *Constants.cs* -Datei enthält die folgende Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="fb0fe-127">Optional können Sie den Webdienst in einem clouddienst wie Azure bereitstellen und den aktualisieren `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="fb0fe-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="fb0fe-128">Erstellen des ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="fb0fe-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="fb0fe-129">Erstellen Sie in Visual Studio eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="fb0fe-130">Wählen Sie die Web-API-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-130">Choose the Web API template.</span></span> <span data-ttu-id="fb0fe-131">Benennen Sie das Projekt mit " *tdoapi*".</span><span class="sxs-lookup"><span data-stu-id="fb0fe-131">Name the project *TodoAPI*.</span></span>

![Dialogfeld „Neue ASP.NET-Webanwendung“ mit ausgewählter Web-API-Projektvorlage](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="fb0fe-133">Die APP sollte auf alle an Port 5000 gerichteten Anforderungen (einschließlich Klartext-HTTP-Datenverkehr) für unseren mobilen Client Antworten.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="fb0fe-134">Update *Startup.cs* wird <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> nicht in der Entwicklung ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="fb0fe-135">Führen Sie die APP direkt anstatt hinter IIS Express aus.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="fb0fe-136">IIS Express ignoriert standardmäßig nicht lokale Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="fb0fe-137">Führen Sie [dotnet Run](/dotnet/core/tools/dotnet-run) über eine Eingabeaufforderung aus, oder wählen Sie in der Visual Studio-Symbolleiste in der Dropdown Liste Debugziel das Profil App-Name aus.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="fb0fe-138">Fügen Sie eine Modellklasse hinzu, in der die To-Do-Elemente dargestellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="fb0fe-139">Markieren Sie erforderliche Felder mit dem Attribut `[Required]`:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="fb0fe-140">Die API-Methoden müssen mit Daten arbeiten können.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="fb0fe-141">Verwenden Sie die gleiche `ITodoRepository`-Schnittstelle, die im ursprünglichen Xamarin-Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="fb0fe-142">In diesem Beispiel verwendet die Implementierung nur eine private Sammlung von Elementen:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="fb0fe-143">Konfigurieren Sie die Implementierung in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="fb0fe-144">Erstellen des Controllers</span><span class="sxs-lookup"><span data-stu-id="fb0fe-144">Creating the Controller</span></span>

<span data-ttu-id="fb0fe-145">Fügen Sie dem Projekt einen neuen Controller (" [dedoitemscontroller](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs)") hinzu.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="fb0fe-146">Er sollte von Erben <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="fb0fe-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="fb0fe-147">Fügen Sie das Attribut `Route` hinzu, um anzugeben, dass der Controller Anforderungen für Pfade bearbeitet, die mit `api/todoitems` beginnen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="fb0fe-148">Das Token `[controller]` in der Route wird durch den Namen des Controllers ersetzt (dabei wird das Suffix `Controller` ausgelassen) und ist für globale Routen besonders nützlich.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="fb0fe-149">Weitere Informationen zu [Routing](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="fb0fe-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="fb0fe-150">Damit der Controller funktioniert, ist eine `ITodoRepository`-Schnittstelle erforderlich. Fordern Sie über den Konstruktor des Controllers eine Instanz dieses Typs an.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="fb0fe-151">Zur Laufzeit wird diese Instanz über die Frameworkunterstützung für [Dependency Injection](../fundamentals/dependency-injection.md) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="fb0fe-152">Diese API unterstützt vier verschiedene HTTP-Verben zur Durchführung von CRUD-Vorgängen für die Datenquelle (CRUD = Create, Read, Update, Delete; Erstellen, Lesen, Aktualisieren, Löschen).</span><span class="sxs-lookup"><span data-stu-id="fb0fe-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="fb0fe-153">Am einfachsten ist der Lesevorgang, der einer HTTP GET-Anforderung entspricht.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="fb0fe-154">Lesen von Elementen</span><span class="sxs-lookup"><span data-stu-id="fb0fe-154">Reading Items</span></span>

<span data-ttu-id="fb0fe-155">Die Anforderung einer Liste mit Elementen erfolgt über eine GET-Anforderung an die Methode `List`.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="fb0fe-156">Das Attribut `[HttpGet]` in der Methode `List` gibt an, dass diese Aktion nur GET-Anforderungen verarbeiten soll.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="fb0fe-157">Die Route für diese Aktion ist die auf dem Controller angegebene Route.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="fb0fe-158">Sie müssen den Aktionsnamen nicht unbedingt als Teil der Route verwenden.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="fb0fe-159">Sie müssen nur sicherstellen, dass die einzelnen Aktionen über eine eindeutige Route verfügen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="fb0fe-160">Routingattribute können auf Controller- und auf Methodenebene für die Erstellung bestimmter Routen angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="fb0fe-161">Die `List` -Methode gibt einen 200 OK-Antwort Code und alle TODO-Elemente zurück, die als JSON serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="fb0fe-162">Sie können Ihre neue API-Methode mit einer Vielzahl von Tools testen, z.B. wie im Folgenden dargestellt mit [Postman](https://www.getpostman.com/docs/):</span><span class="sxs-lookup"><span data-stu-id="fb0fe-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Postman-Konsole mit einer GET-Anforderung für To-Do-Elemente und dem Antworttext, in dem die JSON für drei zurückgegebene Elemente angezeigt wird](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="fb0fe-164">Erstellen von Elementen</span><span class="sxs-lookup"><span data-stu-id="fb0fe-164">Creating Items</span></span>

<span data-ttu-id="fb0fe-165">Gemäß der Konvention wird die Erstellung neuer Datenelemente dem HTTP POST-Verb zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="fb0fe-166">Auf die Methode `Create` wird das Attribut `[HttpPost]` angewendet, und sie akzeptiert eine `TodoItem`-Instanz.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="fb0fe-167">Da das Argument `item` im POST-Text übergeben wird, gibt dieser Parameter das Attribut `[FromBody]` an.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="fb0fe-168">Innerhalb der Methode wird überprüft, ob das Element gültig ist und ob es bereits im Datenspeicher vorhanden ist. Wenn keine Probleme auftreten, wird es über das Repository hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="fb0fe-169">Bei der Überprüfung von `ModelState.IsValid` wird eine [Modellvalidierung](../mvc/models/validation.md) durchgeführt. Dies sollte bei jeder API-Methode geschehen, die Benutzereingaben akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="fb0fe-170">Das Beispiel verwendet einen `enum` , der Fehlercodes enthält, die an den mobilen Client übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="fb0fe-171">Testen Sie das Hinzufügen neuer Elemente mithilfe von Postman, indem Sie das POST-Verb auswählen und im Anforderungstext das neue Objekt im JSON-Format angeben.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="fb0fe-172">Sie sollten auch einen Anforderungsheader hinzufügen, in dem ein `Content-Type` von `application/json` angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Postman-Konsole mit einem POST-Verb und einer Antwort](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="fb0fe-174">Die Methode gibt das neu erstellte Element in der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="fb0fe-175">Aktualisieren von Elementen</span><span class="sxs-lookup"><span data-stu-id="fb0fe-175">Updating Items</span></span>

<span data-ttu-id="fb0fe-176">Datensätze werden mithilfe von HTTP PUT-Anforderungen geändert.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="fb0fe-177">Abgesehen von dieser Änderung ist die Methode `Edit` mit der Methode `Create` weitgehend identisch.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="fb0fe-178">Beachten Sie, dass die Aktion `Edit` die Antwort „`NotFound` (404)“ zurückgibt, wenn der Datensatz nicht gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="fb0fe-179">Ändern Sie zum Testen mit Postman das Verb in PUT.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="fb0fe-180">Geben Sie die aktualisierten Objektdaten in den Anforderungstext ein.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-180">Specify the updated object data in the Body of the request.</span></span>

![Postman-Konsole mit einem PUT-Verb und einer Antwort](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="fb0fe-182">Diese Methode gibt bei erfolgreicher Ausführung die Antwort „`NoContent` (204)“ für die Konsistenz mit der bereits vorhandenen API zurück.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="fb0fe-183">Löschen von Elementen | MSDN</span><span class="sxs-lookup"><span data-stu-id="fb0fe-183">Deleting Items</span></span>

<span data-ttu-id="fb0fe-184">Datensätze werden gelöscht, indem DELETE-Anforderungen an den Dienst gestellt werden und die ID des zu löschenden Elements übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="fb0fe-185">Wie bei Updates erhalten Anforderungen bei nicht vorhandenen Elementen die Antwort `NotFound`.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="fb0fe-186">Bei erfolgreicher Ausführung einer Anforderung hingegen wird die Antwort „`NoContent` (204)“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="fb0fe-187">Beachten Sie, dass beim Testen der DELETE-Funktion im Anforderungstext nichts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Postman-Konsole mit einer DELETE-Funktion und einer Antwort](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="fb0fe-189">Vermeiden von Overposting</span><span class="sxs-lookup"><span data-stu-id="fb0fe-189">Prevent over-posting</span></span>

<span data-ttu-id="fb0fe-190">Derzeit macht die Beispiel-App das gesamte `TodoItem`-Objekt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="fb0fe-191">In den Produktions-Apps sind die Daten, die eingegeben und mithilfe einer Teilmenge des Modells zurückgegeben werden, in der Regel eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="fb0fe-192">Hierfür gibt es mehrere Gründe, wobei die Sicherheit einer der Hauptgründe ist.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="fb0fe-193">Die Teilmenge eines Modells wird üblicherweise als Datenübertragungsobjekt (DTO, Data Transfer Object), Eingabemodell oder Anzeigemodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="fb0fe-194">In diesem Artikel wird das **DTO** verwendet.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="fb0fe-195">Ein DTO kann für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="fb0fe-195">A DTO may be used to:</span></span>

* <span data-ttu-id="fb0fe-196">Vermeiden Sie Overposting.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-196">Prevent over-posting.</span></span>
* <span data-ttu-id="fb0fe-197">Ausblenden von Eigenschaften, die Clients nicht anzeigen sollen</span><span class="sxs-lookup"><span data-stu-id="fb0fe-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="fb0fe-198">Auslassen einiger Eigenschaften, um die Nutzlast zu verringern</span><span class="sxs-lookup"><span data-stu-id="fb0fe-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="fb0fe-199">Vereinfachen von Objektgraphen, die geschachtelte Objekte enthalten</span><span class="sxs-lookup"><span data-stu-id="fb0fe-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="fb0fe-200">Vereinfachte Objektgraphen können für Clients zweckmäßiger sein.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="fb0fe-201">Informationen zum veranschaulichen des DTO-Ansatzes finden Sie unter [verhindern von overposting](xref:tutorials/first-web-api#prevent-over-posting) .</span><span class="sxs-lookup"><span data-stu-id="fb0fe-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="fb0fe-202">Allgemeine Konventionen für die Web-API</span><span class="sxs-lookup"><span data-stu-id="fb0fe-202">Common Web API Conventions</span></span>

<span data-ttu-id="fb0fe-203">Da Sie die Back-End-Dienste für Ihre App entwickeln, sollten Sie sich auch eine Reihe von konsistenten Konventionen oder Richtlinien für den Umgang mit bereichsübergreifenden Anliegen überlegen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="fb0fe-204">Im oben dargestellten Dienst haben Anforderungen für bestimmte Datensätze, die nicht gefunden werden konnten, beispielsweise die Antwort `NotFound` statt der Antwort `BadRequest` erhalten.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="fb0fe-205">Gleichermaßen haben für diesen Dienst durchgeführte Befehle, die gebundene Modelltypen übergeben haben, immer `ModelState.IsValid` überprüft und bei ungültigen Modelltypen eine `BadRequest` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="fb0fe-206">Sobald Sie eine gängige Richtlinie für Ihre APIs ermittelt haben, können Sie diese in der Regel in einen [Filter](../mvc/controllers/filters.md) einschließen.</span><span class="sxs-lookup"><span data-stu-id="fb0fe-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="fb0fe-207">Weitere Informationen zum [Einschließen gängiger API-Richtlinien in ASP.NET Core MVC-Anwendungen](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="fb0fe-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb0fe-208">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fb0fe-208">Additional resources</span></span>

- [<span data-ttu-id="fb0fe-209">Xamarin. Forms: Webdienst Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="fb0fe-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="fb0fe-210">Xamarin. Forms: nutzen Sie einen Rest-Webdienst</span><span class="sxs-lookup"><span data-stu-id="fb0fe-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="fb0fe-211">Microsoft Learn: Verwenden von Rest-Webdiensten in xamarin-apps</span><span class="sxs-lookup"><span data-stu-id="fb0fe-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="fb0fe-212">Microsoft Learn: Erstellen einer Web-API mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fb0fe-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
