---
title: Umgebungstaghilfsprogramm in ASP.NET Core
author: pkellner
description: Definition des Umgebungstaghilfsprogramms für ASP.NET Core, einschließlich aller Eigenschaften
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 4f8330521bb9114f6639c1889cc15ebd18adc0ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061131"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>Umgebungstaghilfsprogramm in ASP.NET Core

Von [Peter Kellner](https://peterkellner.net) und [Hisham Bin Ateya](https://twitter.com/hishambinateya)

Das Umgebungs-taghilfsprogramm rendert den eingeschlossenen Inhalt bedingt basierend auf der aktuellen Host [Umgebung](xref:fundamentals/environments). Das einzelne Attribut `names` des Environment-Taghilfsprogramm ist eine durch Trennzeichen getrennte Liste von Umgebungsnamen. Wenn die Namen der bereitgestellten Umgebung der aktuellen Umgebung entsprechen, wird der eingeschlossene Inhalt gerendert.

Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

## <a name="environment-tag-helper-attributes"></a>Attribute von Umgebungstaghilfsprogrammen

### <a name="names"></a>Namen

`names` akzeptiert den Namen einer einzelnen Hostingumgebung oder eine durch Trennzeichen getrennte Liste mit Namen von Hostingumgebungen, die das Rendering des eingeschlossenen Inhalts auslösen.

Umgebungswerte werden im Vergleich zum aktuellen Wert von [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) zurückgegeben. Bei dem Vergleich wird die Groß-/Kleinschreibung ignoriert.

Im folgenden Beispiel wird ein Environment-Taghilfsprogramm verwendet: Der Inhalt wird wiedergegeben, wenn es sich bei der Hostumgebung um eine Staging- oder Produktionsumgebung handelt:

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>Die Attribute „include“ und „exclude“

`include`& `exclude` Attribute steuern das Rendern des eingeschlossenen Inhalts basierend auf den eingeschlossenen oder ausgeschlossenen Namen der Hostingumgebung.

### <a name="include"></a>include

Die `include`-Eigenschaft zeigt ein ähnliches Verhalten wie das Attribut `names`. Eine im Attributwert `include` aufgeführte Umgebung muss der App-Hostingumgebung ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) entsprechen, damit der Inhalt des Tags `<environment>` gerendert werden kann.

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>Ausschließen

Im Gegensatz zum Attribut `include` wird der Inhalt des `<environment>`-Tags gerendert, wenn die Hostingumgebung nicht einer Umgebung entspricht, die im Attributwert `exclude` aufgeführt wird.

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/environments>
