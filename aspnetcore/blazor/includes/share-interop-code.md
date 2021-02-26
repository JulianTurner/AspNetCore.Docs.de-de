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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552854"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="0f2d4-101">Freigeben von Interop-Code in einer Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="0f2d4-101">Share interop code in a class library</span></span>

<span data-ttu-id="0f2d4-102">JavaScript-Interop-Code kann in einer Klassenbibliothek eingeschlossen werden, wodurch Sie den Code in einem NuGet-Paket freigeben können.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="0f2d4-103">Die Klassenbibliothek behandelt das Einbetten von JavaScript-Ressourcen in der erstellten Assembly.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="0f2d4-104">Die JavaScript-Dateien werden im Ordner `wwwroot` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="0f2d4-105">Die Tools kümmern sich um das Einbetten der Ressourcen, wenn die Bibliothek erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="0f2d4-106">In der Projektdatei der App wird so auf das NuGet-Paket verwiesen, wie auf jedes andere NuGet-Paket verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="0f2d4-107">Nachdem das Paket wiederhergestellt wurde, kann App-Code einen Aufruf in JavaScript wie auch unter C# durchführen.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="0f2d4-108">Weitere Informationen finden Sie unter <xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="0f2d4-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
