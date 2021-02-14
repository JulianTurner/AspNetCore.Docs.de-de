---
title: Schützen von Blazor Server-Apps von ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server-Apps als ASP.NET Core-Anwendungen schützen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/index
ms.openlocfilehash: 5a3d3c6e06653de7f0d01565444d37013f347a5b
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280312"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="7c978-103">Schützen von Blazor Server-Apps von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c978-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="7c978-104">Das Schützen von Blazor Server-Apps funktioniert genau wie bei ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="7c978-104">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="7c978-105">Weitere Informationen finden Sie in den Artikeln unter <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="7c978-105">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="7c978-106">Die Themen in dieser Übersicht gelten speziell für Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="7c978-106">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="7c978-107">Blazor Server-Projektvorlage</span><span class="sxs-lookup"><span data-stu-id="7c978-107">Blazor Server project template</span></span>

<span data-ttu-id="7c978-108">Die Projektvorlage Blazor Server kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7c978-108">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7c978-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7c978-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7c978-110">Befolgen Sie die Visual Studio-Anleitungen in <xref:blazor/tooling>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7c978-110">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="7c978-111">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="7c978-111">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="7c978-112">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="7c978-112">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="7c978-113">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="7c978-113">**No Authentication**</span></span>
* <span data-ttu-id="7c978-114">**Einzelne Benutzerkonten**: Benutzerkonten können wie folgt gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="7c978-114">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="7c978-115">Innerhalb der App anhand des Systems [Identity](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c978-115">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="7c978-116">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="7c978-116">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="7c978-117">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="7c978-117">**Work or School Accounts**</span></span>
* <span data-ttu-id="7c978-118">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="7c978-118">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7c978-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7c978-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7c978-120">Befolgen Sie die Visual Studio Code-Anleitungen in <xref:blazor/tooling>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="7c978-120">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="7c978-121">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7c978-121">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="7c978-122">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="7c978-122">Authentication mechanism</span></span> | <span data-ttu-id="7c978-123">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7c978-123">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="7c978-124">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="7c978-124">`None` (default)</span></span>         | <span data-ttu-id="7c978-125">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="7c978-125">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="7c978-126">In der App mit ASP.NET Core Identity gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="7c978-126">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="7c978-127">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="7c978-127">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="7c978-128">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="7c978-128">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="7c978-129">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="7c978-129">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="7c978-130">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="7c978-130">Windows Authentication</span></span> |

<span data-ttu-id="7c978-131">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="7c978-131">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="7c978-132">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="7c978-132">Create a folder for the project.</span></span>
* <span data-ttu-id="7c978-133">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="7c978-133">Name the project.</span></span>

<span data-ttu-id="7c978-134">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c978-134">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7c978-135">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="7c978-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="7c978-136">Befolgen Sie die Anleitungen für Visual Studio für Mac in <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="7c978-136">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="7c978-137">Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor Server-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="7c978-137">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="7c978-138">Die App wird für einzelne Benutzer erstellt, die mit ASP.NET Core Identity in der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="7c978-138">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="7c978-139">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="7c978-139">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="7c978-140">Erstellen Sie ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus, indem Sie folgenden Befehl an einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="7c978-140">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="7c978-141">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="7c978-141">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="7c978-142">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="7c978-142">Authentication mechanism</span></span> | <span data-ttu-id="7c978-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7c978-143">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="7c978-144">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="7c978-144">`None` (default)</span></span>         | <span data-ttu-id="7c978-145">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="7c978-145">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="7c978-146">In der App mit ASP.NET Core Identity gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="7c978-146">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="7c978-147">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="7c978-147">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="7c978-148">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="7c978-148">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="7c978-149">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="7c978-149">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="7c978-150">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="7c978-150">Windows Authentication</span></span> |

<span data-ttu-id="7c978-151">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="7c978-151">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="7c978-152">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="7c978-152">Create a folder for the project.</span></span>
* <span data-ttu-id="7c978-153">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="7c978-153">Name the project.</span></span>

<span data-ttu-id="7c978-154">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="7c978-154">For more information:</span></span>

* <span data-ttu-id="7c978-155">Lesen Sie im Leitfaden für .NET Core die Informationen zum Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="7c978-155">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="7c978-156">Führen Sie in einer Befehlsshell den Hilfebefehl für die Vorlage Blazor Server (`blazorserver`) aus:</span><span class="sxs-lookup"><span data-stu-id="7c978-156">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-identity"></a><span data-ttu-id="7c978-157">Gerüst Identity</span><span class="sxs-lookup"><span data-stu-id="7c978-157">Scaffold Identity</span></span>

<span data-ttu-id="7c978-158">Gerüst Identity in einem Blazor Server-Projekt:</span><span class="sxs-lookup"><span data-stu-id="7c978-158">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="7c978-159">[Ohne vorhandene Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="7c978-159">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="7c978-160">[Mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="7c978-160">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="azure-app-service-on-linux-with-identity-server"></a><span data-ttu-id="7c978-161">Azure App Service für Linux mit Identity Server</span><span class="sxs-lookup"><span data-stu-id="7c978-161">Azure App Service on Linux with Identity Server</span></span>

<span data-ttu-id="7c978-162">Geben Sie den Zertifikataussteller explizit an, wenn Sie eine Bereitstellung auf Azure App Service für Linux mit Identity Server durchführen.</span><span class="sxs-lookup"><span data-stu-id="7c978-162">Specify the issuer explicitly when deploying to Azure App Service on Linux with Identity Server.</span></span> <span data-ttu-id="7c978-163">Weitere Informationen finden Sie unter <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span><span class="sxs-lookup"><span data-stu-id="7c978-163">For more information, see <xref:security/authentication/identity/spa#azure-app-service-on-linux>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c978-164">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7c978-164">Additional resources</span></span>

* [<span data-ttu-id="7c978-165">Schnellstart: Hinzufügen von „Bei Microsoft anmelden“ zu einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="7c978-165">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="7c978-166">Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="7c978-166">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="7c978-167"><xref:host-and-deploy/proxy-load-balancer>: Umfasst Hinweise zu:</span><span class="sxs-lookup"><span data-stu-id="7c978-167"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="7c978-168">der Verwendung der Middleware für weitergeleitete Header, um HTTPS-Schemainformationen für Proxyserver und interne Netzwerke zu schützen</span><span class="sxs-lookup"><span data-stu-id="7c978-168">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="7c978-169">zusätzlichen Szenarios und Anwendungsfällen, einschließlich der manuellen Schemakonfiguration, Änderungen von Anforderungspfaden für fehlerfreies Routing von Anforderungen und der Weiterleitung des Anforderungsschemas für Linux- und Nicht-IIS-Reverseproxys.</span><span class="sxs-lookup"><span data-stu-id="7c978-169">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
