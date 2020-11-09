---
title: 'Einführung in Identity ASP.net Core'
author: rick-anderson
description: 'Verwenden Sie dies Identity mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.'
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052018"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="daa4e-104">Einführung in Identity ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="daa4e-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="daa4e-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="daa4e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="daa4e-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="daa4e-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="daa4e-107">Ist eine API, die die Benutzeroberflächen-Anmelde Funktionalität unterstützt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="daa4e-108">Von werden Benutzer, Kenn Wörter, Profildaten, Rollen, Ansprüche, Token, e-Mail-Bestätigung und mehr verwaltet.</span><span class="sxs-lookup"><span data-stu-id="daa4e-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="daa4e-109">Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="daa4e-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="daa4e-110">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="daa4e-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="daa4e-111">Der [ Identity Quellcode](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="daa4e-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="daa4e-112">[Gerüst Identity ](xref:security/authentication/scaffold-identity) und zeigen Sie die generierten Dateien an, um die Vorlagen Interaktion mit zu überprüfen Identity .</span><span class="sxs-lookup"><span data-stu-id="daa4e-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="daa4e-113">Identity wird in der Regel mit einer SQL Server Datenbank konfiguriert, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="daa4e-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="daa4e-114">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="daa4e-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="daa4e-115">In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="daa4e-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="daa4e-116">Hinweis: die Vorlagen behandeln Benutzername und e-Mail-Adresse für Benutzer als identisch.</span><span class="sxs-lookup"><span data-stu-id="daa4e-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="daa4e-117">Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie unter [Nächste Schritte](#next).</span><span class="sxs-lookup"><span data-stu-id="daa4e-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="daa4e-118">Die [Microsoft Identity-Plattform](/azure/active-directory/develop/) ist:</span><span class="sxs-lookup"><span data-stu-id="daa4e-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="daa4e-119">Eine Weiterentwicklung der Azure Active Directory (Azure AD)-Entwicklerplattform.</span><span class="sxs-lookup"><span data-stu-id="daa4e-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="daa4e-120">Ohne Beziehung zu ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="daa4e-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="daa4e-121">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="daa4e-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="daa4e-122">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="daa4e-122">Create a Web app with authentication</span></span>

<span data-ttu-id="daa4e-123">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="daa4e-125">Wählen Sie **Datei** > **neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-125">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="daa4e-126">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-126">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="daa4e-127">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="daa4e-128">Klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="daa4e-128">Click **OK** .</span></span>
* <span data-ttu-id="daa4e-129">Wählen Sie eine ASP.net Core **Webanwendung** , und wählen Sie dann **Authentifizierung ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-129">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="daa4e-130">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="daa4e-130">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="daa4e-132">Der vorherige Befehl erstellt eine Razor Web-App mithilfe von SQLite.</span><span class="sxs-lookup"><span data-stu-id="daa4e-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="daa4e-133">Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="daa4e-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="daa4e-134">Das generierte Projekt stellt [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="daa4e-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="daa4e-135">Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="daa4e-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="daa4e-136">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daa4e-136">For example:</span></span>

* <span data-ttu-id="daa4e-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="daa4e-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="daa4e-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="daa4e-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="daa4e-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="daa4e-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="daa4e-140">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="daa4e-140">Apply migrations</span></span>

<span data-ttu-id="daa4e-141">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="daa4e-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="daa4e-143">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="daa4e-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="daa4e-145">In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="daa4e-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="daa4e-146">Führen Sie für localdb den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="daa4e-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="daa4e-147">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="daa4e-147">Test Register and Login</span></span>

<span data-ttu-id="daa4e-148">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="daa4e-148">Run the app and register a user.</span></span> <span data-ttu-id="daa4e-149">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="daa4e-150">IdentityDienste konfigurieren</span><span class="sxs-lookup"><span data-stu-id="daa4e-150">Configure Identity services</span></span>

<span data-ttu-id="daa4e-151">Dienste werden in hinzugefügt `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="daa4e-152">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="daa4e-153">Der vorangehende markierte Code konfiguriert Identity mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="daa4e-154">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="daa4e-155">Identity wird aktiviert, indem aufgerufen wird <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="daa4e-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="daa4e-156">`UseAuthentication` Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="daa4e-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="daa4e-157">Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="daa4e-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="daa4e-158">`app.UseAuthorization` ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung</span><span class="sxs-lookup"><span data-stu-id="daa4e-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="daa4e-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` und `UseEndpoints` müssen in der Reihenfolge aufgerufen werden, die im vorangehenden Code angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="daa4e-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="daa4e-160">Weitere Informationen zu `IdentityOptions` und `Startup` finden Sie unter <xref:Microsoft.AspNetCore.Identity.IdentityOptions> und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="daa4e-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="daa4e-161">Gerüst Register, anmelden, abmelden und Register Bestätigung</span><span class="sxs-lookup"><span data-stu-id="daa4e-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="daa4e-163">Fügen Sie `Register` die `Login` Dateien,, `LogOut` und hinzu `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="daa4e-164">Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="daa4e-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-165">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="daa4e-166">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-166">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="daa4e-167">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="daa4e-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="daa4e-168">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="daa4e-169">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="daa4e-170">Weitere Informationen zum Gerüstbau finden Sie unter Identity [Gerüst Identität in einem Razor Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="daa4e-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="daa4e-171">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="daa4e-171">Examine Register</span></span>

<span data-ttu-id="daa4e-172">Wenn ein Benutzer auf die Schaltfläche **registrieren** auf der `Register` Seite klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="daa4e-173">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:</span><span class="sxs-lookup"><span data-stu-id="daa4e-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="daa4e-174">Anmelden</span><span class="sxs-lookup"><span data-stu-id="daa4e-174">Log in</span></span>

<span data-ttu-id="daa4e-175">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="daa4e-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="daa4e-176">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="daa4e-177">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="daa4e-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="daa4e-178">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="daa4e-179">`PasswordSignInAsync` wird für das- `_signInManager` Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="daa4e-180">Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="daa4e-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="daa4e-181">Abmelden</span><span class="sxs-lookup"><span data-stu-id="daa4e-181">Log out</span></span>

<span data-ttu-id="daa4e-182">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="daa4e-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="daa4e-183">Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="daa4e-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="daa4e-184">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einer gespeicherten Ansprüche des Benutzers cookie .</span><span class="sxs-lookup"><span data-stu-id="daa4e-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="daa4e-185">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml* angegeben:</span><span class="sxs-lookup"><span data-stu-id="daa4e-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="daa4e-186">Test Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-186">Test Identity</span></span>

<span data-ttu-id="daa4e-187">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="daa4e-188">IdentityFügen Sie zum Testen Folgendes hinzu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="daa4e-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="daa4e-189">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="daa4e-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="daa4e-190">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="daa4e-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="daa4e-191">Entdecken Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-191">Explore Identity</span></span>

<span data-ttu-id="daa4e-192">Weitere Informationen finden Sie unter Identity :</span><span class="sxs-lookup"><span data-stu-id="daa4e-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="daa4e-193">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="daa4e-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="daa4e-194">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="daa4e-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="daa4e-195">Identity Komponenten</span><span class="sxs-lookup"><span data-stu-id="daa4e-195">Identity Components</span></span>

<span data-ttu-id="daa4e-196">Alle Identity -abhängigen nuget-Pakete sind im ASP.net Core frei [gegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="daa4e-197">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="daa4e-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="daa4e-198">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.NET Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="daa4e-199">Migrieren zu ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="daa4e-200">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="daa4e-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="daa4e-201">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="daa4e-201">Setting password strength</span></span>

<span data-ttu-id="daa4e-202">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="daa4e-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="daa4e-203">Adddefault Identity und AddIdentity</span><span class="sxs-lookup"><span data-stu-id="daa4e-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="daa4e-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="daa4e-205">`AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="daa4e-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="daa4e-206">Weitere Informationen finden Sie unter [adddefault Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="daa4e-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="daa4e-207">Veröffentlichen statischer Identity Assets verhindern</span><span class="sxs-lookup"><span data-stu-id="daa4e-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="daa4e-208">Identity Identity Fügen Sie die folgende `ResolveStaticWebAssetsInputsDependsOn` Eigenschaft und das `RemoveIdentityAssets` Ziel der Projektdatei der APP hinzu, um zu verhindern, dass statische Assets (Stylesheets und JavaScript-Dateien für die Benutzeroberfläche) im Webstamm Verzeichnis veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="daa4e-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="daa4e-209">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="daa4e-209">Next Steps</span></span>

* <span data-ttu-id="daa4e-210">[ASP.NET Core Identity-Quellcode](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="daa4e-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="daa4e-211">Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="daa4e-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="daa4e-212">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="daa4e-213">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="daa4e-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="daa4e-214">ASP.NET Core Identity ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="daa4e-215">Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="daa4e-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="daa4e-216">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="daa4e-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="daa4e-217">Identity kann mithilfe einer SQL Server-Datenbank zum Speichern von Benutzernamen, Kenn Wörtern und Profildaten konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="daa4e-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="daa4e-218">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="daa4e-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="daa4e-219">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="daa4e-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="daa4e-220">In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="daa4e-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="daa4e-221">Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="daa4e-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="daa4e-222">Adddefault Identity und AddIdentity</span><span class="sxs-lookup"><span data-stu-id="daa4e-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="daa4e-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="daa4e-224">`AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="daa4e-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="daa4e-225">Weitere Informationen finden Sie unter [adddefault Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="daa4e-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="daa4e-226">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="daa4e-226">Create a Web app with authentication</span></span>

<span data-ttu-id="daa4e-227">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="daa4e-229">Wählen Sie **Datei** > **neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-229">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="daa4e-230">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-230">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="daa4e-231">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="daa4e-232">Klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="daa4e-232">Click **OK** .</span></span>
* <span data-ttu-id="daa4e-233">Wählen Sie eine ASP.net Core **Webanwendung** , und wählen Sie dann **Authentifizierung ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-233">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="daa4e-234">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK** .</span><span class="sxs-lookup"><span data-stu-id="daa4e-234">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-235">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="daa4e-236">Das generierte Projekt stellt [ASP.NET Core Identity](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="daa4e-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="daa4e-237">Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="daa4e-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="daa4e-238">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="daa4e-238">For example:</span></span>

* <span data-ttu-id="daa4e-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="daa4e-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="daa4e-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="daa4e-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="daa4e-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="daa4e-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="daa4e-242">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="daa4e-242">Apply migrations</span></span>

<span data-ttu-id="daa4e-243">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="daa4e-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="daa4e-245">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="daa4e-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-246">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="daa4e-247">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="daa4e-247">Test Register and Login</span></span>

<span data-ttu-id="daa4e-248">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="daa4e-248">Run the app and register a user.</span></span> <span data-ttu-id="daa4e-249">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="daa4e-250">IdentityDienste konfigurieren</span><span class="sxs-lookup"><span data-stu-id="daa4e-250">Configure Identity services</span></span>

<span data-ttu-id="daa4e-251">Dienste werden in hinzugefügt `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="daa4e-252">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="daa4e-253">Der vorangehende Code konfiguriert Identity mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="daa4e-254">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="daa4e-255">Identity wird durch Aufrufen von [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)aktiviert.</span><span class="sxs-lookup"><span data-stu-id="daa4e-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="daa4e-256">`UseAuthentication` Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="daa4e-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="daa4e-257">Weitere Informationen finden Sie unter der [ Identity options-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und dem [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="daa4e-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="daa4e-258">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="daa4e-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="daa4e-259">Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="daa4e-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="daa4e-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="daa4e-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="daa4e-261">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="daa4e-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="daa4e-262">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="daa4e-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="daa4e-263">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="daa4e-263">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="daa4e-264">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="daa4e-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="daa4e-265">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="daa4e-266">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="daa4e-267">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="daa4e-267">Examine Register</span></span>

<span data-ttu-id="daa4e-268">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="daa4e-269">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:</span><span class="sxs-lookup"><span data-stu-id="daa4e-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="daa4e-270">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="daa4e-271">**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="daa4e-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="daa4e-272">Anmelden</span><span class="sxs-lookup"><span data-stu-id="daa4e-272">Log in</span></span>

<span data-ttu-id="daa4e-273">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="daa4e-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="daa4e-274">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="daa4e-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="daa4e-275">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="daa4e-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="daa4e-276">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="daa4e-277">`PasswordSignInAsync` wird für das- `_signInManager` Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="daa4e-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="daa4e-278">Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="daa4e-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="daa4e-279">Abmelden</span><span class="sxs-lookup"><span data-stu-id="daa4e-279">Log out</span></span>

<span data-ttu-id="daa4e-280">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="daa4e-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="daa4e-281">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einer gespeicherten Ansprüche des Benutzers cookie .</span><span class="sxs-lookup"><span data-stu-id="daa4e-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="daa4e-282">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml* angegeben:</span><span class="sxs-lookup"><span data-stu-id="daa4e-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="daa4e-283">Test Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-283">Test Identity</span></span>

<span data-ttu-id="daa4e-284">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="daa4e-285">IdentityFügen Sie zum Testen [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) der Seite Datenschutz hinzu.</span><span class="sxs-lookup"><span data-stu-id="daa4e-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="daa4e-286">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="daa4e-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="daa4e-287">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="daa4e-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="daa4e-288">Entdecken Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-288">Explore Identity</span></span>

<span data-ttu-id="daa4e-289">Weitere Informationen finden Sie unter Identity :</span><span class="sxs-lookup"><span data-stu-id="daa4e-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="daa4e-290">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="daa4e-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="daa4e-291">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="daa4e-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="daa4e-292">Identity Komponenten</span><span class="sxs-lookup"><span data-stu-id="daa4e-292">Identity Components</span></span>

<span data-ttu-id="daa4e-293">Alle Identity abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="daa4e-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="daa4e-294">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="daa4e-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="daa4e-295">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.NET Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="daa4e-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="daa4e-296">Migrieren zu ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="daa4e-297">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="daa4e-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="daa4e-298">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="daa4e-298">Setting password strength</span></span>

<span data-ttu-id="daa4e-299">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="daa4e-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="daa4e-300">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="daa4e-300">Next Steps</span></span>

* <span data-ttu-id="daa4e-301">Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="daa4e-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="daa4e-302">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="daa4e-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
