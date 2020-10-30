---
title: ASP.net Core Datenschutz
author: rick-anderson
description: Erfahren Sie mehr über das Konzept des Schutzes von Daten und die Entwurfs Prinzipien der ASP.net Core Datenschutz-APIs.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
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
uid: security/data-protection/introduction
ms.openlocfilehash: 5fd5676b286e758f0648d78bf8cb4171e7a98f60
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051693"
---
# <a name="aspnet-core-data-protection"></a>ASP.net Core Datenschutz

Webanwendungen müssen häufig sicherheitsrelevante Daten speichern. Windows stellt DPAPI für Desktop Anwendungen bereit, ist aber für Webanwendungen ungeeignet. Der ASP.net Core Datenschutz Stapel bietet eine einfache, leicht zu verwendende kryptografieapi, mit der ein Entwickler Daten schützen kann, einschließlich Schlüsselverwaltung und Rotation.

Der ASP.net Core Datenschutz Stapel ist so konzipiert, dass er als langfristiger Ersatz für das &lt; machineKey- &gt; Element in ASP.NET 1. x-4. x fungiert. Es wurde so konzipiert, dass viele der Unzulänglichkeiten des alten kryptografiestapels behandelt werden, während gleichzeitig eine sofort Einsatz orientierte Lösung für die Mehrzahl der Anwendungsfälle bereitgestellt wird, auf denen moderne Anwendungen wahrscheinlich stoßen.

## <a name="problem-statement"></a>Problembeschreibung

Die allgemeine Problem Erklärung kann in einem einzelnen Satz kurz gesagt werden: Ich muss vertrauenswürdige Informationen für den späteren Abruf beibehalten, aber ich vertraue dem Persistenzmechanismus nicht. In webbegriffen könnte dies wie folgt lauten: "Ich muss den vertrauenswürdigen Zustand über einen nicht vertrauenswürdigen Client Roundtrip".

Das kanonische Beispiel hierfür ist ein Authentifizierungs- cookie oder bearertoken. Der Server generiert das Token "I am Groot und hat XYZ-Berechtigungen" und übergibt es an den Client. Zu einem späteren Zeitpunkt zeigt der Client das Token wieder dem Server an, der Server muss jedoch sicher sein, dass der Client das Token nicht gefälscht hat. Daher die erste Anforderung: Authentizität (auch bekannt als Integrität, Manipulationsschutz).

Da der beibehaltene Zustand vom Server als vertrauenswürdig eingestuft wird, erwarten wir, dass dieser Zustand Informationen enthält, die spezifisch für die Betriebsumgebung sind. Dies kann ein Dateipfad, eine Berechtigung, ein Handle oder ein anderer indirekter Verweis oder ein anderer Teil der serverspezifischen Daten sein. Diese Informationen sollten in der Regel nicht einem nicht vertrauenswürdigen Client offengelegt werden. Daher ist dies die zweite Anforderung: Vertraulichkeit.

Da moderne Anwendungen in Komponenten gegliedert werden, sehen wir, dass einzelne Komponenten dieses System ohne Rücksicht auf andere Komponenten des Systems nutzen möchten. Wenn beispielsweise eine bearertokenkomponente diesen Stapel verwendet, sollte Sie ohne Störungen von einem Anti-CSRF-Mechanismus betrieben werden, der möglicherweise auch denselben Stapel verwendet. Daher ist dies die letzte Anforderung: Isolation.

Wir können weitere Einschränkungen bereitstellen, um den Umfang unserer Anforderungen einzuschränken. Wir gehen davon aus, dass alle Dienste, die innerhalb des Kryptosystems ausgeführt werden, gleichermaßen vertrauenswürdig sind und dass die Daten nicht außerhalb der Dienste in unserer direkten Kontrolle generiert oder genutzt werden müssen. Außerdem ist es erforderlich, dass die Vorgänge so schnell wie möglich sind, da jede Anforderung an den Webdienst ein-oder mehrmals durch das Kryptosystem durchlaufen kann. Dadurch eignet sich die symmetrische Kryptografie ideal für unser Szenario, und wir können die asymmetrische Kryptografie bis zu einem solchen Zeitpunkt für die benötigte Zeit Rabatt.

## <a name="design-philosophy"></a>Entwurfs Philosophie

Wir haben damit begonnen, Probleme mit dem vorhandenen Stapel zu identifizieren. Nachdem wir dies hatten, haben wir die Landschaft vorhandener Lösungen befragt und davon abschlossen, dass keine vorhandene Lösung über die von uns gesuchten Funktionen verfügt. Anschließend haben wir eine Lösung auf Grundlage verschiedener Leitfäden entwickelt.

* Das System sollte die Einfachheit der Konfiguration ermöglichen. Im Idealfall wäre das System eine Null-Konfiguration, und Entwickler könnten die Ausführung erreichen. In Situationen, in denen Entwickler einen bestimmten Aspekt konfigurieren müssen (z. b. das schlüsselrepository), sollte berücksichtigt werden, dass diese spezifischen Konfigurationen einfach sind.

* Bietet eine einfache API für den Kunden Zugriff. Die APIs sollten auf einfache Weise ordnungsgemäß und schwer zu verwenden sein.

* Entwickler sollten keine Schlüssel Verwaltungs Prinzipien erlernen. Das System sollte die Algorithmusauswahl und die Schlüssel Lebensdauer im Auftrag des Entwicklers verarbeiten. Im Idealfall sollte der Entwickler nie einmal auf das rohschlüssel Material zugreifen können.

* Schlüssel sollten nach Möglichkeit im Ruhezustand geschützt werden. Das System sollte einen geeigneten Standardschutz Mechanismus ermitteln und ihn automatisch anwenden.

Mit diesen Prinzipien haben wir einen einfachen, [leicht zu verwendenden](xref:security/data-protection/using-data-protection) Datenschutz Stapel entwickelt.

Die ASP.net Core-Datenschutz-APIs sind nicht in erster Linie für die unbegrenzte Persistenz von vertraulichen Nutzlasten vorgesehen. Andere Technologien wie [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) und [Azure Rights Management](/rights-management/) sind besser für das Szenario der unbegrenzten Speicherung geeignet und verfügen über die entsprechenden leistungsstarken Schlüssel Verwaltungsfunktionen. Dies bedeutet, dass ein Entwickler nicht daran hindert, die ASP.net Core Datenschutz-APIs für den langfristigen Schutz vertraulicher Daten zu verwenden.

## <a name="audience"></a>Zielgruppe

Das Datenschutzsystem ist in fünf Haupt Pakete unterteilt. Verschiedene Aspekte dieser APIs sind drei Haupt Zielgruppen.

1. Die [Consumer-APIs (Übersicht](xref:security/data-protection/consumer-apis/overview) ) Ziel Anwendungs-und Frameworkentwickler.

   "Ich möchte nicht erfahren, wie der Stapel funktioniert oder wie er konfiguriert ist. Ich möchte einen Vorgang so einfach wie möglich mit hoher Wahrscheinlichkeit für die erfolgreiche Verwendung der APIs ausführen. "

2. Die [Konfigurations-APIs](xref:security/data-protection/configuration/overview) sind für Anwendungsentwickler und Systemadministratoren konzipiert.

   "Ich muss dem Datenschutzsystem mitteilen, dass meine Umgebung nicht standardmäßige Pfade oder Einstellungen erfordert."

3. Die Erweiterbarkeits-APIs richten sich an Entwickler, die für die Implementierung benutzerdefinierter Richtlinien verantwortlich sind Die Verwendung dieser APIs ist auf seltene Situationen und erfahrene, sicherheitsbewusste Entwickler beschränkt.

   "Ich muss eine gesamte Komponente im System ersetzen, da ich wirklich eindeutige Verhaltens Anforderungen habe. Ich bin bereit, nicht häufig verwendete Teile der API-Oberfläche zu erlernen, um ein Plug-in zu erstellen, das meine Anforderungen erfüllt. "

## <a name="package-layout"></a>Paketlayout

Der Stapel für den Datenschutz besteht aus fünf Paketen.

* [Microsoft. aspnetcore. dataprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) enthält die <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> Schnittstellen und, um Datenschutzdienste zu erstellen. Außerdem sind nützliche Erweiterungs Methoden für die Arbeit mit diesen Typen (z. b. [idataprotector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)) enthalten. Wenn das Datenschutzsystem an einem anderen Speicherort instanziiert wird und Sie die API verwenden, verweisen Sie auf `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. aspnetcore. dataprotection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) enthält die grundlegende Implementierung des Datenschutzsystems, einschließlich der wichtigsten Kryptografievorgänge, Schlüsselverwaltung, Konfiguration und Erweiterbarkeit. Um das Datenschutzsystem zu instanziieren (z. b. hinzufügen zu einem <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) oder um das Verhalten zu ändern oder zu erweitern, verweisen Sie auf `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) enthält zusätzliche APIs, die Entwickler möglicherweise nützlich finden, aber nicht zum Kern Paket gehören. Dieses Paket enthält beispielsweise Factorymethoden zum Instanziieren des Datenschutzsystems zum Speichern von Schlüsseln an einem Speicherort im Dateisystem ohne Abhängigkeitsinjektion (Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). Sie enthält auch Erweiterungs Methoden zum Begrenzen der Lebensdauer geschützter Nutzlasten (siehe <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.Systemweb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) kann in einer vorhandenen ASP.NET 4. x-APP installiert werden, um `<machineKey>` die Vorgänge so umzuleiten, dass der neue ASP.net Core Datenschutz Stapel verwendet wird. Weitere Informationen finden Sie unter <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. aspnetcore. Cryptography. keyderivations](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) stellt eine Implementierung der PBKDF2-Kenn Wort Hash Routine bereit und kann von Systemen verwendet werden, die Benutzer Kennwörter sicher behandeln müssen. Weitere Informationen finden Sie unter <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:host-and-deploy/web-farm>
