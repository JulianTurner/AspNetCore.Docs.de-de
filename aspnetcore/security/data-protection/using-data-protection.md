---
title: Beginnen Sie mit den Datenschutz-APIs in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die ASP.net Core-Datenschutz-APIs verwenden, um Daten in einer APP zu schützen und zu schützen.
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051654"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="c35f3-103">Beginnen Sie mit den Datenschutz-APIs in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c35f3-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="c35f3-104">Am einfachsten besteht der Schutz von Daten aus den folgenden Schritten:</span><span class="sxs-lookup"><span data-stu-id="c35f3-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="c35f3-105">Erstellen Sie eine Daten Schutzvorrichtung von einem Datenschutzanbieter.</span><span class="sxs-lookup"><span data-stu-id="c35f3-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="c35f3-106">Wenden Sie die- `Protect` Methode mit den Daten an, die Sie schützen möchten.</span><span class="sxs-lookup"><span data-stu-id="c35f3-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="c35f3-107">Nennen Sie die- `Unprotect` Methode mit den Daten, die Sie in nur-Text-Daten zurücksetzen möchten.</span><span class="sxs-lookup"><span data-stu-id="c35f3-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="c35f3-108">Bei den meisten Frameworks und App-Modellen, wie ASP.net Core oder SignalR , wird das Datenschutzsystem bereits konfiguriert und einem Dienst Container hinzugefügt, auf den Sie über die Abhängigkeitsinjektion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c35f3-108">Most frameworks and app models, such as ASP.NET Core or SignalR, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="c35f3-109">Das folgende Beispiel veranschaulicht das Konfigurieren eines Dienst Containers für die Abhängigkeitsinjektion und das Registrieren des Datenschutz Stapels, das Empfangen des Datenschutz Anbieters über di, das Erstellen einer Schutzvorrichtung und das Schützen des Schutzes von Daten.</span><span class="sxs-lookup"><span data-stu-id="c35f3-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="c35f3-110">Wenn Sie eine Schutzvorrichtung erstellen, müssen Sie eine oder mehrere [Zweck](xref:security/data-protection/consumer-apis/purpose-strings)Zeichenfolgen angeben.</span><span class="sxs-lookup"><span data-stu-id="c35f3-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="c35f3-111">Eine Zweck Zeichenfolge ermöglicht die Isolation zwischen Consumern.</span><span class="sxs-lookup"><span data-stu-id="c35f3-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="c35f3-112">Beispielsweise wäre eine Schutzvorrichtung, die mit einer Zweck Zeichenfolge "grün" erstellt wurde, nicht in der Lage, die Daten zu schützen, die von einer Schutzvorrichtung mit dem Zweck "Lila" bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="c35f3-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="c35f3-113">Instanzen von `IDataProtectionProvider` und `IDataProtector` sind für mehrere Aufrufer Thread sicher.</span><span class="sxs-lookup"><span data-stu-id="c35f3-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="c35f3-114">Wenn eine Komponente einen Verweis auf einen `IDataProtector` über einen Aufruf von erhält `CreateProtector` , wird dieser Verweis für mehrere Aufrufe von `Protect` und verwendet `Unprotect` .</span><span class="sxs-lookup"><span data-stu-id="c35f3-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="c35f3-115">Ein-Aufrufe von löst `Unprotect` CryptographicException aus, wenn die geschützte Nutzlast nicht überprüft oder nicht verschlüsselt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c35f3-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="c35f3-116">Einige Komponenten möchten möglicherweise Fehler beim Vorgang zum Aufheben des Schutzes ignorieren. eine Komponente, die Authentifizierungs-e liest, cookie kann diesen Fehler verarbeiten und die Anforderung so behandeln, als wäre sie überhaupt nicht voll cookie ständig.</span><span class="sxs-lookup"><span data-stu-id="c35f3-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication cookies might handle this error and treat the request as if it had no cookie at all rather than fail the request outright.</span></span> <span data-ttu-id="c35f3-117">Komponenten, für die dieses Verhalten gewünscht ist, sollten CryptographicException genau abfangen, anstatt alle Ausnahmen zu verschlucken.</span><span class="sxs-lookup"><span data-stu-id="c35f3-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
