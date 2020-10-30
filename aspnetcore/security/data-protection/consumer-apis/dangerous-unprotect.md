---
title: Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in einer ASP.net Core-App aufheben können, die mit Schlüsseln geschützt sind, die seit dem widerrufen wurden
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5f13b53182f720ac8b58d90701561d381981308a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051095"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a><span data-ttu-id="8c3cc-103">Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden</span><span class="sxs-lookup"><span data-stu-id="8c3cc-103">Unprotect payloads whose keys have been revoked in ASP.NET Core</span></span>

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

<span data-ttu-id="8c3cc-104">Die ASP.net Core-Datenschutz-APIs sind nicht in erster Linie für die unbegrenzte Persistenz von vertraulichen Nutzlasten vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-104">The ASP.NET Core data protection APIs are not primarily intended for indefinite persistence of confidential payloads.</span></span> <span data-ttu-id="8c3cc-105">Andere Technologien wie [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) und [Azure Rights Management](/rights-management/) sind besser für das Szenario der unbegrenzten Speicherung geeignet und verfügen über die entsprechenden leistungsstarken Schlüssel Verwaltungsfunktionen.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-105">Other technologies like [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) and [Azure Rights Management](/rights-management/) are more suited to the scenario of indefinite storage, and they have correspondingly strong key management capabilities.</span></span> <span data-ttu-id="8c3cc-106">Dies bedeutet, dass ein Entwickler nicht daran hindert, die ASP.net Core Datenschutz-APIs für den langfristigen Schutz vertraulicher Daten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-106">That said, there's nothing prohibiting a developer from using the ASP.NET Core data protection APIs for long-term protection of confidential data.</span></span> <span data-ttu-id="8c3cc-107">Schlüssel werden nie aus dem Schlüsselbund entfernt. so `IDataProtector.Unprotect` können vorhandene Nutzlasten immer wieder hergestellt werden, solange die Schlüssel verfügbar und gültig sind.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-107">Keys are never removed from the key ring, so `IDataProtector.Unprotect` can always recover existing payloads as long as the keys are available and valid.</span></span>

<span data-ttu-id="8c3cc-108">Es tritt jedoch ein Problem auf, wenn der Entwickler versucht, den Schutz von Daten aufzuheben, die mit einem gesperrten Schlüssel geschützt wurden, da `IDataProtector.Unprotect` in diesem Fall eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-108">However, an issue arises when the developer tries to unprotect data that has been protected with a revoked key, as `IDataProtector.Unprotect` will throw an exception in this case.</span></span> <span data-ttu-id="8c3cc-109">Dies kann bei kurzlebigen oder vorübergehenden Nutzlasten (wie z. b. Authentifizierungs Token) in Ordnung sein, da diese Arten von Nutzlasten problemlos vom System neu erstellt werden können und sich der Standort Besucher im schlimmsten Fall erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-109">This might be fine for short-lived or transient payloads (like authentication tokens), as these kinds of payloads can easily be recreated by the system, and at worst the site visitor might be required to log in again.</span></span> <span data-ttu-id="8c3cc-110">Für persistente Nutzlasten könnte das Auslösen von " `Unprotect` throw" zu nicht akzeptablen Datenverlusten führen.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-110">But for persisted payloads, having `Unprotect` throw could lead to unacceptable data loss.</span></span>

## <a name="ipersisteddataprotector"></a><span data-ttu-id="8c3cc-111">Ipersisteddataprotector</span><span class="sxs-lookup"><span data-stu-id="8c3cc-111">IPersistedDataProtector</span></span>

<span data-ttu-id="8c3cc-112">Um das Szenario zu unterstützen, in dem Nutzlasten auch bei widerrufenen Schlüsseln nicht geschützt werden können, enthält das Datenschutzsystem einen- `IPersistedDataProtector` Typ.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-112">To support the scenario of allowing payloads to be unprotected even in the face of revoked keys, the data protection system contains an `IPersistedDataProtector` type.</span></span> <span data-ttu-id="8c3cc-113">Um eine Instanz von zu erhalten `IPersistedDataProtector` , können Sie einfach eine Instanz von `IDataProtector` in der normalen Weise erhalten und versuchen, die in umzuwandeln `IDataProtector` `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="8c3cc-113">To get an instance of `IPersistedDataProtector`, simply get an instance of `IDataProtector` in the normal fashion and try casting the `IDataProtector` to `IPersistedDataProtector`.</span></span>

> [!NOTE]
> <span data-ttu-id="8c3cc-114">Nicht alle `IDataProtector` Instanzen können in umgewandelt werden `IPersistedDataProtector` .</span><span class="sxs-lookup"><span data-stu-id="8c3cc-114">Not all `IDataProtector` instances can be cast to `IPersistedDataProtector`.</span></span> <span data-ttu-id="8c3cc-115">Entwickler sollten den c#-Operator oder ähnlich verwenden, um Lauf Zeit Ausnahmen zu vermeiden, die durch ungültige Umwandlungen verursacht werden, und Sie sollten darauf vorbereitet sein, den Fehlerfall entsprechend zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-115">Developers should use the C# as operator or similar to avoid runtime exceptions caused by invalid casts, and they should be prepared to handle the failure case appropriately.</span></span>

<span data-ttu-id="8c3cc-116">`IPersistedDataProtector` macht die folgende API-Oberfläche verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8c3cc-116">`IPersistedDataProtector` exposes the following API surface:</span></span>

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

<span data-ttu-id="8c3cc-117">Diese API übernimmt die geschützte Nutzlast (als Bytearray) und gibt die ungeschützte Nutzlast zurück.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-117">This API takes the protected payload (as a byte array) and returns the unprotected payload.</span></span> <span data-ttu-id="8c3cc-118">Es gibt keine Zeichen folgen basierte Überladung.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-118">There's no string-based overload.</span></span> <span data-ttu-id="8c3cc-119">Die zwei out-Parameter lauten wie folgt.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-119">The two out parameters are as follows.</span></span>

* <span data-ttu-id="8c3cc-120">`requiresMigration`: wird auf "true" festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, nicht mehr der aktive Standardschlüssel ist, z. b. wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, veraltet ist und ein Schlüssel rollender Vorgang stattfindet.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-120">`requiresMigration`: will be set to true if the key used to protect this payload is no longer the active default key, e.g., the key used to protect this payload is old and a key rolling operation has since taken place.</span></span> <span data-ttu-id="8c3cc-121">Der Aufrufer möchte ggf. den erneuten Schutz der Nutzlast abhängig von Ihren geschäftlichen Anforderungen in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-121">The caller may wish to consider reprotecting the payload depending on their business needs.</span></span>

* <span data-ttu-id="8c3cc-122">`wasRevoked`: wird auf true festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wurde, widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-122">`wasRevoked`: will be set to true if the key used to protect this payload was revoked.</span></span>

>[!WARNING]
> <span data-ttu-id="8c3cc-123">Gehen Sie beim übergeben `ignoreRevocationErrors: true` an die-Methode mit äußerster Vorsicht vor `DangerousUnprotect` .</span><span class="sxs-lookup"><span data-stu-id="8c3cc-123">Exercise extreme caution when passing `ignoreRevocationErrors: true` to the `DangerousUnprotect` method.</span></span> <span data-ttu-id="8c3cc-124">Wenn nach dem Aufrufen dieser Methode der `wasRevoked` Wert true ist, wurde der zum Schutz dieser Nutzlast verwendete Schlüssel widerrufen, und die Authentizität der Nutzlast sollte als Fehler verdächtig behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-124">If after calling this method the `wasRevoked` value is true, then the key used to protect this payload was revoked, and the payload's authenticity should be treated as suspect.</span></span> <span data-ttu-id="8c3cc-125">In diesem Fall wird nur der Betrieb der nicht geschützten Nutzlast fortgesetzt, wenn Sie eine separate Gewissheit haben, dass Sie authentisch ist, z. b., dass Sie von einer sicheren Datenbank stammt, anstatt von einem nicht vertrauenswürdigen WebClient gesendet zu werden.</span><span class="sxs-lookup"><span data-stu-id="8c3cc-125">In this case, only continue operating on the unprotected payload if you have some separate assurance that it's authentic, e.g. that it's coming from a secure database rather than being sent by an untrusted web client.</span></span>

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
