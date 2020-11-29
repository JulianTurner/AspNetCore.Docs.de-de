---
title: Erstellen einer Blazor-App mit einer Aufgabenliste
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870437"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="13348-103">Erstellen einer Blazor-App mit einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="13348-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="13348-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13348-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="13348-105">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="13348-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="13348-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="13348-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="13348-107">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="13348-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="13348-108">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="13348-108">Modify Razor components</span></span>
> * <span data-ttu-id="13348-109">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="13348-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="13348-110">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="13348-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="13348-111">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Aufgabenlisten-App.</span><span class="sxs-lookup"><span data-stu-id="13348-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="13348-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="13348-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="13348-113">Erstellen einer Aufgabenlisten-Blazor-App</span><span class="sxs-lookup"><span data-stu-id="13348-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="13348-114">Erstellen Sie in einer Befehlsshell eine neue Blazor-App namens `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="13348-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="13348-115">Der oben stehende Befehl erstellt einen Ordner namens `TodoList` zum Speichern der App.</span><span class="sxs-lookup"><span data-stu-id="13348-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="13348-116">Der `TodoList`-Ordner ist der *Stammordner* des Projekts.</span><span class="sxs-lookup"><span data-stu-id="13348-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="13348-117">Ändern Sie das Verzeichnis mit dem folgenden Befehl in den Ordner `TodoList`:</span><span class="sxs-lookup"><span data-stu-id="13348-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="13348-118">Fügen Sie mit dem folgenden Befehl der App im Ordner `Pages` eine neue `Todo` Razor-Komponente hinzu:</span><span class="sxs-lookup"><span data-stu-id="13348-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="13348-119">Der erste Buchstabe von Razor-Komponentendateinamen muss großgeschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="13348-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="13348-120">Öffnen Sie den `Pages`-Ordner. Überprüfen Sie, ob der `Todo`-Komponentendateiname mit einem großgeschriebenen ersten Buchstaben (`T`) beginnt.</span><span class="sxs-lookup"><span data-stu-id="13348-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="13348-121">Der Dateiname sollte `Todo.razor` lauten.</span><span class="sxs-lookup"><span data-stu-id="13348-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="13348-122">Geben Sie in `Pages/Todo.razor` das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="13348-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="13348-123">Speichern Sie die Datei `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="13348-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="13348-124">Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="13348-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="13348-125">Die Komponente `NavMenu` (`Shared/NavMenu.razor`) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="13348-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="13348-126">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="13348-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="13348-127">Fügen Sie ein `<NavLink>`-Element für die `Todo`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der `Shared/NavMenu.razor`-Datei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="13348-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="13348-128">Speichern Sie die Datei `Shared/NavMenu.razor`.</span><span class="sxs-lookup"><span data-stu-id="13348-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="13348-129">Erstellen Sie die App, und führen Sie sie aus, indem Sie den [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)-Befehl in der Befehlsshell im Ordner `TodoList` ausführen.</span><span class="sxs-lookup"><span data-stu-id="13348-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="13348-130">Besuchen Sie unter `https://localhost:5001/todo` die neue „Todo“-Seite, um sicherzustellen, dass der Navigationslink der Seitenleiste zur `Todo`-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="13348-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="13348-131">Fügen Sie dem Stamm des Projekts (der `TodoList`-Ordner) eine `TodoItem.cs`-Datei hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="13348-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="13348-132">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="13348-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="13348-133">Kehren Sie zur `Todo`-Komponente (`Pages/Todo.razor`) zurück:</span><span class="sxs-lookup"><span data-stu-id="13348-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="13348-134">Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="13348-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="13348-135">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="13348-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="13348-136">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="13348-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="13348-137">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="13348-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="13348-138">Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="13348-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="13348-139">Speichern Sie die Datei `TodoItem.cs` und die aktualisierte Datei `Pages/Todo.razor`.</span><span class="sxs-lookup"><span data-stu-id="13348-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="13348-140">In der Befehlsshell wird die App automatisch neu erstellt, wenn die Dateien gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="13348-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="13348-141">Der Browser verliert vorübergehend seine Verbindung mit der App und lädt die Seite dann neu, wenn die Verbindung erneut hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="13348-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="13348-142">Bei Auswahl der Schaltfläche **`Add todo`** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="13348-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="13348-143">Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl.</span><span class="sxs-lookup"><span data-stu-id="13348-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="13348-144">Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="13348-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="13348-145">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="13348-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="13348-146">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="13348-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="13348-147">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="13348-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="13348-148">Speichern Sie die Datei `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="13348-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="13348-149">Die App wird automatisch in der Befehlsshell neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="13348-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="13348-150">Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.</span><span class="sxs-lookup"><span data-stu-id="13348-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="13348-151">Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="13348-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="13348-152">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="13348-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="13348-153">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="13348-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="13348-154">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="13348-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="13348-155">Die fertige `Todo`-Komponente (`Pages/Todo.razor`):</span><span class="sxs-lookup"><span data-stu-id="13348-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="13348-156">Speichern Sie die Datei `Pages/ToDo.razor`.</span><span class="sxs-lookup"><span data-stu-id="13348-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="13348-157">Die App wird automatisch in der Befehlsshell neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="13348-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="13348-158">Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.</span><span class="sxs-lookup"><span data-stu-id="13348-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="13348-159">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="13348-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="13348-160">Wenn Sie fertig sind, fahren Sie die App in der Befehlsshell herunter.</span><span class="sxs-lookup"><span data-stu-id="13348-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="13348-161">In vielen Befehlsshells kann der Tastaturbefehl <kbd>STRG</kbd>+<kbd>C</kbd> verwendet werden, um eine App anzuhalten.</span><span class="sxs-lookup"><span data-stu-id="13348-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="13348-162">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="13348-162">Next steps</span></span>

<span data-ttu-id="13348-163">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="13348-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="13348-164">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="13348-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="13348-165">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="13348-165">Modify Razor components</span></span>
> * <span data-ttu-id="13348-166">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="13348-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="13348-167">Verwenden von Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="13348-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="13348-168">Informationen über Tools für ASP.NET-Core Blazor:</span><span class="sxs-lookup"><span data-stu-id="13348-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
