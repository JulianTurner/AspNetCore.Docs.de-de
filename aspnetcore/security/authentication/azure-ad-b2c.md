---
title: Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core
author: camsoper
description: Erfahren Sie, wie Sie Azure Active Directory B2C Authentifizierung mit ASP.net Core einrichten.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061391"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core

Von [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloud-Identitäts Verwaltungs Lösung für Web-und Mobile Apps. Der Dienst bietet eine Authentifizierung für apps, die in der Cloud und lokal gehostet werden. Zu den Authentifizierungs Typen zählen einzelne Konten, soziale Netzwerk Konten und Verbund Unternehmenskonten. Darüber hinaus können Azure AD B2C Multi-Factor Authentication mit minimaler Konfiguration bereitstellen.

> [!TIP]
> Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote. Ein Azure AD Mandant stellt eine Organisation dar, während ein Azure AD B2C Mandant eine Auflistung von Identitäten darstellt, die mit Anwendungen der vertrauenden Seite verwendet werden sollen. Weitere Informationen finden Sie unter [Azure AD B2C: häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten
> * Registrieren einer APP in Azure AD B2C
> * Erstellen Sie mit Visual Studio eine ASP.net Core Web-App, die für die Verwendung des Azure AD B2C-Mandanten für die Authentifizierung konfiguriert ist
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern

## <a name="prerequisites"></a>Voraussetzungen

Für diese exemplarische Vorgehensweise sind die folgenden Schritte erforderlich:

* [Microsoft Azure-Abonnement](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Erstellen des Azure Active Directory B2C Mandanten

Erstellen Sie einen Azure Active Directory B2C Mandanten, [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started). Wenn Sie dazu aufgefordert werden, ist die Zuordnung des Mandanten zu einem Azure-Abonnement für dieses Tutorial optional.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrieren der app in Azure AD B2C

Registrieren Sie Ihre APP im neu erstellten Azure AD B2C Mandanten mithilfe [der Schritte in der Dokumentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) im Abschnitt **Registrieren einer Web-App** . Legen Sie im Abschnitt **Erstellen eines geheimen Client Schlüssels für eine Web-App** an. Ein geheimer Client Schlüssel ist für dieses Tutorial nicht erforderlich. 

Verwenden Sie die folgenden Werte:

| Einstellung                       | Wert                     | Hinweise                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                      | *&lt;App-Name&gt;*        | Geben Sie einen **Namen** für die APP ein, mit dem Ihre APP für Consumer beschrieben wird.                                                                                                                                 |
| **Web-App/Web-API einschließen** | Yes                       |                                                                                                                                                                                                    |
| **Impliziten Fluss zulassen**       | Yes                       |                                                                                                                                                                                                    |
| **Antwort-URL**                 | `https://localhost:44300/signin-oidc` | Antwort-URLs sind Endpunkte, an denen Azure AD B2C von Ihrer App angeforderte Token zurückgibt. Visual Studio stellt die zu verwendende Antwort-URL bereit. Geben Sie vorerst ein, `https://localhost:44300/signin-oidc` um das Formular abzuschließen. |
| **App-ID-URI**                | Nicht ausfüllen               | Für dieses Tutorial nicht erforderlich.                                                                                                                                                                    |
| **Nativen Client einschließen**     | No                        |                                                                                                                                                                                                    |

> [!WARNING]
> Wenn Sie eine nicht-localhost-Antwort-URL einrichten, beachten Sie die Einschränkungen, die [in der Antwort-URL-Liste zulässig sind](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Nachdem die APP registriert wurde, wird die Liste der apps im Mandanten angezeigt. Wählen Sie die soeben registrierte App aus. Wählen Sie das **Kopier** Symbol rechts neben dem Feld **Anwendungs-ID** aus, um es in die Zwischenablage zu kopieren.

Im Azure AD B2C Mandanten kann zurzeit nichts mehr konfiguriert werden, aber lassen Sie das Browserfenster geöffnet. Nachdem die ASP.net Core-App erstellt wurde, ist eine weitere Konfiguration vorhanden.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Erstellen einer ASP.net Core-app in Visual Studio

Die Visual Studio-Webanwendungs Vorlage kann so konfiguriert werden, dass der Azure AD B2C Mandanten für die Authentifizierung verwendet wird.

In Visual Studio:

1. Erstellen Sie eine neue ASP.NET Core-Webanwendung. 
2. Wählen Sie in der Liste der Vorlagen die Option **Webanwendung** aus.
3. Wählen Sie die Schaltfläche **Authentifizierung ändern** aus.
    
    ![Authentifizierungs Schaltfläche ändern](./azure-ad-b2c/_static/changeauth.png)

4. Wählen Sie im Dialogfeld **Authentifizierung ändern** die Option **einzelne Benutzerkonten** aus, und wählen Sie dann in der Dropdown Liste die **Option mit einem vorhandenen Benutzerspeicher in der Cloud verbinden** aus. 
    
    ![Dialog Feld zur Authentifizierung ändern](./azure-ad-b2c/_static/changeauthdialog.png)

5. Füllen Sie das Formular mit den folgenden Werten aus:
    
    | Einstellung                       | Wert                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Domänen Name**               | *&lt;der Domänen Name Ihres B2C-Mandanten.&gt;*          |
    | **Anwendungs-ID**            | *&lt;Fügen Sie die Anwendungs-ID aus der Zwischenablage ein.&gt;* |
    | **Rückruf Pfad**             | *&lt;Standardwert verwenden&gt;*                       |
    | **Registrierungs-oder Anmelde Richtlinie** | `B2C_1_SiUpIn`                                        |
    | **Kenn Wort Richtlinie zurücksetzen**     | `B2C_1_SSPR`                                          |
    | **Profil Richtlinie bearbeiten**       | *&lt;leer lassen&gt;*                                 |
    
    Wählen Sie den Link **Kopieren** neben dem **Antwort-URI** aus, um den Antwort-URI in die Zwischenablage zu kopieren. Wählen Sie **OK** aus, um das Dialogfeld **Authentifizierung ändern** zu schließen. Wählen Sie **OK** , um die Web-App zu erstellen.

## <a name="finish-the-b2c-app-registration"></a>Abschließen der Registrierung der B2C-App

Kehren Sie zum Browserfenster zurück, in dem die B2C-App-Eigenschaften noch geöffnet sind. Ändern Sie die zuvor angegebene temporäre **Antwort-URL** in den aus Visual Studio kopierten Wert. Wählen Sie am oberen Rand des Fensters die Option **Speichern** aus.

> [!TIP]
> Wenn Sie die Antwort-URL nicht kopiert haben, verwenden Sie die HTTPS-Adresse auf der Registerkarte "Debuggen" in den Webprojekt Eigenschaften, und fügen Sie den **callbackpath** -Wert von an *appsettings.json* .

## <a name="configure-policies"></a>Konfigurieren von Richtlinien

Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, um [eine Registrierungs-oder Anmelde Richtlinie zu erstellen](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), und [Erstellen Sie dann eine Richtlinie zum Zurücksetzen des Kennworts](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Verwenden Sie die Beispiel Werte, die in der Dokumentation für **Identity Anbieter** , Registrierungs **Attribute** und **Anwendungs Ansprüche** bereitgestellt werden. Die Verwendung der Schaltfläche **jetzt ausführen** zum Testen der Richtlinien, wie in der Dokumentation beschrieben, ist optional.

> [!WARNING]
> Stellen Sie sicher, dass die Richtlinien Namen genau wie in der Dokumentation beschrieben sind, da diese Richtlinien im Dialogfeld " **Authentifizierung ändern** " in Visual Studio verwendet wurden. Die Richtlinien Namen können in überprüft werden *appsettings.json* .

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a>Konfigurieren der zugrunde liegenden openidconnectoptions/jwtbearer- Cookie Optionen

Um die zugrunde liegenden Optionen direkt zu konfigurieren, verwenden Sie die entsprechende Schema Konstante in `Startup.ConfigureServices` :

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Ausführen der App

Drücken Sie in Visual Studio **F5** , um die APP zu erstellen und auszuführen. Wählen Sie nach dem Start der Web-App **akzeptieren** aus, um die Verwendung von s zu akzeptieren cookie (falls Sie dazu aufgefordert werden), und wählen Sie dann **Anmelden** aus.

![Anmelden bei der APP](./azure-ad-b2c/_static/signin.png)

Der Browser leitet an den Azure AD B2C-Mandanten weiter. Melden Sie sich mit einem vorhandenen Konto an (sofern erstellt wurde, testen Sie die Richtlinien), oder wählen Sie **jetzt registrieren** aus, um ein neues Konto zu erstellen. Der Link " **Kennwort vergessen?** " wird verwendet, um ein vergessenes Kennwort zurückzusetzen.

![Azure AD B2C-Anmeldung](./azure-ad-b2c/_static/b2csts.png)

Nach erfolgreicher Anmeldung wird der Browser an die Web-App umgeleitet.

![Erfolg](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten
> * Registrieren einer APP in Azure AD B2C
> * Verwenden von Visual Studio zum Erstellen einer ASP.net Core-Webanwendung, die für die Verwendung des Azure AD B2C-Mandanten zur Authentifizierung konfiguriert
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern

Nun, da die ASP.net Core-App für die Verwendung von Azure AD B2C für die Authentifizierung konfiguriert ist, kann das [Autorisierungs Attribut](xref:security/authorization/simple) verwendet werden, um Ihre APP zu sichern. Entwickeln Sie Ihre APP weiter, indem Sie Folgendes lernen:

* [Passen Sie die Azure AD B2C Benutzeroberfläche](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)an.
* Konfigurieren von Anforderungen für die Kenn [Wort Komplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)
* [Aktivieren Sie Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Konfigurieren Sie zusätzliche Identitäts Anbieter wie [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)und andere.
* [Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) , um zusätzliche Benutzerinformationen, wie z. b. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.
* [So sichern Sie eine Web-API, die mit ASP.net Core erstellt wurde, mithilfe des Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).
* [Tutorial: Gewähren des Zugriffs auf eine ASP.net-Web-API mithilfe von Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).
