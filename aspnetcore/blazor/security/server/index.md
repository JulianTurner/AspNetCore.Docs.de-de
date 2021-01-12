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
ms.openlocfilehash: aa24def1a003a2c2608691e6168066c740f47205
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024625"
---
# <a name="secure-aspnet-core-no-locblazor-server-apps"></a><span data-ttu-id="b11b6-103">Schützen von Blazor Server-Apps von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b11b6-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="b11b6-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b11b6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b11b6-105">Das Schützen von Blazor Server-Apps funktioniert genau wie bei ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="b11b6-105">Blazor Server apps are configured for security in the same manner as ASP.NET Core apps.</span></span> <span data-ttu-id="b11b6-106">Weitere Informationen finden Sie in den Artikeln unter <xref:security/index>.</span><span class="sxs-lookup"><span data-stu-id="b11b6-106">For more information, see the articles under <xref:security/index>.</span></span> <span data-ttu-id="b11b6-107">Die Themen in dieser Übersicht gelten speziell für Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="b11b6-107">Topics under this overview apply specifically to Blazor Server.</span></span>

## <a name="no-locblazor-server-project-template"></a><span data-ttu-id="b11b6-108">Blazor Server-Projektvorlage</span><span class="sxs-lookup"><span data-stu-id="b11b6-108">Blazor Server project template</span></span>

<span data-ttu-id="b11b6-109">Die Projektvorlage Blazor Server kann beim Erstellen des Projekts für die Authentifizierung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b11b6-109">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b11b6-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b11b6-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="b11b6-111">Befolgen Sie die Visual Studio-Anleitungen in <xref:blazor/tooling>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b11b6-111">Follow the Visual Studio guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="b11b6-112">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="b11b6-112">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="b11b6-113">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="b11b6-113">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="b11b6-114">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="b11b6-114">**No Authentication**</span></span>
* <span data-ttu-id="b11b6-115">**Einzelne Benutzerkonten**: Benutzerkonten können wie folgt gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="b11b6-115">**Individual User Accounts**: User accounts can be stored:</span></span>
  * <span data-ttu-id="b11b6-116">Innerhalb der App anhand des Systems [Identity](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b11b6-116">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="b11b6-117">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="b11b6-117">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="b11b6-118">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="b11b6-118">**Work or School Accounts**</span></span>
* <span data-ttu-id="b11b6-119">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="b11b6-119">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="b11b6-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b11b6-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="b11b6-121">Befolgen Sie die Visual Studio Code-Anleitungen in <xref:blazor/tooling>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="b11b6-121">Follow the Visual Studio Code guidance in <xref:blazor/tooling> to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b11b6-122">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-122">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b11b6-123">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="b11b6-123">Authentication mechanism</span></span> | <span data-ttu-id="b11b6-124">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b11b6-124">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b11b6-125">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="b11b6-125">`None` (default)</span></span>         | <span data-ttu-id="b11b6-126">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b11b6-126">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b11b6-127">In der App mit ASP.NET Core Identity gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b11b6-127">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b11b6-128">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b11b6-128">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b11b6-129">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="b11b6-129">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b11b6-130">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="b11b6-130">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b11b6-131">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b11b6-131">Windows Authentication</span></span> |

<span data-ttu-id="b11b6-132">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="b11b6-132">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b11b6-133">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-133">Create a folder for the project.</span></span>
* <span data-ttu-id="b11b6-134">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-134">Name the project.</span></span>

<span data-ttu-id="b11b6-135">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b11b6-135">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b11b6-136">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b11b6-136">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b11b6-137">Befolgen Sie die Anleitungen für Visual Studio für Mac in <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="b11b6-137">Follow the Visual Studio for Mac guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="b11b6-138">Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor Server-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="b11b6-138">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="b11b6-139">Die App wird für einzelne Benutzer erstellt, die mit ASP.NET Core Identity in der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="b11b6-139">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b11b6-140">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b11b6-140">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="b11b6-141">Erstellen Sie ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus, indem Sie folgenden Befehl an einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="b11b6-141">Create a new Blazor Server project with an authentication mechanism using the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="b11b6-142">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-142">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="b11b6-143">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="b11b6-143">Authentication mechanism</span></span> | <span data-ttu-id="b11b6-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b11b6-144">Description</span></span> |
| ------------------------ | ----------- |
| <span data-ttu-id="b11b6-145">`None` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="b11b6-145">`None` (default)</span></span>         | <span data-ttu-id="b11b6-146">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b11b6-146">No authentication</span></span> |
| `Individual`             | <span data-ttu-id="b11b6-147">In der App mit ASP.NET Core Identity gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b11b6-147">Users stored in the app with ASP.NET Core Identity</span></span> |
| `IndividualB2C`          | <span data-ttu-id="b11b6-148">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="b11b6-148">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c)</span></span> |
| `SingleOrg`              | <span data-ttu-id="b11b6-149">Organisationsauthentifizierung für einen einzelnen Mandanten</span><span class="sxs-lookup"><span data-stu-id="b11b6-149">Organizational authentication for a single tenant</span></span> |
| `MultiOrg`               | <span data-ttu-id="b11b6-150">Organisationsauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="b11b6-150">Organizational authentication for multiple tenants</span></span> |
| `Windows`                | <span data-ttu-id="b11b6-151">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="b11b6-151">Windows Authentication</span></span> |

<span data-ttu-id="b11b6-152">Bei Verwenden der Option `-o|--output` nutzt der Befehl den für den Platzhalter `{APP NAME}` angegebenen Wert:</span><span class="sxs-lookup"><span data-stu-id="b11b6-152">Using the `-o|--output` option, the command uses the value provided for the `{APP NAME}` placeholder to:</span></span>

* <span data-ttu-id="b11b6-153">Erstellen Sie einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-153">Create a folder for the project.</span></span>
* <span data-ttu-id="b11b6-154">Benennen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="b11b6-154">Name the project.</span></span>

<span data-ttu-id="b11b6-155">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="b11b6-155">For more information:</span></span>

* <span data-ttu-id="b11b6-156">Lesen Sie im Leitfaden für .NET Core die Informationen zum Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="b11b6-156">See the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>
* <span data-ttu-id="b11b6-157">Führen Sie in einer Befehlsshell den Hilfebefehl für die Vorlage Blazor Server (`blazorserver`) aus:</span><span class="sxs-lookup"><span data-stu-id="b11b6-157">Execute the help command for the Blazor Server template (`blazorserver`) in a command shell:</span></span>

  ```dotnetcli
  dotnet new blazorserver --help
  ```

---

## <a name="scaffold-no-locidentity"></a><span data-ttu-id="b11b6-158">Gerüst Identity</span><span class="sxs-lookup"><span data-stu-id="b11b6-158">Scaffold Identity</span></span>

<span data-ttu-id="b11b6-159">Gerüst Identity in einem Blazor Server-Projekt:</span><span class="sxs-lookup"><span data-stu-id="b11b6-159">Scaffold Identity into a Blazor Server project:</span></span>

* <span data-ttu-id="b11b6-160">[Ohne vorhandene Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization)</span><span class="sxs-lookup"><span data-stu-id="b11b6-160">[Without existing authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-without-existing-authorization).</span></span>
* <span data-ttu-id="b11b6-161">[Mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization)</span><span class="sxs-lookup"><span data-stu-id="b11b6-161">[With authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-blazor-server-project-with-authorization).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b11b6-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b11b6-162">Additional resources</span></span>

* [<span data-ttu-id="b11b6-163">Schnellstart: Hinzufügen von „Bei Microsoft anmelden“ zu einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="b11b6-163">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="b11b6-164">Schnellstart: Schützen einer ASP.NET Core-Web-API mit Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="b11b6-164">Quickstart: Protect an ASP.NET Core web API with Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-web-api)
* <span data-ttu-id="b11b6-165"><xref:host-and-deploy/proxy-load-balancer>: Umfasst Hinweise zu:</span><span class="sxs-lookup"><span data-stu-id="b11b6-165"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="b11b6-166">der Verwendung der Middleware für weitergeleitete Header, um HTTPS-Schemainformationen für Proxyserver und interne Netzwerke zu schützen</span><span class="sxs-lookup"><span data-stu-id="b11b6-166">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="b11b6-167">zusätzlichen Szenarios und Anwendungsfällen, einschließlich der manuellen Schemakonfiguration, Änderungen von Anforderungspfaden für fehlerfreies Routing von Anforderungen und der Weiterleitung des Anforderungsschemas für Linux- und Nicht-IIS-Reverseproxys.</span><span class="sxs-lookup"><span data-stu-id="b11b6-167">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
