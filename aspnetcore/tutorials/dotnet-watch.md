---
title: Entwickeln von ASP.NET Core-Apps mit einem Dateiwatcher
author: rick-anderson
description: In diesem Tutorial wird erläutert, wie Sie das Dateiwatcher-Tool (dotnet watch) der .NET Core-CLI in einer ASP.NET Core-App installieren und verwenden.
ms.author: riande
ms.date: 05/31/2018
no-loc:
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 3569e9440b8e431ec0e5357e548af2e3783481ac
ms.sourcegitcommit: 422e02bad384775bfe19a90910737340ad106c5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90083452"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="7cc57-103">Entwickeln von ASP.NET Core-Apps mit einem Dateiwatcher</span><span class="sxs-lookup"><span data-stu-id="7cc57-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="7cc57-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="7cc57-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="7cc57-105">`dotnet watch` ist ein Tool, das einen [.NET Core-CLI](/dotnet/core/tools)-Befehl ausführt, wenn sich Quelldateien ändern.</span><span class="sxs-lookup"><span data-stu-id="7cc57-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="7cc57-106">Eine Dateiänderung kann beispielsweise eine Kompilierung, Testausführung oder Bereitstellung auslösen.</span><span class="sxs-lookup"><span data-stu-id="7cc57-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="7cc57-107">In diesem Tutorial wird eine vorhandene Web-API mit zwei Endpunkten verwendet: der eine gibt eine Summe zurück, der andere ein Produkt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="7cc57-108">Die Produktmethode enthält einen Fehler, der in diesem Tutorial behoben wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="7cc57-109">Laden Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample) herunter.</span><span class="sxs-lookup"><span data-stu-id="7cc57-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="7cc57-110">Sie enthält zwei Projekte: *WebApp* (eine ASP.NET Core-Web-API) und *WebAppTests* (Komponententest für die Web-API).</span><span class="sxs-lookup"><span data-stu-id="7cc57-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="7cc57-111">Navigieren Sie in einer Befehlsshell zum Ordner *WebApp*.</span><span class="sxs-lookup"><span data-stu-id="7cc57-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="7cc57-112">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="7cc57-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="7cc57-113">Sie können `dotnet run --project <PROJECT>` verwenden, um ein auszuführendes Projekt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7cc57-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="7cc57-114">Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet run --project WebApp` ausführen, wird auch das Projekt *WebApp* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="7cc57-115">Die Konsolenausgabe zeigt Meldungen ähnlich der folgenden (die angibt, dass die App ausgeführt wird und auf Anforderungen wartet):</span><span class="sxs-lookup"><span data-stu-id="7cc57-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="7cc57-116">Navigieren Sie in einem Webbrowser zu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="7cc57-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="7cc57-117">Es sollten die Ergebnisse von `9` angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-117">You should see the result of `9`.</span></span>

<span data-ttu-id="7cc57-118">Navigieren Sie zur Produkt-API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span><span class="sxs-lookup"><span data-stu-id="7cc57-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="7cc57-119">Es wird nicht wie erwartet `20` sondern `9` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7cc57-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="7cc57-120">Dieses Problem wird später im Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="7cc57-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="7cc57-121">Hinzufügen von `dotnet watch` zu einem Projekt</span><span class="sxs-lookup"><span data-stu-id="7cc57-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="7cc57-122">Das Dateiwatcher-Tool `dotnet watch` ist im Lieferumfang von .NET Core SDK-Version 2.1.300 enthalten.</span><span class="sxs-lookup"><span data-stu-id="7cc57-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="7cc57-123">Wenn Sie eine frühere Version von .NET Core SDK verwenden, sind die folgenden Schritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7cc57-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="7cc57-124">Fügen Sie der *CSPROJ*-Datei einen `Microsoft.DotNet.Watcher.Tools`-Paketverweis hinzu:</span><span class="sxs-lookup"><span data-stu-id="7cc57-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="7cc57-125">Installieren Sie das `Microsoft.DotNet.Watcher.Tools`-Paket, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="7cc57-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="7cc57-126">Ausführen von .NET Core-CLI-Befehlen mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="7cc57-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="7cc57-127">Jeder [.NET Core-CLI-Befehl](/dotnet/core/tools#cli-commands) kann mit `dotnet watch` ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="7cc57-128">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7cc57-128">For example:</span></span>

| <span data-ttu-id="7cc57-129">Befehl</span><span class="sxs-lookup"><span data-stu-id="7cc57-129">Command</span></span> | <span data-ttu-id="7cc57-130">Befehl mit watch</span><span class="sxs-lookup"><span data-stu-id="7cc57-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="7cc57-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="7cc57-131">dotnet run</span></span> | <span data-ttu-id="7cc57-132">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="7cc57-132">dotnet watch run</span></span> |
| <span data-ttu-id="7cc57-133">dotnet run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="7cc57-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="7cc57-134">dotnet watch run -f netcoreapp3.1</span><span class="sxs-lookup"><span data-stu-id="7cc57-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="7cc57-135">dotnet run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="7cc57-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="7cc57-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="7cc57-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="7cc57-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="7cc57-137">dotnet test</span></span> | <span data-ttu-id="7cc57-138">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="7cc57-138">dotnet watch test</span></span> |

<span data-ttu-id="7cc57-139">Führen Sie `dotnet watch run` im Ordner *WebApp* aus.</span><span class="sxs-lookup"><span data-stu-id="7cc57-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="7cc57-140">Die Konsolenausgabe gibt an, dass `watch` gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="7cc57-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="7cc57-141">Wenn `dotnet watch run` in einer Web-App ausgeführt wird, wird ein Browser aufgerufen, der nach der Bereitstellung zur URL der App navigiert.</span><span class="sxs-lookup"><span data-stu-id="7cc57-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="7cc57-142">Dies geschieht bei `dotnet watch`, indem die Konsolenausgabe der App gelesen und auf die durch <xref:Microsoft.AspNetCore.WebHost> angezeigte Bereitmeldung gewartet wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="7cc57-143">`dotnet watch` aktualisiert den Browser, wenn an den überwachten Dateien Änderungen erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="7cc57-144">Hierzu wird mit dem Watch-Befehl eine Middleware in die App eingefügt, mit der durch die App erstellte HTML-Antworten geändert werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="7cc57-145">Die Middleware fügt der Seite einen JavaScript-Block hinzu, mit dessen Hilfe `dotnet watch` den Browser anweist, eine Aktualisierung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="7cc57-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="7cc57-146">Derzeit führen Änderungen an überwachten Dateien wie etwa an statischen Inhalten wie *HTML* und *CSS*-Dateien dazu, dass die App neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="7cc57-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="7cc57-147">`dotnet watch`:</span></span>

* <span data-ttu-id="7cc57-148">Überwacht nur Dateien, die sich standardmäßig auf Builds auswirken.</span><span class="sxs-lookup"><span data-stu-id="7cc57-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="7cc57-149">Alle (per Konfiguration) zusätzlich überwachten Dateien führen weiterhin dazu, dass ein Build erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="7cc57-150">Weitere Informationen zur Konfiguration finden Sie unter [Konfiguration von dotnet-watch](#dotnet-watch-configuration) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="7cc57-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="7cc57-151">Sie können `dotnet watch --project <PROJECT>` verwenden, um ein zu überwachendes Projekt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7cc57-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="7cc57-152">Wenn Sie beispielsweise im Stammverzeichnis der Beispiel-App `dotnet watch --project WebApp run` ausführen, wird auch das Projekt *WebApp* ausgeführt und überwacht.</span><span class="sxs-lookup"><span data-stu-id="7cc57-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="7cc57-153">Vornehmen von Änderungen mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="7cc57-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="7cc57-154">Stellen Sie sicher, dass `dotnet watch` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="7cc57-155">Korrigieren Sie den Fehler in der `Product`-Methode von *MathController.cs* so, dass das Produkt und nicht die Summe zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="7cc57-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="7cc57-156">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="7cc57-156">Save the file.</span></span> <span data-ttu-id="7cc57-157">Die Konsolenausgabe zeigt an, dass `dotnet watch` eine Dateiänderung erkannt und die App neu gestartet hat.</span><span class="sxs-lookup"><span data-stu-id="7cc57-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="7cc57-158">Vergewissern Sie sich, dass `http://localhost:<port number>/api/math/product?a=4&b=5` das richtige Ergebnis zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="7cc57-159">Ausführen von Tests mit `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="7cc57-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="7cc57-160">Ändern Sie die `Product`-Methode von *MathController.cs* so, dass wieder die Summe zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="7cc57-161">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="7cc57-161">Save the file.</span></span>
1. <span data-ttu-id="7cc57-162">Navigieren Sie in einer Befehlsshell zum Ordner *WebAppTests*.</span><span class="sxs-lookup"><span data-stu-id="7cc57-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="7cc57-163">Führen Sie [dotnet restore](/dotnet/core/tools/dotnet-restore) aus.</span><span class="sxs-lookup"><span data-stu-id="7cc57-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="7cc57-164">Führen Sie aus `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="7cc57-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="7cc57-165">In der Ausgabe wird angezeigt, dass ein Test fehlgeschlagen ist und der Watcher auf Dateiänderungen wartet:</span><span class="sxs-lookup"><span data-stu-id="7cc57-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="7cc57-166">Ändern Sie den Code der `Product`-Methode so, dass das Produkt zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7cc57-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="7cc57-167">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="7cc57-167">Save the file.</span></span>

<span data-ttu-id="7cc57-168">`dotnet watch` erkennt die Dateiänderung und führt die Tests erneut aus.</span><span class="sxs-lookup"><span data-stu-id="7cc57-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="7cc57-169">Die Konsolenausgabe zeigt, dass die Tests bestanden wurden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="7cc57-170">Anpassen der Liste mit den zu überwachenden Dateien</span><span class="sxs-lookup"><span data-stu-id="7cc57-170">Customize files list to watch</span></span>

<span data-ttu-id="7cc57-171">Standardmäßig überwacht `dotnet-watch` alle Dateien mit den folgenden Globmustern:</span><span class="sxs-lookup"><span data-stu-id="7cc57-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="7cc57-172">Durch Bearbeiten der *CSPROJ*-Datei können der Watchlist weitere Elemente hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="7cc57-173">Dabei können die Elemente einzeln oder mithilfe von Globmustern angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="7cc57-174">Abwählen von zu überwachenden Dateien</span><span class="sxs-lookup"><span data-stu-id="7cc57-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="7cc57-175">Sie können `dotnet-watch` so konfigurieren, dass die Standardeinstellungen ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="7cc57-176">Wenn Sie bestimmte Dateien ignorieren möchten, fügen Sie der Definition eines Elements in der *CSPROJ*-Datei das `Watch="false"`-Attribut hinzu:</span><span class="sxs-lookup"><span data-stu-id="7cc57-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="7cc57-177">Benutzerdefinierte Überwachungsprojekte</span><span class="sxs-lookup"><span data-stu-id="7cc57-177">Custom watch projects</span></span>

<span data-ttu-id="7cc57-178">`dotnet-watch` ist nicht auf C#-Projekte beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="7cc57-179">Sie können für unterschiedliche Szenarios benutzerdefinierte Überwachungsprojekte erstellen.</span><span class="sxs-lookup"><span data-stu-id="7cc57-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="7cc57-180">Betrachten Sie das folgende Projektlayout:</span><span class="sxs-lookup"><span data-stu-id="7cc57-180">Consider the following project layout:</span></span>

* <span data-ttu-id="7cc57-181">**test/**</span><span class="sxs-lookup"><span data-stu-id="7cc57-181">**test/**</span></span>
  * <span data-ttu-id="7cc57-182">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="7cc57-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="7cc57-183">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="7cc57-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="7cc57-184">Wenn beide Projekte überwacht werden sollen, erstellen Sie eine benutzerdefinierte Projektdatei, die so konfiguriert ist, dass beide Projekte überwacht werden:</span><span class="sxs-lookup"><span data-stu-id="7cc57-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="7cc57-185">Wechseln Sie zum Starten der Dateiüberwachung in beiden Projekten zum *test*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="7cc57-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="7cc57-186">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="7cc57-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="7cc57-187">VSTest wird ausgeführt, wenn in einer beliebigen Datei in einem der Testprojekte Änderungen vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="7cc57-188">Konfiguration von dotnet-watch</span><span class="sxs-lookup"><span data-stu-id="7cc57-188">dotnet-watch configuration</span></span>

<span data-ttu-id="7cc57-189">Einige Konfigurationsoptionen können über Umgebungsvariablen an `dotnet watch` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="7cc57-190">Folgende Variablen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="7cc57-190">The available variables are:</span></span>

| <span data-ttu-id="7cc57-191">Einstellung</span><span class="sxs-lookup"><span data-stu-id="7cc57-191">Setting</span></span>  | <span data-ttu-id="7cc57-192">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="7cc57-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="7cc57-193">Wenn diese Einstellung auf „1“ oder „true“ festgelegt ist, verwendet `dotnet watch` anstelle von `FileSystemWatcher` von CoreFx einen Polling-Dateiwatcher.</span><span class="sxs-lookup"><span data-stu-id="7cc57-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="7cc57-194">Wird beim Überwachen von Dateien in Netzwerkfreigaben oder in auf Docker bereitgestellten Volumes verwendet.</span><span class="sxs-lookup"><span data-stu-id="7cc57-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="7cc57-195">Das Build wird von `dotnet watch` standardmäßig optimiert. Dies geschieht dadurch, dass bestimmte Vorgänge wie das Ausführen einer Wiederherstellung oder die erneute Auswertung der überwachten Dateien bei jeder Dateiänderung vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="7cc57-196">Wenn diese Einstellung auf „1“ oder „true“ festgelegt ist, werden diese Optimierungen deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="7cc57-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="7cc57-197">`dotnet watch run` versucht, Browser für Web-Apps zu starten, wobei `launchBrowser` in *launchSettings.json* konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7cc57-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="7cc57-198">Wenn diese Einstellung auf „1“ oder „true“ festgelegt ist, wird dieses Verhalten unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="7cc57-199">`dotnet watch run` versucht, Browser zu aktualisieren, wenn Dateiänderungen erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="7cc57-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="7cc57-200">Wenn diese Einstellung auf „1“ oder „true“ festgelegt ist, wird dieses Verhalten unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="7cc57-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="7cc57-201">Dieses Verhalten wird auch unterdrückt, wenn `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="7cc57-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="7cc57-202">`dotnet-watch` in GitHub</span><span class="sxs-lookup"><span data-stu-id="7cc57-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="7cc57-203">`dotnet-watch` ist Teil des GitHub-Repositorys [dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span><span class="sxs-lookup"><span data-stu-id="7cc57-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
