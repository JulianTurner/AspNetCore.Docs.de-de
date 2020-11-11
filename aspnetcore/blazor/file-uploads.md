---
title: ASP.NET Core :::no-loc(Blazor):::-Dateiuploads
author: guardrex
description: 'Erfahren Sie, wie Dateien in :::no-loc(Blazor)::: mit der inputFile-Komponente hochgeladen werden.'
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: c0806c3a68a4d9e698925f6ec955dd2f53d7818f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056126"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="d463f-103">ASP.NET Core :::no-loc(Blazor):::-Dateiuploads</span><span class="sxs-lookup"><span data-stu-id="d463f-103">ASP.NET Core :::no-loc(Blazor)::: file uploads</span></span>

<span data-ttu-id="d463f-104">Von [Daniel Roth](https://github.com/danroth27) und [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="d463f-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="d463f-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d463f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d463f-106">Verwenden Sie die `InputFile`-Komponente, um Browserdateidaten in .NET-Code zu lesen, auch für Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="d463f-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="d463f-107">Befolgen Sie stets die bewährten Sicherheitsmethoden für Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="d463f-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="d463f-108">Weitere Informationen finden Sie unter <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="d463f-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="d463f-109">`InputFile`-Komponente</span><span class="sxs-lookup"><span data-stu-id="d463f-109">`InputFile` component</span></span>

<span data-ttu-id="d463f-110">Die `InputFile`-Komponente wird als HTML-Eingabe vom Typ `file` gerendert.</span><span class="sxs-lookup"><span data-stu-id="d463f-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="d463f-111">Standardmäßig wählt der Benutzer einzelne Dateien aus.</span><span class="sxs-lookup"><span data-stu-id="d463f-111">By default, the user selects single files.</span></span> <span data-ttu-id="d463f-112">Fügen Sie das `multiple`-Attribut hinzu, um Benutzern das gleichzeitige Hochladen mehrerer Dateien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d463f-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="d463f-113">Wenn mindestens eine Datei vom Benutzer ausgewählt wird, löst die `InputFile`-Komponente ein `OnChange`-Ereignis aus und übergibt ein `InputFileChangeEventArgs`-Element, das Zugriff auf die ausgewählte Dateiliste und Details zu jeder Datei bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d463f-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="d463f-114">So lesen Sie Daten aus einer vom Benutzer ausgewählten Datei</span><span class="sxs-lookup"><span data-stu-id="d463f-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="d463f-115">Rufen Sie in der Datei `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` auf, um aus dem zurückgegebenen Stream zu lesen.</span><span class="sxs-lookup"><span data-stu-id="d463f-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="d463f-116">Weitere Informationen finden Sie im Abschnitt [Dateistreams](#file-streams).</span><span class="sxs-lookup"><span data-stu-id="d463f-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="d463f-117">Der von `OpenReadStream` zurückgegebene <xref:System.IO.Stream> erzwingt für den `Stream`, der aktuell gelesen wird, eine maximale Größe in Byte.</span><span class="sxs-lookup"><span data-stu-id="d463f-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="d463f-118">Standardmäßig dürfen nur Dateien gelesen werden, die kleiner als 524.288 KB (512 KB) sind, bevor weitere Lesevorgänge zu einer Ausnahme führen.</span><span class="sxs-lookup"><span data-stu-id="d463f-118">By default, only files smaller than 524,288 KB (512 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="d463f-119">Dieser Grenzwert soll verhindern, dass Entwickler versehentlich große Dateien in den Arbeitsspeicher einlesen.</span><span class="sxs-lookup"><span data-stu-id="d463f-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="d463f-120">Mit dem Parameter `maxAllowedSize` von `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` kann bei Bedarf eine größere Größe angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="d463f-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="d463f-121">Vermeiden Sie das Lesen des eingehenden Dateistreams direkt in den Arbeitsspeicher</span><span class="sxs-lookup"><span data-stu-id="d463f-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="d463f-122">Kopieren Sie beispielsweise Dateibytes nicht in einen <xref:System.IO.MemoryStream>, und lesen Sie sie nicht als Bytearray.</span><span class="sxs-lookup"><span data-stu-id="d463f-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="d463f-123">Diese Vorgänge können zu Leistungs- und Sicherheitsproblemen führen, insbesondere in :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="d463f-123">These approaches can result in performance and security problems, especially in :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="d463f-124">Kopieren Sie stattdessen Dateibytes in einen externen Speicher, wie z. B. ein Blob oder eine Datei auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="d463f-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="d463f-125">Eine Komponente, die eine Bilddatei empfängt, kann die `RequestImageFileAsync`-Hilfsmethode für die Datei aufrufen, um die Größe der Bilddaten in der JavaScript-Laufzeit des Browsers zu ändern, bevor das Bild in die App gestreamt wird.</span><span class="sxs-lookup"><span data-stu-id="d463f-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="d463f-126">Das folgende Beispiel veranschaulicht das Hochladen mehrerer Bilddateien in eine Komponente.</span><span class="sxs-lookup"><span data-stu-id="d463f-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="d463f-127">`InputFileChangeEventArgs.GetMultipleFiles` ermöglicht das Lesen mehrerer Dateien.</span><span class="sxs-lookup"><span data-stu-id="d463f-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="d463f-128">Geben Sie die maximale Anzahl von Dateien an, die Sie für den Lesevorgang erwarten, um zu verhindern, dass ein böswilliger Benutzer eine größere Anzahl von Dateien hochlädt als die App erwartet.</span><span class="sxs-lookup"><span data-stu-id="d463f-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="d463f-129">`InputFileChangeEventArgs.File` ermöglicht das Lesen der ersten und einzigen Datei, wenn das Hochladen mehrerer Dateien nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="d463f-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="d463f-130">`IBrowserFile` gibt Metadaten zurück, die [vom Browser als Eigenschaften verfügbar gemacht werden](https://developer.mozilla.org/docs/Web/API/File#Instance_properties).</span><span class="sxs-lookup"><span data-stu-id="d463f-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="d463f-131">Diese Metadaten können für die vorbereitende Validierung nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="d463f-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="d463f-132">Beispiele hierzu finden Sie in den [Beispielkomponenten `FileUpload.razor` und `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="d463f-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="d463f-133">Dateistreams</span><span class="sxs-lookup"><span data-stu-id="d463f-133">File streams</span></span>

<span data-ttu-id="d463f-134">In einer :::no-loc(Blazor WebAssembly):::-App werden die Daten direkt in den .NET-Code im Browser gestreamt.</span><span class="sxs-lookup"><span data-stu-id="d463f-134">In a :::no-loc(Blazor WebAssembly)::: app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="d463f-135">In einer :::no-loc(Blazor Server):::-App werden Dateidaten über die :::no-loc(SignalR):::-Verbindung in .NET-Code auf dem Server gestreamt, während die Datei aus dem Datenstrom gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="d463f-135">In a :::no-loc(Blazor Server)::: app, the file data is streamed over the :::no-loc(SignalR)::: connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="d463f-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) ermöglicht das Konfigurieren von Dateiuploadeigenschaften für :::no-loc(Blazor Server):::.</span><span class="sxs-lookup"><span data-stu-id="d463f-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for :::no-loc(Blazor Server):::.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d463f-137">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d463f-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
