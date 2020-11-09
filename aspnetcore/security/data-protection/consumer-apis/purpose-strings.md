---
title: Zweck Zeichenfolgen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Zweck Zeichenfolgen in den ASP.net Core Datenschutz-APIs verwendet werden.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060806"
---
# <a name="purpose-strings-in-aspnet-core"></a><span data-ttu-id="6ccef-103">Zweck Zeichenfolgen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6ccef-103">Purpose strings in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-purposes"></a>

<span data-ttu-id="6ccef-104">Komponenten, die nutzen, `IDataProtectionProvider` müssen einen eindeutigen *Verwendungs* Parameter an die- `CreateProtector` Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="6ccef-104">Components which consume `IDataProtectionProvider` must pass a unique *purposes* parameter to the `CreateProtector` method.</span></span> <span data-ttu-id="6ccef-105">Der *Parameter* "zwecks" ist der Sicherheit des Datenschutzsystems, da es die Isolation zwischen kryptografieconsumern ermöglicht, auch wenn die Stamm Kryptografieschlüssel identisch sind.</span><span class="sxs-lookup"><span data-stu-id="6ccef-105">The purposes *parameter* is inherent to the security of the data protection system, as it provides isolation between cryptographic consumers, even if the root cryptographic keys are the same.</span></span>

<span data-ttu-id="6ccef-106">Wenn ein Consumer einen Zweck angibt, wird die Zweck Zeichenfolge zusammen mit den Stamm-Kryptografieschlüsseln verwendet, um kryptografische Unterschlüssel abzuleiten, die für diesen Consumer eindeutig sind.</span><span class="sxs-lookup"><span data-stu-id="6ccef-106">When a consumer specifies a purpose, the purpose string is used along with the root cryptographic keys to derive cryptographic subkeys unique to that consumer.</span></span> <span data-ttu-id="6ccef-107">Dadurch wird der Consumer von allen anderen kryptografieverbrauchern in der Anwendung isoliert: keine andere Komponente kann seine Nutzlasten lesen, und er kann keine Nutzlasten anderer Komponenten lesen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-107">This isolates the consumer from all other cryptographic consumers in the application: no other component can read its payloads, and it cannot read any other component's payloads.</span></span> <span data-ttu-id="6ccef-108">Diese Isolation rendert außerdem alle möglichen Angriffs Kategorien für die Komponente.</span><span class="sxs-lookup"><span data-stu-id="6ccef-108">This isolation also renders infeasible entire categories of attack against the component.</span></span>

![Beispiel für Zweck Diagramm](purpose-strings/_static/purposes.png)

<span data-ttu-id="6ccef-110">In der obigen Abbildung `IDataProtector` **können** die Instanzen A und B nicht die Nutzlasten anderer Benutzer, sondern nur Ihre eigenen Nutzlasten lesen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-110">In the diagram above, `IDataProtector` instances A and B **cannot** read each other's payloads, only their own.</span></span>

<span data-ttu-id="6ccef-111">Die Zweck Zeichenfolge muss nicht geheim sein.</span><span class="sxs-lookup"><span data-stu-id="6ccef-111">The purpose string doesn't have to be secret.</span></span> <span data-ttu-id="6ccef-112">Es sollte einfach so eindeutig sein, dass keine andere gut verhaltene Komponente jemals die gleiche Zweck Zeichenfolge bereitstellen wird.</span><span class="sxs-lookup"><span data-stu-id="6ccef-112">It should simply be unique in the sense that no other well-behaved component will ever provide the same purpose string.</span></span>

>[!TIP]
> <span data-ttu-id="6ccef-113">Die Verwendung des Namespace und Typnamens der Komponente, die die Datenschutz-APIs nutzt, ist eine gute Faustregel, da diese Informationen in der Praxis niemals in Konflikt stehen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-113">Using the namespace and type name of the component consuming the data protection APIs is a good rule of thumb, as in practice this information will never conflict.</span></span>
>
><span data-ttu-id="6ccef-114">Eine von Configuration Manager erstellte Komponente, die für das Minting von bearertoken zuständig ist, kann "" als Zweck Zeichenfolge "" "" als Zweck Zeichenfolge verwenden.</span><span class="sxs-lookup"><span data-stu-id="6ccef-114">A Contoso-authored component which is responsible for minting bearer tokens might use Contoso.Security.BearerToken as its purpose string.</span></span> <span data-ttu-id="6ccef-115">Oder-noch besser: Es kann "" als Zweck Zeichenfolge "" verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ccef-115">Or - even better - it might use Contoso.Security.BearerToken.v1 as its purpose string.</span></span> <span data-ttu-id="6ccef-116">Das Anfügen der Versionsnummer ermöglicht einer zukünftigen Version die Verwendung von "fitoso. Security. bearertoken. v2" als Zweck, und die verschiedenen Versionen werden vollständig voneinander isoliert, soweit Nutzlasten erfolgen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-116">Appending the version number allows a future version to use Contoso.Security.BearerToken.v2 as its purpose, and the different versions would be completely isolated from one another as far as payloads go.</span></span>

<span data-ttu-id="6ccef-117">Da es sich bei dem Parameter "zwecks" um `CreateProtector` ein Zeichen folgen Array handelt, könnte der obige Wert stattdessen als angegeben werden `[ "Contoso.Security.BearerToken", "v1" ]` .</span><span class="sxs-lookup"><span data-stu-id="6ccef-117">Since the purposes parameter to `CreateProtector` is a string array, the above could've been instead specified as `[ "Contoso.Security.BearerToken", "v1" ]`.</span></span> <span data-ttu-id="6ccef-118">Dies ermöglicht das Einrichten einer Hierarchie von Zwecken und eröffnet die Möglichkeit von Szenarien mit mehreren Mandanten mit dem Datenschutzsystem.</span><span class="sxs-lookup"><span data-stu-id="6ccef-118">This allows establishing a hierarchy of purposes and opens up the possibility of multi-tenancy scenarios with the data protection system.</span></span>

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> <span data-ttu-id="6ccef-119">Komponenten sollten nicht vertrauenswürdige Benutzereingaben nicht als alleinige Quelle für die Eingabe Quelle für die Zweck Kette zulassen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-119">Components shouldn't allow untrusted user input to be the sole source of input for the purposes chain.</span></span>
>
><span data-ttu-id="6ccef-120">Stellen Sie sich z. b. eine Komponente "" "" ".</span><span class="sxs-lookup"><span data-stu-id="6ccef-120">For example, consider a component Contoso.Messaging.SecureMessage which is responsible for storing secure messages.</span></span> <span data-ttu-id="6ccef-121">Wenn die Secure Messaging-Komponente aufzurufen `CreateProtector([ username ])` versucht, könnte ein böswilliger Benutzer ein Konto mit dem Benutzernamen "", "" "" "" "," "" "," "" "" "" "". `CreateProtector([ "Contoso.Security.BearerToken" ])`</span><span class="sxs-lookup"><span data-stu-id="6ccef-121">If the secure messaging component were to call `CreateProtector([ username ])`, then a malicious user might create an account with username "Contoso.Security.BearerToken" in an attempt to get the component to call `CreateProtector([ "Contoso.Security.BearerToken" ])`, thus inadvertently causing the secure messaging system to mint payloads that could be perceived as authentication tokens.</span></span>
>
><span data-ttu-id="6ccef-122">Eine bessere Verwendungs Kette für die Messaging Komponente wäre `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , was eine ordnungsgemäße Isolation bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="6ccef-122">A better purposes chain for the messaging component would be `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, which provides proper isolation.</span></span>

<span data-ttu-id="6ccef-123">Die vom-Verhalten und dem Verhalten von, und bereitgestellte Isolation `IDataProtectionProvider` `IDataProtector` lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="6ccef-123">The isolation provided by and behaviors of `IDataProtectionProvider`, `IDataProtector`, and purposes are as follows:</span></span>

* <span data-ttu-id="6ccef-124">Für ein bestimmtes `IDataProtectionProvider` Objekt erstellt die `CreateProtector` Methode ein Objekt, das `IDataProtector` eindeutig an das Objekt gebunden ist, `IDataProtectionProvider` das es erstellt hat, und den Zweck-Parameter, der an die Methode übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="6ccef-124">For a given `IDataProtectionProvider` object, the `CreateProtector` method will create an `IDataProtector` object uniquely tied to both the `IDataProtectionProvider` object which created it and the purposes parameter which was passed into the method.</span></span>

* <span data-ttu-id="6ccef-125">Der Zweck Parameter darf nicht NULL sein.</span><span class="sxs-lookup"><span data-stu-id="6ccef-125">The purpose parameter must not be null.</span></span> <span data-ttu-id="6ccef-126">(Wenn Zwecke als Array angegeben wird, bedeutet dies, dass das Array nicht die Länge 0 (null) aufweisen darf und dass alle Elemente des Arrays ungleich NULL sein müssen.) Ein leerer Zeichen folgen Zweck ist technisch zulässig, wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="6ccef-126">(If purposes is specified as an array, this means that the array must not be of zero length and all elements of the array must be non-null.) An empty string purpose is technically allowed but is discouraged.</span></span>

* <span data-ttu-id="6ccef-127">Zwei Zweck Argumente sind nur dann gleichwertig, wenn Sie dieselben Zeichen folgen (mit einem Ordinalvergleich) in derselben Reihenfolge enthalten.</span><span class="sxs-lookup"><span data-stu-id="6ccef-127">Two purposes arguments are equivalent if and only if they contain the same strings (using an ordinal comparer) in the same order.</span></span> <span data-ttu-id="6ccef-128">Ein Argument mit einem einzelnen Zweck entspricht dem entsprechenden Array von Einzelelement Zwecken.</span><span class="sxs-lookup"><span data-stu-id="6ccef-128">A single purpose argument is equivalent to the corresponding single-element purposes array.</span></span>

* <span data-ttu-id="6ccef-129">Zwei `IDataProtector` Objekte sind gleichwertig, wenn Sie aus gleichwertigen `IDataProtectionProvider` Objekten mit äquivalenten Parametern erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="6ccef-129">Two `IDataProtector` objects are equivalent if and only if they're created from equivalent `IDataProtectionProvider` objects with equivalent purposes parameters.</span></span>

* <span data-ttu-id="6ccef-130">Für ein bestimmtes `IDataProtector` Objekt gibt ein-Rückruf `Unprotect(protectedData)` den ursprünglichen `unprotectedData` if und nur dann zurück, wenn `protectedData := Protect(unprotectedData)` für ein entsprechendes- `IDataProtector` Objekt.</span><span class="sxs-lookup"><span data-stu-id="6ccef-130">For a given `IDataProtector` object, a call to `Unprotect(protectedData)` will return the original `unprotectedData` if and only if `protectedData := Protect(unprotectedData)` for an equivalent `IDataProtector` object.</span></span>

> [!NOTE]
> <span data-ttu-id="6ccef-131">Der Fall, in dem eine Komponente absichtlich eine Zweck Zeichenfolge auswählt, die bekanntermaßen mit einer anderen Komponente in Konflikt steht, wird nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="6ccef-131">We're not considering the case where some component intentionally chooses a purpose string which is known to conflict with another component.</span></span> <span data-ttu-id="6ccef-132">Eine solche Komponente würde im Grunde als schädlich angesehen werden, und dieses System ist nicht für die Bereitstellung von Sicherheitsgarantien gedacht, wenn bereits bösartiger Code innerhalb des Arbeitsprozesses ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6ccef-132">Such a component would essentially be considered malicious, and this system isn't intended to provide security guarantees in the event that malicious code is already running inside of the worker process.</span></span>
