---
title: (Data Protection key management and lifetime in ASP.NET Core) Gültigkeitsdauer und Verwaltung von Schlüsseln für den Schutz von Daten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Verwaltung von Daten und die Lebensdauer in ASP.net Core.
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
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 1303c5c2c993f1d20383457666aebfa2a583e938
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053006"
---
# <a name="data-protection-key-management-and-lifetime-in-aspnet-core"></a>(Data Protection key management and lifetime in ASP.NET Core) Gültigkeitsdauer und Verwaltung von Schlüsseln für den Schutz von Daten in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="key-management"></a>Schlüsselverwaltung

Die APP versucht, ihre Betriebsumgebung zu erkennen und die Schlüssel Konfiguration eigenständig zu verarbeiten.

1. Wenn die app in Azure- [apps](https://azure.microsoft.com/services/app-service/)gehostet wird, werden Schlüssel im Ordner *%Home%\ASP.net\dataschutz-Keys* persistent gespeichert. Dieser Ordner wird von einem Netzwerkspeicher unterstützt und mit allen Computern, auf denen die App gehostet wird, synchronisiert.
   * Ruhende Schlüssel werden nicht geschützt.
   * Der Ordner " *DataProtection-Keys* " stellt den Schlüsselring für alle Instanzen einer APP in einem einzelnen Bereitstellungs Slot bereit.
   * Separate Bereitstellungsslots, wie Staging und Produktion, verwenden keinen gemeinsamen Schlüsselbund. Beim Austauschen zwischen Bereitstellungs Slots, z. B. Austauschen der Staging-in die Produktionsumgebung oder Verwenden von A/B-Tests, kann jede APP, die den Datenschutz verwendet, gespeicherte Daten nicht mithilfe des Schlüsselrings im vorherigen Slot entschlüsseln. Dies führt dazu, dass Benutzer von einer App abgemeldet werden, die die Standard-ASP.net Core cookie Authentifizierung verwendet, da Sie zum Schutz Ihrer e den Schutz von Daten verwendet cookie . Wenn Sie Slot-unabhängige Schlüssel Ringe wünschen, verwenden Sie einen externen Schlüsselring Anbieter, wie z. b. Azure BLOB Storage, Azure Key Vault, einen SQL-Speicher oder redis Cache.

1. Wenn das Benutzerprofil verfügbar ist, werden Schlüssel im Ordner " *%LocalAppData%\ASP.net\dataschutz-Keys* " beibehalten. Wenn das Betriebssystem Windows ist, werden die Schlüssel im Ruhezustand mithilfe von DPAPI verschlüsselt.

   Das [setProfileEnvironment-Attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) des App-Pools muss ebenfalls aktiviert sein. Der Standardwert von `setProfileEnvironment` ist `true`. In einigen Szenarien (z.B. Windows-Betriebssystem) ist `setProfileEnvironment` auf `false` festgelegt. Gehen Sie folgendermaßen vor, wenn Schlüssel nicht wie erwartet im Benutzerprofilverzeichnis gespeichert werden:

   1. Navigieren Sie zum Ordner *%windir%/system32/inetsrv/config* .
   1. Öffnen Sie die Datei *applicationHost.config* .
   1. Suchen Sie das Element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
   1. Bestätigen Sie, dass das `setProfileEnvironment`-Attribut nicht vorhanden ist, das standardmäßig den Wert `true` aufweist, oder legen Sie den Wert des Attributs explizit auf `true` fest.

1. Wenn die app in IIS gehostet wird, werden Schlüssel in der HKLM-Registrierung in einem speziellen Registrierungsschlüssel gespeichert, der nur für das Arbeitsprozess Konto übernommen wird. Schlüssel werden im Ruhezustand mit DPAPI verschlüsselt.

1. Wenn keine dieser Bedingungen zutrifft, werden Schlüssel nicht außerhalb des aktuellen Prozesses beibehalten. Wenn der Prozess heruntergefahren wird, gehen alle generierten Schlüssel verloren.

Der Entwickler hat stets die volle Kontrolle und kann überschreiben, wie und wo Schlüssel gespeichert werden. Die ersten drei oben aufgeführten Optionen sollten für die meisten apps gute Standardwerte bereitstellen, ähnlich wie die ASP.net **\<machineKey>** -automatischen Generierungs Routinen in der Vergangenheit. Die abschließende Option Fall Back ist das einzige Szenario, bei dem der Entwickler die [Konfiguration](xref:security/data-protection/configuration/overview) vorab angeben muss, wenn er eine Schlüssel Persistenz wünschen, aber dieser Fall Back tritt nur in seltenen Fällen auf.

Beim Hosten in einem docker-Container sollten Schlüssel in einem Ordner gespeichert werden, der ein docker-Volume ist (ein frei gegebenes Volume oder ein von einem Host bereitgestelltes Volume, das über die Lebensdauer des Containers hinaus bleibt) oder in einem externen Anbieter wie [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [redis](https://redis.io/). Ein externer Anbieter ist auch bei Webfarm Szenarios nützlich, wenn apps nicht auf ein frei gegebenes Netzwerk Volume zugreifen können (Weitere Informationen finden Sie unter [persistkeystofile System](xref:security/data-protection/configuration/overview#persistkeystofilesystem) ).

> [!WARNING]
> Wenn der Entwickler die oben beschriebenen Regeln überschreibt und auf das Datenschutzsystem in einem bestimmten schlüsselrepository verweist, wird die automatische Verschlüsselung von Schlüsseln im Ruhezustand deaktiviert. Der ruhende Schutz kann über die [Konfiguration](xref:security/data-protection/configuration/overview)wieder aktiviert werden.

## <a name="key-lifetime"></a>Schlüssel Lebensdauer

Schlüssel haben standardmäßig eine Lebensdauer von 90 Tagen. Wenn ein Schlüssel abläuft, generiert die APP automatisch einen neuen Schlüssel und legt den neuen Schlüssel als aktiven Schlüssel fest. Solange abgekoppelte Schlüssel im System verbleiben, kann Ihre APP alle mit Ihnen geschützten Daten entschlüsseln. Weitere Informationen finden Sie unter [Schlüsselverwaltung](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling) .

## <a name="default-algorithms"></a>Standardalgorithmen

Der standardmäßige Nutz Last Schutz Algorithmus ist AES-256-CBC für Vertraulichkeit und HMACSHA256 für Authentizität. Ein 512-Bit-Hauptschlüssel, der alle 90 Tage geändert wird, wird verwendet, um die beiden Unterschlüssel, die für diese Algorithmen verwendet werden, pro Nutzlast abzuleiten. Weitere Informationen finden Sie [unter Unterschlüssel Ableitung](xref:security/data-protection/implementation/subkeyderivation#additional-authenticated-data-and-subkey-derivation) .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/data-protection/extensibility/key-management>
* <xref:host-and-deploy/web-farm>
