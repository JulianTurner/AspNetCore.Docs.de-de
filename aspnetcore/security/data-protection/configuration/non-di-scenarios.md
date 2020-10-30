---
title: Szenarien ohne Unterstützung der Abhängigkeitsinjektion für den Schutz von Daten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Datenschutz Szenarien unterstützen, in denen Sie keinen von der Abhängigkeitsinjektion bereitgestellten Dienst verwenden oder nicht verwenden möchten.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052824"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>Szenarien ohne Unterstützung der Abhängigkeitsinjektion für den Schutz von Daten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das ASP.net Core-Datenschutzsystem wird normalerweise [einem Dienst Container hinzugefügt](xref:security/data-protection/consumer-apis/overview) und von abhängigen Komponenten über Abhängigkeitsinjektion (di) genutzt. Es gibt jedoch Fälle, in denen dies nicht möglich oder erwünscht ist, insbesondere beim Importieren des Systems in eine vorhandene app.

Zur Unterstützung dieser Szenarien bietet das Paket [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) einen konkreten Typ, [dataschutzprovider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), der eine einfache Möglichkeit zur Verwendung des Datenschutzes ohne Verwendung von di bietet. Der- `DataProtectionProvider` Typ implementiert [idataschutzprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider). Bei `DataProtectionProvider` der Erstellung muss nur eine [directoriyinfo](/dotnet/api/system.io.directoryinfo) -Instanz bereitgestellt werden, um anzugeben, wo die Kryptografieschlüssel des Anbieters gespeichert werden sollen, wie im folgenden Codebeispiel gezeigt:

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

Standardmäßig verschlüsselt der `DataProtectionProvider` konkrete Typ das rohschlüssel Material nicht, bevor es im Dateisystem gespeichert wird. Dies dient zur Unterstützung von Szenarios, in denen der Entwickler auf eine Netzwerkfreigabe verweist und das Datenschutzsystem nicht automatisch einen geeigneten Verschlüsselungsmechanismus für ruhende Schlüssel ableiten kann.

Außerdem isoliert der `DataProtectionProvider` konkrete Typ standardmäßig keine [apps](xref:security/data-protection/configuration/overview#per-application-isolation) . Alle apps, die das gleiche Schlüssel Verzeichnis verwenden, können Nutzlasten gemeinsam nutzen, solange Ihre [Zweck Parameter](xref:security/data-protection/consumer-apis/purpose-strings) übereinstimmen.

Der [dataschutzprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) -Konstruktor akzeptiert einen optionalen Konfigurations Rückruf, der zum Anpassen des Verhaltens des Systems verwendet werden kann. Das folgende Beispiel zeigt die Wiederherstellung der Isolation mit einem expliziten [Aufruf von "](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)" "" Das Beispiel veranschaulicht auch das Konfigurieren des Systems zum automatischen Verschlüsseln von persistenten Schlüsseln mithilfe von Windows DPAPI. Wenn das Verzeichnis auf eine UNC-Freigabe verweist, können Sie ein frei gegebenes Zertifikat auf allen relevanten Computern verteilen und das System so konfigurieren, dass die Zertifikat basierte Verschlüsselung mit einem Befehl von " [protectkeyswithcertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)" verwendet wird.

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> `DataProtectionProvider`Es ist aufwendig, Instanzen des konkreten Typs zu erstellen. Wenn eine APP mehrere Instanzen dieses Typs verwaltet und alle das gleiche Schlüsselspeicher Verzeichnis verwenden, kann die Leistung der APP beeinträchtigt werden. Wenn Sie den- `DataProtectionProvider` Typ verwenden, empfiehlt es sich, diesen Typ einmal zu erstellen und so weit wie möglich wiederzuverwenden. Der `DataProtectionProvider` Typ und alle von ihm erstellten [idataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) -Instanzen sind für mehrere Aufrufer Thread sicher.
