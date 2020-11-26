* Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  Der oben genannte Befehl funktioniert nicht unter Linux. In der Dokumentation Ihrer Linux-Distribution finden Sie Informationen dazu, wie Sie ein Zertifikat als vertrauenswürdig kennzeichnen.

  Über den vorherigen Befehl wird der folgende Dialog angezeigt:

  ![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

* Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.

  Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]