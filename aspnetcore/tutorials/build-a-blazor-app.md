---
title: Erstellen einer Blazor-App mit einer Aufgabenliste
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
ms.openlocfilehash: d984023a1c46c5383d47a1634c54e61747b83d60
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101209"
---
# <a name="build-a-blazor-todo-list-app"></a>Erstellen einer Blazor-App mit einer Aufgabenliste

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

## <a name="create-a-todo-list-blazor-app"></a>Erstellen einer Aufgabenlisten-Blazor-App

1. Erstellen Sie in einer Befehlsshell eine neue Blazor-App namens `TodoList`:

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   Mit dem obigen Befehl wird ein Ordner mit dem Namen `TodoList` mit der `-o|--output`-Option zum Speichern der App erstellt. Der `TodoList`-Ordner ist der *Stammordner* des Projekts. Ändern Sie das Verzeichnis mit dem folgenden Befehl in den Ordner `TodoList`:

   ```dotnetcli
   cd TodoList
   ```

1. Fügen Sie der App mithilfe des folgenden Befehls eine neue `Todo` Razor-Komponente hinzu:

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   Die Option `-n|--name` im vorherigen Befehl gibt den Namen der neuen Razor-Komponente an. Die neue Komponente wird im Ordner `Pages` des Projekts mit der `-o|--output`-Option erstellt.

   > [!IMPORTANT]
   > Der erste Buchstabe von Razor-Komponentendateinamen muss großgeschrieben werden. Öffnen Sie den `Pages`-Ordner. Überprüfen Sie, ob der `Todo`-Komponentendateiname mit einem großgeschriebenen ersten Buchstaben (`T`) beginnt. Der Dateiname sollte `Todo.razor` lauten.

1. Öffnen Sie die `Todo`-Komponente in einem beliebigen Datei-Editor, und fügen Sie am Anfang der Datei eine `@page` Razor-Anweisung mit der relativen URL `/todo` hinzu.

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   Speichern Sie die Datei `Pages/Todo.razor`.

1. Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.

   Die Komponente `NavMenu` wird im Layout der App verwendet. Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in einer App vermeiden können. Die `NavLink`-Komponente zeigt einen Hinweis auf der Benutzeroberfläche der App an, wenn die Komponenten-URL von der App geladen wird.

   Fügen Sie in der ungeordneten Liste (`<ul>...</ul>`) der `NavMenu`-Komponente das folgende Listenelement (`<li>...</li>`) und die folgende `NavLink`-Komponente für die `Todo`-Komponente hinzu.

   In `Shared/NavMenu.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   Speichern Sie die Datei `Shared/NavMenu.razor`.

1. Erstellen Sie die App, und führen Sie sie aus, indem Sie den [`dotnet watch run`](xref:tutorials/dotnet-watch)-Befehl in der Befehlsshell im Ordner `TodoList` ausführen. Wenn die App ausgeführt wird, navigieren Sie zur neuen To-do-Seite, indem Sie in der Navigationsleiste der App den Link **`Todo`** auswählen, der die Seite unter `/todo` lädt.

   Lassen Sie die App die Befehlsshell weiter ausführen. Jedes Mal, wenn eine Datei gespeichert wird, wird die App automatisch erneut erstellt. Die Verbindung zwischen dem Browser und der App wird während der Kompilierung und des Neustarts vorübergehend unterbrochen. Die Seite im Browser wird automatisch erneut geladen, wenn die Verbindung wiederhergestellt wird.

1. Fügen Sie dem Stamm des Projekts (der `TodoList`-Ordner) eine `TodoItem.cs`-Datei hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll. Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse.

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > Wenn Sie Visual Studio verwenden, um die `ToDoItem.cs`-Datei und `ToDoItem`-Klasse zu erstellen, verwenden Sie einen der folgenden Ansätze:
   >
   > * Entfernen Sie den Namespace, der von Visual Studio für die Klasse generiert wird.
   > * Verwenden Sie die Schaltfläche **Kopieren** im vorangehenden Codeblock, und ersetzen Sie den gesamten Inhalt der von Visual Studio generierten Datei.

1. Navigieren Sie zur `Todo`-Komponente zurück, und führen Sie Folgendes durch:

   * Fügen Sie ein Feld für die To-Do-Elemente im `@code`-Block hinzu. Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.
   * Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können. Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. Speichern Sie die Datei `TodoItem.cs` und die aktualisierte Datei `Pages/Todo.razor`. In der Befehlsshell wird die App automatisch neu erstellt, wenn die Dateien gespeichert werden. Der Browser verliert vorübergehend seine Verbindung mit der App und lädt die Seite dann neu, wenn die Verbindung erneut hergestellt wird.

1. Bei Auswahl der Schaltfläche **`Add todo`** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.

1. Fügen Sie der `Todo`-Komponente eine `AddTodo`-Methode hinzu, und registrieren Sie die Methode für die Schaltfläche mithilfe des `@onclick`-Attributs. Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. Fügen Sie am oberen Rand des `@code`-Blocks ein `newTodo`-Zeichenfolgenfeld hinzu, um den Titel des neuen To-do-Elements zu erhalten:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   Ändern Sie das`<input>`-Textelement, um `newTodo` mit dem `@bind`-Attribut zu verknüpfen:

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen. Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. Speichern Sie die Datei `Pages/Todo.razor`. Die App wird automatisch in der Befehlsshell neu erstellt. Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.

1. Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen. Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft. Ändern Sie `@todo.Title` in ein `<input>`-Element, das mit `@bind` mit `todo.Title` verknüpft ist:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. Aktualisieren Sie den `<h3>`-Header, um die Anzahl der nicht abgeschlossene To-do-Elemente anzuzeigen (`IsDone` ist `false`).

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Die fertige `Todo`-Komponente (`Pages/Todo.razor`):

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. Speichern Sie die Datei `Pages/Todo.razor`. Die App wird automatisch in der Befehlsshell neu erstellt. Die Seite wird nach dem erneuten Verbinden des Browsers mit der App im Browser neu geladen.

1. Fügen Sie Elemente hinzu, und markieren Sie To-do-Elemente als abgeschlossen, um die Komponente zu testen.

1. Wenn Sie fertig sind, fahren Sie die App in der Befehlsshell herunter. In vielen Befehlsshells kann der Tastaturbefehl <kbd>STRG</kbd>+<kbd>C</kbd> verwendet werden, um eine App anzuhalten.

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
