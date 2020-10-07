---
title: ASP.NET Core Blazor-Dateiuploads
author: guardrex
description: Erfahren Sie, wie Dateien in Blazor mit der inputFile-Komponente hochgeladen werden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606662"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor-Dateiuploads

Von [Daniel Roth](https://github.com/danroth27) und [Pranav Krishnamoorthy](https://github.com/pranavkm)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Verwenden Sie die `InputFile`-Komponente, um Browserdateidaten in .NET-Code zu lesen, auch für Dateiuploads.

> [!WARNING]
> Befolgen Sie stets die bewährten Sicherheitsmethoden für Dateiuploads. Weitere Informationen finden Sie unter <xref:mvc/models/file-uploads#security-considerations>.

## <a name="inputfile-component"></a>`InputFile`-Komponente

Die `InputFile`-Komponente wird als HTML-Eingabe vom Typ `file` gerendert.

Standardmäßig wählt der Benutzer einzelne Dateien aus. Fügen Sie das `multiple`-Attribut hinzu, um Benutzern das gleichzeitige Hochladen mehrerer Dateien zu ermöglichen. Wenn mindestens eine Datei vom Benutzer ausgewählt wird, löst die `InputFile`-Komponente ein `OnChange`-Ereignis aus und übergibt ein `InputFileChangeEventArgs`-Element, das Zugriff auf die ausgewählte Dateiliste und Details zu jeder Datei bereitstellt.

So lesen Sie Daten aus einer vom Benutzer ausgewählten Datei

* Rufen Sie in der Datei `OpenReadStream` auf, um aus dem zurückgegebenen Stream zu lesen. Weitere Informationen finden Sie im Abschnitt [Dateistreams](#file-streams).
* Verwenden Sie `ReadAsync`. Standardmäßig erlaubt `ReadAsync` nur das Lesen von Dateien, die kleiner als 524.288 KB (512 MB) sind. Dieser Grenzwert soll verhindern, dass Entwickler versehentlich große Dateien in den Arbeitsspeicher einlesen. Geben Sie eine angemessene Schätzung für die maximal erwartete Dateigröße an, wenn größere Dateien unterstützt werden müssen. Vermeiden Sie das Lesen des eingehenden Dateistreams direkt in den Arbeitsspeicher Kopieren Sie beispielsweise Dateibytes nicht in einen <xref:System.IO.MemoryStream>, und lesen Sie sie nicht als Bytearray. Diese Vorgänge können zu Leistungs- und Sicherheitsproblemen führen, insbesondere in Blazor Server. Kopieren Sie stattdessen Dateibytes in einen externen Speicher, wie z. B. ein Blob oder eine Datei auf dem Datenträger.

Eine Komponente, die eine Bilddatei empfängt, kann die `RequestImageFileAsync`-Hilfsmethode für die Datei aufrufen, um die Größe der Bilddaten in der JavaScript-Laufzeit des Browsers zu ändern, bevor das Bild in die App gestreamt wird.

Das folgende Beispiel veranschaulicht das Hochladen mehrerer Bilddateien in eine Komponente. `InputFileChangeEventArgs.GetMultipleFiles` ermöglicht das Lesen mehrerer Dateien. Geben Sie die maximale Anzahl von Dateien an, die Sie für den Lesevorgang erwarten, um zu verhindern, dass ein böswilliger Benutzer eine größere Anzahl von Dateien hochlädt als die App erwartet. `InputFileChangeEventArgs.File` ermöglicht das Lesen der ersten und einzigen Datei, wenn das Hochladen mehrerer Dateien nicht unterstützt wird.

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

`IBrowserFile` gibt Metadaten zurück, die [vom Browser als Eigenschaften verfügbar gemacht werden](https://developer.mozilla.org/docs/Web/API/File#Instance_properties). Diese Metadaten können für die vorbereitende Validierung nützlich sein. Beispiele hierzu finden Sie in den [Beispielkomponenten `FileUpload.razor` und `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).

## <a name="file-streams"></a>Dateistreams

In einer Blazor WebAssembly-App werden die Daten direkt in den .NET-Code im Browser gestreamt.

In einer Blazor Server-App werden Dateidaten über die SignalR-Verbindung in .NET-Code auf dem Server gestreamt, während die Datei aus dem Datenstrom gelesen wird. [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) ermöglicht das Konfigurieren von Dateiuploadeigenschaften für Blazor Server.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
