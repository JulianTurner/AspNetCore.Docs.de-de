---
title: Testen von Web-APIs mit HttpRepl
author: scottaddie
description: Hier erfahren Sie, wie Sie das globale .NET Core-Tool HttpRepl verwenden können, um ASP.NET Core-Web-APIs zu durchsuchen und zu testen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/11/2020
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
uid: web-api/http-repl
ms.openlocfilehash: df2d4e63a18471b4c5f4f1c9434921303bb1da8a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550620"
---
# <a name="test-web-apis-with-the-httprepl"></a>Testen von Web-APIs mit HttpRepl

Von [Scott Addie](https://twitter.com/Scott_Addie)

HTTP REPL (read–eval–print-Loop):

* Ein schlankes, plattformübergreifendes Befehlszeilentool, das überall unterstützt wird, wo .NET Core unterstützt wird
* wird zum Senden von HTTP-Anforderungen verwendet, mit denen ASP.NET Core-Web-APIs (und andere APIs) getestet werden können, und zum Überprüfen von deren Ergebnissen
* kann zum Testen von Web-APIs verwendet werden, die in beliebigen Umgebungen gehostet werden (einschließlich Localhost und Azure App Service)

Folgende [HTTP-Verben](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) werden unterstützt:

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [OPTIONS](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

[Sehen Sie sich die beispielhafte ASP.NET Core-Web-API an, oder laden Sie sie herunter ([Downloadanleitung](xref:index#how-to-download-a-sample))](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples), um dem Artikel zu folgen.

## <a name="prerequisites"></a>Voraussetzungen

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

Führen Sie den folgenden Befehl aus, um HttpRepl zu installieren:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) installiert.

## <a name="usage"></a>Verwendung

Nach der erfolgreichen Installation des Tools können Sie folgenden Befehl ausführen, um HttpRepl zu starten:

```console
httprepl
```

Führen Sie einen der folgenden Befehle aus, um eine Liste der verfügbaren HttpRepl-Befehle anzuzeigen:

```console
httprepl -h
```

```console
httprepl --help
```

Die folgende Ausgabe wird angezeigt:

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

In HttpRepl können Befehle vervollständigt werden. Wenn Sie die <kbd>TAB-TASTE</kbd> drücken, können Sie die Liste der Befehle durchlaufen, die die eingegebenen Zeichen oder API-Endpunkte vervollständigen. In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.

## <a name="connect-to-the-web-api"></a>Herstellen einer Verbindung mit der Web-API

Stellen Sie mithilfe des folgenden Befehls eine Verbindung mit einer Web-API her:

```console
httprepl <ROOT URI>
```

`<ROOT URI>` ist der Basis-URI für die Web-API. Zum Beispiel:

```console
httprepl https://localhost:5001
```

Alternativ können Sie den folgenden Befehl jederzeit ausführen, während HttpRepl ausgeführt wird:

```console
connect <ROOT URI>
```

Zum Beispiel:

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a>Manuelles Verweisen auf die OpenAPI-Beschreibung für die Web-API

Der oben genannte connect-Befehl versucht, die OpenAPI-Beschreibung automatisch zu finden. Wenn dies aus irgendeinem Grund nicht möglich ist, können Sie den URI der OpenAPI-Beschreibung für die Web-API mithilfe der Option `--openapi` angeben:

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

Zum Beispiel:

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a>Ausführliche Ausgabe aktivieren, um Details zum Suchen, Analysieren und Validieren der OpenAPI-Beschreibung anzuzeigen

Wenn Sie die `--verbose`-Option mit dem Befehl `connect` angeben, werden weitere Details generiert, wenn das Tool nach der OpenAPI-Beschreibung sucht, diese analysiert und validiert.

```console
connect <ROOT URI> --verbose
```

Zum Beispiel:

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a>Navigieren in der Web-API

### <a name="view-available-endpoints"></a>Anzeigen verfügbarer Endpunkte

Führen Sie den Befehl `ls` oder `dir` aus, um die unterschiedlichen Endpunkte (Controller) im aktuellen Pfad der Web-API-Adresse aufzulisten:

```console
https://localhot:5001/> ls
```

Folgendes Ausgabeformat wird angezeigt:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

Die vorherige Ausgabe gibt an, dass die zwei Controller `Fruits` und `People` verfügbar sind. Beide Controller unterstützen parameterlose HTTP GET- und HTTP POST-Vorgänge.

Wenn Sie zu einem bestimmten Controller navigieren, werden weitere Details angezeigt. Aus der Ausgabe des folgenden Befehls geht beispielsweise hervor, dass der Controller `Fruits` auch HTTP GET-, HTTP PUT- und HTTP DELETE-Vorgänge unterstützt. Jeder dieser Vorgänge erwartet einen `id`-Parameter in der Route:

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

Führen Sie alternativ den Befehl `ui` aus, um die Swagger-Benutzeroberflächenseite der Web-API in einem Browser zu öffnen. Zum Beispiel:

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a>Navigieren zu einem Endpunkt

Führen Sie den Befehl `cd` aus, um zu einem anderen Endpunkt in der Web-API zu navigieren:

```console
https://localhost:5001/> cd people
```

Für den Pfad im Befehl `cd` wird die Groß-/Kleinschreibung nicht beachtet. Folgendes Ausgabeformat wird angezeigt:

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a>Anpassen von HttpRepl

Die [Standardfarben](#set-color-preferences) von HttpRepl können angepasst werden. Außerdem kann definiert werden, welcher [Text-Editor](#set-the-default-text-editor) standardmäßig verwendet wird. Die HttpRepl-Einstellungen werden in der aktuellen Sitzung beibehalten und in zukünftigen Sitzungen berücksichtigt. Nach der Änderung werden die Einstellungen in der folgenden Datei gespeichert:

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

Die *HTTPREPLPREFS-Datei* wird beim Start geladen und bei der Laufzeit nicht auf Änderungen überwacht. Manuelle Änderungen an der Datei werden erst nach dem Neustart des Tools wirksam.

### <a name="view-the-settings"></a>Anzeigen der Einstellungen

Führen Sie den Befehl `pref get` aus, um die verfügbaren Einstellungen anzuzeigen. Zum Beispiel:

```console
https://localhost:5001/> pref get
```

Mit dem vorangehenden Befehl werden die verfügbaren Schlüssel-Wert-Paare angezeigt:

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

### <a name="set-color-preferences"></a>Festlegen von Farbeinstellungen

Die farbliche Kennzeichnung von Antworten wird derzeit nur für JSON unterstützt. Sie können die Standardfarben des HttpRepl-Tools anpassen, indem Sie den Schlüssel für die entsprechende Farbe suchen und ändern. Weitere Informationen zum Suchen von Schlüsseln finden Sie im Abschnitt [Anzeigen der Einstellungen](#view-the-settings). Ändern Sie beispielsweise den Wert des Schlüssels `colors.json` folgendermaßen von `Green` in `White`:

```console
https://localhost:5001/people> pref set colors.json White
```

Es können nur die [zulässigen Farben](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) verwendet werden. Nachfolgende HTTP-Anforderungen zeigen die Ausgabe mit der neuen Farbgebung an.

Wenn bestimmte Farbtasten nicht festgelegt sind, werden mehr generische Schlüssel berücksichtigt. Anhand des folgenden Beispiels wird das Fallbackverhalten veranschaulicht:

* Wenn `colors.json.name` keinen Wert aufweist, wird `colors.json.string` verwendet.
* Wenn `colors.json.string` keinen Wert aufweist, wird `colors.json.literal` verwendet.
* Wenn `colors.json.literal` keinen Wert aufweist, wird `colors.json` verwendet. 
* Wenn `colors.json` keinen Wert aufweist, wird die Standardtextfarbe (`AllowedColors.None`) der Befehlsshell verwendet.

### <a name="set-indentation-size"></a>Festlegen der Einzugsgröße

Die Anpassung der Einzugsgröße für Antworten wird derzeit nur für JSON unterstützt. Der Standardgröße beträgt zwei Leerzeichen. Zum Beispiel:

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

Legen Sie den Schlüssel `formatting.json.indentSize` fest, um die Standardgröße zu ändern. Folgendermaßen werden beispielsweise immer vier Leerzeichen verwendet:

```console
pref set formatting.json.indentSize 4
```

Nachfolgende Antworten berücksichtigen die Einstellung auf vier Leerzeichen:

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

### <a name="set-the-default-text-editor"></a>Festlegen des Standard-Text-Editors

Standardmäßig ist für HttpRepl kein Text-Editor konfiguriert. Wenn Sie Web-API-Methoden testen möchten, für die ein HTTP-Anforderungstext erforderlich ist, müssen Sie einen Standard-Text-Editor festlegen. Das HttpRepl-Tool startet den konfigurierten Text-Editor nur zum Verfassen des Anforderungstexts. Führen Sie den folgenden Befehl aus, um Ihren bevorzugten Text-Editor festzulegen:

```console
pref set editor.command.default "<EXECUTABLE>"
```

Im vorherigen Befehl ist `<EXECUTABLE>` der vollständige Pfad zur ausführbaren Datei des Text-Editors. Führen Sie beispielsweise folgenden Befehl aus, um Visual Studio Code als Standard-Text-Editor festzulegen:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Legen Sie den Schlüssel `editor.command.default.arguments` fest, um den Standard-Text-Editor mit bestimmten CLI-Argumenten zu starten. Nehmen Sie beispielsweise an, dass Visual Studio Code der Standard-Text-Editor ist und Sie möchten, dass Visual Studio Code durch HttpRepl immer in einer neuen Sitzung mit deaktivierten Erweiterungen öffnet. Führen Sie den folgenden Befehl aus:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> Wenn Ihr Standard-Editor Visual Studio Code ist, sollten Sie normalerweise das `-w`- oder `--wait`-Argument übergeben, um Visual Studio Code zu zwingen, auf das Schließen der Datei zu warten, bevor die Rückgabe erfolgt.

### <a name="set-the-openapi-description-search-paths"></a>Festlegen der Suchpfade der OpenAPI-Beschreibung

Standardmäßig weist die HttpReplL eine Reihe von relativen Pfaden auf, die zum Suchen nach der OpenAPI-Beschreibung verwendet werden, wenn der `connect`-Befehl ohne die Option `--openapi` ausgeführt wird. Diese relativen Pfade werden mit den Stamm- und Basispfaden kombiniert, die im `connect`-Befehl angegeben werden. Die standardmäßigen relativen Pfade lauten wie folgt:

- *swagger.json*
- *swagger/v1/swagger.json*
- */swagger.json*
- */swagger/v1/swagger.json*
- *openapi.json*
- */openapi.json*

Um andere Suchpfade in Ihrer Umgebung zu verwenden, legen Sie die Einstellung `swagger.searchPaths` fest. Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln. Zum Beispiel:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

Anstatt die Standardliste vollständig zu ersetzen, kann die Liste auch durch Hinzufügen oder Entfernen von Pfaden geändert werden.

Wenn Sie der Standardliste mindestens einen Suchpfad hinzufügen möchten, legen Sie die Einstellung `swagger.addToSearchPaths` fest. Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln. Zum Beispiel:

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

Wenn Sie mindestens einen Suchpfad aus der Standardliste entfernen möchten, legen Sie die Einstellung `swagger.addToSearchPaths` fest. Bei dem Wert muss es sich um eine durch Pipezeichen getrennte Liste relativer Pfade handeln. Zum Beispiel:

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a>Testen von HTTP GET-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

Für den Befehl `get` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Beispiel

So führen Sie eine HTTP GET-Anforderung aus:

1. Führen Sie den Befehl `get` für einen Endpunkt aus, der ihn unterstützt:

    ```console
    https://localhost:5001/people> get
    ```

    Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:

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

1. Rufen Sie einen bestimmten Datensatz ab, indem Sie einen Parameter an den Befehl `get` übergeben:

    ```console
    https://localhost:5001/people> get 2
    ```

    Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:

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

## <a name="test-http-post-requests"></a>Testen von HTTP POST-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Beispiel

So führen Sie eine HTTP POST-Anforderung aus:

1. Führen Sie den Befehl `post` für einen Endpunkt aus, der ihn unterstützt:

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt. Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt. Zum Beispiel:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).

1. Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor. In der Befehlsshell wird die folgende Ausgabe angezeigt:

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

## <a name="test-http-put-requests"></a>Testen von HTTP PUT-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Beispiel

So führen Sie eine HTTP PUT-Anforderung aus:

1. *Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:

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

1. Führen Sie den Befehl `put` für einen Endpunkt aus, der ihn unterstützt:

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    Im vorherigen Befehl wird der HTTP-Anforderungsheader `Content-Type` so festgelegt, dass er den Medientyp des Anforderungstexts (JSON) angibt. Der Standard-Text-Editor öffnet eine *TMP-Datei* mit einer JSON-Vorlage, die den HTTP-Anforderungstext darstellt. Zum Beispiel:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Weitere Informationen zum Festlegen des Standard-Text-Editors Abschnitt [Festlegen des Standard-Text-Editors](#set-the-default-text-editor).

1. Ändern Sie die JSON-Vorlage so, dass die Anforderungen für die Modellvalidierung erfüllt werden:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Speichern Sie die *TMP-Datei*, und schließen Sie den Text-Editor. In der Befehlsshell wird die folgende Ausgabe angezeigt:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen. Wenn Sie beispielsweise „Cherry“ in den Text-Editor eingeben, gibt `get` die folgende Ausgabe zurück:

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

## <a name="test-http-delete-requests"></a>Testen von HTTP DELETE-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Beispiel

So führen Sie eine HTTP DELETE-Anforderung aus:

1. *Optional:* Führen Sie den Befehl `get` aus, um die Daten vor dem Ändern anzuzeigen:

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

1. Führen Sie den Befehl `delete` für einen Endpunkt aus, der ihn unterstützt:

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    Über den vorherigen Befehl wird das folgende Ausgabeformat angezeigt:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Optional:* Führen Sie den Befehl `get` aus, um die Änderungen anzuzeigen. In diesem Beispiel gibt `get` die folgende Ausgabe zurück:

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

## <a name="test-http-patch-requests"></a>Testen von HTTP PATCH-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testen von HTTP HEAD-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testen von HTTP OPTIONS-Anforderungen

### <a name="synopsis"></a>Übersicht

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumente

`PARAMETER`

Der Routenparameter (sofern vorhanden), der von der zugeordneten Aktionsmethode des Controllers erwartet wird.

### <a name="options"></a>Optionen

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Festlegen von HTTP-Anforderungsheadern

Verwenden Sie einen der folgenden Ansätze, um einen HTTP-Anforderungsheader festzulegen:

* Legen Sie den Header inline mit der HTTP-Anforderung fest. Zum Beispiel:

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    Beim vorherigen Ansatz benötigt jeder eindeutige HTTP-Anforderungsheader eine eigene `-h`-Option.

* Legen Sie den Header fest, bevor Sie die HTTP-Anforderung senden. Zum Beispiel:

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    Wenn Sie den Header vor dem Senden einer Anforderung festlegen, bleibt er für die Dauer der Befehlsshellsitzung festgelegt. Geben Sie einen leeren Wert an, um den Header zu löschen. Zum Beispiel:
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Testen gesicherter Endpunkte

Die HttpRepl unterstützt das Testen von gesicherten Endpunkten auf folgende Weise:

* Über die Standardanmeldeinformationen des angemeldeten Benutzers.
* Über die Verwendung von HTTP-Anforderungsheadern.

### <a name="default-credentials"></a>Standardanmeldeinformationen

Angenommen, Sie testen eine Web-API, die in IIS gehostet und durch Windows-Authentifizierung gesichert wird. Sie möchten, dass die Anmeldeinformationen des Benutzers, der das Tool ausführt, an die zu testenden HTTP-Endpunkte übergeben werden. So übergeben Sie die Standardanmeldeinformationen des angemeldeten Benutzers:

1. Legen Sie die Einstellung `httpClient.useDefaultCredentials` auf `true` fest:

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Beenden und starten Sie das Tool dann neu, bevor Sie eine weitere Anforderung an die Web-API senden.
 
### <a name="default-proxy-credentials"></a>Standard-Proxyanmeldeinformationen

Stellen Sie sich ein Szenario vor, in dem sich die zu testende Web-API hinter einem Proxy befindet, der durch Windows-Authentifizierung gesichert wird. Sie möchten, dass die Anmeldeinformationen des Benutzers, der das Tool ausführt, an den Proxy übergeben werden. So übergeben Sie die Standardanmeldeinformationen des angemeldeten Benutzers:

1. Legen Sie die Einstellung `httpClient.proxy.useDefaultCredentials` auf `true` fest:

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. Beenden und starten Sie das Tool dann neu, bevor Sie eine weitere Anforderung an die Web-API senden.

### <a name="http-request-headers"></a>HTTP-Anforderungsheader

Beispiele für unterstützte Authentifizierungs- und Autorisierungsschemas:

* basic authentication
* JWT-Bearertoken
* Digestauthentifizierung

Beispielsweise können Sie mit dem folgenden Befehl ein Bearertoken an einen Endpunkt senden:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Um auf einen in Azure gehosteten Endpunkt zuzugreifen oder die [Azure-REST-API](/rest/api/azure/) zu verwenden, benötigen Sie ein Bearertoken. Führen Sie die folgenden Schritte aus, um über die [Azure CLI](/cli/azure/) ein Bearertoken für Ihr Azure-Abonnement zu erhalten. Die HttpRepl legt das Bearertoken in einem HTTP-Anforderungsheader fest. Eine Liste mit Azure App Service-Web-Apps wird abgerufen.

1. Melden Sie sich bei Azure an:

    ```azurecli
    az login
    ```

1. Rufen Sie Ihre Abonnement-ID mit dem folgenden Befehl ab:

    ```azurecli
    az account show --query id
    ```

1. Kopieren Sie Ihre Abonnement-ID, und führen Sie den folgenden Befehl aus:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Rufen Sie Ihr Bearertoken mit dem folgenden Befehl ab:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Stellen Sie über HttpRepl eine Verbindung mit der Azure-REST-API her:

    ```console
    httprepl https://management.azure.com
    ```

1. Legen Sie den HTTP-Anforderungsheader `Authorization` fest:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Navigieren Sie zum Abonnement:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Rufen Sie eine Liste der Azure App Service-Web-Apps Ihres Abonnements ab:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    Die folgende Antwort wird angezeigt:

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

## <a name="toggle-http-request-display"></a>Umschalten der Anzeige von HTTP-Anforderungen

Standardmäßig wird die Anzeige der gesendeten HTTP-Anforderung unterdrückt. Sie können die entsprechende Einstellung für die Dauer der Befehlsshellsitzung ändern.

### <a name="enable-request-display"></a>Aktivieren der Anzeige von Anforderungen

Zeigen Sie die gesendete HTTP-Anforderung an, indem Sie den Befehl `echo on` ausführen. Zum Beispiel:

```console
https://localhost:5001/people> echo on
Request echoing is on
```

Nachfolgende HTTP-Anforderungen in der aktuellen Sitzung zeigen die Anforderungsheader an. Zum Beispiel:

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

### <a name="disable-request-display"></a>Deaktivieren der Anzeige von Anforderungen

Unterdrücken Sie die Anzeige der gesendeten HTTP-Anforderung, indem Sie den Befehl `echo off` ausführen. Zum Beispiel:

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a>Ausführen eines Skripts

Wenn Sie häufig die gleichen HttpRepl-Befehle ausführen, können Sie diese in einer Textdatei speichern. Befehle in der Datei nehmen das gleiche Format wie manuell in der Befehlszeile ausgeführte Befehle an. Die Befehle können mithilfe des Befehls `run` auf einmal ausgeführt werden. Zum Beispiel:

1. Erstellen Sie eine Textdatei, die einige Befehle enthält, die jeweils in einer neuen Zeile stehen. Sie können die Datei *people-script.txt*, die folgende Befehle enthält, als Beispiel verwenden:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Führen Sie den Befehl `run` aus, und übergeben Sie den Pfad der Textdatei. Zum Beispiel:

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    Die folgende Ausgabe wird angezeigt:

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

## <a name="clear-the-output"></a>Löschen der Ausgabe

Sie können alle Ausgaben des HttpRepl-Tools aus der Befehlsshell entfernen, indem Sie den Befehl `clear` oder `cls` ausführen. Nehmen Sie beispielsweise an, dass die Befehlsshell folgende Ausgabe enthält:

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

Führen Sie den folgenden Befehl aus, um die Ausgabe zu löschen:

```console
https://localhost:5001/> clear
```

Nachdem der vorherige Befehl ausgeführt wurde, enthält die Befehlsshell nur die folgende Ausgabe:

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [REST-API-Anforderungen](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [GitHub-Repository für HttpRepl](https://github.com/dotnet/HttpRepl)
