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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551767"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="d5ba5-101">Option</span><span class="sxs-lookup"><span data-stu-id="d5ba5-101">Option</span></span>               | <span data-ttu-id="d5ba5-102">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d5ba5-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="d5ba5-103">--model oder -m</span><span class="sxs-lookup"><span data-stu-id="d5ba5-103">--model or -m</span></span>  | <span data-ttu-id="d5ba5-104">Die Modellklasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-104">Model class to use.</span></span> |
| <span data-ttu-id="d5ba5-105">--dataContext oder -dc</span><span class="sxs-lookup"><span data-stu-id="d5ba5-105">--dataContext or -dc</span></span>  | <span data-ttu-id="d5ba5-106">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="d5ba5-107">--bootstrapVersion oder -b</span><span class="sxs-lookup"><span data-stu-id="d5ba5-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="d5ba5-108">Gibt die Bootstrap-Version an.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="d5ba5-109">Gültige Werte sind `3` und `4`.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="d5ba5-110">Der Standardwert ist `4`.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-110">Default is `4`.</span></span> <span data-ttu-id="d5ba5-111">Falls benötigt und noch nicht vorhanden, wird ein *wwwroot*-Verzeichnis erstellt, das die Bootstrap-Dateien der angegebenen Version enthält.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="d5ba5-112">--referenceScriptLibraries oder -scripts</span><span class="sxs-lookup"><span data-stu-id="d5ba5-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="d5ba5-113">Verweist auf Skriptbibliotheken in den erstellten Ansichten.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="d5ba5-114">Fügt `_ValidationScriptsPartial` zum Bearbeiten und Erstellen von Seiten hinzu.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="d5ba5-115">--layout oder -l</span><span class="sxs-lookup"><span data-stu-id="d5ba5-115">--layout or -l</span></span> | <span data-ttu-id="d5ba5-116">Benutzerdefinierte Layoutseite, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="d5ba5-117">--useDefaultLayout oder -udl</span><span class="sxs-lookup"><span data-stu-id="d5ba5-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="d5ba5-118">Verwendet das Standardlayout für die Ansichten.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="d5ba5-119">--force oder -f</span><span class="sxs-lookup"><span data-stu-id="d5ba5-119">--force or -f</span></span> | <span data-ttu-id="d5ba5-120">Überschreibt vorhandene Dateien.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="d5ba5-121">--relativeFolderPath oder -outDir</span><span class="sxs-lookup"><span data-stu-id="d5ba5-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="d5ba5-122">Der relative Ausgabeordnerpfad des Projekts, in dem die Dateien erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="d5ba5-123">Wenn dieser nicht angegeben wird, werden die Dateien im Projektordner erstellt.</span><span class="sxs-lookup"><span data-stu-id="d5ba5-123">If not specified, files are generated in the project folder.</span></span> |