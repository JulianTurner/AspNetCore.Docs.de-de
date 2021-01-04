---
title: Erstellen einer Blazor-App mit einer Aufgabenliste
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/14/2020
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 87626ff30589de82a04c95634fc0dcbcf2eeac18
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507006"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="ea349-103">Erstellen einer Blazor-App mit einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="ea349-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="ea349-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ea349-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ea349-105">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="ea349-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="ea349-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ea349-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ea349-107">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="ea349-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ea349-108">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="ea349-108">Modify Razor components</span></span>
> * <span data-ttu-id="ea349-109">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="ea349-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ea349-110">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="ea349-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="ea349-111">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Aufgabenlisten-App.</span><span class="sxs-lookup"><span data-stu-id="ea349-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ea349-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ea349-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="ea349-113">Erstellen einer Aufgabenlisten-Blazor-App</span><span class="sxs-lookup"><span data-stu-id="ea349-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="ea349-114">Erstellen Sie in einer Befehlsshell eine neue Blazor-App namens `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="ea349-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="ea349-115">Mit dem obigen Befehl wird ein Ordner mit dem Namen `TodoList` mit der `-o|--output`-Option zum Speichern der App erstellt.</span><span class="sxs-lookup"><span data-stu-id="ea349-115">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="ea349-116">Der `TodoList`-Ordner ist der *Stammordner* des Projekts.</span><span class="sxs-lookup"><span data-stu-id="ea349-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="ea349-117">Ändern Sie das Verzeichnis mit dem folgenden Befehl in den Ordner `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="ea349-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="ea349-118">Fügen Sie der App mithilfe des folgenden Befehls eine neue `Todo` Razor-Komponente hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea349-118">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="ea349-119">Die Option `-n|--name` im vorherigen Befehl gibt den Namen der neuen Razor-Komponente an.</span><span class="sxs-lookup"><span data-stu-id="ea349-119">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="ea349-120">Die neue Komponente wird im Ordner `Pages` des Projekts mit der `-o|--output`-Option erstellt.</span><span class="sxs-lookup"><span data-stu-id="ea349-120">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="ea349-121">Der erste Buchstabe von Razor-Komponentendateinamen muss großgeschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="ea349-121">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="ea349-122">Öffnen Sie den `Pages`-Ordner. Überprüfen Sie, ob der `Todo`-Komponentendateiname mit einem großgeschriebenen ersten Buchstaben (`T`) beginnt.</span><span class="sxs-lookup"><span data-stu-id="ea349-122">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="ea349-123">Der Dateiname sollte `Todo.razor` lauten.</span><span class="sxs-lookup"><span data-stu-id="ea349-123">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="ea349-124">Öffnen Sie die `Todo`-Komponente in einem beliebigen Datei-Editor, und fügen Sie am Anfang der Datei eine `@page` Razor-Anweisung mit der relativen URL `/todo` hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea349-124">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="ea349-125">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea349-125">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo0.razor?highlight=1)]

   <span data-ttu-id="ea349-126">Speichern Sie die Datei `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="ea349-126">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="ea349-127">Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea349-127">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="ea349-128">Die Komponente `NavMenu` wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="ea349-128">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="ea349-129">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in einer App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="ea349-129">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="ea349-130">Die `NavLink`-Komponente zeigt einen Hinweis auf der Benutzeroberfläche der App an, wenn die Komponenten-URL von der App geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ea349-130">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="ea349-131">Fügen Sie in der ungeordneten Liste (`<ul>...</ul>`) der `NavMenu`-Komponente das folgende Listenelement (`<li>...</li>`) und die folgende `NavLink`-Komponente für die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea349-131">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="ea349-132">In `Shared/NavMenu.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea349-132">In `Shared/NavMenu.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/NavMenu.razor?highlight=5-9)]

   <span data-ttu-id="ea349-133">Speichern Sie die Datei `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="ea349-133">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="ea349-134">Erstellen Sie die App, und führen Sie sie aus, indem Sie den [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)-Befehl in der Befehlsshell im Ordner `TodoList` ausführen.</span><span class="sxs-lookup"><span data-stu-id="ea349-134">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="ea349-135">Wenn die App ausgeführt wird, navigieren Sie zur neuen To-do-Seite, indem Sie in der Navigationsleiste der App den Link **`Todo`** auswählen, der die Seite unter `/todo` lädt.</span><span class="sxs-lookup"><span data-stu-id="ea349-135">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="ea349-136">Lassen Sie die App die Befehlsshell weiter ausführen.</span><span class="sxs-lookup"><span data-stu-id="ea349-136">Leave the app running the command shell.</span></span> <span data-ttu-id="ea349-137">Jedes Mal, wenn eine Datei gespeichert wird, wird die App automatisch erneut erstellt.</span><span class="sxs-lookup"><span data-stu-id="ea349-137">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="ea349-138">Die Verbindung zwischen dem Browser und der App wird während der Kompilierung und des Neustarts vorübergehend unterbrochen.</span><span class="sxs-lookup"><span data-stu-id="ea349-138">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="ea349-139">Die Seite im Browser wird automatisch erneut geladen, wenn die Verbindung wiederhergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ea349-139">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="ea349-140">Fügen Sie dem Stamm des Projekts (der `TodoList`-Ordner) eine `TodoItem.cs`-Datei hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="ea349-140">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="ea349-141">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="ea349-141">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="ea349-142">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="ea349-142">`TodoItem.cs`:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="ea349-143">Navigieren Sie zur `Todo`-Komponente zurück, und führen Sie Folgendes durch:</span><span class="sxs-lookup"><span data-stu-id="ea349-143">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="ea349-144">Fügen Sie ein Feld für die To-Do-Elemente im `@code`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea349-144">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="ea349-145">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="ea349-145">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="ea349-146">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="ea349-146">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="ea349-147">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="ea349-147">`Pages/Todo.razor`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,13)]

1. <span data-ttu-id="ea349-148">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="ea349-148">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="ea349-149">Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="ea349-149">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="ea349-150">Speichern Sie die Datei `TodoItem.cs` und die aktualisierte Datei `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="ea349-150">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="ea349-151">In der Befehlsshell wird die App automatisch neu erstellt, wenn die Dateien gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="ea349-151">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="ea349-152">Der Browser verliert vorübergehend seine Verbindung mit der App und lädt die Seite dann neu, wenn die Verbindung erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ea349-152">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="ea349-153">Bei Auswahl der Schaltfläche **`Add todo`** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="ea349-153">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="ea349-154">Fügen Sie der `Todo`-Komponente eine `AddTodo`-Methode hinzu, und registrieren Sie die Methode für die Schaltfläche mithilfe des `@onclick`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="ea349-154">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="ea349-155">Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="ea349-155">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="ea349-156">Fügen Sie am oberen Rand des `@code`-Blocks ein `newTodo`-Zeichenfolgenfeld hinzu, um den Titel des neuen To-do-Elements zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="ea349-156">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=3)]

   <span data-ttu-id="ea349-157">Ändern Sie das`<input>`-Textelement, um `newTodo` mit dem `@bind`-Attribut zu verknüpfen:</span><span class="sxs-lookup"><span data-stu-id="ea349-157">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="ea349-158">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ea349-158">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="ea349-159">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="ea349-159">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="ea349-160">Speichern Sie die Datei `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="ea349-160">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="ea349-161">Die App wird automatisch in der Befehlsshell neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="ea349-161">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="ea349-162">Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.</span><span class="sxs-lookup"><span data-stu-id="ea349-162">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="ea349-163">Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="ea349-163">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="ea349-164">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ea349-164">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="ea349-165">Ändern Sie `@todo.Title` in ein `<input>`-Element, das mit `@bind` mit `todo.Title` verknüpft ist:</span><span class="sxs-lookup"><span data-stu-id="ea349-165">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=4-7)]

1. <span data-ttu-id="ea349-166">Aktualisieren Sie den `<h3>`-Header, um die Anzahl der nicht abgeschlossene To-do-Elemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="ea349-166">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="ea349-167">Die fertige `Todo`-Komponente (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="ea349-167">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="ea349-168">Speichern Sie die Datei `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="ea349-168">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="ea349-169">Die App wird automatisch in der Befehlsshell neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="ea349-169">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="ea349-170">Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.</span><span class="sxs-lookup"><span data-stu-id="ea349-170">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="ea349-171">Fügen Sie Elemente hinzu, und markieren Sie To-do-Elemente als abgeschlossen, um die Komponente zu testen.</span><span class="sxs-lookup"><span data-stu-id="ea349-171">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="ea349-172">Wenn Sie fertig sind, fahren Sie die App in der Befehlsshell herunter.</span><span class="sxs-lookup"><span data-stu-id="ea349-172">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="ea349-173">In vielen Befehlsshells kann der Tastaturbefehl <kbd>STRG</kbd>+<kbd>C</kbd> verwendet werden, um eine App anzuhalten.</span><span class="sxs-lookup"><span data-stu-id="ea349-173">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ea349-174">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ea349-174">Next steps</span></span>

<span data-ttu-id="ea349-175">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="ea349-175">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ea349-176">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="ea349-176">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="ea349-177">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="ea349-177">Modify Razor components</span></span>
> * <span data-ttu-id="ea349-178">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="ea349-178">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="ea349-179">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="ea349-179">Use routing in a Blazor app</span></span>

<span data-ttu-id="ea349-180">Informationen über Tools für ASP.NET-Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="ea349-180">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
