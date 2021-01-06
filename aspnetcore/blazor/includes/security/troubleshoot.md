## <a name="troubleshoot"></a>Problembehandlung

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a>Häufige Fehler

* Nicht autorisierter Client für AAD

  > Info: Die Autorisierung von Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization ist fehlgeschlagen. Diese Anforderungen wurden nicht erfüllt: DenyAnonymousAuthorizationRequirement: Erfordert einen authentifizierten Benutzer.

  Anmelderückruffehler von AAD:

  * Fehler: `unauthorized_client`
  * Description (Beschreibung): `AADB2C90058: The provided application is not configured to allow public clients.`

  So beheben Sie den Fehler

  1. Greifen Sie im Azure-Portal auf das [Manifest der App](/azure/active-directory/develop/reference-app-manifest) zu.
  1. Legen Sie das Attribut [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) auf `null` oder `true` fest.

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookies und Standortdaten

Cookies und Standortdaten können über App-Updates hinweg beibehalten werden und das Testen und die Problembehandlung beeinträchtigen. Entfernen Sie Folgendes, wenn Sie Änderungen am App-Code, Änderungen an den Benutzerkonten beim Anbieter oder Konfigurationsänderungen an Anbieter-Apps vornehmen:

* Anmelde-Cookies von Benutzern
* App-Cookies
* Zwischengespeicherte und gespeicherte Standortdaten

Ein Ansatz, um zu verhindern, dass veraltete Cookies und Standortdaten das Testen und die Problembehandlung beeinträchtigen, ist folgender:

* Browser konfigurieren
  * Verwenden Sie zum Testen einen Browser, den Sie so konfigurieren können, dass alle Cookies und Standortdaten jedes Mal gelöscht werden, wenn der Browser geschlossen wird.
  * Stellen Sie sicher, dass der Browser manuell oder durch die IDE für alle Änderungen an der App, dem Testbenutzer oder der Anbieterkonfiguration geschlossen wird.
* Verwenden Sie einen benutzerdefinierten Befehl, um in Visual Studio einen Browser im Inkognito- oder im privaten Modus zu öffnen:
  * Öffnen Sie mithilfe der Schaltfläche **Ausführen** von Visual Studio das Dialogfeld **Browserauswahl**.
  * Wählen Sie die Schaltfläche **Hinzufügen** aus.
  * Geben Sie im Feld **Programm** den Pfad zu Ihrem Browser an. Die folgenden Pfade für ausführbare Dateien sind typische Installationspfade für Windows 10. Wenn Ihr Browser an einem anderen Speicherort installiert ist oder Sie nicht Windows 10 verwenden, geben Sie den Pfad zur ausführbaren Datei des Browsers an.
    * Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`
  * Geben Sie im Feld **Argumente** die Befehlszeilenoption an, die der Browser verwendet, um im Inkognito- oder im privaten Modus geöffnet zu werden. Für einige Browser ist die URL der App erforderlich.
    * Microsoft Edge: Verwenden Sie `-inprivate`.
    * Google Chrome: Verwenden Sie `--incognito --new-window {URL}`, wobei der Platzhalter `{URL}` die zu öffnende URL ist (z. B. `https://localhost:5001`).
    * Mozilla Firefox: Verwenden Sie `-private -url {URL}`, wobei der Platzhalter `{URL}` die zu öffnende URL ist (z. B. `https://localhost:5001`).
  * Geben Sie im Feld **Anzeigename** einen Namen ein. Beispielsweise `Firefox Auth Testing`.
  * Klicken Sie auf die Schaltfläche **OK**.
  * Um zu vermeiden, dass das Browserprofil für jede einzelne Testiteration einer App ausgewählt werden muss, legen Sie das Profil mithilfe der Schaltfläche **Als Standard festlegen** als Standard fest.
  * Stellen Sie sicher, dass der Browser von der IDE für alle Änderungen an der App, dem Testbenutzer oder der Anbieterkonfiguration geschlossen wird.

### <a name="run-the-server-app"></a>Ausführen der Server-App

Stellen Sie beim Testen und Beheben von Problemen bei einer gehosteten Blazor-App sicher, dass Sie die App aus dem **`Server`** -Projekt ausführen. Vergewissern Sie sich z. B. in Visual Studio, dass das Serverprojekt im **Projektmappen-Explorer** markiert ist, bevor Sie die App mit einem der folgenden Ansätze starten:

* Wählen Sie die Schaltfläche **Ausführen**.
* Verwenden Sie im Menü **Debuggen** > **Debuggen starten**.
* Drücken Sie <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Überprüfen des Inhalts eines JSON Web Tokens (JWT)

Verwenden Sie zum Decodieren eines JSON Web Tokens (JWT) das Tool [jwt.ms](https://jwt.ms/) von Microsoft. Werte in der Benutzeroberfläche verlassen nie Ihren Browser.
