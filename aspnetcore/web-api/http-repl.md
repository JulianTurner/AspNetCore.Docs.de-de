---
title: Testen von Web-APIs mit HTTP REPL
author: scottaddie
description: Hier erfahren Sie, wie Sie das globale .NET Core-Tool HTTP REPL verwenden können, um ASP.NET Core-Web-APIs zu durchsuchen und zu testen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 05/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: web-api/http-repl
ms.openlocfilehash: efd2208044ad6392131216266afc34187d738b78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058973"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="87d2f-103">Testen von Web-APIs mit HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="87d2f-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="87d2f-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="87d2f-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="87d2f-105">HTTP REPL (read–eval–print-Loop):</span><span class="sxs-lookup"><span data-stu-id="87d2f-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="87d2f-106">Ein schlankes, plattformübergreifendes Befehlszeilentool, das überall unterstützt wird, wo .NET Core unterstützt wird</span><span class="sxs-lookup"><span data-stu-id="87d2f-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="87d2f-107">wird zum Senden von HTTP-Anforderungen verwendet, mit denen ASP.NET Core-Web-APIs (und andere APIs) getestet werden können, und zum Überprüfen von deren Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="87d2f-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="87d2f-108">kann zum Testen von Web-APIs verwendet werden, die in beliebigen Umgebungen gehostet werden (einschließlich Localhost und Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="87d2f-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="87d2f-109">Folgende [HTTP-Verben](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="87d2f-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="87d2f-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="87d2f-111">GET</span><span class="sxs-lookup"><span data-stu-id="87d2f-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="87d2f-112">Stadt</span><span class="sxs-lookup"><span data-stu-id="87d2f-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="87d2f-113">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="87d2f-114">Patch</span><span class="sxs-lookup"><span data-stu-id="87d2f-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="87d2f-115">POST</span><span class="sxs-lookup"><span data-stu-id="87d2f-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="87d2f-116">PUT</span><span class="sxs-lookup"><span data-stu-id="87d2f-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="87d2f-117">[Sehen Sie sich die beispielhafte ASP.NET Core-Web-API an, oder laden Sie sie herunter ([Downloadanleitung](xref:index#how-to-download-a-sample))](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples), um dem Artikel zu folgen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="87d2f-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="87d2f-119">Installation</span><span class="sxs-lookup"><span data-stu-id="87d2f-119">Installation</span></span>

<span data-ttu-id="87d2f-120">Führen Sie den folgenden Befehl aus, um HTTP REPL zu installieren:</span><span class="sxs-lookup"><span data-stu-id="87d2f-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="87d2f-121">Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) installiert.</span><span class="sxs-lookup"><span data-stu-id="87d2f-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="87d2f-122">Verbrauch</span><span class="sxs-lookup"><span data-stu-id="87d2f-122">Usage</span></span>

<span data-ttu-id="87d2f-123">Nach der erfolgreichen Installation des Tools können Sie folgenden Befehl ausführen, um HTTP REPL zu starten:</span><span class="sxs-lookup"><span data-stu-id="87d2f-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="87d2f-124">Führen Sie einen der folgenden Befehle aus, um eine Liste der verfügbaren HTTP REPL-Befehle anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="87d2f-125">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="87d2f-126">In HTTP REPL können Befehle vervollständigt werden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="87d2f-127">Wenn Sie die <kbd>TAB-TASTE</kbd> drücken, können Sie die Liste der Befehle durchlaufen, die die eingegebenen Zeichen oder API-Endpunkte vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="87d2f-128">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="87d2f-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="87d2f-129">Herstellen einer Verbindung mit der Web-API</span><span class="sxs-lookup"><span data-stu-id="87d2f-129">Connect to the web API</span></span>

<span data-ttu-id="87d2f-130">Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit einer Web-API her:</span><span class="sxs-lookup"><span data-stu-id="87d2f-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="87d2f-131">`<ROOT URI>` ist der Basis-URI für die Web-API.</span><span class="sxs-lookup"><span data-stu-id="87d2f-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="87d2f-132">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="87d2f-133">Alternativ können Sie den folgenden Befehl jederzeit ausführen, während HTTP REPL ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="87d2f-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="87d2f-134">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="87d2f-135">Manuelles Verweisen auf das Swagger-Dokument für die Web-API</span><span class="sxs-lookup"><span data-stu-id="87d2f-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="87d2f-136">Der oben genannte connect-Befehl versucht, das Swagger-Dokument automatisch zu finden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="87d2f-137">Wenn dies aus irgendeinem Grund nicht möglich ist, können Sie den URI des Swagger-Dokuments für die Web-API mithilfe der Option `--swagger` angeben:</span><span class="sxs-lookup"><span data-stu-id="87d2f-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="87d2f-138">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="87d2f-139">Navigieren in der Web-API</span><span class="sxs-lookup"><span data-stu-id="87d2f-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="87d2f-140">Anzeigen verfügbarer Endpunkte</span><span class="sxs-lookup"><span data-stu-id="87d2f-140">View available endpoints</span></span>

<span data-ttu-id="87d2f-141">Führen Sie den Befehl `ls` oder `dir` aus, um die unterschiedlichen Endpunkte (Controller) im aktuellen Pfad der Web-API-Adresse aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="87d2f-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="87d2f-142">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="87d2f-143">Die vorherige Ausgabe gibt an, dass die zwei Controller `Fruits` und `People` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="87d2f-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="87d2f-144">Beide Controller unterstützen parameterlose HTTP GET- und HTTP POST-Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="87d2f-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="87d2f-145">Wenn Sie zu einem bestimmten Controller navigieren, werden weitere Details angezeigt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="87d2f-146">Aus der Ausgabe des folgenden Befehls geht beispielsweise hervor, dass der Controller `Fruits` auch HTTP GET-, HTTP PUT- und HTTP DELETE-Vorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="87d2f-147">Jeder dieser Vorgänge erwartet einen `id`-Parameter in der Route:</span><span class="sxs-lookup"><span data-stu-id="87d2f-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="87d2f-148">Führen Sie alternativ den Befehl `ui` aus, um die Swagger-Benutzeroberflächenseite der Web-API in einem Browser zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="87d2f-149">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="87d2f-150">Navigieren zu einem Endpunkt</span><span class="sxs-lookup"><span data-stu-id="87d2f-150">Navigate to an endpoint</span></span>

<span data-ttu-id="87d2f-151">Führen Sie den Befehl `cd` aus, um zu einem anderen Endpunkt in der Web-API zu navigieren:</span><span class="sxs-lookup"><span data-stu-id="87d2f-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="87d2f-152">Für den Pfad im Befehl `cd` wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="87d2f-153">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="87d2f-154">Anpassen von HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="87d2f-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="87d2f-155">Die [Standardfarben](#set-color-preferences) von HTTP REPL können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="87d2f-156">Außerdem kann definiert werden, welcher [Text-Editor](#set-the-default-text-editor) standardmäßig verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="87d2f-157">Die HTTP REPL-Einstellungen werden in der aktuellen Sitzung beibehalten und in zukünftigen Sitzungen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="87d2f-158">Nach der Änderung werden die Einstellungen in der folgenden Datei gespeichert:</span><span class="sxs-lookup"><span data-stu-id="87d2f-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="87d2f-159">Linux</span><span class="sxs-lookup"><span data-stu-id="87d2f-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="87d2f-160">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="87d2f-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="87d2f-161">macOS</span><span class="sxs-lookup"><span data-stu-id="87d2f-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="87d2f-162">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="87d2f-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="87d2f-163">Windows</span><span class="sxs-lookup"><span data-stu-id="87d2f-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="87d2f-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="87d2f-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="87d2f-165">Die *HTTPREPLPREFS-Datei* wird beim Start geladen und bei der Laufzeit nicht auf Änderungen überwacht.</span><span class="sxs-lookup"><span data-stu-id="87d2f-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="87d2f-166">Manuelle Änderungen an der Datei werden erst nach dem Neustart des Tools wirksam.</span><span class="sxs-lookup"><span data-stu-id="87d2f-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="87d2f-167">Anzeigen der Einstellungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-167">View the settings</span></span>

<span data-ttu-id="87d2f-168">Führen Sie den Befehl `pref get` aus, um die verfügbaren Einstellungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="87d2f-169">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="87d2f-170">Mit dem vorangehenden Befehl werden die verfügbaren Schlüssel-Wert-Paare angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-170">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="87d2f-171">Festlegen von Farbeinstellungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-171">Set color preferences</span></span>

<span data-ttu-id="87d2f-172">Die farbliche Kennzeichnung von Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="87d2f-173">Sie können die Standardfarben des HTTP REPL-Tools anpassen, indem Sie den Schlüssel für die entsprechende Farbe suchen und ändern.</span><span class="sxs-lookup"><span data-stu-id="87d2f-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="87d2f-174">Weitere Informationen zum Suchen von Schlüsseln finden Sie im Abschnitt [Anzeigen der Einstellungen](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="87d2f-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="87d2f-175">Ändern Sie beispielsweise den Wert des Schlüssels `colors.json` folgendermaßen von `Green` in `White`:</span><span class="sxs-lookup"><span data-stu-id="87d2f-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="87d2f-176">Es können nur die [zulässigen Farben](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="87d2f-177">Nachfolgende HTTP-Anforderungen zeigen die Ausgabe mit der neuen Farbgebung an.</span><span class="sxs-lookup"><span data-stu-id="87d2f-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="87d2f-178">Wenn bestimmte Farbtasten nicht festgelegt sind, werden mehr generische Schlüssel berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="87d2f-179">Anhand des folgenden Beispiels wird das Fallbackverhalten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="87d2f-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="87d2f-180">Wenn `colors.json.name` keinen Wert aufweist, wird `colors.json.string` verwendet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="87d2f-181">Wenn `colors.json.string` keinen Wert aufweist, wird `colors.json.literal` verwendet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="87d2f-182">Wenn `colors.json.literal` keinen Wert aufweist, wird `colors.json` verwendet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="87d2f-183">Wenn `colors.json` keinen Wert aufweist, wird die Standardtextfarbe (`AllowedColors.None`) der Befehlsshell verwendet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="87d2f-184">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="87d2f-184">Set indentation size</span></span>

<span data-ttu-id="87d2f-185">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="87d2f-186">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-186">The default size is two spaces.</span></span> <span data-ttu-id="87d2f-187">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-187">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="87d2f-188">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="87d2f-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="87d2f-189">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="87d2f-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="87d2f-190">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-190">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="87d2f-191">Festlegen des Standard-Text-Editors</span><span class="sxs-lookup"><span data-stu-id="87d2f-191">Set the default text editor</span></span>

<span data-ttu-id="87d2f-192">Standardmäßig ist für HTTP REPL kein Text-Editor konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="87d2f-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="87d2f-193">Wenn Sie Web-API-Methoden testen möchten, für die ein HTTP-Anforderungstext erforderlich ist, müssen Sie einen Standard-Text-Editor festlegen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="87d2f-194">Das HTTP REPL-Tool startet den konfigurierten Text-Editor nur zum Verfassen des Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="87d2f-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="87d2f-195">Führen Sie den folgenden Befehl aus, um Ihren bevorzugten Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="87d2f-196">Im vorherigen Befehl ist `<EXECUTABLE>` der vollständige Pfad zur ausführbaren Datei des Text-Editors.</span><span class="sxs-lookup"><span data-stu-id="87d2f-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="87d2f-197">Führen Sie beispielsweise folgenden Befehl aus, um Visual Studio Code als Standard-Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="87d2f-198">Linux</span><span class="sxs-lookup"><span data-stu-id="87d2f-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="87d2f-199">macOS</span><span class="sxs-lookup"><span data-stu-id="87d2f-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="87d2f-200">Windows</span><span class="sxs-lookup"><span data-stu-id="87d2f-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="87d2f-201">Legen Sie den Schlüssel `editor.command.default.arguments` fest, um den Standard-Text-Editor mit bestimmten CLI-Argumenten zu starten.</span><span class="sxs-lookup"><span data-stu-id="87d2f-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="87d2f-202">Nehmen Sie beispielsweise an, dass Visual Studio Code der Standard-Text-Editor ist und Sie möchten, dass Visual Studio Code durch HTTP REPL immer in einer neuen Sitzung mit deaktivierten Erweiterungen öffnet.</span><span class="sxs-lookup"><span data-stu-id="87d2f-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="87d2f-203">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="87d2f-204">Festlegen der Swagger-Suchpfade</span><span class="sxs-lookup"><span data-stu-id="87d2f-204">Set the Swagger search paths</span></span>

<span data-ttu-id="87d2f-205">Standardmäßig weist die HTTP-REPL eine Reihe von relativen Pfaden auf, die zum Suchen des Swagger-Dokuments verwendet werden, wenn der `connect`-Befehl ohne die Option `--swagger` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="87d2f-206">Diese relativen Pfade werden mit den Stamm- und Basispfaden kombiniert, die im `connect`-Befehl angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="87d2f-207">Die standardmäßigen relativen Pfade lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-207">The default relative paths are:</span></span>

- <span data-ttu-id="87d2f-208">*swagger.js*</span><span class="sxs-lookup"><span data-stu-id="87d2f-208">*swagger.json*</span></span>
- <span data-ttu-id="87d2f-209">*Swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="87d2f-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="87d2f-210">*/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="87d2f-210">*/swagger.json*</span></span>
- <span data-ttu-id="87d2f-211">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="87d2f-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="87d2f-212">Um andere Suchpfade in Ihrer Umgebung zu verwenden, legen Sie die Einstellung `swagger.searchPaths` fest.</span><span class="sxs-lookup"><span data-stu-id="87d2f-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="87d2f-213">Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln.</span><span class="sxs-lookup"><span data-stu-id="87d2f-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="87d2f-214">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="87d2f-215">Testen von HTTP GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-216">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-217">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-218">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-219">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-219">Options</span></span>

<span data-ttu-id="87d2f-220">Für den Befehl `get` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="87d2f-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="87d2f-221">Beispiel</span><span class="sxs-lookup"><span data-stu-id="87d2f-221">Example</span></span>

<span data-ttu-id="87d2f-222">So führen Sie eine HTTP GET-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="87d2f-223">Führen Sie den Befehl `get` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="87d2f-224">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-224">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. <span data-ttu-id="87d2f-225">Rufen Sie einen bestimmten Datensatz ab, indem Sie einen Parameter an den Befehl `get` übergeben:</span><span class="sxs-lookup"><span data-stu-id="87d2f-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="87d2f-226">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-226">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="87d2f-227">Testen von HTTP POST-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-228">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-229">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-230">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-231">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="87d2f-232">Beispiel</span><span class="sxs-lookup"><span data-stu-id="87d2f-232">Example</span></span>

<span data-ttu-id="87d2f-233">So führen Sie eine HTTP POST-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="87d2f-234">Führen Sie den Befehl `post` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="87d2f-235">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="87d2f-236">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="87d2f-237">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="87d2f-238">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="87d2f-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="87d2f-239">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="87d2f-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="87d2f-240">Speichern Sie die *TMP-Datei* , und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="87d2f-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="87d2f-241">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-241">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="87d2f-242">Testen von HTTP PUT-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-243">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-244">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-245">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-246">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="87d2f-247">Beispiel</span><span class="sxs-lookup"><span data-stu-id="87d2f-247">Example</span></span>

<span data-ttu-id="87d2f-248">So führen Sie eine HTTP PUT-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="87d2f-249">*Optional* : führen `get` Sie den Befehl aus, um die Daten vor dem ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-249">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="87d2f-250">Führen Sie den Befehl `put` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="87d2f-251">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="87d2f-252">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="87d2f-253">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="87d2f-254">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="87d2f-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="87d2f-255">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="87d2f-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="87d2f-256">Speichern Sie die *TMP-Datei* , und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="87d2f-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="87d2f-257">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="87d2f-258">*Optional* : geben `get` Sie einen Befehl aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-258">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="87d2f-259">Wenn Sie beispielsweise „Cherry“ in den Text-Editor eingeben, gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="87d2f-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="87d2f-260">Testen von HTTP DELETE-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-261">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-262">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-263">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-264">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="87d2f-265">Beispiel</span><span class="sxs-lookup"><span data-stu-id="87d2f-265">Example</span></span>

<span data-ttu-id="87d2f-266">So führen Sie eine HTTP DELETE-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="87d2f-267">*Optional* : führen `get` Sie den Befehl aus, um die Daten vor dem ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-267">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="87d2f-268">Führen Sie den Befehl `delete` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="87d2f-269">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="87d2f-270">*Optional* : geben `get` Sie einen Befehl aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-270">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="87d2f-271">In diesem Beispiel gibt `get` Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="87d2f-271">In this example, a `get` returns the following:</span></span>

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="87d2f-272">Testen von HTTP PATCH-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-273">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-274">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-275">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-276">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="87d2f-277">Testen von HTTP HEAD-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-278">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-279">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-280">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-281">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="87d2f-282">Testen von HTTP OPTIONS-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="87d2f-283">Übersicht</span><span class="sxs-lookup"><span data-stu-id="87d2f-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="87d2f-284">Argumente</span><span class="sxs-lookup"><span data-stu-id="87d2f-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="87d2f-285">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="87d2f-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="87d2f-286">Optionen</span><span class="sxs-lookup"><span data-stu-id="87d2f-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="87d2f-287">Festlegen von HTTP-Anforderungsheadern</span><span class="sxs-lookup"><span data-stu-id="87d2f-287">Set HTTP request headers</span></span>

<span data-ttu-id="87d2f-288">Verwenden Sie einen der folgenden Ansätze, um einen HTTP-Anforderungsheader festzulegen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="87d2f-289">Legen Sie den Header inline mit der HTTP-Anforderung fest.</span><span class="sxs-lookup"><span data-stu-id="87d2f-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="87d2f-290">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="87d2f-291">Beim vorherigen Ansatz benötigt jeder eindeutige HTTP-Anforderungsheader eine eigene `-h`-Option.</span><span class="sxs-lookup"><span data-stu-id="87d2f-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="87d2f-292">Legen Sie den Header fest, bevor Sie die HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="87d2f-293">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="87d2f-294">Wenn Sie den Header vor dem Senden einer Anforderung festlegen, bleibt er für die Dauer der Befehlsshellsitzung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="87d2f-295">Geben Sie einen leeren Wert an, um den Header zu löschen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="87d2f-296">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="87d2f-297">Testen gesicherter Endpunkte</span><span class="sxs-lookup"><span data-stu-id="87d2f-297">Test secured endpoints</span></span>

<span data-ttu-id="87d2f-298">Die http-repl unterstützt das Testen von gesicherten Endpunkten auf zwei Arten: über die Standard Anmelde Informationen des angemeldeten Benutzers oder durch Verwendung von HTTP-Anforderungs Headern.</span><span class="sxs-lookup"><span data-stu-id="87d2f-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="87d2f-299">Standardanmeldeinformationen</span><span class="sxs-lookup"><span data-stu-id="87d2f-299">Default credentials</span></span>

<span data-ttu-id="87d2f-300">Stellen Sie sich ein Szenario vor, in dem die zu testenden Web-API in IIS gehostet wird und mit der Windows-Authentifizierung geschützt ist.</span><span class="sxs-lookup"><span data-stu-id="87d2f-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="87d2f-301">Sie möchten, dass die Anmelde Informationen des Benutzers, der das Tool ausführen soll, über die zu testenden HTTP-Endpunkte fließen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="87d2f-302">So übergeben Sie die Standard Anmelde Informationen des angemeldeten Benutzers:</span><span class="sxs-lookup"><span data-stu-id="87d2f-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="87d2f-303">Legen Sie die Einstellung `httpClient.useDefaultCredentials` auf fest `true` :</span><span class="sxs-lookup"><span data-stu-id="87d2f-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="87d2f-304">Beenden und starten Sie das Tool neu, bevor Sie eine weitere Anforderung an die Web-API senden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="87d2f-305">HTTP-Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="87d2f-305">HTTP request headers</span></span>

<span data-ttu-id="87d2f-306">Beispiele unterstützter Authentifizierungs- und Autorisierungsschemas sind Standardauthentifizierung, JWT-Bearertoken und Digestauthentifizierung.</span><span class="sxs-lookup"><span data-stu-id="87d2f-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="87d2f-307">Beispielsweise können Sie mit dem folgenden Befehl ein Bearertoken an einen Endpunkt senden:</span><span class="sxs-lookup"><span data-stu-id="87d2f-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="87d2f-308">Um auf einen in Azure gehosteten Endpunkt zuzugreifen oder die [Azure-REST-API](/rest/api/azure/) zu verwenden, benötigen Sie ein Bearertoken.</span><span class="sxs-lookup"><span data-stu-id="87d2f-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="87d2f-309">Führen Sie die folgenden Schritte aus, um über die [Azure CLI](/cli/azure/) ein Bearertoken für Ihr Azure-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="87d2f-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="87d2f-310">HTTP REPL legt das Beaertoken in einem HTTP-Anforderungsheader ab und ruft eine Liste der Azure App Service-Web-Apps ab.</span><span class="sxs-lookup"><span data-stu-id="87d2f-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="87d2f-311">Anmelden bei Azure:</span><span class="sxs-lookup"><span data-stu-id="87d2f-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="87d2f-312">Rufen Sie Ihre Abonnement-ID mit dem folgenden Befehl ab:</span><span class="sxs-lookup"><span data-stu-id="87d2f-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="87d2f-313">Kopieren Sie Ihre Abonnement-ID, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="87d2f-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="87d2f-314">Rufen Sie Ihr Bearertoken mit dem folgenden Befehl ab:</span><span class="sxs-lookup"><span data-stu-id="87d2f-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="87d2f-315">Stellen Sie über HTTP REPL eine Verbindung mit der Azure-REST-API her:</span><span class="sxs-lookup"><span data-stu-id="87d2f-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="87d2f-316">Legen Sie den HTTP-Anforderungsheader `Authorization` fest:</span><span class="sxs-lookup"><span data-stu-id="87d2f-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="87d2f-317">Navigieren Sie zum Abonnement:</span><span class="sxs-lookup"><span data-stu-id="87d2f-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="87d2f-318">Rufen Sie eine Liste der Azure App Service-Web-Apps Ihres Abonnements ab:</span><span class="sxs-lookup"><span data-stu-id="87d2f-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="87d2f-319">Die folgende Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-319">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="87d2f-320">Umschalten der Anzeige von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-320">Toggle HTTP request display</span></span>

<span data-ttu-id="87d2f-321">Standardmäßig wird die Anzeige der gesendeten HTTP-Anforderung unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="87d2f-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="87d2f-322">Sie können die entsprechende Einstellung für die Dauer der Befehlsshellsitzung ändern.</span><span class="sxs-lookup"><span data-stu-id="87d2f-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="87d2f-323">Aktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-323">Enable request display</span></span>

<span data-ttu-id="87d2f-324">Zeigen Sie die gesendete HTTP-Anforderung an, indem Sie den Befehl `echo on` ausführen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="87d2f-325">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="87d2f-326">Nachfolgende HTTP-Anforderungen in der aktuellen Sitzung zeigen die Anforderungsheader an.</span><span class="sxs-lookup"><span data-stu-id="87d2f-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="87d2f-327">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-327">For example:</span></span>

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a><span data-ttu-id="87d2f-328">Deaktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-328">Disable request display</span></span>

<span data-ttu-id="87d2f-329">Unterdrücken Sie die Anzeige der gesendeten HTTP-Anforderung, indem Sie den Befehl `echo off` ausführen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="87d2f-330">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="87d2f-331">Ausführen eines Skripts</span><span class="sxs-lookup"><span data-stu-id="87d2f-331">Run a script</span></span>

<span data-ttu-id="87d2f-332">Wenn Sie häufig die gleichen HTTP REPL-Befehle ausführen, können Sie diese in einer Textdatei speichern.</span><span class="sxs-lookup"><span data-stu-id="87d2f-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="87d2f-333">Die Befehle in der Datei haben das gleiche Format wie die manuell in der Befehlszeile ausgeführten Befehle.</span><span class="sxs-lookup"><span data-stu-id="87d2f-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="87d2f-334">Die Befehle können mithilfe des Befehls `run` auf einmal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="87d2f-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="87d2f-335">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-335">For example:</span></span>

1. <span data-ttu-id="87d2f-336">Erstellen Sie eine Textdatei, die einige Befehle enthält, die jeweils in einer neuen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="87d2f-337">Sie können die Datei *people-script.txt* , die folgende Befehle enthält, als Beispiel verwenden:</span><span class="sxs-lookup"><span data-stu-id="87d2f-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="87d2f-338">Führen Sie den Befehl `run` aus, und übergeben Sie den Pfad der Textdatei.</span><span class="sxs-lookup"><span data-stu-id="87d2f-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="87d2f-339">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="87d2f-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="87d2f-340">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="87d2f-340">The following output appears:</span></span>

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a><span data-ttu-id="87d2f-341">Löschen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="87d2f-341">Clear the output</span></span>

<span data-ttu-id="87d2f-342">Sie können alle Ausgaben des HTTP REPL-Tools aus der Befehlsshell entfernen, indem Sie den Befehl `clear` oder `cls` ausführen.</span><span class="sxs-lookup"><span data-stu-id="87d2f-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="87d2f-343">Nehmen Sie beispielsweise an, dass die Befehlsshell folgende Ausgabe enthält:</span><span class="sxs-lookup"><span data-stu-id="87d2f-343">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="87d2f-344">Führen Sie den folgenden Befehl aus, um die Ausgabe zu löschen:</span><span class="sxs-lookup"><span data-stu-id="87d2f-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="87d2f-345">Nachdem der vorherige Befehl ausgeführt wurde, enthält die Befehlsshell nur die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="87d2f-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="87d2f-346">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="87d2f-346">Additional resources</span></span>

* [<span data-ttu-id="87d2f-347">REST-API-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="87d2f-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="87d2f-348">GitHub-Repository für HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="87d2f-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
