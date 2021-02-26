---
title: Konfigurieren des Trimmers für ASP.NET Core Blazor
author: guardrex
description: Hier erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) (Trimmer) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975213"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Konfigurieren des Trimmers für ASP.NET Core Blazor

Blazor WebAssembly führt eine [IL-Kürzung (Intermediate Language, Zwischensprache)](/dotnet/standard/managed-code#intermediate-language--execution) aus, um die Größe der veröffentlichten Ausgabe zu verringern. Standardmäßig erfolgt die Kürzung beim Veröffentlichen einer App.

Das Kürzen kann negative Auswirkungen haben. In Apps, die Reflexion verwenden, kann der Trimmer oft nicht die erforderlichen Typen für die Reflexion zur Laufzeit bestimmen. Zur Kürzung von Apps, die die Reflektion verwenden, muss der Trimmer über die Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks der App erforderlich sind, von denen die App abhängig ist. Der Trimmer kann auch nicht auf das dynamische Verhalten einer App zur Laufzeit reagieren. Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, testen Sie während der Entwicklung regelmäßig die veröffentlichte Ausgabe.

Informationen zum Konfigurieren des Trimmers finden Sie im Artikel [Kürzungsoptionen](/dotnet/core/deploying/trimming-options) in der Dokumentation zu .NET-Grundlagen, die zudem Leitlinien zu folgenden Themen enthält:

* Deaktivieren der Kürzung für die gesamte App mit der Eigenschaft `<PublishTrimmed>` in der Projektdatei
* Steuern, wie aggressiv nicht verwendete IL vom Trimmer verworfen wird
* Verhindern, dass der Trimmer bestimmte Assemblys kürzt
* „Root“-Assemblys (Stammassemblys) für die Kürzung
* Oberflächenwarnungen für reflektierte Typen, indem die `<SuppressTrimAnalysisWarnings>`-Eigenschaft in der Projektdatei auf `false` festgelegt wird.
* Steuern der Symbolkürzung und Debuggerunterstützung
* Festlegen von Trimmerfeatures für Bibliotheksfeatures des Trimmingframeworks

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Kürzen eigenständiger Bereitstellungen und ausführbarer Dateien](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
