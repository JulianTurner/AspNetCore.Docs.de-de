---
title: HTTP-repl-Telemetrie
author: scottaddie
description: Erfahren Sie mehr über die von der http-repl gesammelten Telemetriedaten.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/10/2020
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
ms.openlocfilehash: 8590959e43c2dda69090acb358e740b271426a44
ms.sourcegitcommit: fb72e9c1ae5b279817f1fb4b46a52170449b6f30
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94502003"
---
# <a name="http-repl-telemetry"></a><span data-ttu-id="ff086-103">HTTP-repl-Telemetrie</span><span class="sxs-lookup"><span data-stu-id="ff086-103">HTTP REPL telemetry</span></span>

<span data-ttu-id="ff086-104">Die [http-Read-eval-Print-Schleife (repl)](xref:web-api/http-repl) enthält eine telemetriefunktion, mit der Verwendungs Daten erfasst werden.</span><span class="sxs-lookup"><span data-stu-id="ff086-104">The [HTTP Read-Eval-Print Loop (REPL)](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="ff086-105">Es ist wichtig, dass das http-repl-Team versteht, wie das Tool verwendet wird, um es zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="ff086-105">It's important that the HTTP REPL team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="ff086-106">Deaktivierung der Telemetriefunktion</span><span class="sxs-lookup"><span data-stu-id="ff086-106">How to opt out</span></span>

<span data-ttu-id="ff086-107">Die http-repl-telemetriefunktion ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="ff086-107">The HTTP REPL telemetry feature is enabled by default.</span></span> <span data-ttu-id="ff086-108">Sie können das Telemetriefeature deaktivieren, indem Sie die Umgebungsvariable `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` auf `1` oder `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="ff086-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="ff086-109">Offenlegung</span><span class="sxs-lookup"><span data-stu-id="ff086-109">Disclosure</span></span>

<span data-ttu-id="ff086-110">Httprepl zeigt Text an, der dem folgenden ähnelt, wenn Sie das Tool zum ersten Mal ausführen.</span><span class="sxs-lookup"><span data-stu-id="ff086-110">HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="ff086-111">Der Text kann abhängig von der Version des Tools, das Sie ausführen, leicht abweichen.</span><span class="sxs-lookup"><span data-stu-id="ff086-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="ff086-112">Über diese erste Ausführung benachrichtigt Microsoft Sie über die Datensammlung.</span><span class="sxs-lookup"><span data-stu-id="ff086-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET Core tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

## <a name="data-points"></a><span data-ttu-id="ff086-113">Datenpunkte</span><span class="sxs-lookup"><span data-stu-id="ff086-113">Data points</span></span>

<span data-ttu-id="ff086-114">Die telemetriefunktion ist nicht:</span><span class="sxs-lookup"><span data-stu-id="ff086-114">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="ff086-115">Erfassen Sie persönliche Daten, z. b. Benutzernamen, e-Mail-Adressen oder URLs.</span><span class="sxs-lookup"><span data-stu-id="ff086-115">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="ff086-116">Überprüfen Sie die HTTP-Anforderungen oder-Antworten.</span><span class="sxs-lookup"><span data-stu-id="ff086-116">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="ff086-117">Die Daten werden sicher an Microsoft-Server gesendet und mit eingeschränktem Zugriff gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ff086-117">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="ff086-118">Der Schutz Ihrer Privatsphäre ist uns wichtig.</span><span class="sxs-lookup"><span data-stu-id="ff086-118">Protecting your privacy is important to us.</span></span> <span data-ttu-id="ff086-119">Wenn Sie vermuten, dass die telemetriefunktion sensible Daten sammelt oder die Daten unsicher oder unpassend verarbeitet werden, führen Sie eine der folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="ff086-119">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="ff086-120">Melden Sie ein Problem im [dotnet/httprepl-](https://github.com/dotnet/httprepl/issues) Repository.</span><span class="sxs-lookup"><span data-stu-id="ff086-120">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="ff086-121">Senden Sie eine e-Mail zur [dotnet@microsoft.com](mailto:dotnet@microsoft.com) Untersuchung an.</span><span class="sxs-lookup"><span data-stu-id="ff086-121">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="ff086-122">Die telemetriefunktion sammelt die folgenden Daten.</span><span class="sxs-lookup"><span data-stu-id="ff086-122">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="ff086-123">.NET SDK-Versionen</span><span class="sxs-lookup"><span data-stu-id="ff086-123">.NET SDK versions</span></span> | <span data-ttu-id="ff086-124">Daten</span><span class="sxs-lookup"><span data-stu-id="ff086-124">Data</span></span> |
|--------------|------|
| <span data-ttu-id="ff086-125">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-125">>=5.0</span></span>        | <span data-ttu-id="ff086-126">Den Zeitstempel des Aufrufs</span><span class="sxs-lookup"><span data-stu-id="ff086-126">Timestamp of invocation.</span></span> |
| <span data-ttu-id="ff086-127">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-127">>=5.0</span></span>        | <span data-ttu-id="ff086-128">IP-Adresse für das drei-Oktett, mit der der geografische Standort bestimmt wird.</span><span class="sxs-lookup"><span data-stu-id="ff086-128">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="ff086-129">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-129">>=5.0</span></span>        | <span data-ttu-id="ff086-130">Betriebssystem und Version</span><span class="sxs-lookup"><span data-stu-id="ff086-130">Operating system and version.</span></span> |
| <span data-ttu-id="ff086-131">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-131">>=5.0</span></span>        | <span data-ttu-id="ff086-132">Die Runtime-ID (RID), auf der das Tool ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff086-132">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="ff086-133">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-133">>=5.0</span></span>        | <span data-ttu-id="ff086-134">Gibt an, ob das Tool in einem Container ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff086-134">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="ff086-135">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-135">>=5.0</span></span>        | <span data-ttu-id="ff086-136">Hash Media Access Control (Mac)-Adresse: ein kryptografischer (SHA256) Hashwert und eine eindeutige ID für einen Computer.</span><span class="sxs-lookup"><span data-stu-id="ff086-136">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="ff086-137">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-137">>=5.0</span></span>        | <span data-ttu-id="ff086-138">Kernelversion</span><span class="sxs-lookup"><span data-stu-id="ff086-138">Kernel version.</span></span> |
| <span data-ttu-id="ff086-139">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-139">>=5.0</span></span>        | <span data-ttu-id="ff086-140">HTTP-repl-Version.</span><span class="sxs-lookup"><span data-stu-id="ff086-140">HTTP REPL version.</span></span> |
| <span data-ttu-id="ff086-141">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-141">>=5.0</span></span>        | <span data-ttu-id="ff086-142">Gibt an, ob das Tool mit-,-oder-Argumenten gestartet wurde `help` `run` `connect` .</span><span class="sxs-lookup"><span data-stu-id="ff086-142">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="ff086-143">Tatsächliche Argument Werte werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="ff086-143">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="ff086-144">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-144">>=5.0</span></span>        | <span data-ttu-id="ff086-145">Der Befehl wurde aufgerufen (z `get` . b.), und ob er erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="ff086-145">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="ff086-146">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-146">>=5.0</span></span>        | <span data-ttu-id="ff086-147">Für den- `connect` Befehl, ob die- `root` ,-oder- `base` `openapi` Argumente angegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="ff086-147">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="ff086-148">Tatsächliche Argument Werte werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="ff086-148">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="ff086-149">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-149">>=5.0</span></span>        | <span data-ttu-id="ff086-150">Für den `pref` Befehl, ob `get` oder `set` ausgestellt wurde und auf welche Einstellung zugegriffen wurde.</span><span class="sxs-lookup"><span data-stu-id="ff086-150">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="ff086-151">Wenn es sich nicht um eine bekannte bevorzugte Einstellung handelt, wird der Name als Hashwert bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="ff086-151">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="ff086-152">Der Wert wird nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="ff086-152">The value isn't collected.</span></span> |
| <span data-ttu-id="ff086-153">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-153">>=5.0</span></span>        | <span data-ttu-id="ff086-154">Für den `set header` Befehl wird der Header Name festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ff086-154">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="ff086-155">Wenn es sich nicht um einen bekannten Header handelt, wird der Name mit einem Hashwert versehen.</span><span class="sxs-lookup"><span data-stu-id="ff086-155">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="ff086-156">Der Wert wird nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="ff086-156">The value isn't collected.</span></span> |
| <span data-ttu-id="ff086-157">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-157">>=5.0</span></span>        | <span data-ttu-id="ff086-158">Für den `connect` -Befehl, ob ein Sonderfall für `dotnet new webapi` verwendet wurde und, ob er per Preference umgangen wurde.</span><span class="sxs-lookup"><span data-stu-id="ff086-158">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="ff086-159">>= 5,0</span><span class="sxs-lookup"><span data-stu-id="ff086-159">>=5.0</span></span>        | <span data-ttu-id="ff086-160">Für alle HTTP-Befehle (z. b. Get, Post, Put), unabhängig davon, ob jede der Optionen angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="ff086-160">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="ff086-161">Die Werte der Optionen werden nicht erfasst.</span><span class="sxs-lookup"><span data-stu-id="ff086-161">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="ff086-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff086-162">Additional resources</span></span>

* [<span data-ttu-id="ff086-163">.NET Core SDK-Telemetrie</span><span class="sxs-lookup"><span data-stu-id="ff086-163">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="ff086-164">.Net Core-CLI von Telemetriedaten</span><span class="sxs-lookup"><span data-stu-id="ff086-164">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
