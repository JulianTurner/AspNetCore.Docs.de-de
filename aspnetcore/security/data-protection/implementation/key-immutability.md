---
title: Schlüssel Unveränderlichkeit und Schlüssel Einstellungen in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails der ASP.net Core Datenschutz schlüsselimmuability-APIs.
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
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 8efca1d96945cb7af0132b27801b23a45714e08a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061248"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a><span data-ttu-id="e92db-103">Schlüssel Unveränderlichkeit und Schlüssel Einstellungen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e92db-103">Key immutability and key settings in ASP.NET Core</span></span>

<span data-ttu-id="e92db-104">Wenn ein Objekt im Sicherungs Speicher persistent gespeichert wird, wird seine Darstellung immer wieder korrigiert.</span><span class="sxs-lookup"><span data-stu-id="e92db-104">Once an object is persisted to the backing store, its representation is forever fixed.</span></span> <span data-ttu-id="e92db-105">Dem Sicherungs Speicher können neue Daten hinzugefügt werden, vorhandene Daten können jedoch nie mutiert werden.</span><span class="sxs-lookup"><span data-stu-id="e92db-105">New data can be added to the backing store, but existing data can never be mutated.</span></span> <span data-ttu-id="e92db-106">Der Hauptzweck dieses Verhaltens besteht darin, die Beschädigung von Daten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="e92db-106">The primary purpose of this behavior is to prevent data corruption.</span></span>

<span data-ttu-id="e92db-107">Eine Folge dieses Verhaltens besteht darin, dass ein Schlüssel, der in den Sicherungs Speicher geschrieben wird, unveränderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e92db-107">One consequence of this behavior is that once a key is written to the backing store, it's immutable.</span></span> <span data-ttu-id="e92db-108">Die Erstellungs-, Aktivierungs-und Ablaufdatums Angaben können niemals geändert werden, obwohl Sie mithilfe von widerrufen werden können `IKeyManager` .</span><span class="sxs-lookup"><span data-stu-id="e92db-108">Its creation, activation, and expiration dates can never be changed, though it can revoked by using `IKeyManager`.</span></span> <span data-ttu-id="e92db-109">Außerdem sind die zugrunde liegenden algorithmische Informationen, die Eigenschaften für das Masterschlüssel Material und die Verschlüsselung ruhender Daten unveränderlich.</span><span class="sxs-lookup"><span data-stu-id="e92db-109">Additionally, its underlying algorithmic information, master keying material, and encryption at rest properties are also immutable.</span></span>

<span data-ttu-id="e92db-110">Wenn der Entwickler eine Einstellung ändert, die die Schlüssel Persistenz beeinflusst, werden diese Änderungen erst beim nächsten Generieren eines Schlüssels wirksam, entweder über einen expliziten Aufruf von `IKeyManager.CreateNewKey` oder über das eigene Verhalten der [automatischen Schlüsselgenerierung](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) des Datenschutzsystems.</span><span class="sxs-lookup"><span data-stu-id="e92db-110">If the developer changes any setting that affects key persistence, those changes won't go into effect until the next time a key is generated, either via an explicit call to `IKeyManager.CreateNewKey` or via the data protection system's own [automatic key generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) behavior.</span></span> <span data-ttu-id="e92db-111">Folgende Einstellungen haben Auswirkungen auf die Schlüssel Persistenz:</span><span class="sxs-lookup"><span data-stu-id="e92db-111">The settings that affect key persistence are as follows:</span></span>

* [<span data-ttu-id="e92db-112">Die Standard Lebensdauer des Schlüssels</span><span class="sxs-lookup"><span data-stu-id="e92db-112">The default key lifetime</span></span>](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [<span data-ttu-id="e92db-113">Der Verschlüsselungsmechanismus für Schlüssel Verschlüsselung</span><span class="sxs-lookup"><span data-stu-id="e92db-113">The key encryption at rest mechanism</span></span>](xref:security/data-protection/implementation/key-encryption-at-rest)

* [<span data-ttu-id="e92db-114">Die algorithmischen Informationen, die im Schlüssel enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="e92db-114">The algorithmic information contained within the key</span></span>](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

<span data-ttu-id="e92db-115">Wenn Sie diese Einstellungen vor der nächsten automatischen Zeit zum automatischen Ausführen von Schlüsseln benötigen, sollten Sie einen expliziten Aufruf von erstellen, `IKeyManager.CreateNewKey` um die Erstellung eines neuen Schlüssels zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="e92db-115">If you need these settings to kick in earlier than the next automatic key rolling time, consider making an explicit call to `IKeyManager.CreateNewKey` to force the creation of a new key.</span></span> <span data-ttu-id="e92db-116">Stellen Sie sicher, dass Sie ein explizites Aktivierungsdatum angeben ({Now + 2 Days} ist eine gute Faustregel, um Zeit für die Änderung der Änderung zuzulassen) und das Ablaufdatum im Aufruf.</span><span class="sxs-lookup"><span data-stu-id="e92db-116">Remember to provide an explicit activation date ({ now + 2 days } is a good rule of thumb to allow time for the change to propagate) and expiration date in the call.</span></span>

>[!TIP]
> <span data-ttu-id="e92db-117">Alle Anwendungen, die das Repository berühren, sollten dieselben Einstellungen mit den `IDataProtectionBuilder` Erweiterungs Methoden angeben.</span><span class="sxs-lookup"><span data-stu-id="e92db-117">All applications touching the repository should specify the same settings with the `IDataProtectionBuilder` extension methods.</span></span> <span data-ttu-id="e92db-118">Andernfalls werden die Eigenschaften des beibehaltenen Schlüssels von der jeweiligen Anwendung abhängen, die die Schlüssel Generierungs Routinen aufgerufen hat.</span><span class="sxs-lookup"><span data-stu-id="e92db-118">Otherwise, the properties of the persisted key will be dependent on the particular application that invoked the key generation routines.</span></span>
