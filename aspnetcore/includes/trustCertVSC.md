---
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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551351"
---
* <span data-ttu-id="7162c-101">Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="7162c-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="7162c-102">Der oben genannte Befehl funktioniert nicht unter Linux.</span><span class="sxs-lookup"><span data-stu-id="7162c-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="7162c-103">In der Dokumentation Ihrer Linux-Distribution finden Sie Informationen dazu, wie Sie ein Zertifikat als vertrauenswürdig kennzeichnen.</span><span class="sxs-lookup"><span data-stu-id="7162c-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="7162c-104">Über den vorherigen Befehl wird der folgende Dialog angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7162c-104">The preceding command displays the following dialog:</span></span>

  ![Dialogfeld „Sicherheitswarnung“](~/getting-started/_static/cert.png)

* <span data-ttu-id="7162c-106">Klicken Sie auf **Ja**, wenn Sie zustimmen möchten, dass das Entwicklungszertifikat vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="7162c-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="7162c-107">Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="7162c-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]