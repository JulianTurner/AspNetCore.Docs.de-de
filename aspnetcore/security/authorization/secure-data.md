---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689304"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="0a6b7-104">Erstellen einer ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="0a6b7-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="0a6b7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0a6b7-106">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a6b7-107">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0a6b7-108">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0a6b7-109">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-109">There are three security groups:</span></span>

* <span data-ttu-id="0a6b7-110">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0a6b7-111">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0a6b7-112">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0a6b7-113">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0a6b7-114">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="0a6b7-115">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0a6b7-116">Rick kann nur genehmigte Kontakte anzeigen und  / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0a6b7-117">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0a6b7-118">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="0a6b7-120">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="0a6b7-122">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-122">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="0a6b7-124">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0a6b7-125">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="0a6b7-127">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-127">The administrator has all privileges.</span></span> <span data-ttu-id="0a6b7-128">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0a6b7-129">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0a6b7-130">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0a6b7-131">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0a6b7-132">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0a6b7-133">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a6b7-134">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-134">Prerequisites</span></span>

<span data-ttu-id="0a6b7-135">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-135">This tutorial is advanced.</span></span> <span data-ttu-id="0a6b7-136">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-136">You should be familiar with:</span></span>

* [<span data-ttu-id="0a6b7-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a6b7-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0a6b7-138">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0a6b7-139">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0a6b7-140">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0a6b7-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0a6b7-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0a6b7-142">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-142">The starter and completed app</span></span>

<span data-ttu-id="0a6b7-143">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0a6b7-144">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0a6b7-145">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-145">The starter app</span></span>

<span data-ttu-id="0a6b7-146">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0a6b7-147">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="0a6b7-148">Informationen zum Erstellen der Starter-App finden Sie unter [Erstellen der Starter-App](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0a6b7-149">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-149">Secure user data</span></span>

<span data-ttu-id="0a6b7-150">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0a6b7-151">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0a6b7-152">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-152">Tie the contact data to the user</span></span>

<span data-ttu-id="0a6b7-153">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0a6b7-154">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0a6b7-155">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0a6b7-156">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0a6b7-157">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0a6b7-158">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="0a6b7-158">Add Role services to Identity</span></span>

<span data-ttu-id="0a6b7-159">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="0a6b7-160">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="0a6b7-160">Require authenticated users</span></span>

<span data-ttu-id="0a6b7-161">Legen Sie die Fall Back Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="0a6b7-162">Der obige markierte Code legt die [Fall Back Authentifizierungs Richtlinie](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)fest.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="0a6b7-163">Die Fall Back Authentifizierungs Richtlinie erfordert, dass \**_all_* _-Benutzer authentifiziert werden, mit Ausnahme von Razor Seiten, Controllern oder Aktionsmethoden mit einem Authentifizierungs Attribut.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="0a6b7-164">Beispielsweise werden Razor Seiten, Controller oder Aktionsmethoden mit `[AllowAnonymous]` oder `[Authorize(PolicyName="MyPolicy")]` das angewendete Authentifizierungs Attribut anstelle der Fall Back Authentifizierungs Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="0a6b7-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> fügt <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> der aktuellen-Instanz hinzu, die erzwingt, dass der aktuelle Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="0a6b7-166">Die Richtlinie für die Fall Back Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-166">The fallback authentication policy:</span></span>

<span data-ttu-id="0a6b7-167">_ Wird auf alle Anforderungen angewendet, die nicht explizit eine Authentifizierungs Richtlinie angeben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="0a6b7-168">Bei Anforderungen, die von der Endpunkt Weiterleitung verarbeitet werden, würde dies alle Endpunkte einschließen, für die kein Autorisierungs Attribut angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="0a6b7-169">Bei Anforderungen, die von einer anderen Middleware nach der Autorisierungs Middleware, wie z. b. [statischen Dateien](xref:fundamentals/static-files), verarbeitet werden, wird die Richtlinie auf alle Anforderungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="0a6b7-170">Das Festlegen der Fall Back Authentifizierungs Richtlinie, damit Benutzer authentifiziert werden müssen, schützt neu hinzugefügte Razor Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0a6b7-171">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0a6b7-172">Die- <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> Klasse enthält ebenfalls <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0a6b7-173">`DefaultPolicy`Ist die Richtlinie, die mit dem-Attribut verwendet wird, `[Authorize]` Wenn keine Richtlinie angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="0a6b7-174">`[Authorize]` enthält im Gegensatz zu keine benannte Richtlinie `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="0a6b7-175">Weitere Informationen zu Richtlinien finden Sie unter <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0a6b7-176">Eine alternative Möglichkeit für MVC-Controller und- Razor Seiten, dass alle Benutzer authentifiziert werden müssen, besteht darin, einen Autorisierungs Filter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="0a6b7-177">Im vorangehenden Code wird ein Autorisierungs Filter verwendet, bei dem die Fall Back Richtlinie das Endpunkt Routing verwendet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="0a6b7-178">Das Festlegen der Fall Back Richtlinie ist die bevorzugte Methode, um alle Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="0a6b7-179">Fügen Sie den Seiten und die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) hinzu, `Index` `Privacy` damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0a6b7-180">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="0a6b7-180">Configure the test account</span></span>

<span data-ttu-id="0a6b7-181">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0a6b7-182">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0a6b7-183">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0a6b7-184">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0a6b7-185">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0a6b7-186">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="0a6b7-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0a6b7-187">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0a6b7-188">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0a6b7-189">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="0a6b7-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0a6b7-190">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0a6b7-191">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0a6b7-192">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="0a6b7-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0a6b7-193">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a6b7-194">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0a6b7-195">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0a6b7-196">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="0a6b7-197">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0a6b7-198">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0a6b7-199">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0a6b7-200">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0a6b7-201">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0a6b7-202">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0a6b7-203">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0a6b7-203">Create a manager authorization handler</span></span>

<span data-ttu-id="0a6b7-204">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a6b7-205">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0a6b7-206">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0a6b7-207">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0a6b7-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="0a6b7-208">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a6b7-209">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0a6b7-210">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0a6b7-211">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="0a6b7-211">Register the authorization handlers</span></span>

<span data-ttu-id="0a6b7-212">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0a6b7-213">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0a6b7-214">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a6b7-215">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="0a6b7-216">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0a6b7-217">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0a6b7-218">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-218">Support authorization</span></span>

<span data-ttu-id="0a6b7-219">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0a6b7-220">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="0a6b7-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="0a6b7-221">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0a6b7-222">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0a6b7-223">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="0a6b7-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0a6b7-224">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0a6b7-225">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0a6b7-226">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-226">The preceding code:</span></span>

* <span data-ttu-id="0a6b7-227">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0a6b7-228">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0a6b7-229">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0a6b7-230">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="0a6b7-230">Update the CreateModel</span></span>

<span data-ttu-id="0a6b7-231">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0a6b7-232">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0a6b7-233">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0a6b7-234">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="0a6b7-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0a6b7-235">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="0a6b7-235">Update the IndexModel</span></span>

<span data-ttu-id="0a6b7-236">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0a6b7-237">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="0a6b7-237">Update the EditModel</span></span>

<span data-ttu-id="0a6b7-238">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0a6b7-239">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0a6b7-240">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0a6b7-241">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0a6b7-242">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0a6b7-243">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0a6b7-244">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="0a6b7-244">Update the DeleteModel</span></span>

<span data-ttu-id="0a6b7-245">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0a6b7-246">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="0a6b7-247">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0a6b7-248">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0a6b7-249">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0a6b7-250">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0a6b7-251">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0a6b7-252">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0a6b7-253">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0a6b7-254">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0a6b7-255">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="0a6b7-255">Update Details</span></span>

<span data-ttu-id="0a6b7-256">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0a6b7-257">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0a6b7-258">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="0a6b7-258">Add or remove a user to a role</span></span>

<span data-ttu-id="0a6b7-259">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0a6b7-260">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-260">Removing privileges from a user.</span></span> <span data-ttu-id="0a6b7-261">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0a6b7-262">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="0a6b7-263">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="0a6b7-264">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#rau)sind.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="0a6b7-265">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-265">The following code allows anonymous users.</span></span> <span data-ttu-id="0a6b7-266">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="0a6b7-267">Im obigen Code:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-267">In the preceding code:</span></span>

* <span data-ttu-id="0a6b7-268">Wenn der Benutzer **nicht** authentifiziert ist, `ChallengeResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="0a6b7-269">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="0a6b7-270">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, `ForbidResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="0a6b7-271">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0a6b7-272">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-272">Test the completed app</span></span>

<span data-ttu-id="0a6b7-273">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0a6b7-274">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0a6b7-275">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0a6b7-276">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="0a6b7-277">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-277">If the app has contacts:</span></span>

* <span data-ttu-id="0a6b7-278">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="0a6b7-279">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="0a6b7-280">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0a6b7-281">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0a6b7-282">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0a6b7-283">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-283">Verify the following operations:</span></span>

* <span data-ttu-id="0a6b7-284">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0a6b7-285">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0a6b7-286">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0a6b7-287">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0a6b7-288">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0a6b7-289">Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-289">User</span></span>                | <span data-ttu-id="0a6b7-290">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="0a6b7-290">Seeded by the app</span></span> | <span data-ttu-id="0a6b7-291">Optionen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0a6b7-292">Nein</span><span class="sxs-lookup"><span data-stu-id="0a6b7-292">No</span></span>                | <span data-ttu-id="0a6b7-293">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0a6b7-294">Ja</span><span class="sxs-lookup"><span data-stu-id="0a6b7-294">Yes</span></span>               | <span data-ttu-id="0a6b7-295">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0a6b7-296">Ja</span><span class="sxs-lookup"><span data-stu-id="0a6b7-296">Yes</span></span>               | <span data-ttu-id="0a6b7-297">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0a6b7-298">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0a6b7-299">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0a6b7-300">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0a6b7-301">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-301">Create the starter app</span></span>

* <span data-ttu-id="0a6b7-302">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="0a6b7-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0a6b7-303">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0a6b7-304">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0a6b7-305">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0a6b7-306">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0a6b7-307">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0a6b7-308">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="0a6b7-309">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="0a6b7-310">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="0a6b7-311">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0a6b7-312">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="0a6b7-312">Seed the database</span></span>

<span data-ttu-id="0a6b7-313">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="0a6b7-314">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="0a6b7-315">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-315">Test that the app seeded the database.</span></span> <span data-ttu-id="0a6b7-316">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="0a6b7-317">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0a6b7-318">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0a6b7-319">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-319">There are three security groups:</span></span>

* <span data-ttu-id="0a6b7-320">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0a6b7-321">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0a6b7-322">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0a6b7-323">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0a6b7-324">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0a6b7-325">Rick kann nur genehmigte Kontakte anzeigen und  / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0a6b7-326">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0a6b7-327">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="0a6b7-329">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="0a6b7-331">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-331">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="0a6b7-333">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0a6b7-334">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="0a6b7-336">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-336">The administrator has all privileges.</span></span> <span data-ttu-id="0a6b7-337">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0a6b7-338">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0a6b7-339">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0a6b7-340">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0a6b7-341">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0a6b7-342">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0a6b7-343">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-343">Prerequisites</span></span>

<span data-ttu-id="0a6b7-344">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-344">This tutorial is advanced.</span></span> <span data-ttu-id="0a6b7-345">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-345">You should be familiar with:</span></span>

* [<span data-ttu-id="0a6b7-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a6b7-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0a6b7-347">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0a6b7-348">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0a6b7-349">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0a6b7-350">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0a6b7-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0a6b7-351">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-351">The starter and completed app</span></span>

<span data-ttu-id="0a6b7-352">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0a6b7-353">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0a6b7-354">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-354">The starter app</span></span>

<span data-ttu-id="0a6b7-355">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0a6b7-356">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0a6b7-357">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-357">Secure user data</span></span>

<span data-ttu-id="0a6b7-358">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0a6b7-359">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0a6b7-360">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-360">Tie the contact data to the user</span></span>

<span data-ttu-id="0a6b7-361">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0a6b7-362">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0a6b7-363">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0a6b7-364">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0a6b7-365">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0a6b7-366">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="0a6b7-366">Add Role services to Identity</span></span>

<span data-ttu-id="0a6b7-367">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0a6b7-368">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="0a6b7-368">Require authenticated users</span></span>

<span data-ttu-id="0a6b7-369">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="0a6b7-370">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0a6b7-371">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0a6b7-372">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0a6b7-373">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0a6b7-374">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="0a6b7-374">Configure the test account</span></span>

<span data-ttu-id="0a6b7-375">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0a6b7-376">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0a6b7-377">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0a6b7-378">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0a6b7-379">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0a6b7-380">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="0a6b7-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0a6b7-381">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0a6b7-382">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0a6b7-383">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="0a6b7-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0a6b7-384">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0a6b7-385">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0a6b7-386">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="0a6b7-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0a6b7-387">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen `ContactIsOwnerAuthorizationHandler` Sie eine Klasse darin.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="0a6b7-388">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0a6b7-389">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0a6b7-390">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="0a6b7-391">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0a6b7-392">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0a6b7-393">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0a6b7-394">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0a6b7-395">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0a6b7-396">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0a6b7-397">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0a6b7-397">Create a manager authorization handler</span></span>

<span data-ttu-id="0a6b7-398">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a6b7-399">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0a6b7-400">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0a6b7-401">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0a6b7-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="0a6b7-402">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0a6b7-403">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0a6b7-404">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0a6b7-405">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="0a6b7-405">Register the authorization handlers</span></span>

<span data-ttu-id="0a6b7-406">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0a6b7-407">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0a6b7-408">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0a6b7-409">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="0a6b7-410">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0a6b7-411">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0a6b7-412">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-412">Support authorization</span></span>

<span data-ttu-id="0a6b7-413">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0a6b7-414">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="0a6b7-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="0a6b7-415">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0a6b7-416">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0a6b7-417">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="0a6b7-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0a6b7-418">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0a6b7-419">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0a6b7-420">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-420">The preceding code:</span></span>

* <span data-ttu-id="0a6b7-421">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0a6b7-422">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0a6b7-423">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0a6b7-424">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="0a6b7-424">Update the CreateModel</span></span>

<span data-ttu-id="0a6b7-425">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0a6b7-426">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0a6b7-427">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0a6b7-428">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="0a6b7-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0a6b7-429">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="0a6b7-429">Update the IndexModel</span></span>

<span data-ttu-id="0a6b7-430">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0a6b7-431">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="0a6b7-431">Update the EditModel</span></span>

<span data-ttu-id="0a6b7-432">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0a6b7-433">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0a6b7-434">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0a6b7-435">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0a6b7-436">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0a6b7-437">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0a6b7-438">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="0a6b7-438">Update the DeleteModel</span></span>

<span data-ttu-id="0a6b7-439">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="0a6b7-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0a6b7-440">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="0a6b7-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="0a6b7-441">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0a6b7-442">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0a6b7-443">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0a6b7-444">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0a6b7-445">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0a6b7-446">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0a6b7-447">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0a6b7-448">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0a6b7-449">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="0a6b7-449">Update Details</span></span>

<span data-ttu-id="0a6b7-450">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0a6b7-451">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0a6b7-452">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="0a6b7-452">Add or remove a user to a role</span></span>

<span data-ttu-id="0a6b7-453">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0a6b7-454">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-454">Removing privileges from a user.</span></span> <span data-ttu-id="0a6b7-455">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0a6b7-456">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0a6b7-457">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-457">Test the completed app</span></span>

<span data-ttu-id="0a6b7-458">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0a6b7-459">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0a6b7-460">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0a6b7-461">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="0a6b7-462">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="0a6b7-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="0a6b7-463">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="0a6b7-464">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0a6b7-465">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0a6b7-466">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0a6b7-467">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-467">Verify the following operations:</span></span>

* <span data-ttu-id="0a6b7-468">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0a6b7-469">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0a6b7-470">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0a6b7-471">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="0a6b7-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0a6b7-472">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0a6b7-473">Benutzer</span><span class="sxs-lookup"><span data-stu-id="0a6b7-473">User</span></span>                | <span data-ttu-id="0a6b7-474">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="0a6b7-474">Seeded by the app</span></span> | <span data-ttu-id="0a6b7-475">Optionen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0a6b7-476">Nein</span><span class="sxs-lookup"><span data-stu-id="0a6b7-476">No</span></span>                | <span data-ttu-id="0a6b7-477">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0a6b7-478">Ja</span><span class="sxs-lookup"><span data-stu-id="0a6b7-478">Yes</span></span>               | <span data-ttu-id="0a6b7-479">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0a6b7-480">Ja</span><span class="sxs-lookup"><span data-stu-id="0a6b7-480">Yes</span></span>               | <span data-ttu-id="0a6b7-481">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0a6b7-482">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0a6b7-483">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0a6b7-484">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0a6b7-485">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="0a6b7-485">Create the starter app</span></span>

* <span data-ttu-id="0a6b7-486">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="0a6b7-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0a6b7-487">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0a6b7-488">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0a6b7-489">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0a6b7-490">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0a6b7-491">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0a6b7-492">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0a6b7-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="0a6b7-493">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="0a6b7-494">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0a6b7-495">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="0a6b7-495">Seed the database</span></span>

<span data-ttu-id="0a6b7-496">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="0a6b7-497">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="0a6b7-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="0a6b7-498">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-498">Test that the app seeded the database.</span></span> <span data-ttu-id="0a6b7-499">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="0a6b7-500">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0a6b7-500">Additional resources</span></span>

* [<span data-ttu-id="0a6b7-501">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0a6b7-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="0a6b7-502">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="0a6b7-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="0a6b7-503">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="0a6b7-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="0a6b7-504">Benutzerdefinierte Richtlinien basierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0a6b7-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
