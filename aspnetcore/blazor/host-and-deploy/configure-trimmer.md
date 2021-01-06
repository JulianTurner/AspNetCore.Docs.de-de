---
title: Konfigurieren des Trimmers für ASP.NET Core Blazor
author: guardrex
description: Hier erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) (Trimmer) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055801"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Konfigurieren des Trimmers für ASP.NET Core Blazor

Von [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly führt eine [IL-Kürzung (Intermediate Language, Zwischensprache)](/dotnet/standard/managed-code#intermediate-language--execution) aus, um die Größe der veröffentlichten Ausgabe zu verringern.

Durch das Kürzen einer App wird die Größe optimiert. Dies kann jedoch auch negative Auswirkungen haben. Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Trimmer dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind. Zur Kürzung solcher Apps muss der Trimmer über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.

Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung veröffentlichte Ausgaben häufig zu testen.

Das Kürzen kann für .NET-Apps deaktiviert werden, indem die MSBuild-Eigenschaft `PublishTrimmed` in der Projektdatei der App auf `false` festgelegt wird:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
Weitere Optionen zum Konfigurieren des Trimmers finden Sie unter [Kürzungsoptionen](/dotnet/core/deploying/trimming-options).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Kürzen eigenständiger Bereitstellungen und ausführbarer Dateien](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
