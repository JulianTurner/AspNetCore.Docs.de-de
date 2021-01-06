---
title: IIS-Module mit ASP.NET Core
author: rick-anderson
description: Lernen Sie aktive und inaktive IIS-Module für ASP.NET Core-Apps kennen, und erfahren Sie, wie Sie diese verwalten können.
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
uid: host-and-deploy/iis/modules
ms.openlocfilehash: 47ba04f199f9b77cf6032de9f80f2410f5c69424
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057400"
---
# <a name="iis-modules-with-aspnet-core"></a>IIS-Module mit ASP.NET Core

Einige der nativen IIS-Module und sämtliche der IIS-verwalteten Module sind nicht in der Lage, Anforderungen für ASP.NET Core-Apps zu verarbeiten. In vielen Fällen bietet ASP.NET Core eine Alternative zu den Szenarien, die von nativen und verwalteten IIS-Modulen abgedeckt werden.

## <a name="native-modules"></a>Native Module

Die Tabelle enthält native IIS-Module, die mit ASP.NET Core-Apps und dem ASP.NET Core-Modul funktionieren.

| Modul | Funktionsfähig mit ASP.NET Core-Apps | ASP.NET Core-Option |
| --- | :---: | --- |
| **Anonyme Authentifizierung**<br>`AnonymousAuthenticationModule`                                  | Ja | |
| **Standardauthentifizierung**<br>`BasicAuthenticationModule`                                          | Ja | |
| **Authentifizierung durch Clientzertifikatszuordnung**<br>`CertificateMappingAuthenticationModule`      | Ja | |
| **CGI**<br>`CgiModule`                                                                           | Nein  | |
| **Konfigurationsvalidierung**<br>`ConfigurationValidationModule`                                  | Ja | |
| **HTTP-Fehler**<br>`CustomErrorModule`                                                           | Nein  | [Middleware für Statuscodeseiten](xref:fundamentals/error-handling#usestatuscodepages) |
| **Benutzerdefinierte Protokollierung**<br>`CustomLoggingModule`                                                      | Ja | |
| **Standarddokument**<br>`DefaultDocumentModule`                                                  | Nein  | [Middleware für Standarddateien](xref:fundamentals/static-files#serve-a-default-document) |
| **Hashwertauthentifizierung**<br>`DigestAuthenticationModule`                                        | Ja | |
| **Verzeichnissuche**<br>`DirectoryListingModule`                                               | Nein  | [Middleware für Verzeichnissuche](xref:fundamentals/static-files#enable-directory-browsing) |
| **Dynamische Komprimierung**<br>`DynamicCompressionModule`                                            | Ja | [Antworten komprimierende Middleware](xref:performance/response-compression) |
| **Ablaufverfolgung für Anforderungsfehler**<br>`FailedRequestsTracingModule`                                     | Ja | [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#tracesource-provider) |
| **Dateizwischenspeicherung**<br>`FileCacheModule`                                                            | Nein  | [Antworten zwischenspeichernde Middleware](xref:performance/caching/middleware) |
| **HTTP-Zwischenspeicherung**<br>`HttpCacheModule`                                                            | Nein  | [Antworten zwischenspeichernde Middleware](xref:performance/caching/middleware) |
| **HTTP-Protokollierung**<br>`HttpLoggingModule`                                                          | Ja | [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index) |
| **HTTP-Umleitung**<br>`HttpRedirectionModule`                                                  | Ja | [URL-umschreibende Middleware](xref:fundamentals/url-rewriting) |
| **HTTP-Ablaufverfolgung**<br>`TracingModule`                                                              | Ja | |
| **Authentifizierung durch IIS-Clientzertifikatszuordnung**<br>`IISCertificateMappingAuthenticationModule` | Ja | |
| **IP- und Domäneneinschränkungen**<br>`IpRestrictionModule`                                          | Ja | |
| **ISAPI-Filter**<br>`IsapiFilterModule`                                                         | Ja | [Middleware](xref:fundamentals/middleware/index) |
| **ISAPI**<br>`IsapiModule`                                                                       | Ja | [Middleware](xref:fundamentals/middleware/index) |
| **Protokollunterstützung**<br>`ProtocolSupportModule`                                                  | Ja | |
| **Anforderungsfilterung**<br>`RequestFilteringModule`                                                | Ja | [URL-umschreibende Middleware`IRule`](xref:fundamentals/url-rewriting#irule-based-rule) |
| **Anforderungsüberwachung**<br>`RequestMonitorModule`                                                    | Ja | |
| **URL-Umschreibung**&#8224;<br>`RewriteModule`                                                      | Ja | [URL-umschreibende Middleware](xref:fundamentals/url-rewriting) |
| **Serverseitige Includedateien**<br>`ServerSideIncludeModule`                                            | Nein  | |
| **Statische Komprimierung**<br>`StaticCompressionModule`                                              | Nein  | [Antworten komprimierende Middleware](xref:performance/response-compression) |
| **Statischer Inhalt**<br>`StaticFileModule`                                                         | Nein  | [Middleware für statische Dateien](xref:fundamentals/static-files) |
| **Token-Zwischenspeicherung**<br>`TokenCacheModule`                                                          | Ja | |
| **URI-Zwischenspeicherung**<br>`UriCacheModule`                                                              | Ja | |
| **URL-Autorisierung**<br>`UrlAuthorizationModule`                                                | Ja | [ASP.NET Core Identity](xref:security/authentication/identity) |
| **Windows-Authentifizierung**<br>`WindowsAuthenticationModule`                                      | Ja | |

&#8224;Die Übereinstimmungstypen `isFile` und `isDirectory` des URL-Rewrite-Moduls können aufgrund der Änderungen in der [Verzeichnisstruktur](xref:host-and-deploy/directory-structure) nicht in ASP.NET Core-Apps ausgeführt werden.

## <a name="managed-modules"></a>Verwaltete Module

Verwaltete Module sind mit gehosteten ASP.NET Core-Apps *nicht* funktionsfähig, wenn die .NET CLR-Version des App-Pools auf **Kein verwalteter Code** festgelegt ist. ASP.NET Core bietet in verschiedenen Fällen Alternativen für Middleware an.

| Modul                  | ASP.NET Core-Option |
| ----------------------- | ------------------- |
| AnonymousIdentification | |
| DefaultAuthentication   | |
| FileAuthorization       | |
| FormsAuthentication     | [Middleware für Cookieauthentifizierung](xref:security/authentication/cookie) |
| OutputCache             | [Antworten zwischenspeichernde Middleware](xref:performance/caching/middleware) |
| Profile                 | |
| RoleManager             | |
| ScriptModule-4.0        | |
| Sitzung                 | [Middleware für Sitzungen](xref:fundamentals/app-state) |
| UrlAuthorization        | |
| UrlMappingsModule       | [URL-umschreibende Middleware](xref:fundamentals/url-rewriting) |
| UrlRoutingModule-4.0    | [ASP.NET Core Identity](xref:security/authentication/identity) |
| WindowsAuthentication   | |

## <a name="iis-manager-application-changes"></a>IIS Manager-Anwendungsänderungen

Bei Verwendung von IIS Manager für die Konfiguration von Einstellungen wurde die Datei *web.config* der App geändert. Wenn eine App bereitgestellt und *web.config* einbezogen wird, werden sämtliche mit IIS Manager vorgenommene Änderungen von der bereitgestellten Datei *web.config* überschrieben. Wenn Änderungen an der Datei *web.config* des Servers vorgenommen werden, kopieren Sie die aktualisierte Datei *web.config* auf dem Server direkt in das lokale Projekt.

## <a name="disabling-iis-modules"></a>Deaktivieren von IIS-Modulen

Wenn ein IIS-Modul auf Serverebene konfiguriert wird und für eine App deaktiviert werden muss, ist dies durch eine Erweiterung der Datei *web.config* der App möglich. Lassen Sie das Modul an der Stelle und deaktivieren Sie es mit einer Konfigurationseinstellung (falls verfügbar), oder entfernen Sie das Modul aus der App.

### <a name="module-deactivation"></a>Deaktivierung von Modulen

Viele Module bieten eine Konfigurationseinstellung, mit der sie deaktiviert werden können, ohne aus der App entfernt werden zu müssen. Dies ist der einfachste und schnellste Weg zur Deaktivierung eines Moduls. Das HTTP-Umleitungsmodul kann beispielsweise mit dem `<httpRedirect>`-Element in *web.config* deaktiviert werden:

```xml
<configuration>
  <system.webServer>
    <httpRedirect enabled="false" />
  </system.webServer>
</configuration>
```

Für weitere Informationen zum Deaktivieren von Modulen mit Konfigurationseinstellungen folgen Sie den Links im Abschnitt *Untergeordnete Elemente* von [IIS \<system.webServer>](/iis/configuration/system.webServer/).

### <a name="module-removal"></a>Entfernen von Modulen

Wenn Sie ein Modul mit einer Einstellung in der Datei *web.config* entfernen können, entsperren Sie das Modul, und entsperren Sie zunächst den Abschnitt `<modules>` in der Datei *web.config*:

1. Entsperren Sie das Modul auf Serverebene. Wählen Sie den IIS-Server in der IIS-Manager-Sidebar **Verbindungen** aus. Öffnen Sie **Module** im Bereich **IIS**. Wählen Sie das Modul aus der Liste aus. Wählen Sie in der Sidebar **Aktionen** auf der rechten Seite die Option **Entsperren** aus. Wenn der Aktionseintrag für das Modul **Lock** (Sperren) lautet, wurde das Modul bereits entsperrt, und es ist keine weitere Aktion erforderlich. Entsperren Sie alle Module, die Sie später aus *web.config* entfernen möchten.

2. Stellen Sie die App ohne den Abschnitt `<modules>` in der Datei *web.config* bereit. Wenn eine App bereitgestellt wird, in der die Datei *web.config* den Abschnitt `<modules>` enthält, ohne dass der Abschnitt zuvor in IIS-Manager entsperrt wurde, löst der Configuration Manager bei dem Versuch, den Abschnitt zu entsperren, eine Ausnahme aus. Daher sollten Sie die App ohne einen Abschnitt `<modules>` bereitstellen.

3. Entsperren Sie den Abschnitt `<modules>` in der Datei *web.config*. Wählen Sie in der Sidebar **Verbindungen** unter **Sites** die Website aus. Öffnen Sie im Bereich **Verwaltung** den **Konfigurations-Editor**. Wählen Sie mithilfe der Navigationssteuerelemente den Abschnitt `system.webServer/modules` aus. Wählen Sie in der Seitenleiste **Aktionen** auf der rechten Seite die Option **Entsperren** für den Abschnitt aus. Wenn der Aktionseintrag für den Modulabschnitt **Lock Section** (Abschnitt sperren) lautet, wurde der Modulabschnitt bereits entsperrt, und es ist keine weitere Aktion erforderlich.

4. Fügen Sie den Abschnitt `<modules>` zur Datei *web.config* mit dem Element `<remove>` hinzu, um das Modul aus der App zu entfernen. Es können mehrere `<remove>`-Elemente hinzugefügt werden, um mehrere Module zu entfernen. Wenn auf dem Server Änderungen an der Datei *web.config* vorgenommen werden, nehmen Sie sofort lokal die gleichen Änderungen an der Datei *web.config* des Projekts vor. Wenn ein Modul auf diese Weise entfernt wird, hat dies keine Auswirkungen auf die Verwendung des Moduls mit anderen Apps auf dem Server.

   ```xml
   <configuration>
    <system.webServer>
      <modules>
        <remove name="MODULE_NAME" />
      </modules>
    </system.webServer>
   </configuration>
   ```

Sie können Module für IIS Express mit *web.config* hinzufügen oder entfernen. Ändern Sie dazu die Datei *applicationHost.config*, und entsperren Sie den Abschnitt `<modules>`:

1. Öffnen Sie *{ANWENDUNGSSTAMM}\\.vs\config\applicationhost.config*.

1. Machen Sie das Element `<section>` für IIS-Module ausfindig, und ändern Sie `overrideModeDefault` von `Deny` in `Allow`:

   ```xml
   <section name="modules"
            allowDefinition="MachineToApplication"
            overrideModeDefault="Allow" />
   ```

1. Machen Sie den Abschnitt `<location path="" overrideMode="Allow"><system.webServer><modules>` ausfindig. Ändern Sie für alle zu entfernenden Module `lockItem` von `true` in `false`. Im folgenden Beispiel wird das CGI-Modul entsperrt:

   ```xml
   <add name="CgiModule" lockItem="false" />
   ```

1. Nachdem der Abschnitt `<modules>` und die einzelnen Module entsperrt wurden, können Sie IIS-Module mit der *web.config*-Datei der App hinzufügen, um die App in IIS Express auszuführen.

Ein IIS-Modul kann auch mit *Appcmd.exe* entfernt werden. Geben Sie `MODULE_NAME` und `APPLICATION_NAME` im Befehl an:

```console
Appcmd.exe delete module MODULE_NAME /app.name:APPLICATION_NAME
```

Entfernen Sie beispielsweise `DynamicCompressionModule` aus „Default Web Site“ (Standardwebsite):

```console
%windir%\system32\inetsrv\appcmd.exe delete module DynamicCompressionModule /app.name:"Default Web Site"
```

## <a name="minimum-module-configuration"></a>Modulmindestkonfiguration

Die einzigen Module, die für die Ausführung einer ASP.NET Core-App erforderlich sind, sind das anonyme Authentifizierungsmodul und das ASP.NET Core-Modul.

Mit dem URI-Zwischenspeicherungsmodul (`UriCacheModule`) kann IIS die Websitekonfiguration auf URL-Ebene zwischenspeichern. Ohne dieses Modul muss IIS die Konfiguration bei der Anforderung lesen und analysieren, selbst dann, wenn wiederholt die gleiche URL angefordert wird. Das Analysieren der Konfiguration bei jeder Anforderung führt zu erheblichen Leistungseinbußen. *Auch wenn das URI-Zwischenspeicherungsmodul grundsätzlich nicht für die Ausführung einer gehosteten ASP.NET Core-App erforderlich ist, wird empfohlen, das URI-Zwischenspeicherungsmodul für sämtliche ASP.NET Core-Bereitstellungen zu aktivieren.*

Das HTTP-Zwischenspeicherungsmodul (`HttpCacheModule`) implementiert den IIS-Ausgabecache und die Logik für das Zwischenspeichern von Elementen im HTTP.sys-Cache. Ohne dieses Modul werden Inhalte nicht mehr im Kernelmodus zwischengespeichert, und Cacheprofile werden ignoriert. Das Entfernen des HTTP-Zwischenspeicherungsmoduls hat in der Regel negative Auswirkungen auf Leistung und Ressourcennutzung. *Auch wenn das HTTP-Zwischenspeicherungsmodul grundsätzlich nicht für die Ausführung einer gehosteten ASP.NET Core-App erforderlich ist, wird empfohlen, das HTTP-Zwischenspeicherungsmodul für sämtliche ASP.NET Core-Bereitstellungen zu aktivieren.*

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Introduction to IIS Architectures: Modules in IIS (Einführung in IIS-Architekturen: Module in IIS)](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#modules-in-iis)
* [Übersicht über IIS-Module](/iis/get-started/introduction-to-iis/iis-modules-overview)
* [Anpassen von IIS 7.0-Rollen und -Modulen](/previous-versions/tn-archive/cc627313(v=technet.10))
* [IIS\<system.webServer>](/iis/configuration/system.webServer/)