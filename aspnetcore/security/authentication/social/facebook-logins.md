---
title: Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Authentifizierung von Facebook-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulichen.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053357"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0. Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.

## <a name="create-the-app-in-facebook"></a>Erstellen der app in Facebook

* Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.

* Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an. Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.  Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.

* Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.

   ![Facebook für Entwickler Portal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .

  ![Erstellen Sie ein neues APP-ID-Formular.](index/_static/FBNewAppId.png)

* Wählen Sie auf der neuen App-Karte **Produkt hinzufügen** aus.  Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** . 

  ![Seite "Produkt Setup"](index/_static/FBProductSetup.png)

* Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus. Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:

  ![Client-OAuth-Einstellungsseite](index/_static/FBOAuthSetup.png)

* Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (Beispiel: `https://localhost:44320/signin-facebook` ). Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.

> [!NOTE]
> Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.

* Klicken Sie auf **Änderungen speichern** .

* Klicken Sie im linken Navigationsbereich auf **Einstellungen**  >  **Basic** -Link.

  Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret` . Sie fügen beide im nächsten Abschnitt Ihrer ASP.net Core Anwendung hinzu:

* Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.

## <a name="store-the-facebook-app-id-and-secret"></a>Speichern der Facebook-APP-ID und des geheimen Schlüssels

Speichern Sie sensible Einstellungen wie die Facebook-APP-ID und die geheimen Werte mit dem [Geheimnis-Manager](xref:security/app-secrets). Führen Sie für dieses Beispiel die folgenden Schritte aus:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den geheimen Schlüsseln `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurieren der Facebook-Authentifizierung

Fügen Sie den Facebook-Dienst in der- `ConfigureServices` Methode in der *Startup.cs* -Datei hinzu:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Mit Facebook anmelden

* Führen Sie die APP aus, und wählen Sie **Anmelden aus** . 
* Wählen Sie unter **anderen Dienst zum Anmelden verwenden. die** Option Facebook aus.
* Sie werden zur Authentifizierung an **Facebook** umgeleitet.
* Ihre Facebook-Anmelde Informationen eingeben.
* Sie werden zurück zu Ihrer Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit ihren Facebook-Anmelde Informationen angemeldet:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagieren auf Abbrechen der externen Anmeldung

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> kann einen Umleitungs Pfad zum Benutzer-Agent angeben, wenn der Benutzer die angeforderte Autorisierungs Anforderung nicht genehmigt.

Mit dem folgenden Code wird der auf festgelegt `AccessDeniedPath` `"/AccessDeniedPathInfo"` :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Wir empfehlen, dass die `AccessDeniedPath` Seite die folgenden Informationen enthält:

*  Die Remote Authentifizierung wurde abgebrochen.
* Diese APP erfordert eine Authentifizierung.
* Wählen Sie den Anmeldelink aus, um erneut zu versuchen, sich anzumelden.

### <a name="test-accessdeniedpath"></a>Testen von accessdeniedpath

* Navigieren Sie zu [Facebook.com](https://www.facebook.com/)
* Wenn Sie angemeldet sind, müssen Sie sich abmelden.
* Führen Sie die APP aus, und wählen Sie die Facebook-Anmeldung aus.
* Wählen Sie **nicht jetzt** aus. Sie werden auf die angegebene `AccessDeniedPath` Seite umgeleitet.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz. Konfigurationsoptionen können für Folgendes verwendet werden:

* Anfordern von anderen Informationen über den Benutzer.
* Fügen Sie Abfrage Zeichen folgen Argumente hinzu, um die Anmeldung anzupassen.

## <a name="troubleshooting"></a>Problembehandlung

* **Nur ASP.net Core 2. x:** Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, führt der Versuch, sich zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden* . Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich mit Facebook authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.

* Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
