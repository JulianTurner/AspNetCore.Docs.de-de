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
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="6c70e-103">ASP.NET Core Blazor-Dateiuploads</span><span class="sxs-lookup"><span data-stu-id="6c70e-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="6c70e-104">Von [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6c70e-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="6c70e-105">Verwenden Sie die `InputFile`-Komponente, um Browserdateidaten in .NET-Code zu lesen, auch für Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="6c70e-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="6c70e-106">Die `InputFile`-Komponente wird als HTML-Eingabe vom Typ `file` gerendert.</span><span class="sxs-lookup"><span data-stu-id="6c70e-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="6c70e-107">Standardmäßig wählt der Benutzer einzelne Dateien aus.</span><span class="sxs-lookup"><span data-stu-id="6c70e-107">By default, the user selects single files.</span></span> <span data-ttu-id="6c70e-108">Fügen Sie das `multiple`-Attribut hinzu, um Benutzern das gleichzeitige Hochladen mehrerer Dateien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="6c70e-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="6c70e-109">Wenn mindestens eine Datei vom Benutzer ausgewählt wird, löst die `InputFile`-Komponente ein `OnChange`-Ereignis aus und übergibt ein `InputFileChangeEventArgs`-Element, das Zugriff auf die ausgewählte Dateiliste und Details zu jeder Datei bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="6c70e-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="6c70e-110">Eine Komponente, die eine Bilddatei empfängt, kann die `RequestImageFileAsync`-Hilfsmethode für die Datei aufrufen, um die Größe der Bilddaten in der JavaScript-Laufzeit des Browsers zu ändern, bevor das Bild in die App gestreamt wird.</span><span class="sxs-lookup"><span data-stu-id="6c70e-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="6c70e-111">Das folgende Beispiel veranschaulicht das Hochladen mehrerer Bilddateien in einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="6c70e-111">The following example demonstrates multiple image file upload in a component:</span></span>

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

<span data-ttu-id="6c70e-112">Um Daten aus einer vom Benutzer ausgewählten Datei zu lesen, rufen Sie `OpenReadStream` für die Datei auf und lesen aus dem zurückgegebenen Stream.</span><span class="sxs-lookup"><span data-stu-id="6c70e-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="6c70e-113">In einer Blazor WebAssembly-App werden die Daten direkt in den .NET-Code im Browser gestreamt.</span><span class="sxs-lookup"><span data-stu-id="6c70e-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="6c70e-114">In einer Blazor Server-App werden Dateidaten in .NET-Code auf dem Server gestreamt, wenn die Datei aus dem Datenstrom gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="6c70e-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
