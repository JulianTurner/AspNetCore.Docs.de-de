---
title: Ersetzen Sie ASP.net machineKey in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie machineKey in ASP.net ersetzen, um die Verwendung eines neuen und sichereren Datenschutzsystems zu ermöglichen.
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053058"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Ersetzen Sie ASP.net machineKey in ASP.net Core

<a name="compatibility-replacing-machinekey"></a>

Die Implementierung des- `<machineKey>` Elements in ASP.net kann [ersetzt werden](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Dadurch können die meisten Aufrufe von ASP.net-kryptografieroutinen über einen Mechanismus zum Schutz von Daten, einschließlich des neuen Datenschutzsystems, weitergeleitet werden.

## <a name="package-installation"></a>Paketinstallation

> [!NOTE]
> Das neue Datenschutzsystem kann nur in einer vorhandenen ASP.NET-Anwendung installiert werden, die auf .NET 4.5.1 oder höher ausgerichtet ist. Die Installation schlägt fehl, wenn die Anwendung .NET 4,5 oder niedriger als Zielversion verwendet.

Um das neue Datenschutzsystem in einem vorhandenen ASP.NET 4.5.1 +-Projekt zu installieren, installieren Sie das Paket Microsoft.AspNetCore.DataProtection.Systemweb. Dadurch wird das Datenschutzsystem mithilfe der [Standard Konfigurations](xref:security/data-protection/configuration/default-settings) Einstellungen instanziiert.

Wenn Sie das Paket installieren, fügt es eine Zeile in *Web.config* ein, die ASP.net anweist, es für [die meisten Kryptografievorgänge](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)zu verwenden, einschließlich der Formular Authentifizierung, des Ansichts Zustands und der Aufrufe von machineKey. Protect. Die eingefügte Zeile wird wie folgt gelesen.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Sie können erkennen, ob das neue Datenschutzsystem aktiv ist, indem Sie Felder wie überprüfen `__VIEWSTATE` , die mit "CfDJ8" beginnen sollen, wie im folgenden Beispiel gezeigt. "CfDJ8" ist die Base64-Darstellung des Magic "09 F0, F0"-Headers, der eine vom Datenschutzsystem geschützte Nutzlast identifiziert.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Paketkonfiguration

Das Datenschutzsystem wird mit einer Standardkonfiguration für die Konfiguration von NULL instanziiert. Da jedoch standardmäßig Schlüssel im lokalen Dateisystem beibehalten werden, funktioniert dies nicht für Anwendungen, die in einer Farm bereitgestellt werden. Um dieses Problem zu beheben, können Sie die Konfiguration bereitstellen, indem Sie einen Typ erstellen, der die Unterklassen dataschutzstartup und die Konfiguration der konfigurierungsmethode überschreibt.

Im folgenden finden Sie ein Beispiel für einen benutzerdefinierten Starttyp für den Datenschutz, der sowohl die beibehaltenen Schlüssel als auch die Verschlüsselung im Ruhezustand konfiguriert hat. Außerdem wird die standardmäßige App-Isolations Richtlinie durch Bereitstellen eines eigenen Anwendungs Namens überschrieben.

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
> Sie können auch `<machineKey applicationName="my-app" ... />` anstelle eines expliziten Aufrufens von "stapplicationname" verwenden. Dies ist eine praktische Methode, um zu verhindern, dass der Entwickler einen von dataschutzstartup abgeleiteten Typ erstellt, wenn der Anwendungsname von allen Elementen, die konfiguriert werden sollen, festgelegt wurde.

Um diese benutzerdefinierte Konfiguration zu aktivieren, wechseln Sie zurück zu Web.config, und suchen Sie nach dem Element, das von `<appSettings>` der Paketinstallation zur Konfigurationsdatei hinzugefügt wurde. Es sieht wie das folgende Markup aus:

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

Geben Sie den leeren Wert mit dem durch die Assembly qualifizierten Namen des von dataschutzstartup abgeleiteten Typs ein, den Sie soeben erstellt haben. Wenn der Name der Anwendung dataschutzdemo lautet, würde dies wie folgt aussehen.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Das neu konfigurierte Datenschutzsystem ist jetzt für die Verwendung in der Anwendung bereit.
