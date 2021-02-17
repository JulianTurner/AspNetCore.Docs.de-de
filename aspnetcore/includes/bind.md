---
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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536293"
---
<span data-ttu-id="50840-101">Die bevorzugte Methode für das Lesen zugehöriger Konfigurationswerte ist die Verwendung des [Optionsmusters](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="50840-101">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="50840-102">Um z. B. die folgenden Konfigurationswerte zu lesen:</span><span class="sxs-lookup"><span data-stu-id="50840-102">For example, to read the following configuration values:</span></span>

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

<span data-ttu-id="50840-103">Erstellen Sie die folgende neue `PositionOptions`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="50840-103">Create the following `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

<span data-ttu-id="50840-104">Eine Optionsklasse:</span><span class="sxs-lookup"><span data-stu-id="50840-104">An options class:</span></span>

* <span data-ttu-id="50840-105">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="50840-105">Must be non-abstract with a public parameterless constructor.</span></span>
* <span data-ttu-id="50840-106">Alle öffentlichen Lese-/Schreibeigenschaften des Typs sind gebunden.</span><span class="sxs-lookup"><span data-stu-id="50840-106">All public read-write properties of the type are bound.</span></span>
* <span data-ttu-id="50840-107">Felder werden ***nicht*** gebunden.</span><span class="sxs-lookup"><span data-stu-id="50840-107">Fields are ***not*** bound.</span></span> <span data-ttu-id="50840-108">Im vorangehenden Code ist `Position` nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="50840-108">In the preceding code, `Position` is not bound.</span></span> <span data-ttu-id="50840-109">Die `Position`-Eigenschaft wird verwendet, sodass die Zeichenfolge `"Position"` nicht in der App hartcodiert werden muss, wenn die Klasse an einen Konfigurationsanbieter gebunden wird.</span><span class="sxs-lookup"><span data-stu-id="50840-109">The `Position` property is used so the string `"Position"` doesn't need to be hard coded in the app when binding the class to a configuration provider.</span></span>

<span data-ttu-id="50840-110">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="50840-110">The following code:</span></span>

* <span data-ttu-id="50840-111">Ruft [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) auf, um die `PositionOptions`-Klasse an den `Position`-Abschnitt zu binden.</span><span class="sxs-lookup"><span data-stu-id="50840-111">Calls [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) to bind the `PositionOptions` class to the `Position` section.</span></span>
* <span data-ttu-id="50840-112">Zeigt die `Position`-Konfigurationsdaten an.</span><span class="sxs-lookup"><span data-stu-id="50840-112">Displays the `Position` configuration data.</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

<span data-ttu-id="50840-113">Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="50840-113">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="50840-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="50840-114">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="50840-115">`ConfigurationBinder.Get<T>` ist möglicherweise praktischer als die Verwendung von `ConfigurationBinder.Bind`.</span><span class="sxs-lookup"><span data-stu-id="50840-115">`ConfigurationBinder.Get<T>` may be more convenient than using `ConfigurationBinder.Bind`.</span></span> <span data-ttu-id="50840-116">Der folgende Code zeigt die Verwendung von `ConfigurationBinder.Get<T>` mit der `PositionOptions`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="50840-116">The following code shows how to use `ConfigurationBinder.Get<T>` with the `PositionOptions` class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

<span data-ttu-id="50840-117">Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="50840-117">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<span data-ttu-id="50840-118">Eine alternative Vorgehensweise bei der Verwendung des **Optionsmusters** besteht darin, den `Position`-Abschnitt zu binden und ihn zum [Dependency-Injection-Dienstcontainer](xref:fundamentals/dependency-injection) hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="50840-118">An alternative approach when using the \***options pattern** _ is to bind the `Position` section and add it to the [dependency injection service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="50840-119">Im folgenden Code wird `PositionOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="50840-119">In the following code, `PositionOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

<span data-ttu-id="50840-120">Mithilfe des vorangehenden Codes liest der folgende Code die Positionsoptionen:</span><span class="sxs-lookup"><span data-stu-id="50840-120">Using the preceding code, the following code reads the position options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="50840-121">Im vorangehenden Code werden Änderungen an der JSON-Konfigurationsdatei nach dem Start der App ***nicht*** gelesen.</span><span class="sxs-lookup"><span data-stu-id="50840-121">In the preceding code, changes to the JSON configuration file after the app has started are ***not*** read.</span></span> <span data-ttu-id="50840-122">Verwenden Sie [IOptionsSnapshot](xref:fundamentals/configuration/options#ios), um Änderungen lesen zu können, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="50840-122">To read changes after the app has started, use [IOptionsSnapshot](xref:fundamentals/configuration/options#ios).</span></span>
