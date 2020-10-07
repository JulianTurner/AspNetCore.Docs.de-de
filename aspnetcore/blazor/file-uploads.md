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
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="db9a6-103">ASP.NET Core Blazor-Dateiuploads</span><span class="sxs-lookup"><span data-stu-id="db9a6-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="db9a6-104">Von [Daniel Roth](https://github.com/danroth27) und [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="db9a6-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="db9a6-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db9a6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="db9a6-106">Verwenden Sie die `InputFile`-Komponente, um Browserdateidaten in .NET-Code zu lesen, auch für Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="db9a6-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="db9a6-107">Befolgen Sie stets die bewährten Sicherheitsmethoden für Dateiuploads.</span><span class="sxs-lookup"><span data-stu-id="db9a6-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="db9a6-108">Weitere Informationen finden Sie unter <xref:mvc/models/file-uploads#security-considerations>.</span><span class="sxs-lookup"><span data-stu-id="db9a6-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="db9a6-109">`InputFile`-Komponente</span><span class="sxs-lookup"><span data-stu-id="db9a6-109">`InputFile` component</span></span>

<span data-ttu-id="db9a6-110">Die `InputFile`-Komponente wird als HTML-Eingabe vom Typ `file` gerendert.</span><span class="sxs-lookup"><span data-stu-id="db9a6-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="db9a6-111">Standardmäßig wählt der Benutzer einzelne Dateien aus.</span><span class="sxs-lookup"><span data-stu-id="db9a6-111">By default, the user selects single files.</span></span> <span data-ttu-id="db9a6-112">Fügen Sie das `multiple`-Attribut hinzu, um Benutzern das gleichzeitige Hochladen mehrerer Dateien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="db9a6-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="db9a6-113">Wenn mindestens eine Datei vom Benutzer ausgewählt wird, löst die `InputFile`-Komponente ein `OnChange`-Ereignis aus und übergibt ein `InputFileChangeEventArgs`-Element, das Zugriff auf die ausgewählte Dateiliste und Details zu jeder Datei bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="db9a6-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="db9a6-114">So lesen Sie Daten aus einer vom Benutzer ausgewählten Datei</span><span class="sxs-lookup"><span data-stu-id="db9a6-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="db9a6-115">Rufen Sie in der Datei `OpenReadStream` auf, um aus dem zurückgegebenen Stream zu lesen.</span><span class="sxs-lookup"><span data-stu-id="db9a6-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="db9a6-116">Weitere Informationen finden Sie im Abschnitt [Dateistreams](#file-streams).</span><span class="sxs-lookup"><span data-stu-id="db9a6-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="db9a6-117">Verwenden Sie `ReadAsync`.</span><span class="sxs-lookup"><span data-stu-id="db9a6-117">Use `ReadAsync`.</span></span> <span data-ttu-id="db9a6-118">Standardmäßig erlaubt `ReadAsync` nur das Lesen von Dateien, die kleiner als 524.288 KB (512 MB) sind.</span><span class="sxs-lookup"><span data-stu-id="db9a6-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="db9a6-119">Dieser Grenzwert soll verhindern, dass Entwickler versehentlich große Dateien in den Arbeitsspeicher einlesen.</span><span class="sxs-lookup"><span data-stu-id="db9a6-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="db9a6-120">Geben Sie eine angemessene Schätzung für die maximal erwartete Dateigröße an, wenn größere Dateien unterstützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="db9a6-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="db9a6-121">Vermeiden Sie das Lesen des eingehenden Dateistreams direkt in den Arbeitsspeicher</span><span class="sxs-lookup"><span data-stu-id="db9a6-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="db9a6-122">Kopieren Sie beispielsweise Dateibytes nicht in einen <xref:System.IO.MemoryStream>, und lesen Sie sie nicht als Bytearray.</span><span class="sxs-lookup"><span data-stu-id="db9a6-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="db9a6-123">Diese Vorgänge können zu Leistungs- und Sicherheitsproblemen führen, insbesondere in Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="db9a6-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="db9a6-124">Kopieren Sie stattdessen Dateibytes in einen externen Speicher, wie z. B. ein Blob oder eine Datei auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="db9a6-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="db9a6-125">Eine Komponente, die eine Bilddatei empfängt, kann die `RequestImageFileAsync`-Hilfsmethode für die Datei aufrufen, um die Größe der Bilddaten in der JavaScript-Laufzeit des Browsers zu ändern, bevor das Bild in die App gestreamt wird.</span><span class="sxs-lookup"><span data-stu-id="db9a6-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="db9a6-126">Das folgende Beispiel veranschaulicht das Hochladen mehrerer Bilddateien in eine Komponente.</span><span class="sxs-lookup"><span data-stu-id="db9a6-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="db9a6-127">`InputFileChangeEventArgs.GetMultipleFiles` ermöglicht das Lesen mehrerer Dateien.</span><span class="sxs-lookup"><span data-stu-id="db9a6-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="db9a6-128">Geben Sie die maximale Anzahl von Dateien an, die Sie für den Lesevorgang erwarten, um zu verhindern, dass ein böswilliger Benutzer eine größere Anzahl von Dateien hochlädt als die App erwartet.</span><span class="sxs-lookup"><span data-stu-id="db9a6-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="db9a6-129">`InputFileChangeEventArgs.File` ermöglicht das Lesen der ersten und einzigen Datei, wenn das Hochladen mehrerer Dateien nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="db9a6-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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

<span data-ttu-id="db9a6-130">`IBrowserFile` gibt Metadaten zurück, die [vom Browser als Eigenschaften verfügbar gemacht werden](https://developer.mozilla.org/docs/Web/API/File#Instance_properties).</span><span class="sxs-lookup"><span data-stu-id="db9a6-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="db9a6-131">Diese Metadaten können für die vorbereitende Validierung nützlich sein.</span><span class="sxs-lookup"><span data-stu-id="db9a6-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="db9a6-132">Beispiele hierzu finden Sie in den [Beispielkomponenten `FileUpload.razor` und `FilePreview.razor`](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span><span class="sxs-lookup"><span data-stu-id="db9a6-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="db9a6-133">Dateistreams</span><span class="sxs-lookup"><span data-stu-id="db9a6-133">File streams</span></span>

<span data-ttu-id="db9a6-134">In einer Blazor WebAssembly-App werden die Daten direkt in den .NET-Code im Browser gestreamt.</span><span class="sxs-lookup"><span data-stu-id="db9a6-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="db9a6-135">In einer Blazor Server-App werden Dateidaten über die SignalR-Verbindung in .NET-Code auf dem Server gestreamt, während die Datei aus dem Datenstrom gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="db9a6-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="db9a6-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) ermöglicht das Konfigurieren von Dateiuploadeigenschaften für Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="db9a6-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db9a6-137">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="db9a6-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
