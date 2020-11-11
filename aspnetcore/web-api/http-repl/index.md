---
title: Testen von Web-APIs mit HTTP REPL
author: scottaddie
description: Hier erfahren Sie, wie Sie das globale .NET Core-Tool HTTP REPL verwenden können, um ASP.NET Core-Web-APIs zu durchsuchen und zu testen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl
ms.openlocfilehash: 81174b551c5b6d81e6ac80975f7f77ee6664059d
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94501986"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="eef15-103">Testen von Web-APIs mit HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="eef15-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="eef15-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="eef15-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="eef15-105">HTTP REPL (read–eval–print-Loop):</span><span class="sxs-lookup"><span data-stu-id="eef15-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="eef15-106">Ein schlankes, plattformübergreifendes Befehlszeilentool, das überall unterstützt wird, wo .NET Core unterstützt wird</span><span class="sxs-lookup"><span data-stu-id="eef15-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="eef15-107">wird zum Senden von HTTP-Anforderungen verwendet, mit denen ASP.NET Core-Web-APIs (und andere APIs) getestet werden können, und zum Überprüfen von deren Ergebnissen</span><span class="sxs-lookup"><span data-stu-id="eef15-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="eef15-108">kann zum Testen von Web-APIs verwendet werden, die in beliebigen Umgebungen gehostet werden (einschließlich Localhost und Azure App Service)</span><span class="sxs-lookup"><span data-stu-id="eef15-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="eef15-109">Folgende [HTTP-Verben](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eef15-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="eef15-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="eef15-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="eef15-111">GET</span><span class="sxs-lookup"><span data-stu-id="eef15-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="eef15-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="eef15-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="eef15-113">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="eef15-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="eef15-114">PATCH</span><span class="sxs-lookup"><span data-stu-id="eef15-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="eef15-115">POST</span><span class="sxs-lookup"><span data-stu-id="eef15-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="eef15-116">PUT</span><span class="sxs-lookup"><span data-stu-id="eef15-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="eef15-117">[Sehen Sie sich die beispielhafte ASP.NET Core-Web-API an, oder laden Sie sie herunter ([Downloadanleitung](xref:index#how-to-download-a-sample))](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples), um dem Artikel zu folgen.</span><span class="sxs-lookup"><span data-stu-id="eef15-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eef15-118">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eef15-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="eef15-119">Installation</span><span class="sxs-lookup"><span data-stu-id="eef15-119">Installation</span></span>

<span data-ttu-id="eef15-120">Führen Sie den folgenden Befehl aus, um HTTP REPL zu installieren:</span><span class="sxs-lookup"><span data-stu-id="eef15-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="eef15-121">Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) installiert.</span><span class="sxs-lookup"><span data-stu-id="eef15-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="eef15-122">Verwendung</span><span class="sxs-lookup"><span data-stu-id="eef15-122">Usage</span></span>

<span data-ttu-id="eef15-123">Nach der erfolgreichen Installation des Tools können Sie folgenden Befehl ausführen, um HTTP REPL zu starten:</span><span class="sxs-lookup"><span data-stu-id="eef15-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="eef15-124">Führen Sie einen der folgenden Befehle aus, um eine Liste der verfügbaren HTTP REPL-Befehle anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="eef15-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="eef15-125">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-125">The following output is displayed:</span></span>

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

<span data-ttu-id="eef15-126">In HTTP REPL können Befehle vervollständigt werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="eef15-127">Wenn Sie die <kbd>TAB-TASTE</kbd> drücken, können Sie die Liste der Befehle durchlaufen, die die eingegebenen Zeichen oder API-Endpunkte vervollständigen.</span><span class="sxs-lookup"><span data-stu-id="eef15-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="eef15-128">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="eef15-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="eef15-129">Herstellen einer Verbindung mit der Web-API</span><span class="sxs-lookup"><span data-stu-id="eef15-129">Connect to the web API</span></span>

<span data-ttu-id="eef15-130">Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit einer Web-API her:</span><span class="sxs-lookup"><span data-stu-id="eef15-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="eef15-131">`<ROOT URI>` ist der Basis-URI für die Web-API.</span><span class="sxs-lookup"><span data-stu-id="eef15-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="eef15-132">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="eef15-133">Alternativ können Sie den folgenden Befehl jederzeit ausführen, während HTTP REPL ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="eef15-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="eef15-134">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="eef15-135">Manuelles Verweisen auf die OpenAPI-Beschreibung für die Web-API</span><span class="sxs-lookup"><span data-stu-id="eef15-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="eef15-136">Der oben genannte connect-Befehl versucht, die OpenAPI-Beschreibung automatisch zu finden.</span><span class="sxs-lookup"><span data-stu-id="eef15-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="eef15-137">Wenn dies aus irgendeinem Grund nicht möglich ist, können Sie den URI der OpenAPI-Beschreibung für die Web-API mithilfe der Option `--openapi` angeben:</span><span class="sxs-lookup"><span data-stu-id="eef15-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="eef15-138">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="eef15-139">Ausführliche Ausgabe aktivieren, um Details zum Suchen, Analysieren und Validieren der OpenAPI-Beschreibung anzuzeigen</span><span class="sxs-lookup"><span data-stu-id="eef15-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="eef15-140">Wenn Sie die `--verbose`-Option mit dem Befehl `connect` angeben, werden weitere Details generiert, wenn das Tool nach der OpenAPI-Beschreibung sucht, diese analysiert und validiert.</span><span class="sxs-lookup"><span data-stu-id="eef15-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="eef15-141">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="eef15-142">Navigieren in der Web-API</span><span class="sxs-lookup"><span data-stu-id="eef15-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="eef15-143">Anzeigen verfügbarer Endpunkte</span><span class="sxs-lookup"><span data-stu-id="eef15-143">View available endpoints</span></span>

<span data-ttu-id="eef15-144">Führen Sie den Befehl `ls` oder `dir` aus, um die unterschiedlichen Endpunkte (Controller) im aktuellen Pfad der Web-API-Adresse aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="eef15-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="eef15-145">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="eef15-146">Die vorherige Ausgabe gibt an, dass die zwei Controller `Fruits` und `People` verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="eef15-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="eef15-147">Beide Controller unterstützen parameterlose HTTP GET- und HTTP POST-Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="eef15-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="eef15-148">Wenn Sie zu einem bestimmten Controller navigieren, werden weitere Details angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eef15-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="eef15-149">Aus der Ausgabe des folgenden Befehls geht beispielsweise hervor, dass der Controller `Fruits` auch HTTP GET-, HTTP PUT- und HTTP DELETE-Vorgänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eef15-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="eef15-150">Jeder dieser Vorgänge erwartet einen `id`-Parameter in der Route:</span><span class="sxs-lookup"><span data-stu-id="eef15-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="eef15-151">Führen Sie alternativ den Befehl `ui` aus, um die Swagger-Benutzeroberflächenseite der Web-API in einem Browser zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="eef15-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="eef15-152">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="eef15-153">Navigieren zu einem Endpunkt</span><span class="sxs-lookup"><span data-stu-id="eef15-153">Navigate to an endpoint</span></span>

<span data-ttu-id="eef15-154">Führen Sie den Befehl `cd` aus, um zu einem anderen Endpunkt in der Web-API zu navigieren:</span><span class="sxs-lookup"><span data-stu-id="eef15-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="eef15-155">Für den Pfad im Befehl `cd` wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="eef15-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="eef15-156">Folgendes Ausgabeformat wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="eef15-157">Anpassen von HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="eef15-157">Customize the HTTP REPL</span></span>

<span data-ttu-id="eef15-158">Die [Standardfarben](#set-color-preferences) von HTTP REPL können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-158">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="eef15-159">Außerdem kann definiert werden, welcher [Text-Editor](#set-the-default-text-editor) standardmäßig verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="eef15-160">Die HTTP REPL-Einstellungen werden in der aktuellen Sitzung beibehalten und in zukünftigen Sitzungen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="eef15-160">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="eef15-161">Nach der Änderung werden die Einstellungen in der folgenden Datei gespeichert:</span><span class="sxs-lookup"><span data-stu-id="eef15-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="eef15-162">Linux</span><span class="sxs-lookup"><span data-stu-id="eef15-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="eef15-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eef15-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="eef15-164">macOS</span><span class="sxs-lookup"><span data-stu-id="eef15-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="eef15-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eef15-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="eef15-166">Windows</span><span class="sxs-lookup"><span data-stu-id="eef15-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="eef15-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="eef15-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="eef15-168">Die *HTTPREPLPREFS-Datei* wird beim Start geladen und bei der Laufzeit nicht auf Änderungen überwacht.</span><span class="sxs-lookup"><span data-stu-id="eef15-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="eef15-169">Manuelle Änderungen an der Datei werden erst nach dem Neustart des Tools wirksam.</span><span class="sxs-lookup"><span data-stu-id="eef15-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="eef15-170">Anzeigen der Einstellungen</span><span class="sxs-lookup"><span data-stu-id="eef15-170">View the settings</span></span>

<span data-ttu-id="eef15-171">Führen Sie den Befehl `pref get` aus, um die verfügbaren Einstellungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="eef15-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="eef15-172">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="eef15-173">Mit dem vorangehenden Befehl werden die verfügbaren Schlüssel-Wert-Paare angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="eef15-174">Festlegen von Farbeinstellungen</span><span class="sxs-lookup"><span data-stu-id="eef15-174">Set color preferences</span></span>

<span data-ttu-id="eef15-175">Die farbliche Kennzeichnung von Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eef15-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="eef15-176">Sie können die Standardfarben des HTTP REPL-Tools anpassen, indem Sie den Schlüssel für die entsprechende Farbe suchen und ändern.</span><span class="sxs-lookup"><span data-stu-id="eef15-176">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="eef15-177">Weitere Informationen zum Suchen von Schlüsseln finden Sie im Abschnitt [Anzeigen der Einstellungen](#view-the-settings).</span><span class="sxs-lookup"><span data-stu-id="eef15-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="eef15-178">Ändern Sie beispielsweise den Wert des Schlüssels `colors.json` folgendermaßen von `Green` in `White`:</span><span class="sxs-lookup"><span data-stu-id="eef15-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="eef15-179">Es können nur die [zulässigen Farben](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="eef15-180">Nachfolgende HTTP-Anforderungen zeigen die Ausgabe mit der neuen Farbgebung an.</span><span class="sxs-lookup"><span data-stu-id="eef15-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="eef15-181">Wenn bestimmte Farbtasten nicht festgelegt sind, werden mehr generische Schlüssel berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="eef15-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="eef15-182">Anhand des folgenden Beispiels wird das Fallbackverhalten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="eef15-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="eef15-183">Wenn `colors.json.name` keinen Wert aufweist, wird `colors.json.string` verwendet.</span><span class="sxs-lookup"><span data-stu-id="eef15-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="eef15-184">Wenn `colors.json.string` keinen Wert aufweist, wird `colors.json.literal` verwendet.</span><span class="sxs-lookup"><span data-stu-id="eef15-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="eef15-185">Wenn `colors.json.literal` keinen Wert aufweist, wird `colors.json` verwendet.</span><span class="sxs-lookup"><span data-stu-id="eef15-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="eef15-186">Wenn `colors.json` keinen Wert aufweist, wird die Standardtextfarbe (`AllowedColors.None`) der Befehlsshell verwendet.</span><span class="sxs-lookup"><span data-stu-id="eef15-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="eef15-187">Festlegen der Einzugsgröße</span><span class="sxs-lookup"><span data-stu-id="eef15-187">Set indentation size</span></span>

<span data-ttu-id="eef15-188">Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eef15-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="eef15-189">Der Standardgröße beträgt zwei Leerzeichen.</span><span class="sxs-lookup"><span data-stu-id="eef15-189">The default size is two spaces.</span></span> <span data-ttu-id="eef15-190">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-190">For example:</span></span>

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

<span data-ttu-id="eef15-191">Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern.</span><span class="sxs-lookup"><span data-stu-id="eef15-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="eef15-192">Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:</span><span class="sxs-lookup"><span data-stu-id="eef15-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="eef15-193">Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:</span><span class="sxs-lookup"><span data-stu-id="eef15-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="eef15-194">Festlegen des Standard-Text-Editors</span><span class="sxs-lookup"><span data-stu-id="eef15-194">Set the default text editor</span></span>

<span data-ttu-id="eef15-195">Standardmäßig ist für HTTP REPL kein Text-Editor konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="eef15-195">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="eef15-196">Wenn Sie Web-API-Methoden testen möchten, für die ein HTTP-Anforderungstext erforderlich ist, müssen Sie einen Standard-Text-Editor festlegen.</span><span class="sxs-lookup"><span data-stu-id="eef15-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="eef15-197">Das HTTP REPL-Tool startet den konfigurierten Text-Editor nur zum Verfassen des Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="eef15-197">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="eef15-198">Führen Sie den folgenden Befehl aus, um Ihren bevorzugten Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="eef15-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="eef15-199">Im vorherigen Befehl ist `<EXECUTABLE>` der vollständige Pfad zur ausführbaren Datei des Text-Editors.</span><span class="sxs-lookup"><span data-stu-id="eef15-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="eef15-200">Führen Sie beispielsweise folgenden Befehl aus, um Visual Studio Code als Standard-Text-Editor festzulegen:</span><span class="sxs-lookup"><span data-stu-id="eef15-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="eef15-201">Linux</span><span class="sxs-lookup"><span data-stu-id="eef15-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="eef15-202">macOS</span><span class="sxs-lookup"><span data-stu-id="eef15-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="eef15-203">Windows</span><span class="sxs-lookup"><span data-stu-id="eef15-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="eef15-204">Legen Sie den Schlüssel `editor.command.default.arguments` fest, um den Standard-Text-Editor mit bestimmten CLI-Argumenten zu starten.</span><span class="sxs-lookup"><span data-stu-id="eef15-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="eef15-205">Nehmen Sie beispielsweise an, dass Visual Studio Code der Standard-Text-Editor ist und Sie möchten, dass Visual Studio Code durch HTTP REPL immer in einer neuen Sitzung mit deaktivierten Erweiterungen öffnet.</span><span class="sxs-lookup"><span data-stu-id="eef15-205">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="eef15-206">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="eef15-207">Wenn Ihr Standard-Editor Visual Studio Code ist, sollten Sie normalerweise das `-w`- oder `--wait`-Argument übergeben, um Visual Studio Code zu zwingen, auf das Schließen der Datei zu warten, bevor die Rückgabe erfolgt.</span><span class="sxs-lookup"><span data-stu-id="eef15-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="eef15-208">Festlegen der Suchpfade der OpenAPI-Beschreibung</span><span class="sxs-lookup"><span data-stu-id="eef15-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="eef15-209">Standardmäßig weist die HTTP-REPL eine Reihe von relativen Pfaden auf, die zum Suchen nach der OpenAPI-Beschreibung verwendet werden, wenn der `connect`-Befehl ohne die Option `--openapi` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-209">By default, the HTTP REPL has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="eef15-210">Diese relativen Pfade werden mit den Stamm- und Basispfaden kombiniert, die im `connect`-Befehl angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="eef15-211">Die standardmäßigen relativen Pfade lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="eef15-211">The default relative paths are:</span></span>

- <span data-ttu-id="eef15-212">*swagger.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-212">*swagger.json*</span></span>
- <span data-ttu-id="eef15-213">*swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="eef15-214">*/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-214">*/swagger.json*</span></span>
- <span data-ttu-id="eef15-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="eef15-216">*openapi.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-216">*openapi.json*</span></span>
- <span data-ttu-id="eef15-217">*/openapi.json*</span><span class="sxs-lookup"><span data-stu-id="eef15-217">*/openapi.json*</span></span>

<span data-ttu-id="eef15-218">Um andere Suchpfade in Ihrer Umgebung zu verwenden, legen Sie die Einstellung `swagger.searchPaths` fest.</span><span class="sxs-lookup"><span data-stu-id="eef15-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="eef15-219">Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln.</span><span class="sxs-lookup"><span data-stu-id="eef15-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eef15-220">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="eef15-221">Anstatt die Standardliste vollständig zu ersetzen, kann die Liste auch durch Hinzufügen oder Entfernen von Pfaden geändert werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="eef15-222">Wenn Sie der Standardliste mindestens einen Suchpfad hinzufügen möchten, legen Sie die Einstellung `swagger.addToSearchPaths` fest.</span><span class="sxs-lookup"><span data-stu-id="eef15-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="eef15-223">Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln.</span><span class="sxs-lookup"><span data-stu-id="eef15-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eef15-224">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="eef15-225">Wenn Sie mindestens einen Suchpfad aus der Standardliste entfernen möchten, legen Sie die Einstellung `swagger.addToSearchPaths` fest.</span><span class="sxs-lookup"><span data-stu-id="eef15-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="eef15-226">Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln.</span><span class="sxs-lookup"><span data-stu-id="eef15-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="eef15-227">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="eef15-228">Testen von HTTP GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-229">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-230">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-231">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-232">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-232">Options</span></span>

<span data-ttu-id="eef15-233">Für den Befehl `get` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="eef15-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="eef15-234">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eef15-234">Example</span></span>

<span data-ttu-id="eef15-235">So führen Sie eine HTTP GET-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="eef15-236">Führen Sie den Befehl `get` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eef15-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="eef15-237">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-237">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="eef15-238">Rufen Sie einen bestimmten Datensatz ab, indem Sie einen Parameter an den Befehl `get` übergeben:</span><span class="sxs-lookup"><span data-stu-id="eef15-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="eef15-239">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-239">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="eef15-240">Testen von HTTP POST-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-241">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-242">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-243">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-244">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="eef15-245">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eef15-245">Example</span></span>

<span data-ttu-id="eef15-246">So führen Sie eine HTTP POST-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="eef15-247">Führen Sie den Befehl `post` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eef15-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="eef15-248">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="eef15-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="eef15-249">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="eef15-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="eef15-250">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="eef15-251">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="eef15-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="eef15-252">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="eef15-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="eef15-253">Speichern Sie die *TMP-Datei* , und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="eef15-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="eef15-254">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-254">The following output appears in the command shell:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="eef15-255">Testen von HTTP PUT-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-256">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-257">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-258">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-259">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="eef15-260">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eef15-260">Example</span></span>

<span data-ttu-id="eef15-261">So führen Sie eine HTTP PUT-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="eef15-262">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="eef15-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="eef15-263">Führen Sie den Befehl `put` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eef15-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="eef15-264">Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt.</span><span class="sxs-lookup"><span data-stu-id="eef15-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="eef15-265">Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt.</span><span class="sxs-lookup"><span data-stu-id="eef15-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="eef15-266">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="eef15-267">Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).</span><span class="sxs-lookup"><span data-stu-id="eef15-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="eef15-268">Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:</span><span class="sxs-lookup"><span data-stu-id="eef15-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="eef15-269">Speichern Sie die *TMP-Datei* , und schließen Sie den Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="eef15-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="eef15-270">In der Befehlsshell wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="eef15-271">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="eef15-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="eef15-272">Wenn Sie beispielsweise „Cherry“ in den Text-Editor eingeben, gibt `get` die folgende Ausgabe zurück:</span><span class="sxs-lookup"><span data-stu-id="eef15-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="eef15-273">Testen von HTTP DELETE-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-274">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-275">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-276">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-277">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="eef15-278">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eef15-278">Example</span></span>

<span data-ttu-id="eef15-279">So führen Sie eine HTTP DELETE-Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="eef15-280">*Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="eef15-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="eef15-281">Führen Sie den Befehl `delete` für einen Endpunkt aus, der ihn unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eef15-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="eef15-282">Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="eef15-283">*Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="eef15-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="eef15-284">In diesem Beispiel gibt `get` die folgende Ausgabe zurück:</span><span class="sxs-lookup"><span data-stu-id="eef15-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="eef15-285">Testen von HTTP PATCH-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-286">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-287">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-288">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-289">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="eef15-290">Testen von HTTP HEAD-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-291">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-292">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-293">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-294">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="eef15-295">Testen von HTTP OPTIONS-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="eef15-296">Übersicht</span><span class="sxs-lookup"><span data-stu-id="eef15-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="eef15-297">Argumente</span><span class="sxs-lookup"><span data-stu-id="eef15-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="eef15-298">Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="eef15-299">Optionen</span><span class="sxs-lookup"><span data-stu-id="eef15-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="eef15-300">Festlegen von HTTP-Anforderungsheadern</span><span class="sxs-lookup"><span data-stu-id="eef15-300">Set HTTP request headers</span></span>

<span data-ttu-id="eef15-301">Verwenden Sie einen der folgenden Ansätze, um einen HTTP-Anforderungsheader festzulegen:</span><span class="sxs-lookup"><span data-stu-id="eef15-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="eef15-302">Legen Sie den Header inline mit der HTTP-Anforderung fest.</span><span class="sxs-lookup"><span data-stu-id="eef15-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="eef15-303">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="eef15-304">Beim vorherigen Ansatz benötigt jeder eindeutige HTTP-Anforderungsheader eine eigene `-h`-Option.</span><span class="sxs-lookup"><span data-stu-id="eef15-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="eef15-305">Legen Sie den Header fest, bevor Sie die HTTP-Anforderung senden.</span><span class="sxs-lookup"><span data-stu-id="eef15-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="eef15-306">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="eef15-307">Wenn Sie den Header vor dem Senden einer Anforderung festlegen, bleibt er für die Dauer der Befehlsshellsitzung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eef15-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="eef15-308">Geben Sie einen leeren Wert an, um den Header zu löschen.</span><span class="sxs-lookup"><span data-stu-id="eef15-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="eef15-309">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="eef15-310">Testen gesicherter Endpunkte</span><span class="sxs-lookup"><span data-stu-id="eef15-310">Test secured endpoints</span></span>

<span data-ttu-id="eef15-311">Die HTTP-REPL unterstützt das Testen von gesicherten Endpunkten auf folgende Weise:</span><span class="sxs-lookup"><span data-stu-id="eef15-311">The HTTP REPL supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="eef15-312">Über die Standardanmeldeinformationen des angemeldeten Benutzers.</span><span class="sxs-lookup"><span data-stu-id="eef15-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="eef15-313">Über die Verwendung von HTTP-Anforderungsheadern.</span><span class="sxs-lookup"><span data-stu-id="eef15-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="eef15-314">Standardanmeldeinformationen</span><span class="sxs-lookup"><span data-stu-id="eef15-314">Default credentials</span></span>

<span data-ttu-id="eef15-315">Angenommen, Sie testen eine Web-API, die in IIS gehostet und durch Windows-Authentifizierung gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="eef15-316">Sie möchten, dass die Anmeldeinformationen des Benutzers, der das Tool ausführt, an die zu testenden HTTP-Endpunkte übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="eef15-317">So übergeben Sie die Standardanmeldeinformationen des angemeldeten Benutzers:</span><span class="sxs-lookup"><span data-stu-id="eef15-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="eef15-318">Legen Sie die Einstellung `httpClient.useDefaultCredentials` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="eef15-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="eef15-319">Beenden und starten Sie das Tool dann neu, bevor Sie eine weitere Anforderung an die Web-API senden.</span><span class="sxs-lookup"><span data-stu-id="eef15-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="eef15-320">Standard-Proxyanmeldeinformationen</span><span class="sxs-lookup"><span data-stu-id="eef15-320">Default proxy credentials</span></span>

<span data-ttu-id="eef15-321">Stellen Sie sich ein Szenario vor, in dem sich die zu testende Web-API hinter einem Proxy befindet, der durch Windows-Authentifizierung gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="eef15-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="eef15-322">Sie möchten, dass die Anmeldeinformationen des Benutzers, der das Tool ausführt, an den Proxy übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="eef15-323">So übergeben Sie die Standardanmeldeinformationen des angemeldeten Benutzers:</span><span class="sxs-lookup"><span data-stu-id="eef15-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="eef15-324">Legen Sie die Einstellung `httpClient.proxy.useDefaultCredentials` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="eef15-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="eef15-325">Beenden und starten Sie das Tool dann neu, bevor Sie eine weitere Anforderung an die Web-API senden.</span><span class="sxs-lookup"><span data-stu-id="eef15-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="eef15-326">HTTP-Anforderungsheader</span><span class="sxs-lookup"><span data-stu-id="eef15-326">HTTP request headers</span></span>

<span data-ttu-id="eef15-327">Beispiele für unterstützte Authentifizierungs- und Autorisierungsschemas:</span><span class="sxs-lookup"><span data-stu-id="eef15-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="eef15-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="eef15-328">basic authentication</span></span>
* <span data-ttu-id="eef15-329">JWT-Bearertoken</span><span class="sxs-lookup"><span data-stu-id="eef15-329">JWT bearer tokens</span></span>
* <span data-ttu-id="eef15-330">Digestauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="eef15-330">digest authentication</span></span>

<span data-ttu-id="eef15-331">Beispielsweise können Sie mit dem folgenden Befehl ein Bearertoken an einen Endpunkt senden:</span><span class="sxs-lookup"><span data-stu-id="eef15-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="eef15-332">Um auf einen in Azure gehosteten Endpunkt zuzugreifen oder die [Azure-REST-API](/rest/api/azure/) zu verwenden, benötigen Sie ein Bearertoken.</span><span class="sxs-lookup"><span data-stu-id="eef15-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="eef15-333">Führen Sie die folgenden Schritte aus, um über die [Azure CLI](/cli/azure/) ein Bearertoken für Ihr Azure-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="eef15-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="eef15-334">Die HTTP-REPL legt das Bearertoken in einem HTTP-Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="eef15-334">The HTTP REPL sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="eef15-335">Eine Liste mit Azure App Service-Web-Apps wird abgerufen.</span><span class="sxs-lookup"><span data-stu-id="eef15-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="eef15-336">Melden Sie sich bei Azure an:</span><span class="sxs-lookup"><span data-stu-id="eef15-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="eef15-337">Rufen Sie Ihre Abonnement-ID mit dem folgenden Befehl ab:</span><span class="sxs-lookup"><span data-stu-id="eef15-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="eef15-338">Kopieren Sie Ihre Abonnement-ID, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="eef15-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="eef15-339">Rufen Sie Ihr Bearertoken mit dem folgenden Befehl ab:</span><span class="sxs-lookup"><span data-stu-id="eef15-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="eef15-340">Stellen Sie über HTTP REPL eine Verbindung mit der Azure-REST-API her:</span><span class="sxs-lookup"><span data-stu-id="eef15-340">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="eef15-341">Legen Sie den HTTP-Anforderungsheader `Authorization` fest:</span><span class="sxs-lookup"><span data-stu-id="eef15-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="eef15-342">Navigieren Sie zum Abonnement:</span><span class="sxs-lookup"><span data-stu-id="eef15-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="eef15-343">Rufen Sie eine Liste der Azure App Service-Web-Apps Ihres Abonnements ab:</span><span class="sxs-lookup"><span data-stu-id="eef15-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="eef15-344">Die folgende Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="eef15-345">Umschalten der Anzeige von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-345">Toggle HTTP request display</span></span>

<span data-ttu-id="eef15-346">Standardmäßig wird die Anzeige der gesendeten HTTP-Anforderung unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="eef15-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="eef15-347">Sie können die entsprechende Einstellung für die Dauer der Befehlsshellsitzung ändern.</span><span class="sxs-lookup"><span data-stu-id="eef15-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="eef15-348">Aktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-348">Enable request display</span></span>

<span data-ttu-id="eef15-349">Zeigen Sie die gesendete HTTP-Anforderung an, indem Sie den Befehl `echo on` ausführen.</span><span class="sxs-lookup"><span data-stu-id="eef15-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="eef15-350">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="eef15-351">Nachfolgende HTTP-Anforderungen in der aktuellen Sitzung zeigen die Anforderungsheader an.</span><span class="sxs-lookup"><span data-stu-id="eef15-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="eef15-352">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-352">For example:</span></span>

```console
https://localhost:5001/people> post

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


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="eef15-353">Deaktivieren der Anzeige von Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-353">Disable request display</span></span>

<span data-ttu-id="eef15-354">Unterdrücken Sie die Anzeige der gesendeten HTTP-Anforderung, indem Sie den Befehl `echo off` ausführen.</span><span class="sxs-lookup"><span data-stu-id="eef15-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="eef15-355">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="eef15-356">Ausführen eines Skripts</span><span class="sxs-lookup"><span data-stu-id="eef15-356">Run a script</span></span>

<span data-ttu-id="eef15-357">Wenn Sie häufig die gleichen HTTP REPL-Befehle ausführen, können Sie diese in einer Textdatei speichern.</span><span class="sxs-lookup"><span data-stu-id="eef15-357">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="eef15-358">Befehle in der Datei nehmen das gleiche Format wie manuell in der Befehlszeile ausgeführte Befehle an.</span><span class="sxs-lookup"><span data-stu-id="eef15-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="eef15-359">Die Befehle können mithilfe des Befehls `run` auf einmal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="eef15-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="eef15-360">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-360">For example:</span></span>

1. <span data-ttu-id="eef15-361">Erstellen Sie eine Textdatei, die einige Befehle enthält, die jeweils in einer neuen Zeile stehen.</span><span class="sxs-lookup"><span data-stu-id="eef15-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="eef15-362">Sie können die Datei *people-script.txt* , die folgende Befehle enthält, als Beispiel verwenden:</span><span class="sxs-lookup"><span data-stu-id="eef15-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="eef15-363">Führen Sie den Befehl `run` aus, und übergeben Sie den Pfad der Textdatei.</span><span class="sxs-lookup"><span data-stu-id="eef15-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="eef15-364">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eef15-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="eef15-365">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="eef15-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="eef15-366">Löschen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="eef15-366">Clear the output</span></span>

<span data-ttu-id="eef15-367">Sie können alle Ausgaben des HTTP REPL-Tools aus der Befehlsshell entfernen, indem Sie den Befehl `clear` oder `cls` ausführen.</span><span class="sxs-lookup"><span data-stu-id="eef15-367">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="eef15-368">Nehmen Sie beispielsweise an, dass die Befehlsshell folgende Ausgabe enthält:</span><span class="sxs-lookup"><span data-stu-id="eef15-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="eef15-369">Führen Sie den folgenden Befehl aus, um die Ausgabe zu löschen:</span><span class="sxs-lookup"><span data-stu-id="eef15-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="eef15-370">Nachdem der vorherige Befehl ausgeführt wurde, enthält die Befehlsshell nur die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="eef15-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="eef15-371">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eef15-371">Additional resources</span></span>

* [<span data-ttu-id="eef15-372">REST-API-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eef15-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="eef15-373">GitHub-Repository für HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="eef15-373">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
