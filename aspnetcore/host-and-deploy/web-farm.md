---
title: Hosten von ASP.NET Core in einer Webfarm
author: rick-anderson
description: Hier erfahren Sie, wie Sie mehrere Instanzen einer ASP.NET Core-App mit gemeinsam genutzten Ressourcen in einer Webfarmumgebung hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057517"
---
# <a name="host-aspnet-core-in-a-web-farm"></a>Hosten von ASP.NET Core in einer Webfarm

Von [Chris Ross](https://github.com/Tratcher)

Eine *Webfarm* ist eine Gruppe von mindestens zwei Webservern (oder *Knoten*), die mehrere Instanzen einer App hostet. Wenn eine Webfarm Benutzeranforderungen empfängt, verteilt ein *Lastenausgleichsmodul* die Anforderungen auf die Knoten der Webfarm. Webfarmen verbessern:

* **Zuverlässigkeit/Verfügbarkeit:** Wenn bei mindestens einem Knoten ein Fehler auftritt, kann der Lastenausgleich Anforderungen zur weiteren Verarbeitung an andere funktionierende Knoten weiterleiten.
* **Kapazität/Leistung:** Mehrere Knoten können mehr Anforderungen verarbeiten als ein einzelner Server. Das Lastenausgleichsmodul verteilt den Workload, indem Anforderungen auf die Knoten verteilt werden.
* **Skalierbarkeit:** Wenn mehr oder weniger Kapazität benötigt wird, kann die Anzahl der aktiven Knoten entsprechend der Arbeitsauslastung erhöht oder verringert werden. Webfarmtechnologien wie [Azure App Service](https://azure.microsoft.com/services/app-service/) können Knoten automatisch auf Systemadministratoranforderungen hin hinzufügen oder entfernen bzw. diese Vorgänge komplett ohne manuelles Eingreifen ausführen.
* **Wartbarkeit**: Knoten einer Webfarm können auf einer Reihe von gemeinsamen Diensten basieren, wodurch die Systemverwaltung vereinfacht wird. Beispielsweise können die Knoten einer Webfarm auf einem einzigen Datenbankserver und einer gemeinsamen Netzwerkadresse für statische Ressourcen wie Images und herunterladbare Dateien basieren.

Dieses Thema beschreibt die Konfiguration und Abhängigkeiten für ASP.NET Core-Apps, die in einer Webfarm gehostet werden und auf freigegebenen Ressourcen basieren.

## <a name="general-configuration"></a>Allgemeine Konfiguration

<xref:host-and-deploy/index>  
Erfahren Sie, wie Sie Hostingumgebungen einrichten und ASP.NET Core-Apps bereitstellen. Konfigurieren Sie einen Prozess-Manager auf jedem Knoten der Webfarm, um App-Starts und -Neustarts zu automatisieren. Jeder Knoten benötigt die ASP.NET Core-Laufzeit. Weitere Informationen finden Sie in der Dokumentation [Hosten und Bereitstellen von ASP.NET Core](xref:host-and-deploy/index).

<xref:host-and-deploy/proxy-load-balancer>  
Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden

<xref:host-and-deploy/azure-apps/index>  
[Azure App Service](https://azure.microsoft.com/services/app-service/) ist ein [Microsoft Cloud Computing-Plattformdienst](https://azure.microsoft.com/) zum Hosten von Web-Apps. Dazu gehört auch ASP.NET Core. App Service ist eine vollständig verwaltete Plattform, die automatisches Skalieren, Lastenausgleich, Patchen und Continuous Deployment bietet.

## <a name="app-data"></a>App-Daten

Wenn eine App auf mehrere Instanzen skaliert wird, muss der App-Status möglicherweise über Knoten hinweg freigegeben werden. Im Falle eines Übergangsstatus sollten Sie [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) freigeben. Wenn der freigegebene Status Persistenz erfordert, speichern Sie ihn in einer Datenbank.

## <a name="required-configuration"></a>Erforderliche Konfiguration

Schutz von Daten und Zwischenspeichern benötigen eine in einer Webfarm bereitgestellte App-Konfiguration.

### <a name="data-protection"></a>Schutz von Daten

Der [ASP.NET Core-Datenschutz](xref:security/data-protection/introduction) wird von Apps verwendet, um Daten zu schützen. Der Schutz von Daten basiert auf einem Satz kryptografischer Schlüssel in einer *Schlüsselsammlung*. Wenn das System zum Schutz von Daten initialisiert wird, werden [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) angewendet, die die Schlüsselsammlung lokal speichern. In der Standardkonfiguration wird eine eindeutige Schlüsselsammlung auf jedem Knoten der Webfarm gespeichert. Folglich kann kein Webfarmknoten Daten entschlüsseln, die von einer App auf einem anderen Knoten verschlüsselt werden. Die Standardkonfiguration eignet sich in der Regel nicht für das Hosten von Apps in einer Webfarm. Eine Alternative zur Implementierung einer freigegebenen Schlüsselsammlung besteht darin, Benutzeranforderungen immer an denselben Knoten weiterzuleiten. Weitere Informationen zur Systemkonfiguration zum Schutz von Daten für Webfarmbereitstellungen finden Sie unter <xref:security/data-protection/configuration/overview>.

### <a name="caching"></a>Zwischenspeicherung

In einer Webfarmumgebung muss der Zwischenspeichermechanismus zwischengespeicherte Elemente über die Webfarmknoten hinweg freigeben. Das Zwischenspeichern muss auf einem Redis Cache, einer freigegebenen SQL Server-Datenbank oder einer benutzerdefinierten Zwischenspeicherimplementierung basieren, die zwischengespeicherte Elemente über die Webfarm hinweg freigibt. Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.

## <a name="dependent-components"></a>Abhängige Komponenten

Die folgenden Szenarien erfordern keine zusätzliche Konfiguration, hängen jedoch von Technologien ab, die eine Webfarmkonfiguration benötigen.

| Szenario | Abhängig von&hellip; |
| -------- | ------------------- |
| Authentifizierung | Schutz von Daten (siehe <xref:security/data-protection/configuration/overview>).<br><br>Weitere Informationen finden Sie unter <xref:security/authentication/cookie> und <xref:security/cookie-sharing>. |
| Identity | Authentifizierung und Datenbankkonfiguration.<br><br>Weitere Informationen finden Sie unter <xref:security/authentication/identity>. |
| Sitzung | Schutz von Daten (verschlüsselte cookies) (siehe <xref:security/data-protection/configuration/overview>) und Zwischenspeichern (siehe <xref:performance/caching/distributed>)<br><br>Weitere Informationen finden Sie unter [Sitzungs- und Zustandsverwaltung: Sitzungszustand](xref:fundamentals/app-state#session-state). |
| TempData | Schutz von Daten (verschlüsselte cookies) (siehe <xref:security/data-protection/configuration/overview>) oder Sitzung (siehe [Sitzungs- und Zustandsverwaltung: Sitzungszustand](xref:fundamentals/app-state#session-state)).<br><br>Weitere Informationen finden Sie unter [Sitzungs- und Zustandsverwaltung: TempData](xref:fundamentals/app-state#tempdata). |
| Fälschungssicherheit | Schutz von Daten (siehe <xref:security/data-protection/configuration/overview>).<br><br>Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>. |

## <a name="troubleshoot"></a>Problembehandlung

### <a name="data-protection-and-caching"></a>Schutz von Daten und Zwischenspeichern

Wenn der Schutz von Daten oder Zwischenspeichern für eine Webfarmumgebung nicht konfiguriert wurde, treten beim Verarbeiten von Anforderungen zeitweilig Fehler auf. Dies geschieht, weil Knoten nicht dieselben Ressourcen teilen und Benutzeranforderungen nicht immer an denselben Knoten zurückgeleitet werden.

Gehen Sie beispielsweise von einem Benutzer aus, der sich mithilfe der cookieauthentifizierung bei der App anmeldet. Der Benutzer meldet sich in der App auf einem Webfarmknoten an. Wenn seine nächste Anforderung auf demselben Knoten eintrifft, auf dem er sich angemeldet hat, kann die App das Authentifizierungscookie entschlüsseln und den Zugriff auf die App-Ressource gewähren. Wenn seine nächste Anforderung auf einem anderen Knoten eintrifft, kann die App das Authentifizierungscookie nicht auf dem Knoten entschlüsseln, auf dem der Benutzer angemeldet ist, und die Autorisierung für die angeforderte Ressource schlägt fehl.

Wenn eines der folgenden Symptome **zeitweilig** auftritt, ist das Problem in der Regel auf eine falsche Konfiguration zum Schutz von Daten oder zum Zwischenspeichern für eine Webfarmumgebung zurückgeführt:

* Authentifizierungsfehler: Das Authentifizierungscookie ist falsch konfiguriert oder kann nicht entschlüsselt werden. OAuth (Facebook, Microsoft und Twitter) oder OpenIdConnect-Anmeldungen schlagen mit der Fehlermeldung „Korrelationsfehler“ fehl.
* Authentifizierungsfehler: Identity geht verloren.
* Im Sitzungszustand gehen Daten verloren.
* Zwischengespeicherte Elemente werden nicht angezeigt.
* Fehler bei TempData.
* Fehler bei POSTs: Die Überprüfung zur Fälschungssicherheit schlägt fehl.

Weitere Informationen zur Konfiguration zum Schutz von Daten für Webfarmbereitstellungen finden Sie unter <xref:security/data-protection/configuration/overview>. Weitere Informationen zur Zwischenspeicherkonfiguration für Webfarmbereitstellungen finden Sie unter <xref:performance/caching/distributed>.

## <a name="obtain-data-from-apps"></a>Abrufen von Daten aus Apps

Wenn die Webfarm-Apps in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals. Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Benutzerdefinierte Skripterweiterung für Windows:](/azure/virtual-machines/extensions/custom-script-windows) Diese Erweiterung lädt Skripts auf virtuellen Azure-Computern herunter und führt sie dort aus. Dies ist für die Konfiguration und Softwareinstallation nach der Bereitstellung nützlich.
* <xref:host-and-deploy/proxy-load-balancer>
 