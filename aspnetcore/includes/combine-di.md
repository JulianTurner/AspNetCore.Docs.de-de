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
ms.openlocfilehash: 08d212b48c3f97531bea34b11d5b8c9a9069ae34
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536292"
---
<a name="csc"></a>

<span data-ttu-id="11476-101">Mit der folgenden `ConfigureServices`-Methode können Sie Dienste registrieren und Optionen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="11476-101">Consider the following `ConfigureServices` method, which registers services and configures options:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

<span data-ttu-id="11476-102">Ähnliche Registrierungsgruppen können in eine Erweiterungsmethode verschoben werden, um Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="11476-102">Related groups of registrations can be moved to an extension method to register services.</span></span> <span data-ttu-id="11476-103">Die Konfigurationsdienste werden beispielsweise folgender Klasse hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="11476-103">For example, the configuration services are added to the following class:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

<span data-ttu-id="11476-104">Die verbleibenden Dienste werden in einer ähnlichen Klasse registriert.</span><span class="sxs-lookup"><span data-stu-id="11476-104">The remaining services are registered in a similar class.</span></span> <span data-ttu-id="11476-105">Die folgende `ConfigureServices`-Methode verwendet die neuen Erweiterungsmethoden, um die Dienste zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="11476-105">The following `ConfigureServices` method uses the new extension methods to register the services:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

<span data-ttu-id="11476-106">**_Hinweis:_** Jede `services.Add{GROUP_NAME}`-Erweiterungsmethode fügt Dienste hinzu und konfiguriert diese möglicherweise.</span><span class="sxs-lookup"><span data-stu-id="11476-106">**_Note:_** Each `services.Add{GROUP_NAME}` extension method adds and potentially configures services.</span></span> <span data-ttu-id="11476-107">Beispielsweise fügt <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> den MVC-Controller für Dienste mit den erforderlichen Ansichten hinzu, und <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> fügt die für Razor Pages benötigten Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="11476-107">For example, <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> adds the services MVC controllers with views require, and <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> adds the services Razor Pages requires.</span></span> <span data-ttu-id="11476-108">Es wird empfohlen, dass Apps dieser Namenskonvention folgen.</span><span class="sxs-lookup"><span data-stu-id="11476-108">We recommended that apps follow this naming convention.</span></span> <span data-ttu-id="11476-109">Platzieren Sie Erweiterungsmethoden im Namespace <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName>, um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="11476-109">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span>
