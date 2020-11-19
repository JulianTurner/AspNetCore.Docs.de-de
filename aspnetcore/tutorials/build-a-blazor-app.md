---
title: Erstellen einer Blazor-App mit einer Aufgabenliste
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2020
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
ms.openlocfilehash: 1efcd167d9a45b2def271b239c9b360749d72791
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570184"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>Erstellen einer Blazor-App mit einer Aufgabenliste

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Aufgabenlisten-Blazor-App-Projekts
> * Ändern von Razor-Komponenten
> * Verwenden von Ereignisbehandlung und Datenbindung in Komponenten
> * Verwenden von Routing in einer Blazor-App

Am Ende dieses Tutorials verfügen Sie über eine funktionierende Aufgabenlisten-App.

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>Erstellen einer Aufgabenlisten-Blazor-App

1. Erstellen Sie in einer Befehlsshell eine neue Blazor-App namens `TodoList`:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Der oben stehende Befehl erstellt einen Ordner namens `TodoList` zum Speichern der App. Der `TodoList`-Ordner ist der *Stammordner* des Projekts. Ändern Sie das Verzeichnis mit dem folgenden Befehl in den Ordner `TodoList`:

   ```dotnetcli
   cd TodoList
   ```

1. Fügen Sie mit dem folgenden Befehl der App im Ordner `Pages` eine neue `Todo` Razor-Komponente hinzu:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Der erste Buchstabe von Razor-Komponentendateinamen muss großgeschrieben werden. Öffnen Sie den `Pages`-Ordner. Überprüfen Sie, ob der `Todo`-Komponentendateiname mit einem großgeschriebenen ersten Buchstaben (`T`) beginnt. Der Dateiname sollte `Todo.razor` lauten.

1. Geben Sie in `Pages/Todo.razor` das ursprüngliche Markup für die Komponente an:

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.

   Die Komponente `NavMenu` (`Shared/NavMenu.razor`) wird im Layout der App verwendet. Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.

   Fügen Sie ein `<NavLink>`-Element für die `Todo`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der `Shared/NavMenu.razor`-Datei hinzufügen:

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Erstellen Sie die App, und führen Sie sie aus, indem Sie den `dotnet run`-Befehl in der Befehlsshell im `TodoList`-Ordner ausführen. Besuchen Sie unter `https://localhost:5001/todo` die neue „Todo“-Seite, um sicherzustellen, dass der Link zur `Todo`-Komponente funktioniert.

1. Fügen Sie dem Stamm des Projekts (der `TodoList`-Ordner) eine `TodoItem.cs`-Datei hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll. Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. Kehren Sie zur `Todo`-Komponente (`Pages/Todo.razor`) zurück:

   * Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu. Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.
   * Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können. Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. Halten Sie die Ausführung der App in der Befehlsshell an. In vielen Befehlsshells kann der Tastaturbefehl <kbd>STRG</kbd>+<kbd>C</kbd> verwendet werden, um eine App anzuhalten. Erstellen Sie die App neu, und führen Sie sie mit dem `dotnet run`-Befehl aus. Bei Auswahl der Schaltfläche **`Add todo`** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.

1. Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl. Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen. Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. Halten Sie die Ausführung der App in der Befehlsshell an. Erstellen Sie die App neu, und führen Sie sie mit dem `dotnet run`-Befehl aus. Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.

1. Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen. Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft. Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Die fertige `Todo`-Komponente (`Pages/Todo.razor`):

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. Halten Sie die Ausführung der App in der Befehlsshell an. Erstellen Sie die App neu, und führen Sie sie mit dem `dotnet run`-Befehl aus. Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Aufgabenlisten-Blazor-App-Projekts
> * Ändern von Razor-Komponenten
> * Verwenden von Ereignisbehandlung und Datenbindung in Komponenten
> * Verwenden von Routing in einer Blazor-App

Informationen über Tools für ASP.NET-Core Blazor:

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
