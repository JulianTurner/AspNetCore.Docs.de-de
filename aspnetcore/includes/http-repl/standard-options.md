* `-F|--no-formatting`

  <span data-ttu-id="5eb1e-101">Ein Flag, das die Unterdrückung der HTTP-Antwortformatierung bewirkt.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="5eb1e-102">Legt einen HTTP-Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-102">Sets an HTTP request header.</span></span> <span data-ttu-id="5eb1e-103">Die folgenden zwei Werteformate werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="5eb1e-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="5eb1e-104">Gibt eine Datei an, in die der HTTP-Antworttext geschrieben werden soll.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="5eb1e-105">Beispiel: `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="5eb1e-106">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="5eb1e-107">Gibt eine Datei an, in die die HTTP-Antwortheader geschrieben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="5eb1e-108">Beispiel: `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="5eb1e-109">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="5eb1e-110">Ein Flag, das das Streaming der HTTP-Antwort aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb1e-110">A flag whose presence enables streaming of the HTTP response.</span></span>
