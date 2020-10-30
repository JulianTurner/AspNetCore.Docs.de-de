---
title: Unterstützung für Datenschutz-Grundverordnung (dsgvo) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie in einer ASP.net Core Web-App auf die dsgvo-Erweiterungs Punkte zugreifen.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051004"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="f2762-103">Unterstützung der EU-Datenschutz-Grundverordnung (dsgvo) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f2762-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="f2762-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f2762-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f2762-105">ASP.net Core bietet APIs und Vorlagen, mit denen einige der Anforderungen der [EU-Datenschutz-Grundverordnung (](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) dsgvo) erfüllt werden können:</span><span class="sxs-lookup"><span data-stu-id="f2762-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="f2762-106">Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und :::no-loc(cookie)::: Nutzungsrichtlinien ersetzen können.</span><span class="sxs-lookup"><span data-stu-id="f2762-106">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="f2762-107">Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="f2762-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="f2762-108">So aktivieren Sie das standardmäßige :::no-loc(cookie)::: Zustimmungs Feature wie das in den ASP.net Core 2,2-Vorlagen in einer von ASP.net Core 3,0-Vorlagen generierten App:</span><span class="sxs-lookup"><span data-stu-id="f2762-108">To enable the default :::no-loc(cookie)::: consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="f2762-109">Fügen Sie `using Microsoft.AspNetCore.Http` der Liste der using-Direktiven hinzu.</span><span class="sxs-lookup"><span data-stu-id="f2762-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="f2762-110">Fügen Sie [ :::no-loc(Cookie)::: policyoptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) hinzu, `Startup.ConfigureServices` und verwenden Sie folgende [ :::no-loc(Cookie)::: Richtlinie](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f2762-110">Add [:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) to `Startup.ConfigureServices` and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="f2762-111">Fügen Sie die :::no-loc(cookie)::: Zustimmung zur Datei *_Layout. cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2762-111">Add the :::no-loc(cookie)::: consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="f2762-112">Fügen Sie dem Projekt die Datei " *\_ :::no-loc(Cookie)::: genehmipartial. cshtml* " hinzu:</span><span class="sxs-lookup"><span data-stu-id="f2762-112">Add the *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

* <span data-ttu-id="f2762-113">Wählen Sie den ASP.net Core 2,2-Version dieses Artikels aus, um Informationen über die :::no-loc(cookie)::: Zustimmungs Funktion zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="f2762-113">Select the ASP.NET Core 2.2 version of this article to read about the :::no-loc(cookie)::: consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="f2762-114">Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und :::no-loc(cookie)::: Nutzungsrichtlinien ersetzen können.</span><span class="sxs-lookup"><span data-stu-id="f2762-114">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="f2762-115">:::no-loc(cookie):::Mit einer Zustimmungs Funktion können Sie Ihre Benutzer für die Speicherung persönlicher Informationen auffordern (und nachverfolgen).</span><span class="sxs-lookup"><span data-stu-id="f2762-115">A :::no-loc(cookie)::: consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="f2762-116">Wenn ein Benutzer der Datensammlung nicht zugestimmt hat und für die APP [checkgenehmigbar](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) auf festgelegt ist `true` , werden nicht erforderliche :::no-loc(cookie)::: e nicht an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="f2762-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) set to `true`, non-essential :::no-loc(cookie):::s aren't sent to the browser.</span></span>
* <span data-ttu-id="f2762-117">:::no-loc(Cookie):::e können als unverzichtbar gekennzeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="f2762-117">:::no-loc(Cookie):::s can be marked as essential.</span></span> <span data-ttu-id="f2762-118">Wichtige :::no-loc(cookie)::: e werden an den Browser gesendet, auch wenn der Benutzer nicht zugestimmt hat und die Nachverfolgung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="f2762-118">Essential :::no-loc(cookie):::s are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="f2762-119">[TempData und die Sitzung :::no-loc(cookie)::: s](#tempdata) sind nicht funktionsfähig, wenn die Nachverfolgung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="f2762-119">[TempData and Session :::no-loc(cookie):::s](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="f2762-120">Die Seite [ :::no-loc(Identity)::: Verwalten](#pd) bietet einen Link zum herunterladen und Löschen von Benutzerdaten.</span><span class="sxs-lookup"><span data-stu-id="f2762-120">The [:::no-loc(Identity)::: manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="f2762-121">Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) können Sie die meisten dsgvo-Erweiterungs Punkte und APIs testen, die den ASP.net Core 2,1-Vorlagen hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="f2762-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="f2762-122">Weitere Informationen finden Sie [in der](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) Infodatei.</span><span class="sxs-lookup"><span data-stu-id="f2762-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="f2762-123">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2762-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="f2762-124">ASP.net Core dsgvo-Unterstützung in Vorlagen generiertem Code</span><span class="sxs-lookup"><span data-stu-id="f2762-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="f2762-125">:::no-loc(Razor)::: Seiten und MVC-Projekte, die mit den Projektvorlagen erstellt wurden, enthalten die folgende dsgvo-Unterstützung:</span><span class="sxs-lookup"><span data-stu-id="f2762-125">:::no-loc(Razor)::: Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="f2762-126">[ :::no-loc(Cookie)::: Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) und [use :::no-loc(Cookie)::: Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) werden in der- `Startup` Klasse festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f2762-126">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="f2762-127">Die [Teilansicht](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)" *\_ :::no-loc(Cookie)::: Zustimmung partial. cshtml* ".</span><span class="sxs-lookup"><span data-stu-id="f2762-127">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="f2762-128">Eine **Accept** -Schaltfläche ist in dieser Datei enthalten.</span><span class="sxs-lookup"><span data-stu-id="f2762-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="f2762-129">Wenn der Benutzer auf die Schaltfläche " **annehmen** " klickt, wird die Zustimmung zum Store :::no-loc(cookie)::: s bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f2762-129">When the user clicks the **Accept** button, consent to store :::no-loc(cookie):::s is provided.</span></span>
* <span data-ttu-id="f2762-130">Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="f2762-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="f2762-131">Die Datei " *\_ :::no-loc(Cookie)::: genehmipartial. cshtml* " generiert einen Link zur Datenschutzseite.</span><span class="sxs-lookup"><span data-stu-id="f2762-131">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="f2762-132">Für apps, die mit einzelnen Benutzerkonten erstellt wurden, stellt die Seite verwalten Links zum herunterladen und Löschen [persönlicher Benutzerdaten](#pd)bereit.</span><span class="sxs-lookup"><span data-stu-id="f2762-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="f2762-133">:::no-loc(Cookie):::Policyoptions und use :::no-loc(Cookie)::: Policy</span><span class="sxs-lookup"><span data-stu-id="f2762-133">:::no-loc(Cookie):::PolicyOptions and Use:::no-loc(Cookie):::Policy</span></span>

<span data-ttu-id="f2762-134">[ :::no-loc(Cookie)::: Policyoptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) werden in initialisiert `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2762-134">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="f2762-135">[Verwenden :::no-loc(Cookie)::: Sie Die Richtlinie](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) wird in aufgerufen `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f2762-135">[Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="f2762-136">\_:::no-loc(Cookie):::Teilansicht von "Zustimmung partial. cshtml"</span><span class="sxs-lookup"><span data-stu-id="f2762-136">\_:::no-loc(Cookie):::ConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="f2762-137">Die Teilansicht " *\_ :::no-loc(Cookie)::: Zustimmung partial. cshtml* ":</span><span class="sxs-lookup"><span data-stu-id="f2762-137">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

<span data-ttu-id="f2762-138">Diese partielle:</span><span class="sxs-lookup"><span data-stu-id="f2762-138">This partial:</span></span>

* <span data-ttu-id="f2762-139">Ruft den Status der Nachverfolgung für den Benutzer ab.</span><span class="sxs-lookup"><span data-stu-id="f2762-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="f2762-140">Wenn die APP so konfiguriert ist, dass Sie Zustimmung erfordert, muss der Benutzer zustimmen, bevor :::no-loc(cookie)::: s nachverfolgt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f2762-140">If the app is configured to require consent, the user must consent before :::no-loc(cookie):::s can be tracked.</span></span> <span data-ttu-id="f2762-141">Wenn Zustimmung erforderlich ist, :::no-loc(cookie)::: wird der Zustimmungs Bereich am oberen Rand der von der Datei " *\_ Layout. cshtml* " erstellten Navigationsleiste korrigiert.</span><span class="sxs-lookup"><span data-stu-id="f2762-141">If consent is required, the :::no-loc(cookie)::: consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="f2762-142">Stellt ein HTML `<p>` -Element bereit, um Ihre Datenschutz-und :::no-loc(cookie)::: Nutzungsrichtlinien zusammenzufassen.</span><span class="sxs-lookup"><span data-stu-id="f2762-142">Provides an HTML `<p>` element to summarize your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="f2762-143">Enthält einen Link zur Datenschutzseite oder-Ansicht, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.</span><span class="sxs-lookup"><span data-stu-id="f2762-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="f2762-144">Wichtige :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="f2762-144">Essential :::no-loc(cookie):::s</span></span>

<span data-ttu-id="f2762-145">Wenn die Zustimmung zu :::no-loc(cookie)::: den Geschäfts-e nicht angegeben wurde, :::no-loc(cookie)::: werden nur die als wesentlich gekennzeichneten s an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="f2762-145">If consent to store :::no-loc(cookie):::s hasn't been provided, only :::no-loc(cookie):::s marked essential are sent to the browser.</span></span> <span data-ttu-id="f2762-146">Der folgende Code stellt eine :::no-loc(cookie)::: wichtige Bedeutung:</span><span class="sxs-lookup"><span data-stu-id="f2762-146">The following code makes a :::no-loc(cookie)::: essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/:::no-loc(Cookie):::.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="f2762-147">Der TempData-Anbieter und der Sitzungs Status :::no-loc(cookie)::: sind nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f2762-147">TempData provider and session state :::no-loc(cookie):::s aren't essential</span></span>

<span data-ttu-id="f2762-148">Der [TempData-Anbieter](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: ist nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f2762-148">The [TempData provider](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: isn't essential.</span></span> <span data-ttu-id="f2762-149">Wenn die Nachverfolgung deaktiviert ist, ist der TempData-Anbieter nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="f2762-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="f2762-150">Um den TempData-Anbieter zu aktivieren, wenn die Nachverfolgung deaktiviert ist, markieren Sie TempData :::no-loc(cookie)::: in als unverzichtbar `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f2762-150">To enable the TempData provider when tracking is disabled, mark the TempData :::no-loc(cookie)::: as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="f2762-151">[Sitzungszustand](xref:fundamentals/app-state) :::no-loc(cookie)::: s sind nicht zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f2762-151">[Session state](xref:fundamentals/app-state) :::no-loc(cookie):::s are not essential.</span></span> <span data-ttu-id="f2762-152">Der Sitzungszustand ist nicht funktionsfähig, wenn die Überwachung deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="f2762-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="f2762-153">Der folgende Code macht die Sitzung :::no-loc(cookie)::: notwendig:</span><span class="sxs-lookup"><span data-stu-id="f2762-153">The following code makes session :::no-loc(cookie):::s essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="f2762-154">Personenbezogene Daten</span><span class="sxs-lookup"><span data-stu-id="f2762-154">Personal data</span></span>

<span data-ttu-id="f2762-155">ASP.net Core apps, die mit einzelnen Benutzerkonten erstellt wurden, enthalten Code zum herunterladen und Löschen von persönlichen Daten.</span><span class="sxs-lookup"><span data-stu-id="f2762-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="f2762-156">Wählen Sie den Benutzernamen aus, und wählen Sie dann **persönliche Daten** aus:</span><span class="sxs-lookup"><span data-stu-id="f2762-156">Select the user name and then select **Personal data** :</span></span>

![Seite "persönliche Daten verwalten"](gdpr/_static/pd.png)

<span data-ttu-id="f2762-158">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="f2762-158">Notes:</span></span>

* <span data-ttu-id="f2762-159">Informationen zum Generieren des `Account/Manage` Codes finden Sie unter [ :::no-loc(Identity)::: Gerüstbau ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="f2762-159">To generate the `Account/Manage` code, see [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="f2762-160">Die Links zum **Löschen** und **herunterladen** wirken sich nur auf die Standard Identitätsdaten aus.</span><span class="sxs-lookup"><span data-stu-id="f2762-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="f2762-161">Apps, die benutzerdefinierte Benutzerdaten erstellen, müssen erweitert werden, um die benutzerdefinierten Benutzerdaten zu löschen/herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="f2762-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="f2762-162">Weitere Informationen finden Sie unter [hinzufügen, herunterladen und Löschen von benutzerdefinierten :::no-loc(Identity)::: Benutzerdaten in ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="f2762-162">For more information, see [Add, download, and delete custom user data to :::no-loc(Identity):::](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="f2762-163">Gespeicherte Token für den Benutzer, die in der :::no-loc(Identity)::: Datenbanktabelle gespeichert sind, `AspNetUserTokens` werden gelöscht, wenn der Benutzer aufgrund des [Fremd Schlüssels](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152)über das kaskadierende Lösch Verhalten gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="f2762-163">Saved tokens for the user that are stored in the :::no-loc(Identity)::: database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span></span>
* <span data-ttu-id="f2762-164">Die [Authentifizierung externer Anbieter](xref:security/authentication/social/index), wie Facebook und Google, ist nicht verfügbar, bevor die :::no-loc(cookie)::: Richtlinie akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="f2762-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the :::no-loc(cookie)::: policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="f2762-165">Verschlüsselung ruhender Daten</span><span class="sxs-lookup"><span data-stu-id="f2762-165">Encryption at rest</span></span>

<span data-ttu-id="f2762-166">Einige Datenbanken und Speicher Mechanismen ermöglichen die Verschlüsselung ruhender Daten.</span><span class="sxs-lookup"><span data-stu-id="f2762-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="f2762-167">Verschlüsselung ruhender Daten:</span><span class="sxs-lookup"><span data-stu-id="f2762-167">Encryption at rest:</span></span>

* <span data-ttu-id="f2762-168">Verschlüsselte Daten werden automatisch verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="f2762-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="f2762-169">Verschlüsselt, ohne Konfiguration, Programmierung oder andere Aufgaben für die Software, die auf die Daten zugreift.</span><span class="sxs-lookup"><span data-stu-id="f2762-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="f2762-170">Ist die einfachste und sicherste Option.</span><span class="sxs-lookup"><span data-stu-id="f2762-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="f2762-171">Ermöglicht der Datenbank die Verwaltung von Schlüsseln und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="f2762-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="f2762-172">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f2762-172">For example:</span></span>

* <span data-ttu-id="f2762-173">Microsoft SQL und Azure SQL bieten [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="f2762-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="f2762-174">Die Datenbank wird von SQL Azure standardmäßig verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="f2762-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="f2762-175">[Azure-blobdateien,-Dateien,-Tabellen und-Queue Storage werden standardmäßig verschlüsselt](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="f2762-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="f2762-176">Bei Datenbanken, die keine integrierte Verschlüsselung im Ruhezustand bereitstellen, können Sie die Datenträger Verschlüsselung möglicherweise verwenden, um denselben Schutz zu gewährleisten.</span><span class="sxs-lookup"><span data-stu-id="f2762-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="f2762-177">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f2762-177">For example:</span></span>

* [<span data-ttu-id="f2762-178">BitLocker für Windows Server</span><span class="sxs-lookup"><span data-stu-id="f2762-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="f2762-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="f2762-179">Linux:</span></span>
  * [<span data-ttu-id="f2762-180">ecryptfs</span><span class="sxs-lookup"><span data-stu-id="f2762-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="f2762-181">[EncFS](https://github.com/vgough/encfs)Ein.</span><span class="sxs-lookup"><span data-stu-id="f2762-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2762-182">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f2762-182">Additional resources</span></span>

* [<span data-ttu-id="f2762-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="f2762-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="f2762-184">Dsgvo: Hinzufügen einer Schaltfläche zum Widerrufen der Zustimmung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="f2762-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
