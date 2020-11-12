---
title: Httprepl-Telemetrie
author: scottaddie
description: Erfahren Sie mehr über die Telemetriedaten, die von httprepl gesammelt werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550607"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="2ae14-103">Httprepl-Telemetrie</span><span class="sxs-lookup"><span data-stu-id="2ae14-103">HttpRepl telemetry</span></span>

<span data-ttu-id="2ae14-104">[Httprepl](xref:web-api/http-repl) enthält eine telemetriefunktion, mit der Verwendungs Daten erfasst werden.</span><span class="sxs-lookup"><span data-stu-id="2ae14-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="2ae14-105">Es ist wichtig, dass das httprepl-Team versteht, wie das Tool verwendet wird, um es zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="2ae14-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="2ae14-106">Deaktivierung der Telemetriefunktion</span><span class="sxs-lookup"><span data-stu-id="2ae14-106">How to opt out</span></span>

<span data-ttu-id="2ae14-107">Die httprepl-telemetriefunktion ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="2ae14-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="2ae14-108">Sie können das Telemetriefeature deaktivieren, indem Sie die Umgebungsvariable `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` auf `1` oder `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="2ae14-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="2ae14-109">Offenlegung</span><span class="sxs-lookup"><span data-stu-id="2ae14-109">Disclosure</span></span>

<span data-ttu-id="2ae14-110">Die httprepl zeigt Text an, der dem folgenden ähnelt, wenn Sie das Tool zum ersten Mal ausführen.</span><span class="sxs-lookup"><span data-stu-id="2ae14-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="2ae14-111">Der Text kann abhängig von der Version des Tools, das Sie ausführen, leicht abweichen.</span><span class="sxs-lookup"><span data-stu-id="2ae14-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="2ae14-112">Über diese erste Ausführung benachrichtigt Microsoft Sie über die Datensammlung.</span><span class="sxs-lookup"><span data-stu-id="2ae14-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="2ae14-113">Legen Sie die `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` Umgebungsvariable auf oder fest, um den Text der "ersten Testlauf" zu unterdrücken `1` `true` .</span><span class="sxs-lookup"><span data-stu-id="2ae14-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="2ae14-114">Datenpunkte</span><span class="sxs-lookup"><span data-stu-id="2ae14-114">Data points</span></span>

<span data-ttu-id="2ae14-115">Die telemetriefunktion ist nicht:</span><span class="sxs-lookup"><span data-stu-id="2ae14-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="2ae14-116">Erfassen Sie persönliche Daten, z. b. Benutzernamen, e-Mail-Adressen oder URLs.</span><span class="sxs-lookup"><span data-stu-id="2ae14-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="2ae14-117">Überprüfen Sie die HTTP-Anforderungen oder-Antworten.</span><span class="sxs-lookup"><span data-stu-id="2ae14-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="2ae14-118">Die Daten werden sicher an Microsoft-Server gesendet und mit eingeschränktem Zugriff gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2ae14-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="2ae14-119">Der Schutz Ihrer Privatsphäre ist uns wichtig.</span><span class="sxs-lookup"><span data-stu-id="2ae14-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="2ae14-120">Wenn Sie vermuten, dass die telemetriefunktion sensible Daten sammelt oder die Daten unsicher oder unpassend verarbeitet werden, führen Sie eine der folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="2ae14-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="2ae14-121">Melden Sie ein Problem im [dotnet/httprepl-](https://github.com/dotnet/httprepl/issues) Repository.</span><span class="sxs-lookup"><span data-stu-id="2ae14-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="2ae14-122">Senden Sie eine e-Mail zur [dotnet@microsoft.com](mailto:dotnet@microsoft.com) Untersuchung an.</span><span class="sxs-lookup"><span data-stu-id="2ae14-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="2ae14-123">Die telemetriefunktion sammelt die folgenden Daten.</span><span class="sxs-lookup"><span data-stu-id="2ae14-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="2ae14-124">.NET SDK-Versionen</span><span class="sxs-lookup"><span data-stu-id="2ae14-124">.NET SDK versions</span></span> | <span data-ttu-id="2ae14-125">Daten</span><span class="sxs-lookup"><span data-stu-id="2ae14-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="2ae14-126">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-126">>=5.0</span></span>        | <span data-ttu-id="2ae14-127">Den Zeitstempel des Aufrufs</span><span class="sxs-lookup"><span data-stu-id="2ae14-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="2ae14-128">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-128">>=5.0</span></span>        | <span data-ttu-id="2ae14-129">IP-Adresse für das drei-Oktett, mit der der geografische Standort bestimmt wird.</span><span class="sxs-lookup"><span data-stu-id="2ae14-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="2ae14-130">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-130">>=5.0</span></span>        | <span data-ttu-id="2ae14-131">Betriebssystem und Version</span><span class="sxs-lookup"><span data-stu-id="2ae14-131">Operating system and version.</span></span> |
| <span data-ttu-id="2ae14-132">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-132">>=5.0</span></span>        | <span data-ttu-id="2ae14-133">Die Runtime-ID (RID), auf der das Tool ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2ae14-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="2ae14-134">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-134">>=5.0</span></span>        | <span data-ttu-id="2ae14-135">Gibt an, ob das Tool in einem Container ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2ae14-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="2ae14-136">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-136">>=5.0</span></span>        | <span data-ttu-id="2ae14-137">Hash Media Access Control (Mac)-Adresse: ein kryptografischer (SHA256) Hashwert und eine eindeutige ID für einen Computer.</span><span class="sxs-lookup"><span data-stu-id="2ae14-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="2ae14-138">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-138">>=5.0</span></span>        | <span data-ttu-id="2ae14-139">Kernelversion</span><span class="sxs-lookup"><span data-stu-id="2ae14-139">Kernel version.</span></span> |
| <span data-ttu-id="2ae14-140">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-140">>=5.0</span></span>        | <span data-ttu-id="2ae14-141">Httprepl-Version.</span><span class="sxs-lookup"><span data-stu-id="2ae14-141">HttpRepl version.</span></span> |
| <span data-ttu-id="2ae14-142">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-142">>=5.0</span></span>        | <span data-ttu-id="2ae14-143">Gibt an, ob das Tool mit-,-oder-Argumenten gestartet wurde `help` `run` `connect` .</span><span class="sxs-lookup"><span data-stu-id="2ae14-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="2ae14-144">Tatsächliche Argument Werte werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="2ae14-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="2ae14-145">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-145">>=5.0</span></span>        | <span data-ttu-id="2ae14-146">Der Befehl wurde aufgerufen (z `get` . b.), und ob er erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="2ae14-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="2ae14-147">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-147">>=5.0</span></span>        | <span data-ttu-id="2ae14-148">Für den- `connect` Befehl, ob die- `root` ,-oder- `base` `openapi` Argumente angegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="2ae14-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="2ae14-149">Tatsächliche Argument Werte werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="2ae14-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="2ae14-150">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-150">>=5.0</span></span>        | <span data-ttu-id="2ae14-151">Für den `pref` Befehl, ob `get` oder `set` ausgestellt wurde und auf welche Einstellung zugegriffen wurde.</span><span class="sxs-lookup"><span data-stu-id="2ae14-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="2ae14-152">Wenn es sich nicht um eine bekannte bevorzugte Einstellung handelt, wird der Name als Hashwert bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="2ae14-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="2ae14-153">Der Wert wird nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="2ae14-153">The value isn't collected.</span></span> |
| <span data-ttu-id="2ae14-154">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-154">>=5.0</span></span>        | <span data-ttu-id="2ae14-155">Für den `set header` Befehl wird der Header Name festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2ae14-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="2ae14-156">Wenn es sich nicht um einen bekannten Header handelt, wird der Name mit einem Hashwert versehen.</span><span class="sxs-lookup"><span data-stu-id="2ae14-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="2ae14-157">Der Wert wird nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="2ae14-157">The value isn't collected.</span></span> |
| <span data-ttu-id="2ae14-158">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-158">>=5.0</span></span>        | <span data-ttu-id="2ae14-159">Für den `connect` -Befehl, ob ein Sonderfall für `dotnet new webapi` verwendet wurde und, ob er per Preference umgangen wurde.</span><span class="sxs-lookup"><span data-stu-id="2ae14-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="2ae14-160">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="2ae14-160">>=5.0</span></span>        | <span data-ttu-id="2ae14-161">Für alle HTTP-Befehle (z. b. Get, Post, Put), unabhängig davon, ob jede der Optionen angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="2ae14-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="2ae14-162">Die Werte der Optionen werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="2ae14-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="2ae14-163">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2ae14-163">Additional resources</span></span>

* [<span data-ttu-id="2ae14-164">.NET Core SDK-Telemetrie</span><span class="sxs-lookup"><span data-stu-id="2ae14-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="2ae14-165">.Net Core-CLI von Telemetriedaten</span><span class="sxs-lookup"><span data-stu-id="2ae14-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
