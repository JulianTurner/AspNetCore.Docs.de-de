* <span data-ttu-id="5e4db-101">Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="5e4db-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="5e4db-102">Der oben genannte Befehl funktioniert nicht unter Linux.</span><span class="sxs-lookup"><span data-stu-id="5e4db-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="5e4db-103">In der Dokumentation Ihrer Linux-Distribution finden Sie Informationen dazu, wie Sie ein Zertifikat als vertrauenswürdig kennzeichnen.</span><span class="sxs-lookup"><span data-stu-id="5e4db-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="5e4db-104">Über den vorherigen Befehl wird der folgende Dialog angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5e4db-104">The preceding command displays the following dialog:</span></span>

  ![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

* <span data-ttu-id="5e4db-106">Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="5e4db-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="5e4db-107">Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="5e4db-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]