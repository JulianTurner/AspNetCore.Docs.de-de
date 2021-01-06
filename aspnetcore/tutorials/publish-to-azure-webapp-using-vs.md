---
title: Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060221"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio

Von [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Lesen Sie [Veröffentlichen einer Web-App in Azure App Service mit Visual Studio für Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019), wenn Sie unter macOS arbeiten.

Informationen zur Problembehandlung bei der App Service-Bereitstellung finden Sie unter <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Einrichten

* Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen. 

## <a name="create-a-web-app"></a>Erstellen einer Web-App

Klicken Sie auf der Startseite von Visual Studio auf **Datei > Neu > Projekt...** .

![Datei (Menü)](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Schließen Sie das Dialogfeld **Neues Projekt** ab:

* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Klicken Sie auf **Weiter**.

![Dialogfeld "Neues Projekt"](publish-to-azure-webapp-using-vs/_static/new_prj.png)

Gehen Sie im Dialogfeld **ASP.NET Core-Webanwendung** folgendermaßen vor:

* Wählen Sie **Webanwendung** aus.
* Wählen Sie **Ändern** unter „Authentifizierung“ aus.

![Neues ASP.NET Core Web-Dialogfeld](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Das Dialogfeld **Authentifizierung ändern** wird angezeigt. 

* Wählen Sie **Einzelne Benutzerkonten** aus.
* Wählen Sie **OK** aus, um zur Seite **Neue ASP.NET Core-Webanwendung** zurückzukehren, und dann **Erstellen**.

![Dialogfeld „New ASP.NET Core Web Authentication“ (Neue ASP.NET Core-Webauthentifizierung)](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio erstellt die Projektmappe.

## <a name="run-the-app"></a>Ausführen der App

* Drücken Sie STRG+F5, um das Projekt auszuführen.
* Testen Sie den Link **Datenschutz**.

![Auf „localhost“ in Microsoft Edge geöffnete Webanwendung](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Registrieren eines Benutzers

* Wählen Sie **Registrieren**, und registrieren Sie einen neuen Benutzer. Sie können eine fiktive E-Mail-Adresse verwenden. Sobald Sie diese übermitteln, zeigt die Seite die folgende Fehlermeldung an:

    *„Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.“*
* Wählen Sie **Migrationen anwenden** aus, und aktualisieren Sie die Seite.

![Fehler bei einem Datenbankvorgang beim Verarbeiten der Anforderung. Das Anwenden vorhandener Migrationen für den Datenbankkontext der Anwendung kann dieses Problem beheben.](publish-to-azure-webapp-using-vs/_static/mig.png)

Die App zeigt die zum Registrieren des neuen Benutzers verwendete E-Mail-Adresse und den Link **Abmelden** an.

![In Microsoft Edge geöffnete Webanwendung Der Link „Register“ wird durch den Text „Hello user1@example.com!“ ersetzt.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Bereitstellen der App in Azure

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen...** aus.

![Geöffnetes Kontextmenü mit hervorgehobenem Link „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/pub.png)

Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:

* Wählen Sie **Azure** aus.
* Klicken Sie auf **Weiter**.

![Dialogfeld „Veröffentlichen“](publish-to-azure-webapp-using-vs/_static/maas1.png)

Führen Sie im Dialogfeld **Veröffentlichen** folgende Schritte aus:

* Wählen Sie **Azure App Service (Linux)** aus.
* Klicken Sie auf **Weiter**.

![Dialogfeld „Veröffentlichen“: Azure Service auswählen](publish-to-azure-webapp-using-vs/_static/maas2.png)

Wählen Sie im Dialogfeld **Veröffentlichen** die Option **Neuen Azure App Service erstellen...** aus.

![Dialogfeld „Veröffentlichen“: Azure Service-Instanz auswählen](publish-to-azure-webapp-using-vs/_static/maas3.png)

Das Dialogfeld **App Service erstellen** wird angezeigt:

* Die Eingabefelder **App-Name**, **Ressourcengruppe** und **App Service-Plan** werden aufgefüllt. Sie können diese Namen beibehalten oder ändern.
* Wählen Sie **Erstellen** aus.

![Dialogfeld „App Service erstellen“](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen, und das Dialogfeld **Veröffentlichen** wird wieder angezeigt:

* Die gerade erstellte neue Instanz wird automatisch ausgewählt.
* Wählen Sie **Fertig stellen** aus.

![Dialogfeld „Veröffentlichen“: App Service-Instanz auswählen](publish-to-azure-webapp-using-vs/_static/select_as.png)

Als Nächstes wird die Seite **Zusammenfassung des Veröffentlichungsprofils** angezeigt. Visual Studio hat festgestellt, dass für diese Anwendung eine SQL Server-Datenbankinstanz erforderlich ist, und Sie werden aufgefordert, Sie zu konfigurieren. Wählen Sie **Konfigurieren** aus.

![Seite „Zusammenfassung des Veröffentlichungsprofils“: Konfigurieren der SQL Server-Abhängigkeit](publish-to-azure-webapp-using-vs/_static/sql.png)

Das Dialogfeld **Abhängigkeit konfigurieren** wird angezeigt:

* Wählen Sie **Azure SQL-Datenbank** aus.
* Klicken Sie auf **Weiter**.

![Dialogfeld „SQL Server-Abhängigkeit konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql1.png)

Wählen Sie im Dialogfeld **Azure SQL-Datenbank konfigurieren** die Option **Erstellen einer SQL-Datenbank** aus.

![Dialogfeld „Azure SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql2.png)

Das Dialogfeld **Erstellen einer Azure SQL-Datenbank** wird angezeigt:

* Die Eingabefelder **Datenbankname**, **Ressourcengruppe**, **Datenbankserver** und **App Service-Plan** werden aufgefüllt. Sie können diese Werte beibehalten oder ändern.
* Geben Sie **Datenbankadministrator-Benutzername** und **Datenbankadministrator-Kennwort** für den ausgewählten **Datenbankserver** ein (beachten Sie, dass das verwendete Konto über die erforderlichen Berechtigungen zum Erstellen der neuen Instanz von Azure SQL-Datenbank verfügen muss).
* Wählen Sie **Erstellen** aus.

![Dialogfeld „Neue Azure SQL-Datenbank“](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Nachdem die Erstellung abgeschlossen ist, wird das Dialogfeld automatisch geschlossen, und das Dialogfeld **Azure SQL-Datenbank konfigurieren** wird wieder angezeigt:

* Die gerade erstellte neue Instanz wird automatisch ausgewählt.
* Klicken Sie auf **Weiter**.

![Dialogfeld „Azure SQL-Datenbank konfigurieren“](publish-to-azure-webapp-using-vs/_static/sql_select.png)

Im nächsten Schritt des Dialogfelds **Azure SQL-Datenbank konfigurieren**:

* Füllen Sie die Felder **Datenbankverbindungs-Benutzername** und **Datenbankverbindungs-Kennwort** aus. Diese Details verwendet Ihre Anwendung zur Runtime zum Herstellen einer Verbindung mit der Datenbank. Sie sollten vermeiden, den gleichen Administratorbenutzernamen und das gleiche Kennwort wie im vorherigen Schritt zu verwenden.
* Wählen Sie **Fertig stellen** aus.

![Dialogfeld „Azure SQL-Datenbank konfigurieren“, Verbindungszeichenfolgen-Details](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Wählen Sie auf der Seite **Zusammenfassung des Veröffentlichungsprofils** die Option **Einstellungen** aus:

![Seite „Zusammenfassung des Veröffentlichungsprofils“: Einstellungen bearbeiten](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Gehen Sie im Bereich **Einstellungen** im Dialogfeld **Veröffentlichen** so vor:

* Erweitern Sie **Datenbanken**, und aktivieren Sie **Diese Verbindungszeichenfolge zur Laufzeit verwenden**.
* Erweitern Sie **Entity Framework-Migrationen**, und aktivieren Sie **Diese Migration auf Veröffentlichung anwenden**.

* Klicken Sie auf **Speichern**. Visual Studio kehrt zum Dialogfeld **Veröffentlichen** zurück. 

![Dialogfeld „Veröffentlichen“: Panel „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Klicken Sie auf **Veröffentlichen**. Visual Studio veröffentlicht Ihre App in Azure. Wenn die Bereitstellung abgeschlossen ist, wird die App im Browser geöffnet.

![Dialogfeld „Veröffentlichen“: Panel „Einstellungen“](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Aktualisieren der App

* Bearbeiten Sie die Razor-Seite *Pages/Index.cshtml*, und verändern Sie deren Inhalt. Sie können beispielsweise den Absatz so verändern, dass er „Hallo ASP.NET Core!“ anzeigt.

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Wählen Sie erneut **Veröffentlichen** auf der Seite **Zusammenfassung des Veröffentlichungsprofils** aus.

![Seite „Zusammenfassung des Veröffentlichungsprofils“](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Nachdem die Anwendung veröffentlicht wurde, vergewissern Sie sich, dass die vorgenommenen Änderungen in Azure verfügbar sind.

![Der Überprüfungstask ist abgeschlossen.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Bereinigen

Sobald Sie das Testen der App abgeschlossen haben, wechseln Sie zum [Azure-Portal](https://portal.azure.com/), und löschen Sie die App.

* Wählen Sie **Ressourcengruppen** aus, und wählen Sie dann die Ressourcengruppe aus, die Sie erstellt haben.

![Azure-Portal: Ressourcengruppen im Menü auf der Randleiste](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Wählen Sie auf der Seite **Ressourcengruppen** **Löschen** aus.

![Azure-Portal: Seite „Ressourcengruppen“](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie auf **Löschen**. Ihre App und alle anderen in diesem Tutorial erstellten Ressourcen werden nun aus Azure gelöscht.

### <a name="next-steps"></a>Nächste Schritte

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Visual Studio Code finden Sie unter [Veröffentlichungsprofile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure-Ressourcengruppen](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL-Datenbank](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>