---
title: Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/social/index
ms.openlocfilehash: 1f7c8cd0716f1ada3517add0d37a09e419f38774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053305"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="6d473-103">Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6d473-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="6d473-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6d473-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6d473-105">Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 3.0-App erstellen, mit der Benutzer sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anmelden können.</span><span class="sxs-lookup"><span data-stu-id="6d473-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="6d473-106">Die Anbieter [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) und [Microsoft](xref:security/authentication/microsoft-logins) werden in den folgenden Abschnitten behandelt und verwenden das in diesem Artikel erstellte Startprojekt.</span><span class="sxs-lookup"><span data-stu-id="6d473-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="6d473-107">Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6d473-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="6d473-108">Das Anmelden mit vorhandenen Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="6d473-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="6d473-109">ist für Benutzer sehr praktisch.</span><span class="sxs-lookup"><span data-stu-id="6d473-109">Is convenient for the users.</span></span>
* <span data-ttu-id="6d473-110">lagert die Verwaltung des Anmeldevorgangs größtenteils an einen Drittanbieter aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="6d473-111">Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="6d473-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="6d473-112">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="6d473-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d473-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d473-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6d473-114">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="6d473-114">Create a new project.</span></span>
* <span data-ttu-id="6d473-115">Wählen Sie **ASP.NET Core-Webanwendung** und **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="6d473-116">Geben Sie einen **Projektnamen** ein, und bestätigen oder ändern Sie den **Speicherort**.</span><span class="sxs-lookup"><span data-stu-id="6d473-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="6d473-117">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-117">Select **Create**.</span></span>
* <span data-ttu-id="6d473-118">Wählen Sie die neueste Version von ASP.NET Core in der Dropdownliste ( **ASP.NET Core {X.Y}** ) und dann **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-118">Select the latest version of ASP.NET Core in the drop-down ( **ASP.NET Core {X.Y}** ), and then select **Web Application**.</span></span>
* <span data-ttu-id="6d473-119">Wählen Sie unter **Authentifizierung** die Option **Ändern** aus, und legen Sie die Authentifizierung auf **Einzelne Benutzerkonten** fest.</span><span class="sxs-lookup"><span data-stu-id="6d473-119">Under **Authentication** , select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="6d473-120">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6d473-120">Select **OK**.</span></span>
* <span data-ttu-id="6d473-121">Wählen Sie im Fenster **Neue ASP.NET Core-Webanwendung erstellen** die Option **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="6d473-122">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6d473-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="6d473-123">Öffnen Sie das Terminal.</span><span class="sxs-lookup"><span data-stu-id="6d473-123">Open the terminal.</span></span>  <span data-ttu-id="6d473-124">Für Visual Studio Code können Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) öffnen.</span><span class="sxs-lookup"><span data-stu-id="6d473-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="6d473-125">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="6d473-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="6d473-126">Führen Sie für Windows den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6d473-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="6d473-127">Führen Sie für macOS und Linux den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6d473-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="6d473-128">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="6d473-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="6d473-129">`-au Individual` erstellt den Code für die einzelne Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="6d473-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="6d473-130">`-uld` verwendet LocalDB, eine vereinfachte Version von SQL Server Express für Windows.</span><span class="sxs-lookup"><span data-stu-id="6d473-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="6d473-131">Lassen Sie `-uld` weg, um SQLite zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="6d473-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="6d473-132">Mit dem Befehl `code` wird der Ordner *WebApp1* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="6d473-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="6d473-133">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="6d473-133">Apply migrations</span></span>

* <span data-ttu-id="6d473-134">Führen Sie die App aus, und klicken Sie auf den Link **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="6d473-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="6d473-135">Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="6d473-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="6d473-136">Befolgen Sie die Anweisungen zum Anwenden von Migrationen.</span><span class="sxs-lookup"><span data-stu-id="6d473-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="6d473-137">Verwenden von SecretManager zum Speichern von Token, die von Anmeldeanbietern zugewiesen wurden</span><span class="sxs-lookup"><span data-stu-id="6d473-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="6d473-138">Anmeldeanbieter aus dem Bereich der sozialen Medien weisen während des Registrierungsvorgangs Token des Typs **Anwendungs-ID** und **Anwendungsgeheimnis** zu.</span><span class="sxs-lookup"><span data-stu-id="6d473-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="6d473-139">Die genauen Tokennamen unterscheiden sich je nach Anbieter.</span><span class="sxs-lookup"><span data-stu-id="6d473-139">The exact token names vary by provider.</span></span> <span data-ttu-id="6d473-140">Diese Token stellen die Anmeldeinformationen dar, mit denen Ihre App auf ihre API zugreift.</span><span class="sxs-lookup"><span data-stu-id="6d473-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="6d473-141">Diese Token setzen sich zu „Geheimnissen“ zusammen, die mithilfe von [Secret Manager](xref:security/app-secrets#secret-manager) mit Ihrer App-Konfiguration verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="6d473-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="6d473-142">Secret Manager ist eine sicherere Alternative zum Speichern von Token in einer Konfigurationsdatei wie z. B. *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="6d473-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6d473-143">Secret Manager ist nur zur Entwicklung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="6d473-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="6d473-144">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="6d473-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="6d473-145">Führen Sie die im Artikel [Sichere Speicherung von App-Geheimnissen bei einer Entwicklung in ASP.NET Core](xref:security/app-secrets) beschriebenen Schritte durch, um Token zu speichern, die von den folgenden Anmeldeanbietern zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6d473-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="6d473-146">Einrichten der für Ihre Anwendung erforderlichen Anmeldeanbietern</span><span class="sxs-lookup"><span data-stu-id="6d473-146">Setup login providers required by your application</span></span>

<span data-ttu-id="6d473-147">In den folgenden Themen erfahren Sie, wie Sie Ihre Anwendung für die entsprechenden Anbieter konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="6d473-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="6d473-148">Anweisungen für [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="6d473-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="6d473-149">Anweisungen für [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="6d473-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="6d473-150">Anweisungen für [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="6d473-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="6d473-151">Anweisungen für [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="6d473-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="6d473-152">Anweisungen für [andere Anbieter](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="6d473-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="6d473-153">Optionales Festlegen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="6d473-153">Optionally set password</span></span>

<span data-ttu-id="6d473-154">Wenn Sie sich bei einem externen Anmeldeanbieter registrieren, wird kein Kennwort bei der App registriert.</span><span class="sxs-lookup"><span data-stu-id="6d473-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="6d473-155">Dadurch entfällt für Sie Erstellen und Merken eines Kennworts für die Website. Sie werden allerdings auch vom externen Anmeldeanbieter abhängig.</span><span class="sxs-lookup"><span data-stu-id="6d473-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="6d473-156">Wenn der externe Anmeldeanbieter nicht verfügbar ist, können Sie sich nicht bei der Website anmelden.</span><span class="sxs-lookup"><span data-stu-id="6d473-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="6d473-157">So erstellen Sie ein Kennwort und melden sich mithilfe Ihrer E-Mail-Adresse an, die Sie während des Anmeldevorgangs bei externen Anbietern festgelegt haben:</span><span class="sxs-lookup"><span data-stu-id="6d473-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="6d473-158">Klicken Sie rechts oben auf den Link **Hallo &lt;E-Mail-Alias&gt;** , um zur Ansicht **Verwalten** zu gelangen.</span><span class="sxs-lookup"><span data-stu-id="6d473-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* <span data-ttu-id="6d473-160">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="6d473-160">Select **Create**</span></span>

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* <span data-ttu-id="6d473-162">Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.</span><span class="sxs-lookup"><span data-stu-id="6d473-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6d473-163">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6d473-163">Next steps</span></span>

* <span data-ttu-id="6d473-164">Weitere Informationen zum Anpassen der Anmeldeschaltfläche finden Sie unter [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/10563).</span><span class="sxs-lookup"><span data-stu-id="6d473-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="6d473-165">In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.</span><span class="sxs-lookup"><span data-stu-id="6d473-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="6d473-166">Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.</span><span class="sxs-lookup"><span data-stu-id="6d473-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="6d473-167">Möglicherweise möchten Sie zusätzliche Daten zum Benutzer und seinen Zugriffs- und Aktualisierungstoken persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="6d473-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="6d473-168">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="6d473-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
