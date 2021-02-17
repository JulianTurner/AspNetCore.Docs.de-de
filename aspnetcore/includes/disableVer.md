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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552926"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>Standardkonto Überprüfung deaktivieren

Mit den Standardvorlagen wird der Benutzer an den umgeleitet, `Account.RegisterConfirmation` wo er einen Link auswählen kann, um das Konto zu bestätigen. Der Standardwert `Account.RegisterConfirmation` wird ***nur*** für Tests verwendet, die automatische Kontoüberprüfung sollte in einer Produktions-App deaktiviert werden.

Wenn Sie ein bestätigtes Konto benötigen und eine sofortige Anmeldung bei der Registrierung verhindern möchten, legen Sie `DisplayConfirmAccountLink = false` in */Areas/ Identity /pages/Account/RegisterConfirmation.cshtml.cs* fest:

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]