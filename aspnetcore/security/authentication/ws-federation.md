---
title: Authentifizieren von Benutzern mit WS-Federation in ASP.net Core
author: chlowell
description: In diesem Tutorial wird veranschaulicht, wie WS-Federation in einer ASP.net Core-App verwendet wird.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053370"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Authentifizieren von Benutzern mit WS-Federation in ASP.net Core

In diesem Tutorial wird veranschaulicht, wie Sie es Benutzern ermöglichen, sich mit einem WS-Federation Authentifizierungs Anbieter wie Active Directory-Verbunddienste (AD FS) (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD) anzumelden. Dabei wird die ASP.net Core-Beispiel-App verwendet, die in [Facebook, Google und der Authentifizierung externer Anbieter beschrieben wird](xref:security/authentication/social/index).

Für ASP.net Core-apps wird WS-Federation Support von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)bereitgestellt. Diese Komponente wird von [Microsoft. owin. Security. wsfederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) portiert und nutzt viele der Mechanismen dieser Komponente. Allerdings unterscheiden sich die Komponenten auf einige wichtige Weise.

Standardmäßig wird die neue Middleware:

* Lässt keine nicht angeforderten Anmeldungen zu. Diese Funktion des WS-Federation Protokolls ist anfällig für XSRF-Angriffe. Sie kann jedoch mit der Option aktiviert werden `AllowUnsolicitedLogins` .
* Prüft nicht alle Formular Beiträge auf Anmelde Nachrichten. Nur Anforderungen an werden auf Anmeldungen `CallbackPath` geprüft. der Standardwert ist `CallbackPath` , `/signin-wsfed` aber kann über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [wsfederationoptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) -Klasse geändert werden. Dieser Pfad kann für andere Authentifizierungs Anbieter freigegeben werden, indem die [skipunerkenzedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) -Option aktiviert wird.

## <a name="register-the-app-with-active-directory"></a>Registrieren der App bei Active Directory

### <a name="active-directory-federation-services"></a>Active Directory-Verbunddienste (AD FS)

* Öffnen **Sie den Assistenten zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite** in der AD FS-Verwaltungskonsole:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsAddTrust.png)

* Daten manuell eingeben:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite: Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* Geben Sie einen anzeigen Amen für die vertrauende Seite ein. Der Name ist für die ASP.net Core-APP nicht wichtig.

* [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) hat keine Unterstützung für die tokenverschlüsselung, konfigurieren Sie also kein tokenverschlüsselungszertifikat:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureCert.png)

* Aktivieren Sie die Unterstützung für WS-Federation passives Protokoll mithilfe der App-URL. Vergewissern Sie sich, dass der Port für die APP richtig ist:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Dabei muss es sich um eine HTTPS-URL handeln. IIS Express können ein selbst signiertes Zertifikat bereitstellen, wenn die APP während der Entwicklung gehostet wird. Kestrel erfordert eine manuelle Zertifikat Konfiguration. Weitere Informationen finden Sie in der [Kestrel-Dokumentation](xref:fundamentals/servers/kestrel) .

* Klicken Sie im restlichen Assistenten auf **weiter** , und **Schließen** Sie am Ende.

* ASP.NET Core Identity erfordert einen **namens-ID** -Anspruch. Fügen Sie eine aus dem Dialogfeld " **Anspruchs Regeln bearbeiten** " hinzu:

![Anspruchsregeln bearbeiten](ws-federation/_static/EditClaimRules.png)

* Belassen Sie im **Assistenten zum Hinzufügen von Transformations Anspruchs Regeln** die Vorlage Standardwert **für LDAP-Attribute als Ansprüche senden** , und klicken Sie auf **weiter** . Fügen Sie eine Regel hinzu, die das LDAP **-Attribut Sam-Account-Name** dem ausgehenden Anspruch der **namens-ID** zuordnet:

![Assistent zum Hinzufügen von Transformations Anspruchs Regeln: Konfigurieren von Anspruchs Regeln](ws-federation/_static/AddTransformClaimRule.png)

* Klicken **Finish** Sie  >  **OK** im Fenster **Anspruchs Regeln bearbeiten** auf Fertigstellen.

### <a name="azure-active-directory"></a>Azure Active Directory

* Navigieren Sie zum Blatt App-Registrierungen des Aad-Mandanten. Klicken Sie auf **neue Anwendungs Registrierung** :

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* Geben Sie einen Namen für die APP-Registrierung ein. Dies ist für die ASP.net Core-APP nicht von Bedeutung.
* Geben Sie die URL ein, die die APP als **Anmelde-URL** überwacht:

![Azure Active Directory: App-Registrierung erstellen](ws-federation/_static/AadCreateAppRegistration.png)

* Klicken Sie auf **Endpunkte** , und notieren Sie die Verbund **Metadaten-Dokument** -URL Dies sind die WS-Federation Middleware `MetadataAddress` :

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* Navigieren Sie zur neuen App-Registrierung. Klicken Sie auf **API** verfügbar machen. Klicken Sie auf Anwendungs-ID-URI **Satz**  >  **Speichern** . Notieren Sie sich den  **Anwendungs-ID-URI** . Dies sind die WS-Federation Middleware `Wtrealm` :

![Azure Active Directory: Eigenschaften der APP-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a>Verwenden Sie WS-Federation ohne ASP.NET Core Identity

Die WS-Federation Middleware kann ohne verwendet werden Identity . Zum Beispiel:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a>Fügen Sie WS-Federation als externen Anmelde Anbieter für hinzu. ASP.NET Core Identity

* Fügen Sie eine Abhängigkeit von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt hinzu.
* WS-Federation hinzufügen zu `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Melden Sie sich mit WS-Federation

Navigieren Sie zur APP, und klicken Sie auf den Link **Anmelden** im Navigations Header. Es gibt eine Option zum Anmelden mit wsfederation: ![ Anmeldeseite.](ws-federation/_static/WsFederationButton.png)

Bei ADFS als Anbieter wird die Schaltfläche zu einer AD FS-Anmeldeseite umgeleitet: ![ ADFS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)

Wenn Azure Active Directory als Anbieter fungiert, wird die Schaltfläche an eine Aad-Anmeldeseite umgeleitet: ![ Aad-Anmeldeseite](ws-federation/_static/AadSignIn.png)

Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet zur Benutzer Registrierungsseite der App um: ![ Seite registrieren.](ws-federation/_static/Register.png)
