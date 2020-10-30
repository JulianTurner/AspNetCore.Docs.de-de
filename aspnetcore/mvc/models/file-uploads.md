---
title: Hochladen von Dateien in ASP.NET Core
author: rick-anderson
description: Verwenden von Modellbindung und Streaming zum Hochladen von Dateien in ASP.NET Core MVC
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 14561bace565c104d0a9c926cad3105c4865e72a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061170"
---
# <a name="upload-files-in-aspnet-core"></a>Hochladen von Dateien in ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Rutger Storm](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Sicherheitshinweise

Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen. Angreifer versuchen möglicherweise Folgendes:

* Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen
* Hochladen von Viren oder Schadsoftware
* Gefährden von Netzwerken und Servern auf andere Weise

Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:

* Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk. Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen. Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;
* Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;
* Wählen Sie einen sicheren von der App festgelegten Dateinamen. Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird. Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).
* Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.
* Überprüfen Sie die Größe einer hochgeladenen Datei. Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;
* Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.
* **Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**

&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.

> [!WARNING]
> Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:
>
> * Erlangen der vollständigen Kontrolle über ein System.
> * Überlasten eines Systems mit dem Ziel eines Systemausfalls.
> * Kompromittieren von Benutzer- oder Systemdaten
> * Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.
>
> Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:
>
> * [Unrestricted File Upload (Uneingeschränkter Dateiupload)](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).

## <a name="storage-scenarios"></a>Speicherszenarien

Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:

* Datenbank

  * Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).
  * Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.
  * Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.

* Physischer Speicher (Dateisystem oder Netzwerkfreigabe)

  * Für das Hochladen großer Dateien:
    * Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.
    * Physischer Speicher ist oft teurer als Datenbankspeicher.
  * Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.
  * Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben. **Erteilen Sie niemals die Ausführungsberechtigung.**

* Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.
  * Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.

  Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Szenarien für das Hochladen von Dateien

Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.

**Pufferung**

Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.

Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig. Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist. Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.

> [!NOTE]
> Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.

Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:

* [Physischer Speicher](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Datenbank](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert. Streaming verbessert die Leistung nicht wesentlich. Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.

Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher

Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.

Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei ( *pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:

* Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.
* Keine Validierung erfolgt.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:

* Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Verwenden Sie `XMLHttpRequest`. Zum Beispiel:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.

Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden. Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.

<a name="filename"></a>

> [!WARNING]
> Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung. Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML. Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade. Anwendungen sollten folgende Aktionen ausführen:
>
> * den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen
> * den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern
> * einen neuen zufälligen Dateinamen für die Speicherung generieren
>
> Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:

* Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Eine der folgenden Sammlungen, die mehrere Dateien darstellen:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Zur Bindung werden Formulardateien anhand des Namens abgeglichen. So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`). Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).

Im Beispiel unten geschieht Folgendes:

* Durchläuft mindestens eine hochgeladene Datei.
* Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben. 
* Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.
* Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren. Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten. Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.

Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert. Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen. Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* .

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen. Der Grenzwert von 65.535 Dateien gilt pro Server. Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:
>
> * [GetTempFileNameA-Funktion](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank

Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden. Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.

Die `FileUpload` wird im Seiten Format verwendet Razor :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank. Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.
>
> Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht. Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.
>
> Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

### <a name="upload-large-files-with-streaming"></a>Hochladen von großen Dateien mittels Streaming

Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen. Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben. Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert. Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden. Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.

Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut). Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin). Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen. Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen. Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert. Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.

Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.

## <a name="validation"></a>Überprüfen

Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads. Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode. Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.

> [!WARNING]
> Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien. In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.
>
> Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:
>
> * Sie verstehen die Implementierung vollständig.
> * Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.
>
> **Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**

### <a name="content-validation"></a>Validierung von Inhalten

**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**

Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen. Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird. Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft. Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben. Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt. Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.

### <a name="file-extension-validation"></a>Validierung von Dateierweiterungen

Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden. Zum Beispiel:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validierung der Dateisignatur

Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt. Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht. Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen. Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.

### <a name="file-name-security"></a>Sicherheit von Dateinamen

Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher. Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.

Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige. Der folgende Code kann sicher verwendet werden:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.

Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben. Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.

### <a name="size-validation"></a>Validierung der Größe

Begrenzen Sie die Größe hochgeladener Dateien.

In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes). Der Grenzwert wird durch die [Konfiguration](xref:fundamentals/configuration/index) aus der *appsettings.json* Datei angegeben:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode

In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.

Siehe folgendes Beispiel:

* Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):

* Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Für eine MVC POST-Controlleraktionsmethode:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Server- und App-Konfiguration

### <a name="multipart-body-length-limit"></a>Grenzwert der Länge von mehrteiligem Text

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest. Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus. Der Standardwert ist 134.217.728 (128 MB). Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maximale Größe des Anforderungstexts für Kestrel

Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB. Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Kann auch mit der- [`@attribute`](xref:mvc/views/razor#attribute) Razor Direktive angewendet werden:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Andere Kestrel-Grenzwerte

Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:

* [Maximale Anzahl der Clientverbindungen](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Datenraten für Anforderungen und Antworten](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt. Passen Sie den Grenzwert in der `web.config` Datei an. Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Die `maxAllowedContentLength` Einstellung gilt nur für IIS. Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

## <a name="troubleshoot"></a>Problembehandlung

Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server

Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Weitere Informationen finden Sie im Abschnitt [IIS](#iis).

### <a name="connection-failure"></a>Verbindungsfehler

Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet. Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size). Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.

### <a name="null-reference-exception-with-iformfile"></a>Ausnahme bei möglichem NULL-Verweis mit IFormFile

Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt. Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`. Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.

### <a name="stream-was-too-long"></a>Stream war zu lang.

Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält. Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`. Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Sicherheitshinweise

Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen. Angreifer versuchen möglicherweise Folgendes:

* Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen
* Hochladen von Viren oder Schadsoftware
* Gefährden von Netzwerken und Servern auf andere Weise

Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:

* Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk. Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen. Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;
* Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;
* Wählen Sie einen sicheren von der App festgelegten Dateinamen. Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird. Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).
* Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.
* Überprüfen Sie die Größe einer hochgeladenen Datei. Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;
* Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.
* **Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**

&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.

> [!WARNING]
> Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:
>
> * Erlangen der vollständigen Kontrolle über ein System.
> * Überlasten eines Systems mit dem Ziel eines Systemausfalls.
> * Kompromittieren von Benutzer- oder Systemdaten
> * Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.
>
> Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:
>
> * [Unrestricted File Upload (Uneingeschränkter Dateiupload)](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).

## <a name="storage-scenarios"></a>Speicherszenarien

Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:

* Datenbank

  * Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).
  * Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.
  * Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.

* Physischer Speicher (Dateisystem oder Netzwerkfreigabe)

  * Für das Hochladen großer Dateien:
    * Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.
    * Physischer Speicher ist oft teurer als Datenbankspeicher.
  * Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.
  * Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben. **Erteilen Sie niemals die Ausführungsberechtigung.**

* Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.
  * Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.

  Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet).

## <a name="file-upload-scenarios"></a>Szenarien für das Hochladen von Dateien

Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.

**Pufferung**

Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.

Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig. Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist. Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.

> [!NOTE]
> Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.

Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:

* [Physischer Speicher](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Datenbank](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert. Streaming verbessert die Leistung nicht wesentlich. Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.

Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher

Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.

Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei ( *pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:

* Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.
* Keine Validierung erfolgt.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:

* Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Verwenden Sie `XMLHttpRequest`. Zum Beispiel:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.

Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden. Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.

<a name="filename"></a>

> [!WARNING]
> Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung. Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML. Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade. Anwendungen sollten folgende Aktionen ausführen:
>
> * den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen
> * den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern
> * einen neuen zufälligen Dateinamen für die Speicherung generieren
>
> Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:

* Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Eine der folgenden Sammlungen, die mehrere Dateien darstellen:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Zur Bindung werden Formulardateien anhand des Namens abgeglichen. So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`). Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).

Im Beispiel unten geschieht Folgendes:

* Durchläuft mindestens eine hochgeladene Datei.
* Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben. 
* Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.
* Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren. Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten. Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.

Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert. Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen. Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* .

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen. Der Grenzwert von 65.535 Dateien gilt pro Server. Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:
>
> * [GetTempFileNameA-Funktion](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank

Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden. Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.

Die `FileUpload` wird im Seiten Format verwendet Razor :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank. Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.
>
> Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht. Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.
>
> Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

### <a name="upload-large-files-with-streaming"></a>Hochladen von großen Dateien mittels Streaming

Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen. Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben. Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert. Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden. Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.

Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut). Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin). Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen. Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen. Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert. Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.

Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.

## <a name="validation"></a>Überprüfen

Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads. Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode. Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.

> [!WARNING]
> Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien. In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.
>
> Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:
>
> * Sie verstehen die Implementierung vollständig.
> * Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.
>
> **Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**

### <a name="content-validation"></a>Validierung von Inhalten

**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**

Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen. Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird. Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft. Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben. Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt. Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.

### <a name="file-extension-validation"></a>Validierung von Dateierweiterungen

Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden. Zum Beispiel:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validierung der Dateisignatur

Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt. Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht. Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen. Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.

### <a name="file-name-security"></a>Sicherheit von Dateinamen

Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher. Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.

Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige. Der folgende Code kann sicher verwendet werden:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.

Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben. Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.

### <a name="size-validation"></a>Validierung der Größe

Begrenzen Sie die Größe hochgeladener Dateien.

In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes). Der Grenzwert wird durch die [Konfiguration](xref:fundamentals/configuration/index) aus der *appsettings.json* Datei angegeben:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode

In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.

Siehe folgendes Beispiel:

* Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):

* Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Für eine MVC POST-Controlleraktionsmethode:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Server- und App-Konfiguration

### <a name="multipart-body-length-limit"></a>Grenzwert der Länge von mehrteiligem Text

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest. Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus. Der Standardwert ist 134.217.728 (128 MB). Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maximale Größe des Anforderungstexts für Kestrel

Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB. Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`Kann auch mit der- [`@attribute`](xref:mvc/views/razor#attribute) Razor Direktive angewendet werden:

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>Andere Kestrel-Grenzwerte

Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:

* [Maximale Anzahl der Clientverbindungen](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Datenraten für Anforderungen und Antworten](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt. Passen Sie den Grenzwert in der `web.config` Datei an. Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Die `maxAllowedContentLength` Einstellung gilt nur für IIS. Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Vergrößern Sie die maximale Anforderungs Text Größe für die HTTP-Anforderung, indem Sie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in festlegen `Startup.ConfigureServices` . Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Problembehandlung

Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server

Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Weitere Informationen finden Sie im Abschnitt [IIS](#iis).

### <a name="connection-failure"></a>Verbindungsfehler

Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet. Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size). Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.

### <a name="null-reference-exception-with-iformfile"></a>Ausnahme bei möglichem NULL-Verweis mit IFormFile

Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt. Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`. Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.

### <a name="stream-was-too-long"></a>Stream war zu lang.

Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält. Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`. Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core unterstützt das Hochladen einer oder mehrerer Dateien über die gepufferte Modellbindung für kleinere Dateien und ungepuffertes Streaming für größere Dateien.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>Sicherheitshinweise

Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen. Angreifer versuchen möglicherweise Folgendes:

* Ausführen von [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffen
* Hochladen von Viren oder Schadsoftware
* Gefährden von Netzwerken und Servern auf andere Weise

Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:

* Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien hoch, vorzugsweise auf ein Nicht-Systemlaufwerk. Ein dedizierter Speicherort erleichtert es, Sicherheitsbeschränkungen für hochgeladene Dateien zu erzwingen. Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;
* Speichern Sie hochgeladene Dateien **nicht** persistent in der Verzeichnisstruktur, in der sich auch die App befindet.&dagger;
* Wählen Sie einen sicheren von der App festgelegten Dateinamen. Verwenden Sie keinen Dateinamen, der vom Benutzer bereitgestellt wird, oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei. &dagger; Der HTML-Code codiert den nicht vertrauenswürdigen Dateinamen, wenn er angezeigt wird. Beispielsweise die Protokollierung des Datei namens oder die Anzeige auf der Benutzeroberfläche ( Razor Automatisches HTML-codieren der Ausgabe).
* Lassen Sie nur genehmigte Dateierweiterungen für die Entwurfsspezifikation der App zu.&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* Überprüfen Sie, ob Client seitige Überprüfungen auf dem Server ausgeführt werden. &dagger; Client seitige Überprüfungen können problemlos umgangen werden.
* Überprüfen Sie die Größe einer hochgeladenen Datei. Legen Sie einen Grenzwert für die maximale Größe fest, um große Uploads zu verhindern.&dagger;
* Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.
* **Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**

&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.

> [!WARNING]
> Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:
>
> * Erlangen der vollständigen Kontrolle über ein System.
> * Überlasten eines Systems mit dem Ziel eines Systemausfalls.
> * Kompromittieren von Benutzer- oder Systemdaten
> * Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.
>
> Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:
>
> * [Unrestricted File Upload (Uneingeschränkter Dateiupload)](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen gelten, wenn Dateien vom Benutzer akzeptiert werden](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Weitere Informationen zur Implementierung von Sicherheitsmaßnahmen, einschließlich Beispiele aus der Beispielanwendung, finden Sie im Abschnitt [Validierung](#validation).

## <a name="storage-scenarios"></a>Speicherszenarien

Zu den allgemeinen Speicheroptionen für Dateien gehören u. a.:

* Datenbank

  * Für das Hochladen kleiner Dateien ist eine Datenbank oft schneller als physische Speicheroptionen (Dateisystem oder Netzwerkfreigabe).
  * Eine Datenbank ist oft praktischer als physische Speicheroptionen, da das Abrufen eines Datenbank-Datensatzes für Benutzerdaten gleichzeitig den Dateiinhalt (z. B. ein Avatarbild) bereitstellen kann.
  * Eine Datenbank ist potenziell kostengünstiger als die Nutzung eines Datenspeicherdiensts.

* Physischer Speicher (Dateisystem oder Netzwerkfreigabe)

  * Für das Hochladen großer Dateien:
    * Für die Datenbank geltende Grenzwerte können die Größe des Uploads einschränken.
    * Physischer Speicher ist oft teurer als Datenbankspeicher.
  * Physischer Speicher ist potenziell teurer als die Nutzung eines Datenspeicherdiensts.
  * Der Prozess der App muss Lese- und Schreibberechtigungen für den Speicherort haben. **Erteilen Sie niemals die Ausführungsberechtigung.**

* Datenspeicherdienst (z. B. [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))

  * Dienste bieten in der Regel eine bessere Skalierbarkeit und Resilienz gegenüber lokalen Lösungen, die in der Regel Single Points of Failure aufweisen.
  * Dienste sind bei Szenarien mit großen Speicherinfrastrukturen potenziell kostengünstiger.

  Weitere Informationen finden Sie unter [Schnellstart: Verwenden von .net zum Erstellen eines BLOBs im Objektspeicher](/azure/storage/blobs/storage-quickstart-blobs-dotnet). Das Thema veranschaulicht <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, aber <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> kann verwendet werden, um einen <xref:System.IO.FileStream> in Blobspeicher zu speichern, wenn ein <xref:System.IO.Stream> verwendet wird.

## <a name="file-upload-scenarios"></a>Szenarien für das Hochladen von Dateien

Zwei allgemeine Ansätze für das Hochladen von Dateien sind Pufferung und Streaming.

**Pufferung**

Die gesamte Datei wird in eine <xref:Microsoft.AspNetCore.Http.IFormFile> eingelesen, die eine C#-Darstellung der Datei ist, die zum Verarbeiten oder Speichern der Datei verwendet wird.

Welche Ressourcen (Datenträger, Arbeitsspeicher) für das Hochladen von Dateien verwendet werden, ist von der Anzahl und Größe gleichzeitig hochgeladener Dateien abhängig. Wenn eine App versucht, zu viele Uploads zu puffern, stürzt die Website ab, sobald der Arbeitsspeicher oder Speicherplatz auf dem Datenträger ausgelastet ist. Wenn die Größe oder Häufigkeit von Dateiuploads die Ressourcen der App auslastet, wählen Sie Streaming.

> [!NOTE]
> Jede einzelne gepufferte Datei, die 64 KB überschreitet, wird aus dem Arbeitsspeicher in eine temporäre Datei auf dem Datenträger verschoben.

Die Pufferung kleiner Dateien wird in den folgenden Abschnitten dieses Themas behandelt:

* [Physischer Speicher](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Datenbank](#upload-small-files-with-buffered-model-binding-to-a-database)

**Streaming**

Die Datei wird über eine mehrteilige Anforderung empfangen und von der App direkt verarbeitet oder gespeichert. Streaming verbessert die Leistung nicht wesentlich. Streaming reduziert beim Hochladen von Dateien die Anforderungen an den Arbeitsspeicher oder Speicherplatz auf dem Datenträger.

Das Streamen großer Dateien wird im Abschnitt [Hochladen großer Dateien mit Streaming](#upload-large-files-with-streaming) beschrieben.

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in physischen Speicher

Zum Hochladen kleiner Dateien können Sie ein mehrteiliges Formular verwenden oder über JavaScript eine POST-Anforderung erstellen.

Das folgende Beispiel veranschaulicht die Verwendung eines Razor pages-Formulars zum Hochladen einer einzelnen Datei ( *pages/bufferedsinglefileuploadphysical. cshtml* in der Beispiel-APP):

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

Das folgende Beispiel ist analog zum vorherigen Beispiel, mit der Ausnahme, dass:

* Die ([Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) von JavaScript zum Senden der Formulardaten verwendet wird.
* Keine Validierung erfolgt.

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

Um den POST-Befehl für das Formular in JavaScript für Clients auszuführen, die [die Fetch-API nicht unterstützen](https://caniuse.com/#feat=fetch), wählen Sie einen der folgenden Ansätze:

* Verwenden Sie Fetch Polyfill (Beispiel: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).
* Verwenden Sie `XMLHttpRequest`. Zum Beispiel:

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

HTML-Formulare müssen den Codierungstyp (`enctype`) `multipart/form-data` angeben, damit Dateiuploads unterstützt werden.

Für ein Eingabeelement des Typs `files`, welches das Hochladen mehrerer Dateien unterstützt, geben Sie das Attribut `multiple` für das Element `<input>` an:

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

Auf die einzelnen Dateien, die auf den Server geladen werden, kann über eine [Modellbindung](xref:mvc/models/model-binding) mittels <xref:Microsoft.AspNetCore.Http.IFormFile>zugegriffen werden. Die Beispiel-App veranschaulicht mehrere gepufferte Dateiuploads für Szenarien mit Datenbank und physischem Speicher.

<a name="filename2"></a>

> [!WARNING]
> Verwenden Sie die Eigenschaft `FileName` von <xref:Microsoft.AspNetCore.Http.IFormFile>, **ausschließlich** für die Anzeige und Protokollierung. Codieren Sie den Dateinamen für die Anzeige und Protokollierung mit HTML. Ein Angreifer kann einen bösartigen Dateinamen bereitstellen, einschließlich vollständiger oder relativer Pfade. Anwendungen sollten folgende Aktionen ausführen:
>
> * den Pfad aus dem vom Benutzer angegebenen Dateinamen entfernen
> * den mit HTML codierten Dateinamen, aus dem der Pfad entfernt wurde, für die Benutzeroberfläche oder Protokollierung speichern
> * einen neuen zufälligen Dateinamen für die Speicherung generieren
>
> Mit dem folgenden Code wird der Pfad aus dem Dateinamen entfernt:
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> Bei den bisher vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

Beim Hochladen von Dateien mit Modellbindung und <xref:Microsoft.AspNetCore.Http.IFormFile> kann die Aktionsmethode Folgendes akzeptieren:

* Eine einzelne <xref:Microsoft.AspNetCore.Http.IFormFile>.
* Eine der folgenden Sammlungen, die mehrere Dateien darstellen:
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> Zur Bindung werden Formulardateien anhand des Namens abgeglichen. So muss beispielsweise der HTML-Wert `name` in `<input type="file" name="formFile">` mit der C#-Parameter-/Eigenschaftsbindung übereinstimmen (`FormFile`). Weitere Informationen finden Sie im Abschnitt [Abgleichen des Werts des Namensattributs mit dem Parameternamen in der POST-Methode](#match-name-attribute-value-to-parameter-name-of-post-method).

Im Beispiel unten geschieht Folgendes:

* Durchläuft mindestens eine hochgeladene Datei.
* Verwendet [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*), um einen vollständigen Pfad für eine Datei samt Dateinamen zurückzugeben. 
* Speichert die Dateien im lokalen Dateisystem mit einem von der App generierten Dateinamen.
* Gibt die Gesamtanzahl und Größe der hochgeladenen Dateien zurück.

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

Verwenden Sie `Path.GetRandomFileName`, um einen Dateinamen ohne Pfad zu generieren. Im folgenden Beispiel wird der Pfad aus der Konfiguration abgerufen:

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

Der an  übergebene Pfad <xref:System.IO.FileStream> *muss* den Dateinamen enthalten. Ist dies nicht der Fall, wird zur Laufzeit eine <xref:System.UnauthorizedAccessException> ausgelöst.

Dateien, die über die <xref:Microsoft.AspNetCore.Http.IFormFile>-Technik hochgeladen werden, werden vor der Verarbeitung im Arbeitsspeicher oder auf einem Datenträger des Servers gepuffert. Innerhalb der Aktionsmethode können Sie über einen <xref:System.IO.Stream> auf die <xref:Microsoft.AspNetCore.Http.IFormFile>-Inhalte zugreifen. Zusätzlich zum lokalen Dateisystem können Dateien in einer Netzwerkfreigabe oder einem Dateispeicherdienst gespeichert werden, wie beispielsweise [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).

Ein weiteres Beispiel, das mehrere hochzuladende Dateien in einer Schleife durchläuft und sichere Dateinamen verwendet, finden Sie in der Beispiel-App unter *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* .

> [!WARNING]
> [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) löst eine <xref:System.IO.IOException> aus, wenn mehr als 65.535 Dateien erstellt werden, ohne alte temporäre Dateien zu löschen. Der Grenzwert von 65.535 Dateien gilt pro Server. Weitere Informationen zu diesem Grenzwert für Windows-Betriebssysteme finden Sie in den Hinweisen in den folgenden Themen:
>
> * [GetTempFileNameA-Funktion](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>Hochladen kleiner Dateien mit gepufferten Modellbindungen in eine Datenbank

Zum Speichern von Binärdateidaten in einer Datenbank über das [Entity Framework](/ef/core/index) definieren Sie für die Entität eine Arrayeigenschaft des Typs <xref:System.Byte>:

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

Geben Sie eine Seitenmodelleigenschaft für die Klasse an, die eine <xref:Microsoft.AspNetCore.Http.IFormFile> enthält:

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> kann wie oben dargestellt direkt als Parameter einer Aktionsmethode oder als gebundene Modelleigenschaft verwendet werden. Im vorherigen Beispiel wird eine gebundene Modelleigenschaft verwendet.

Die `FileUpload` wird im Seiten Format verwendet Razor :

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

Wenn das Formular per POST an den Server gesendet wird, kopieren Sie die <xref:Microsoft.AspNetCore.Http.IFormFile> in einen Stream, und speichern Sie ihn als Bytearray in der Datenbank. Im folgenden Beispiel speichert `_dbContext` den Datenbankkontext der App:

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

Das vorherige Beispiel ähnelt einem Szenario, das in der Beispiel-App veranschaulicht wird:

* *Pages/BufferedSingleFileUploadDb.cshtml*
* *Pages/BufferedSingleFileUploadDb.cshtml.cs*

> [!WARNING]
> Speichern Sie Binärdaten in relationalen Datenbanken mit Bedacht, da sie Auswirkungen auf die Leistung haben können.
>
> Verlassen Sie sich nicht ohne Validierung auf die `FileName`-Eigenschaft <xref:Microsoft.AspNetCore.Http.IFormFile>, bzw. vertrauen Sie ihr nicht. Die `FileName`-Eigenschaft darf nur für Anzeigezwecke und erst nach der HTML-Codierung verwendet werden.
>
> Bei den vorgestellten Beispielen werden keine Sicherheitsaspekte berücksichtigt. Weitere Informationen finden Sie in den folgenden Abschnitten und in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):
>
> * [Überlegungen zur Sicherheit](#security-considerations)
> * [Überprüfung](#validation)

### <a name="upload-large-files-with-streaming"></a>Hochladen von großen Dateien mittels Streaming

Das folgende Beispiel zeigt, wie JavaScript verwendet wird, um eine Datei an eine Controlleraktion zu streamen. Das Fälschungssicherheitstoken einer Datei wird mithilfe eines benutzerdefinierten Filterattributs generiert und an die HTTP-Header des Clients anstelle des Anforderungstexts übergeben. Da die Aktionsmethode die hochgeladenen Daten direkt verarbeitet, wird die Modellbindung des Formulars von einem anderen benutzerdefinierten Filter deaktiviert. Innerhalb der Aktion werden die Inhalte des Formulars über `MultipartReader` gelesen. Dieses Element liest jede einzelne `MultipartSection`-Klasse, wodurch die Datei verarbeitet wird oder die Inhalte angemessen gespeichert werden. Nachdem alle mehrteiligen Abschnitte gelesen wurden, führt die Aktion ihre eigene Modellbindung aus.

Die erste Seiten Antwort lädt das Formular und speichert ein antifälschungstoken in einem cookie (über das- `GenerateAntiforgeryTokenCookieAttribute` Attribut). Das-Attribut verwendet die integrierte [antifälschungs Unterstützung](xref:security/anti-request-forgery) von ASP.net Core, um eine cookie mit einem Anforderungs Token festzulegen:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

Das `DisableFormValueModelBindingAttribute` wird zum Deaktivieren der Modellbindung verwendet:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

In der Beispiel-APP `GenerateAntiforgeryTokenCookieAttribute` `DisableFormValueModelBindingAttribute` werden und als Filter auf die Seiten Anwendungsmodelle von `/StreamedSingleFileUploadDb` und in der Verwendung von `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` [ Razor Seiten Konventionen](xref:razor-pages/razor-pages-conventions)angewendet:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

Da die Modellbindung das Formular nicht liest, werden Parameter, die über das Formular gebunden werden, nicht gebunden (Abfrage, Route und Header funktionieren weiterhin). Die Aktionsmethode arbeitet direkt mit der `Request`-Eigenschaft zusammen. Ein `MultipartReader` wird verwendet, um die verschiedenen Abschnitte zu lesen. Schlüssel-Wert-Daten werden in einem `KeyValueAccumulator` gespeichert. Nachdem die mehrteiligen Abschnitte gelesen wurden, werden die Inhalte von `KeyValueAccumulator` verwendet, um die Formulardaten an einen Modelltyp zu binden.

Die vollständige `StreamingController.UploadDatabase`-Methode für das Streaming an eine Datenbank mit EF Core:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

Die vollständige `StreamingController.UploadPhysical`-Methode für das Streaming an einen physischen Speicherort:

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

In der Beispiel-App werden Validierungsprüfungen von `FileHelpers.ProcessStreamedFile` übernommen.

## <a name="validation"></a>Überprüfen

Die `FileHelpers`-Klasse der Beispiel-App veranschaulicht eine Reihe von Prüfungen für gepufferte <xref:Microsoft.AspNetCore.Http.IFormFile>- und gestreamte Dateiuploads. Informationen zum Verarbeiten mit <xref:Microsoft.AspNetCore.Http.IFormFile> gepufferter Dateiuploads in der Beispiel-App finden Sie in der Datei *Utilities/FileHelpers.cs* in der `ProcessFormFile`-Methode. Informationen zum Verarbeiten gestreamter Dateien finden Sie in der `ProcessStreamedFile`-Methode in der gleichen Datei.

> [!WARNING]
> Die in der Beispiel-App demonstrierten Validierungsverarbeitungsmethoden untersuchen nicht den Inhalt hochgeladener Dateien. In den meisten Produktionsszenarien wird eine API zum Scannen auf Viren/Schadsoftware auf die Datei angewendet, bevor die Datei Benutzern oder anderen Systemen zur Verfügung gestellt wird.
>
> Obwohl das Themenbeispiel ein funktionierendes Beispiel für Validierungstechniken darstellt, implementieren Sie die `FileHelpers`-Klasse nur unter folgenden Voraussetzungen in einer Produktions-App:
>
> * Sie verstehen die Implementierung vollständig.
> * Sie ändern die Implementierung entsprechend der Umgebung und den Spezifikationen der App.
>
> **Implementieren Sie niemals willkürlich Sicherheitscode in einer App, wenn Sie diese Anforderungen nicht erfüllen.**

### <a name="content-validation"></a>Validierung von Inhalten

**Wenden Sie für hochgeladene Inhalte eine API zum Scannen auf Viren/Schadsoftware von Drittanbietern an.**

Das Scannen von Dateien stellt in Szenarien mit hohem Verarbeitungsvolumen hohe Anforderungen an die Serverressourcen. Wenn die Leistung bei der Verarbeitung von Anforderungen durch das Scannen von Dateien beeinträchtigt wird, erwägen Sie, die Scanaufgaben an einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) auszulagern, möglicherweise an einen Dienst, der auf einem anderen Server als dem mit der App ausgeführt wird. Üblicherweise werden hochgeladene Dateien in einem Quarantänebereich aufbewahrt, bis der Hintergrundvirenscanner sie prüft. Wenn eine Datei die Prüfung besteht, wird sie an den normalen Speicherort verschoben. Diese Schritte erfolgen in der Regel in Verbindung mit einem Datenbank-Datensatz, der den Scanstatus einer Datei angibt. Bei einem solchen Ansatz bleiben App und App-Server auf die Bearbeitung von Anforderungen ausgerichtet.

### <a name="file-extension-validation"></a>Validierung von Dateierweiterungen

Die Erweiterung der hochgeladenen Datei muss mit einer Liste zulässiger Erweiterungen abgeglichen werden. Zum Beispiel:

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>Validierung der Dateisignatur

Die Signatur einer Datei wird durch die ersten Bytes am Anfang einer Datei bestimmt. Diese Bytes können verwendet werden, um anzugeben, ob die Erweiterung dem Inhalt der Datei entspricht. Die Beispiel-App überprüft Dateisignaturen auf gängige Dateitypen. Im folgenden Beispiel wird die Dateisignatur eines JPEG-Bilds mit der Datei abgeglichen:

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

Informationen zu zusätzlichen Dateisignaturen finden Sie in der [Datenbank „File Signatures“](https://www.filesignatures.net/) und offiziellen Dateispezifikationen.

### <a name="file-name-security"></a>Sicherheit von Dateinamen

Verwenden Sie niemals einen vom Kunden angegebenen Dateinamen zum Speichern einer Datei in physischem Speicher. Erstellen Sie mithilfe von [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) oder [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) einen sicheren Dateinamen für die Datei, um einen vollständigen Pfad (einschließlich des Dateinamens) für die temporäre Speicherung zu erstellen.

Razor automatisch HTML-codiert Eigenschaftswerte für die Anzeige. Der folgende Code kann sicher verwendet werden:

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Außerhalb von Razor ist immer der <xref:System.Net.WebUtility.HtmlEncode*> Name Inhalt aus der Anforderung eines Benutzers.

Bei vielen Implementierungen muss geprüft werden, ob die Datei existiert. Andernfalls wird die Datei durch eine gleichnamige Datei überschrieben. Stellen Sie zusätzliche Logik bereit, um die Vorgaben Ihrer App zu erfüllen.

### <a name="size-validation"></a>Validierung der Größe

Begrenzen Sie die Größe hochgeladener Dateien.

In der Beispiel-App ist die Größe der Datei auf 2 MB begrenzt (angegeben in Bytes). Der Grenzwert wird durch die [Konfiguration](xref:fundamentals/configuration/index) aus der *appsettings.json* Datei angegeben:

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit` wird in `PageModel`-Klassen eingefügt:

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

Wenn eine Dateigröße den Grenzwert überschreitet, wird die Datei abgelehnt:

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>Vergleichen des Werts des Namensattributs mit dem Parameternamen der POST-Methode

In nicht- Razor Formularen, die Formulardaten veröffentlichen oder direkt JavaScript verwenden `FormData` , muss der im-Element des Formulars angegebene Name dem `FormData` Namen des Parameters in der Aktion des Controllers entsprechen.

Siehe folgendes Beispiel:

* Wenn ein `<input>`-Element verwendet wird, wird das `name`-Attribut auf den Wert `battlePlans` festgelegt:

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* Bei Verwendung von `FormData` in JavaScript wird der Name auf den Wert `battlePlans` festgelegt:

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

Verwenden Sie einen übereinstimmenden Namen für den Parameter der C#-Methode (`battlePlans`):

* Für eine Razor Seiten Handler-Methode mit dem Namen `Upload` :

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* Für eine MVC POST-Controlleraktionsmethode:

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>Server- und App-Konfiguration

### <a name="multipart-body-length-limit"></a>Grenzwert der Länge von mehrteiligem Text

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> legt den Grenzwert der Länge jedes mehrteiligen Texts fest. Formularabschnitte, die diesen Grenzwert überschreiten, lösen beim Analysieren eine <xref:System.IO.InvalidDataException> aus. Der Standardwert ist 134.217.728 (128 MB). Passen Sie den Grenzwert mithilfe der Einstellung <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> in `Startup.ConfigureServices` an:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> dient zum Festlegen des <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

RazorWenden Sie in einer Pages-APP oder einer MVC-App den Filter auf das Seiten Modell oder die Aktionsmethode an:

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Maximale Größe des Anforderungstexts für Kestrel

Die maximale Größe des Anforderungstexts beträgt für von Kestrel gehostete Apps standardmäßig 30.000.000 Bytes, also ungefähr 28,6 MB. Passen Sie den Grenzwert mit der Kestrel-Serveroption [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) an:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> dient zum Festlegen von [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) für eine einzelne Seite oder Aktion.

Wenden Sie in einer Razor pages-App den Filter mit einer [Konvention](xref:razor-pages/razor-pages-conventions) in an `Startup.ConfigureServices` :

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

RazorWenden Sie den Filter in einer Pages-APP oder einer MVC-App auf die Seitenhandlerklasse oder Aktionsmethode an:

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>Andere Kestrel-Grenzwerte

Andere Kestrel-Grenzwerte können für von Kestrel gehostete Apps gelten:

* [Maximale Anzahl der Clientverbindungen](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [Datenraten für Anforderungen und Antworten](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

Das Standard Anforderungs Limit ( `maxAllowedContentLength` ) beträgt 30 Millionen bytes, was etwa 28,6 MB beträgt. Passen Sie den Grenzwert in der `web.config` Datei an. Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

Die `maxAllowedContentLength` Einstellung gilt nur für IIS. Weitere Informationen finden Sie unter [Anforderungs Limits `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).

Vergrößern Sie die maximale Anforderungs Text Größe für die HTTP-Anforderung, indem Sie <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in festlegen `Startup.ConfigureServices` . Im folgenden Beispiel wird der Grenzwert auf 50 MB (52.428.800 Bytes) festgelegt:

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index#iis-options>.

## <a name="troubleshoot"></a>Problembehandlung

Nachfolgend werden einige häufig auftretenden Probleme aufgeführt, die entstehen können, wenn Dateien hochgeladen werden. Außerdem wird erläutert, wie Sie diese Probleme beheben können.

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>Fehler „Nicht gefunden“ bei Bereitstellung auf einem IIS-Server

Der folgende Fehler gibt an, dass die hochgeladene Datei die konfigurierte Inhaltslänge des Servers überschreitet:

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

Weitere Informationen finden Sie im Abschnitt [IIS](#iis).

### <a name="connection-failure"></a>Verbindungsfehler

Ein Verbindungsfehler und eine zurückgesetzte Serververbindung deuten wahrscheinlich darauf hin, dass die hochgeladene Datei die maximale Anforderungstextgröße von Kestrel überschreitet. Weitere Informationen finden Sie im Abschnitt [Maximale Größe des Anforderungstexts für Kestrel](#kestrel-maximum-request-body-size). Die Verbindungsgrenzwerte für Kestrel-Clients müssen ggf. ebenfalls angepasst werden.

### <a name="null-reference-exception-with-iformfile"></a>Ausnahme bei möglichem NULL-Verweis mit IFormFile

Wenn der Controller hochgeladene Dateien mit <xref:Microsoft.AspNetCore.Http.IFormFile> akzeptiert, der Wert aber `null` ist, bestätigen Sie, dass das HTML-Formular den `enctype`-Wert `multipart/form-data` angibt. Wenn dieses Attribut für das `<form>`-Element festgelegt ist, werden keine Dateien hochgeladen, und alle gebundenen <xref:Microsoft.AspNetCore.Http.IFormFile>-Argumente sind `null`. Bestätigen Sie auch, dass die [Uploadbenennung in den Formulardaten mit der Benennung der App](#match-name-attribute-value-to-parameter-name-of-post-method) übereinstimmt.

### <a name="stream-was-too-long"></a>Stream war zu lang.

Bei den Beispielen in diesem Thema wird davon ausgegangen, dass <xref:System.IO.MemoryStream> den Inhalt der hochgeladenen Datei enthält. Die maximale Größe für einen `MemoryStream` beträgt `int.MaxValue`. Wenn das Dateiuploadszenario der App das Speichern von Dateiinhalten mit einer Größe über 50 MB erfordert, verwenden Sie einen alternativen Ansatz, der nicht auf einem einzelnen `MemoryStream` zum Speichern des Inhalts einer hochgeladenen Datei basiert.

::: moniker-end


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [HTTP-Verbindungs Anforderungs Ableitung](xref:fundamentals/servers/kestrel#http11-request-draining)
* [Unrestricted File Upload (Uneingeschränkter Dateiupload)](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Azure-Sicherheit: Sicherheitsrahmen: Eingabevalidierung | Entschärfungen](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure Cloud Design Patterns: Valet-Schlüssel Muster](/azure/architecture/patterns/valet-key)
