---
title: ASP.NET Core Blazor-Dateiuploads
author: guardrex
description: Erfahren Sie, wie Dateien in Blazor mit der inputFile-Komponente hochgeladen werden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722936"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core Blazor-Dateiuploads

Von [Daniel Roth](https://github.com/danroth27)

Verwenden Sie die `InputFile`-Komponente, um Browserdateidaten in .NET-Code zu lesen, auch für Dateiuploads. Die `InputFile`-Komponente wird als HTML-Eingabe vom Typ `file` gerendert.

Standardmäßig wählt der Benutzer einzelne Dateien aus. Fügen Sie das `multiple`-Attribut hinzu, um Benutzern das gleichzeitige Hochladen mehrerer Dateien zu ermöglichen. Wenn mindestens eine Datei vom Benutzer ausgewählt wird, löst die `InputFile`-Komponente ein `OnChange`-Ereignis aus und übergibt ein `InputFileChangeEventArgs`-Element, das Zugriff auf die ausgewählte Dateiliste und Details zu jeder Datei bereitstellt.

Eine Komponente, die eine Bilddatei empfängt, kann die `RequestImageFileAsync`-Hilfsmethode für die Datei aufrufen, um die Größe der Bilddaten in der JavaScript-Laufzeit des Browsers zu ändern, bevor das Bild in die App gestreamt wird.

Das folgende Beispiel veranschaulicht das Hochladen mehrerer Bilddateien in einer Komponente:

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

Um Daten aus einer vom Benutzer ausgewählten Datei zu lesen, rufen Sie `OpenReadStream` für die Datei auf und lesen aus dem zurückgegebenen Stream. In einer Blazor WebAssembly-App werden die Daten direkt in den .NET-Code im Browser gestreamt. In einer Blazor Server-App werden Dateidaten in .NET-Code auf dem Server gestreamt, wenn die Datei aus dem Datenstrom gelesen wird. 
