---
title: Microsoft Identity Platform und Azure Active Directory mit ASP.NET Core
author: rick-anderson
description: Entdecken Sie die Themen zur Authentifizierung mit Microsoft Identity Platform, Azure Active Directory für Web-Apps und APIs in ASP.NET Core.
ms.author: riande
ms.date: 01/21/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/azure-active-directory/index
ms.openlocfilehash: c8a3d6838b9b93ff58dfaff8423bd6946b5ca743
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061404"
---
# <a name="azure-active-directory-with-aspnet-core"></a><span data-ttu-id="c4eb7-103">Azure Active Directory mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4eb7-103">Azure Active Directory with ASP.NET Core</span></span>

<span data-ttu-id="c4eb7-104">In diesen Tutorials und Beispielen wird die Authentifizierung in ASP.NET Core mithilfe von Microsoft Identity Platform und Azure Active Directory veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="c4eb7-104">These tutorials and samples demonstrate authentication in ASP.NET Core using Microsoft identity platform and Azure Active Directory.</span></span> <span data-ttu-id="c4eb7-105">Weitere Tutorials und Beispiele zur Verwendung von ASP.NET Core mit Azure AD finden Sie unter [Microsoft Identity Platform](/azure/active-directory/develop/).</span><span class="sxs-lookup"><span data-stu-id="c4eb7-105">For additional tutorials and samples using ASP.NET Core with Azure AD, see [Microsoft identity platform](/azure/active-directory/develop/).</span></span>

## <a name="application-scenarios"></a><span data-ttu-id="c4eb7-106">Anwendungsszenarios</span><span class="sxs-lookup"><span data-stu-id="c4eb7-106">Application Scenarios</span></span>

* [<span data-ttu-id="c4eb7-107">Schnellstart: Hinzufügen von „Bei Microsoft anmelden“ zu einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="c4eb7-107">Quickstart: Add sign-in with Microsoft to an ASP.NET Core web app</span></span>](/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp)
* [<span data-ttu-id="c4eb7-108">Web-App, die Benutzer anmeldet</span><span class="sxs-lookup"><span data-stu-id="c4eb7-108">Web app that signs in users</span></span>](/azure/active-directory/develop/scenario-web-app-sign-user-overview?tabs=aspnetcore)
* [<span data-ttu-id="c4eb7-109">Web-App, die Web-APIs aufruft</span><span class="sxs-lookup"><span data-stu-id="c4eb7-109">Web app that calls web APIs</span></span>](/azure/active-directory/develop/scenario-web-app-call-api-overview)
* [<span data-ttu-id="c4eb7-110">Geschützte Web-API</span><span class="sxs-lookup"><span data-stu-id="c4eb7-110">Protected web API</span></span>](/azure/active-directory/develop/scenario-protected-web-api-overview)
* [<span data-ttu-id="c4eb7-111">Web-API, die Web-APIs aufruft</span><span class="sxs-lookup"><span data-stu-id="c4eb7-111">Web API that calls other web APIs</span></span>](/azure/active-directory/develop/scenario-web-api-call-api-overview)
* [<span data-ttu-id="c4eb7-112">Cloudauthentifizierung mit Azure Active Directory B2C in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4eb7-112">Web app that signs in users with Azure AD B2C</span></span>](xref:security/authentication/azure-ad-b2c)

## <a name="samples"></a><span data-ttu-id="c4eb7-113">Proben</span><span class="sxs-lookup"><span data-stu-id="c4eb7-113">Samples</span></span>

* <span data-ttu-id="c4eb7-114">[Aktivieren Ihrer Web-Apps für die Anmeldung von Benutzern und das Aufrufen von APIs mithilfe von Microsoft Identity Platform (für Entwickler)](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span><span class="sxs-lookup"><span data-stu-id="c4eb7-114">[Enable your ASP.NET Core app to sign-in users and call web APIs using Azure AD V2](/samples/azure-samples/active-directory-aspnetcore-webapp-openidconnect-v2/enable-webapp-signin/):</span></span> 
  * <span data-ttu-id="c4eb7-115">Weitere Informationen erhalten Sie [in diesem Video](https://channel9.msdn.com/Events/Build/2018/THR5001).</span><span class="sxs-lookup"><span data-stu-id="c4eb7-115">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5001)</span></span>
* <span data-ttu-id="c4eb7-116">[Aufrufen einer ASP.NET Core-Web-API über eine WPF-Anwendung mithilfe von Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span><span class="sxs-lookup"><span data-stu-id="c4eb7-116">[Calling an ASP.NET Core 2.0 Web API from a WPF application using Azure AD V2](/samples/azure-samples/active-directory-dotnet-native-aspnetcore-v2/calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2/):</span></span> 
  * <span data-ttu-id="c4eb7-117">Weitere Informationen erhalten Sie [in diesem Video](https://channel9.msdn.com/Events/Build/2018/THR5000).</span><span class="sxs-lookup"><span data-stu-id="c4eb7-117">See [this associated video](https://channel9.msdn.com/Events/Build/2018/THR5000)</span></span>
* [<span data-ttu-id="c4eb7-118">Eine ASP.NET Core-Web-App in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="c4eb7-118">An ASP.NET Core web app with Azure AD B2C</span></span>](/samples/azure-samples/active-directory-b2c-dotnetcore-webapp/an-aspnet-core-web-app-with-azure-ad-b2c/)
