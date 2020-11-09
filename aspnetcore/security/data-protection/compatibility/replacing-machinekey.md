---
title: Ersetzen Sie ASP.net machineKey in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie machineKey in ASP.net ersetzen, um die Verwendung eines neuen und sichereren Datenschutzsystems zu ermöglichen.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053058"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="154f0-103">Ersetzen Sie ASP.net machineKey in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="154f0-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="154f0-104">Die Implementierung des- `<machineKey>` Elements in ASP.net kann [ersetzt werden](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="154f0-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="154f0-105">Dadurch können die meisten Aufrufe von ASP.net-kryptografieroutinen über einen Mechanismus zum Schutz von Daten, einschließlich des neuen Datenschutzsystems, weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="154f0-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="154f0-106">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="154f0-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="154f0-107">Das neue Datenschutzsystem kann nur in einer vorhandenen ASP.NET-Anwendung installiert werden, die auf .NET 4.5.1 oder höher ausgerichtet ist.</span><span class="sxs-lookup"><span data-stu-id="154f0-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="154f0-108">Die Installation schlägt fehl, wenn die Anwendung .NET 4,5 oder niedriger als Zielversion verwendet.</span><span class="sxs-lookup"><span data-stu-id="154f0-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="154f0-109">Um das neue Datenschutzsystem in einem vorhandenen ASP.NET 4.5.1 +-Projekt zu installieren, installieren Sie das Paket Microsoft.AspNetCore.DataProtection.Systemweb.</span><span class="sxs-lookup"><span data-stu-id="154f0-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="154f0-110">Dadurch wird das Datenschutzsystem mithilfe der [Standard Konfigurations](xref:security/data-protection/configuration/default-settings) Einstellungen instanziiert.</span><span class="sxs-lookup"><span data-stu-id="154f0-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="154f0-111">Wenn Sie das Paket installieren, fügt es eine Zeile in *Web.config* ein, die ASP.net anweist, es für [die meisten Kryptografievorgänge](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)zu verwenden, einschließlich der Formular Authentifizierung, des Ansichts Zustands und der Aufrufe von machineKey. Protect.</span><span class="sxs-lookup"><span data-stu-id="154f0-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="154f0-112">Die eingefügte Zeile wird wie folgt gelesen.</span><span class="sxs-lookup"><span data-stu-id="154f0-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="154f0-113">Sie können erkennen, ob das neue Datenschutzsystem aktiv ist, indem Sie Felder wie überprüfen `__VIEWSTATE` , die mit "CfDJ8" beginnen sollen, wie im folgenden Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="154f0-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="154f0-114">"CfDJ8" ist die Base64-Darstellung des Magic "09 F0, F0"-Headers, der eine vom Datenschutzsystem geschützte Nutzlast identifiziert.</span><span class="sxs-lookup"><span data-stu-id="154f0-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="154f0-115">Paketkonfiguration</span><span class="sxs-lookup"><span data-stu-id="154f0-115">Package configuration</span></span>

<span data-ttu-id="154f0-116">Das Datenschutzsystem wird mit einer Standardkonfiguration für die Konfiguration von NULL instanziiert.</span><span class="sxs-lookup"><span data-stu-id="154f0-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="154f0-117">Da jedoch standardmäßig Schlüssel im lokalen Dateisystem beibehalten werden, funktioniert dies nicht für Anwendungen, die in einer Farm bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="154f0-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="154f0-118">Um dieses Problem zu beheben, können Sie die Konfiguration bereitstellen, indem Sie einen Typ erstellen, der die Unterklassen dataschutzstartup und die Konfiguration der konfigurierungsmethode überschreibt.</span><span class="sxs-lookup"><span data-stu-id="154f0-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="154f0-119">Im folgenden finden Sie ein Beispiel für einen benutzerdefinierten Starttyp für den Datenschutz, der sowohl die beibehaltenen Schlüssel als auch die Verschlüsselung im Ruhezustand konfiguriert hat.</span><span class="sxs-lookup"><span data-stu-id="154f0-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="154f0-120">Außerdem wird die standardmäßige App-Isolations Richtlinie durch Bereitstellen eines eigenen Anwendungs Namens überschrieben.</span><span class="sxs-lookup"><span data-stu-id="154f0-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="154f0-121">Sie können auch `<machineKey applicationName="my-app" ... />` anstelle eines expliziten Aufrufens von "stapplicationname" verwenden.</span><span class="sxs-lookup"><span data-stu-id="154f0-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="154f0-122">Dies ist eine praktische Methode, um zu verhindern, dass der Entwickler einen von dataschutzstartup abgeleiteten Typ erstellt, wenn der Anwendungsname von allen Elementen, die konfiguriert werden sollen, festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="154f0-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="154f0-123">Um diese benutzerdefinierte Konfiguration zu aktivieren, wechseln Sie zurück zu Web.config, und suchen Sie nach dem Element, das von `<appSettings>` der Paketinstallation zur Konfigurationsdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="154f0-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="154f0-124">Es sieht wie das folgende Markup aus:</span><span class="sxs-lookup"><span data-stu-id="154f0-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="154f0-125">Geben Sie den leeren Wert mit dem durch die Assembly qualifizierten Namen des von dataschutzstartup abgeleiteten Typs ein, den Sie soeben erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="154f0-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="154f0-126">Wenn der Name der Anwendung dataschutzdemo lautet, würde dies wie folgt aussehen.</span><span class="sxs-lookup"><span data-stu-id="154f0-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="154f0-127">Das neu konfigurierte Datenschutzsystem ist jetzt für die Verwendung in der Anwendung bereit.</span><span class="sxs-lookup"><span data-stu-id="154f0-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
