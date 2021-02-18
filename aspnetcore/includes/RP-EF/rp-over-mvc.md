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
ms.openlocfilehash: 6808c71b1ca43755eea4958ff9409f40e8685694
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551746"
---
<span data-ttu-id="2b680-101">In diesem Tutorial erfahren Sie die Grundlagen zu ASP.NET Core MVS und Entity Framework Core mit Controllern und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="2b680-101">This tutorial teaches ASP.NET Core MVC and Entity Framework Core with controllers and views.</span></span> <span data-ttu-id="2b680-102">[Razor Pages](xref:razor-pages/index) ist ein alternatives Programmiermodell.</span><span class="sxs-lookup"><span data-stu-id="2b680-102">[Razor Pages](xref:razor-pages/index) is an alternative programming model.</span></span> <span data-ttu-id="2b680-103">Für neue Entwicklungsaufgaben wird empfohlen, Razor Pages mit MVC mit Controllern und Ansichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2b680-103">For new development, we recommend Razor Pages over MVC with controllers and views.</span></span> <span data-ttu-id="2b680-104">Informationen finden Sie in der [Razor Pages](xref:data/ef-rp/intro)-Version dieses Tutorials.</span><span class="sxs-lookup"><span data-stu-id="2b680-104">See the [Razor Pages](xref:data/ef-rp/intro) version of this tutorial.</span></span> <span data-ttu-id="2b680-105">In jedem Tutorial werden einige Themen behandelt, die in den anderen Tutorials nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="2b680-105">Each tutorial covers some material the other doesn't:</span></span>

<span data-ttu-id="2b680-106">Einige Aspekte, die in diesem MVC-Tutorial behandelt werden, nicht aber im Razor Pages-Tutorial:</span><span class="sxs-lookup"><span data-stu-id="2b680-106">Some things this MVC tutorial has that the Razor Pages tutorial doesn't:</span></span>

* <span data-ttu-id="2b680-107">Implementieren von Vererbung im Datenmodell</span><span class="sxs-lookup"><span data-stu-id="2b680-107">Implement inheritance in the data model</span></span>
* <span data-ttu-id="2b680-108">Durchführen unformatierter SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="2b680-108">Perform raw SQL queries</span></span>
* <span data-ttu-id="2b680-109">Verwenden dynamischer LINQs zum Vereinfachen des Codes</span><span class="sxs-lookup"><span data-stu-id="2b680-109">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="2b680-110">Einige Aspekte, die im Razor Pages-Tutorial behandelt werden, nicht aber in diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="2b680-110">Some things the Razor Pages tutorial has that this one doesn't:</span></span>

* <span data-ttu-id="2b680-111">Verwenden der Select-Methode zum Laden zugehöriger Daten</span><span class="sxs-lookup"><span data-stu-id="2b680-111">Use Select method to load related data</span></span>
* <span data-ttu-id="2b680-112">Best Practices für EF</span><span class="sxs-lookup"><span data-stu-id="2b680-112">Best practices for EF.</span></span>