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
ms.openlocfilehash: c96c5e66d2e15db03c321d239a64b98119a7543a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552650"
---
<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="38e35-101">Hinzufügen von Validierungsregeln zum Modell „Movie“</span><span class="sxs-lookup"><span data-stu-id="38e35-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="38e35-102">Der Namespace „DataAnnotations“ bietet eine Gruppe integrierter Validierungsattribute, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="38e35-102">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="38e35-103">„DataAnnotations“ enthält auch Formatierungsattribute wie `DataType`, die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="38e35-103">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="38e35-104">Aktualisieren Sie die `Movie`-Klasse, um die integrierten Validierungsattribute `Required`, `StringLength`, `RegularExpression` und `Range` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="38e35-104">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="38e35-105">Die Validierungsattribute geben das Verhalten an, das Sie in den Modelleigenschaften erzwingen möchten, auf die sie angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="38e35-105">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="38e35-106">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="38e35-106">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="38e35-107">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="38e35-107">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="38e35-108">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="38e35-108">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="38e35-109">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="38e35-109">Must only use letters.</span></span>
  * <span data-ttu-id="38e35-110">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="38e35-110">The first letter is required to be uppercase.</span></span> <span data-ttu-id="38e35-111">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="38e35-111">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="38e35-112">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="38e35-112">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="38e35-113">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="38e35-113">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="38e35-114">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="38e35-114">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="38e35-115">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="38e35-115">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="38e35-116">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="38e35-116">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="38e35-117">Mit dem Attribut `StringLength` können Sie die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegen.</span><span class="sxs-lookup"><span data-stu-id="38e35-117">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="38e35-118">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="38e35-118">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="38e35-119">Indem Validierungsregeln von ASP.NET Core automatisch erzwungen werden, wird Ihre App stabiler.</span><span class="sxs-lookup"><span data-stu-id="38e35-119">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="38e35-120">Darüber hinaus wird sichergestellt, dass Sie die Validierung nicht vergessen und nicht versehentlich falsche Daten in die Datenbank übernehmen.</span><span class="sxs-lookup"><span data-stu-id="38e35-120">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
