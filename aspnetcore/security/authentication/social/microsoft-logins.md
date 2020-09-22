---
title: Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core
author: rick-anderson
description: In diesem Beispiel wird die Integration von Microsoft-Konto Benutzerauthentifizierung in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 36341a0e439be57d7da4f787aa6103b92c624e96
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847584"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Einrichtung externer Anmelde Informationen für Microsoft-Konto mit ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Beispiel wird gezeigt, wie Sie es Benutzern mithilfe des Projekts ASP.net Core 3,0, das auf der [vorherigen Seite](xref:security/authentication/social/index)erstellt wurde, ermöglichen, sich mit Ihren Geschäfts-, Schul-oder uniMicrosoft-Konto anzumelden.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen der APP im Microsoft-Entwickler Portal

* Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. microsoftaccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) hinzu.
* Navigieren Sie zur Seite " [Azure-Portal-App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) ", und erstellen Sie eine Microsoft-Konto, oder melden Sie sich an:

Wenn Sie keine Microsoft-Konto haben, wählen Sie **Erstellen**aus. Nachdem Sie sich angemeldet haben, werden Sie auf die Seite **App-Registrierungen** umgeleitet:

* **Neue Registrierung** auswählen
* Geben Sie einen **Namen** ein.
* Wählen Sie eine Option für **unterstützte Konto Typen**aus.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * Das `MicrosoftAccount` Paket unterstützt standardmäßig App-Registrierungen, die mit "Konten in einem beliebigen Organisations Verzeichnis" oder "Konten in beliebigen Organisations Verzeichnis-und Microsoft-Konten" erstellt wurden.
  * Um andere Optionen zu verwenden, `AuthorizationEndpoint` legen `TokenEndpoint` Sie und Member von `MicrosoftAccountOptions` zum Initialisieren der Microsoft-Konto Authentifizierung für die URLs, die auf der Seite **Endpunkte** der APP-Registrierung angezeigt werden, nach der Erstellung fest (verfügbar, indem Sie auf der Seite **Übersicht** auf Endpunkte klicken).
* Geben Sie unter **Umleitungs-URI**Ihre Entwicklungs-URL mit angefügt ein `/signin-microsoft` . Beispiel: `https://localhost:5001/signin-microsoft`. Das Microsoft-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet Anforderungen bei der `/signin-microsoft` Route automatisch, um den OAuth-Fluss zu implementieren.
* Wählen Sie **Registrieren** aus.

### <a name="create-client-secret"></a>Erstellen eines geheimen Clientschlüssels

* Wählen Sie im linken Bereich **Zertifikate und Geheimnisse** aus.
* Wählen Sie unter geheime **Client**Schlüssel die Option **neuer geheimer Client** Schlüssel

  * Fügen Sie eine Beschreibung für den geheimen Client Schlüssel hinzu.
  * Wählen Sie die Schaltfläche **Hinzufügen** aus.

* Kopieren Sie unter geheime **Client**Schlüssel den Wert des geheimen Client Schlüssels.

Das URI-Segment `/signin-microsoft` wird als Standard Rückruf des Microsoft-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI beim Konfigurieren der Microsoft-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) -Klasse ändern.

## <a name="store-the-microsoft-client-id-and-secret"></a>Speichern der Microsoft-Client-ID und des geheimen Schlüssels

Speichern Sie sensible Einstellungen wie die Microsoft-Client-ID und die geheimen Werte mit [Secret Manager](xref:security/app-secrets). Führen Sie für dieses Beispiel die folgenden Schritte aus:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den geheimen Schlüsseln `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren der Microsoft-Konto Authentifizierung

Fügen Sie den Microsoft-Konto Dienst hinzu `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Konto Authentifizierung unterstützt werden, finden Sie in der [microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) -API Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Bei Microsoft anmelden Konto

Führen Sie die APP aus, und klicken Sie auf **Anmelden**. Eine Option zum Anmelden bei Microsoft wird angezeigt. Wenn Sie auf Microsoft klicken, werden Sie zur Authentifizierung an Microsoft umgeleitet. Nachdem Sie sich mit Ihrem Microsoft-Konto angemeldet haben, werden Sie aufgefordert, der APP den Zugriff auf Ihre Informationen zu gestatten:

Tippen Sie auf **Ja** , und Sie werden zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit Ihren Microsoft-Anmelde Informationen angemeldet:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Konto Anbieter Sie an eine Anmeldefehler Seite weiterleitet, beachten Sie die Parameter für die Fehler Titel und die Beschreibungs Abfrage Zeichenfolge direkt nach dem `#` (hashtag) im URI.

  Obwohl in der Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angezeigt wird, ist die häufigste Ursache, dass ihr Anwendungs-URI mit keinem der für die **Webplattform** angegebenen **Umleitungs-URIs** übereinstimmt.
* Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, führt der Versuch, sich zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden*. Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.
* Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich bei Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, erstellen Sie im Microsoft-Entwickler Portal ein neues Client Geheimnis.

* Legen Sie die `Authentication:Microsoft:ClientId` und `Authentication:Microsoft:ClientSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
