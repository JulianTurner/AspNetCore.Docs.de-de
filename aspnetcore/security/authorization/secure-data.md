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
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854651"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="0c650-104">Erstellen einer ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0c650-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="0c650-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="0c650-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0c650-106">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="0c650-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0c650-107">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="0c650-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0c650-108">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="0c650-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0c650-109">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="0c650-109">There are three security groups:</span></span>

* <span data-ttu-id="0c650-110">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0c650-111">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0c650-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0c650-112">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="0c650-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0c650-113">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0c650-114">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="0c650-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="0c650-115">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="0c650-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0c650-116">Rick kann nur genehmigte Kontakte anzeigen und  / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="0c650-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0c650-117">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="0c650-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0c650-118">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="0c650-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="0c650-120">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="0c650-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="0c650-122">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="0c650-122">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="0c650-124">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0c650-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0c650-125">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="0c650-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="0c650-127">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="0c650-127">The administrator has all privileges.</span></span> <span data-ttu-id="0c650-128">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="0c650-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0c650-129">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="0c650-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0c650-130">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="0c650-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0c650-131">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0c650-132">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0c650-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0c650-133">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0c650-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c650-134">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0c650-134">Prerequisites</span></span>

<span data-ttu-id="0c650-135">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="0c650-135">This tutorial is advanced.</span></span> <span data-ttu-id="0c650-136">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="0c650-136">You should be familiar with:</span></span>

* [<span data-ttu-id="0c650-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c650-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0c650-138">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0c650-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0c650-139">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="0c650-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0c650-140">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0c650-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0c650-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0c650-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0c650-142">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="0c650-142">The starter and completed app</span></span>

<span data-ttu-id="0c650-143">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0c650-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0c650-144">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="0c650-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0c650-145">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="0c650-145">The starter app</span></span>

<span data-ttu-id="0c650-146">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0c650-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0c650-147">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="0c650-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="0c650-148">Informationen zum Erstellen der Starter-App finden Sie unter [Erstellen der Starter-App](#create-the-starter-app).</span><span class="sxs-lookup"><span data-stu-id="0c650-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0c650-149">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0c650-149">Secure user data</span></span>

<span data-ttu-id="0c650-150">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="0c650-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0c650-151">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="0c650-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0c650-152">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-152">Tie the contact data to the user</span></span>

<span data-ttu-id="0c650-153">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="0c650-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0c650-154">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0c650-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0c650-155">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0c650-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0c650-156">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0c650-157">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0c650-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0c650-158">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="0c650-158">Add Role services to Identity</span></span>

<span data-ttu-id="0c650-159">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="0c650-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="0c650-160">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="0c650-160">Require authenticated users</span></span>

<span data-ttu-id="0c650-161">Legen Sie die Fall Back Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0c650-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="0c650-162">Der obige markierte Code legt die [Fall Back Authentifizierungs Richtlinie](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)fest.</span><span class="sxs-lookup"><span data-stu-id="0c650-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="0c650-163">Die Fall Back Authentifizierungs Richtlinie erfordert, dass \**_all_* _-Benutzer authentifiziert werden, mit Ausnahme von Razor Seiten, Controllern oder Aktionsmethoden mit einem Authentifizierungs Attribut.</span><span class="sxs-lookup"><span data-stu-id="0c650-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="0c650-164">Beispielsweise werden Razor Seiten, Controller oder Aktionsmethoden mit `[AllowAnonymous]` oder `[Authorize(PolicyName="MyPolicy")]` das angewendete Authentifizierungs Attribut anstelle der Fall Back Authentifizierungs Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="0c650-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="0c650-165">Die Richtlinie für die Fall Back Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="0c650-165">The fallback authentication policy:</span></span>

<span data-ttu-id="0c650-166">_ Wird auf alle Anforderungen angewendet, die nicht explizit eine Authentifizierungs Richtlinie angeben.</span><span class="sxs-lookup"><span data-stu-id="0c650-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="0c650-167">Bei Anforderungen, die von der Endpunkt Weiterleitung verarbeitet werden, würde dies alle Endpunkte einschließen, für die kein Autorisierungs Attribut angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="0c650-168">Bei Anforderungen, die von einer anderen Middleware nach der Autorisierungs Middleware, wie z. b. [statischen Dateien](xref:fundamentals/static-files), verarbeitet werden, wird die Richtlinie auf alle Anforderungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="0c650-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="0c650-169">Das Festlegen der Fall Back Authentifizierungs Richtlinie, damit Benutzer authentifiziert werden müssen, schützt neu hinzugefügte Razor Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="0c650-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0c650-170">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="0c650-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0c650-171">Die- <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> Klasse enthält ebenfalls <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="0c650-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0c650-172">`DefaultPolicy`Ist die Richtlinie, die mit dem-Attribut verwendet wird, `[Authorize]` Wenn keine Richtlinie angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0c650-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="0c650-173">`[Authorize]` enthält im Gegensatz zu keine benannte Richtlinie `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="0c650-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="0c650-174">Weitere Informationen zu Richtlinien finden Sie unter <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="0c650-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="0c650-175">Eine alternative Möglichkeit für MVC-Controller und- Razor Seiten, dass alle Benutzer authentifiziert werden müssen, besteht darin, einen Autorisierungs Filter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="0c650-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="0c650-176">Im vorangehenden Code wird ein Autorisierungs Filter verwendet, bei dem die Fall Back Richtlinie das Endpunkt Routing verwendet.</span><span class="sxs-lookup"><span data-stu-id="0c650-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="0c650-177">Das Festlegen der Fall Back Richtlinie ist die bevorzugte Methode, um alle Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="0c650-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="0c650-178">Fügen Sie den Seiten und die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) hinzu, `Index` `Privacy` damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können:</span><span class="sxs-lookup"><span data-stu-id="0c650-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0c650-179">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="0c650-179">Configure the test account</span></span>

<span data-ttu-id="0c650-180">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="0c650-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0c650-181">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="0c650-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0c650-182">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="0c650-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0c650-183">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0c650-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0c650-184">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0c650-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0c650-185">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="0c650-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0c650-186">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0c650-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0c650-187">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0c650-188">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="0c650-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0c650-189">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0c650-190">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0c650-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0c650-191">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="0c650-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0c650-192">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0c650-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0c650-193">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="0c650-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0c650-194">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0c650-195">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="0c650-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="0c650-196">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="0c650-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0c650-197">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0c650-198">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0c650-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0c650-199">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0c650-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0c650-200">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="0c650-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0c650-201">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0c650-202">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0c650-202">Create a manager authorization handler</span></span>

<span data-ttu-id="0c650-203">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0c650-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0c650-204">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0c650-205">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="0c650-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0c650-206">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0c650-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="0c650-207">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0c650-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0c650-208">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0c650-209">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="0c650-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0c650-210">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="0c650-210">Register the authorization handlers</span></span>

<span data-ttu-id="0c650-211">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0c650-212">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="0c650-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0c650-213">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0c650-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0c650-214">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0c650-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="0c650-215">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0c650-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0c650-216">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="0c650-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0c650-217">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="0c650-217">Support authorization</span></span>

<span data-ttu-id="0c650-218">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0c650-219">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="0c650-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="0c650-220">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="0c650-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0c650-221">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0c650-222">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="0c650-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0c650-223">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="0c650-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0c650-224">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="0c650-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0c650-225">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0c650-225">The preceding code:</span></span>

* <span data-ttu-id="0c650-226">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0c650-227">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0c650-228">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0c650-229">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="0c650-229">Update the CreateModel</span></span>

<span data-ttu-id="0c650-230">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0c650-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0c650-231">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0c650-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0c650-232">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0c650-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0c650-233">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="0c650-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0c650-234">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="0c650-234">Update the IndexModel</span></span>

<span data-ttu-id="0c650-235">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0c650-236">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="0c650-236">Update the EditModel</span></span>

<span data-ttu-id="0c650-237">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="0c650-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0c650-238">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="0c650-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0c650-239">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0c650-240">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="0c650-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0c650-241">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="0c650-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0c650-242">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="0c650-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0c650-243">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="0c650-243">Update the DeleteModel</span></span>

<span data-ttu-id="0c650-244">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="0c650-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0c650-245">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="0c650-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="0c650-246">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0c650-247">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0c650-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0c650-248">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0c650-249">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0c650-250">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="0c650-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0c650-251">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0c650-252">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="0c650-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0c650-253">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="0c650-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0c650-254">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="0c650-254">Update Details</span></span>

<span data-ttu-id="0c650-255">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="0c650-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0c650-256">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="0c650-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0c650-257">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="0c650-257">Add or remove a user to a role</span></span>

<span data-ttu-id="0c650-258">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="0c650-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0c650-259">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0c650-259">Removing privileges from a user.</span></span> <span data-ttu-id="0c650-260">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="0c650-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0c650-261">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="0c650-262">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="0c650-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="0c650-263">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#rau)sind.</span><span class="sxs-lookup"><span data-stu-id="0c650-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="0c650-264">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0c650-264">The following code allows anonymous users.</span></span> <span data-ttu-id="0c650-265">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0c650-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="0c650-266">Im obigen Code:</span><span class="sxs-lookup"><span data-stu-id="0c650-266">In the preceding code:</span></span>

* <span data-ttu-id="0c650-267">Wenn der Benutzer **nicht** authentifiziert ist, `ChallengeResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0c650-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="0c650-268">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0c650-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="0c650-269">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, `ForbidResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0c650-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="0c650-270">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0c650-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0c650-271">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="0c650-271">Test the completed app</span></span>

<span data-ttu-id="0c650-272">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="0c650-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0c650-273">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="0c650-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0c650-274">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="0c650-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0c650-275">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="0c650-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="0c650-276">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="0c650-276">If the app has contacts:</span></span>

* <span data-ttu-id="0c650-277">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="0c650-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="0c650-278">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="0c650-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="0c650-279">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="0c650-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0c650-280">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="0c650-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0c650-281">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="0c650-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0c650-282">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0c650-282">Verify the following operations:</span></span>

* <span data-ttu-id="0c650-283">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0c650-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0c650-284">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0c650-285">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0c650-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0c650-286">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="0c650-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0c650-287">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0c650-288">Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-288">User</span></span>                | <span data-ttu-id="0c650-289">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="0c650-289">Seeded by the app</span></span> | <span data-ttu-id="0c650-290">Optionen</span><span class="sxs-lookup"><span data-stu-id="0c650-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0c650-291">Nein</span><span class="sxs-lookup"><span data-stu-id="0c650-291">No</span></span>                | <span data-ttu-id="0c650-292">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="0c650-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0c650-293">Ja</span><span class="sxs-lookup"><span data-stu-id="0c650-293">Yes</span></span>               | <span data-ttu-id="0c650-294">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="0c650-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0c650-295">Ja</span><span class="sxs-lookup"><span data-stu-id="0c650-295">Yes</span></span>               | <span data-ttu-id="0c650-296">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0c650-297">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="0c650-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0c650-298">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="0c650-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0c650-299">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0c650-300">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="0c650-300">Create the starter app</span></span>

* <span data-ttu-id="0c650-301">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="0c650-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0c650-302">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="0c650-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0c650-303">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0c650-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0c650-304">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="0c650-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0c650-305">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="0c650-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0c650-306">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="0c650-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0c650-307">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0c650-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="0c650-308">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="0c650-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="0c650-309">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0c650-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="0c650-310">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0c650-311">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="0c650-311">Seed the database</span></span>

<span data-ttu-id="0c650-312">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0c650-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="0c650-313">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="0c650-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="0c650-314">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="0c650-314">Test that the app seeded the database.</span></span> <span data-ttu-id="0c650-315">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0c650-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="0c650-316">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="0c650-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="0c650-317">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="0c650-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="0c650-318">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="0c650-318">There are three security groups:</span></span>

* <span data-ttu-id="0c650-319">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="0c650-320">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0c650-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="0c650-321">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="0c650-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="0c650-322">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="0c650-323">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="0c650-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="0c650-324">Rick kann nur genehmigte Kontakte anzeigen und  / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="0c650-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="0c650-325">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="0c650-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="0c650-326">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="0c650-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="0c650-328">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="0c650-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="0c650-330">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="0c650-330">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="0c650-332">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0c650-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="0c650-333">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="0c650-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="0c650-335">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="0c650-335">The administrator has all privileges.</span></span> <span data-ttu-id="0c650-336">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="0c650-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="0c650-337">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="0c650-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="0c650-338">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="0c650-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="0c650-339">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="0c650-340">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0c650-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="0c650-341">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="0c650-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0c650-342">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0c650-342">Prerequisites</span></span>

<span data-ttu-id="0c650-343">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="0c650-343">This tutorial is advanced.</span></span> <span data-ttu-id="0c650-344">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="0c650-344">You should be familiar with:</span></span>

* [<span data-ttu-id="0c650-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c650-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="0c650-346">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0c650-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="0c650-347">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="0c650-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="0c650-348">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0c650-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="0c650-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0c650-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="0c650-350">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="0c650-350">The starter and completed app</span></span>

<span data-ttu-id="0c650-351">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0c650-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="0c650-352">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="0c650-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="0c650-353">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="0c650-353">The starter app</span></span>

<span data-ttu-id="0c650-354">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="0c650-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="0c650-355">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="0c650-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="0c650-356">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="0c650-356">Secure user data</span></span>

<span data-ttu-id="0c650-357">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="0c650-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="0c650-358">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="0c650-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="0c650-359">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-359">Tie the contact data to the user</span></span>

<span data-ttu-id="0c650-360">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="0c650-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="0c650-361">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="0c650-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="0c650-362">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0c650-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="0c650-363">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="0c650-364">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0c650-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="0c650-365">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="0c650-365">Add Role services to Identity</span></span>

<span data-ttu-id="0c650-366">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="0c650-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="0c650-367">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="0c650-367">Require authenticated users</span></span>

<span data-ttu-id="0c650-368">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0c650-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="0c650-369">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0c650-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="0c650-370">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="0c650-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="0c650-371">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="0c650-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="0c650-372">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="0c650-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="0c650-373">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="0c650-373">Configure the test account</span></span>

<span data-ttu-id="0c650-374">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="0c650-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="0c650-375">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="0c650-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="0c650-376">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="0c650-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="0c650-377">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0c650-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="0c650-378">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0c650-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="0c650-379">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="0c650-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="0c650-380">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0c650-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="0c650-381">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="0c650-382">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="0c650-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="0c650-383">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="0c650-384">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0c650-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="0c650-385">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="0c650-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="0c650-386">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen `ContactIsOwnerAuthorizationHandler` Sie eine Klasse darin.</span><span class="sxs-lookup"><span data-stu-id="0c650-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="0c650-387">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="0c650-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="0c650-388">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="0c650-389">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="0c650-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="0c650-390">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="0c650-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="0c650-391">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="0c650-392">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="0c650-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="0c650-393">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="0c650-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="0c650-394">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="0c650-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="0c650-395">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="0c650-396">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0c650-396">Create a manager authorization handler</span></span>

<span data-ttu-id="0c650-397">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0c650-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0c650-398">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="0c650-399">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="0c650-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="0c650-400">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="0c650-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="0c650-401">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="0c650-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="0c650-402">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="0c650-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="0c650-403">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="0c650-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="0c650-404">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="0c650-404">Register the authorization handlers</span></span>

<span data-ttu-id="0c650-405">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="0c650-406">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="0c650-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="0c650-407">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="0c650-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="0c650-408">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="0c650-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="0c650-409">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0c650-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="0c650-410">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="0c650-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="0c650-411">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="0c650-411">Support authorization</span></span>

<span data-ttu-id="0c650-412">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="0c650-413">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="0c650-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="0c650-414">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="0c650-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="0c650-415">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="0c650-416">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="0c650-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="0c650-417">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="0c650-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="0c650-418">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="0c650-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="0c650-419">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="0c650-419">The preceding code:</span></span>

* <span data-ttu-id="0c650-420">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="0c650-421">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="0c650-422">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="0c650-423">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="0c650-423">Update the CreateModel</span></span>

<span data-ttu-id="0c650-424">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0c650-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="0c650-425">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="0c650-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="0c650-426">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="0c650-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="0c650-427">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="0c650-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="0c650-428">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="0c650-428">Update the IndexModel</span></span>

<span data-ttu-id="0c650-429">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="0c650-430">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="0c650-430">Update the EditModel</span></span>

<span data-ttu-id="0c650-431">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="0c650-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="0c650-432">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="0c650-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="0c650-433">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="0c650-434">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="0c650-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="0c650-435">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="0c650-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="0c650-436">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="0c650-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="0c650-437">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="0c650-437">Update the DeleteModel</span></span>

<span data-ttu-id="0c650-438">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="0c650-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="0c650-439">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="0c650-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="0c650-440">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="0c650-441">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="0c650-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="0c650-442">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="0c650-443">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="0c650-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="0c650-444">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="0c650-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="0c650-445">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="0c650-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="0c650-446">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="0c650-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="0c650-447">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="0c650-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="0c650-448">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="0c650-448">Update Details</span></span>

<span data-ttu-id="0c650-449">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="0c650-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="0c650-450">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="0c650-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="0c650-451">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="0c650-451">Add or remove a user to a role</span></span>

<span data-ttu-id="0c650-452">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="0c650-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="0c650-453">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0c650-453">Removing privileges from a user.</span></span> <span data-ttu-id="0c650-454">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="0c650-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="0c650-455">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="0c650-456">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="0c650-456">Test the completed app</span></span>

<span data-ttu-id="0c650-457">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="0c650-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="0c650-458">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="0c650-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="0c650-459">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="0c650-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="0c650-460">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="0c650-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="0c650-461">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="0c650-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="0c650-462">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="0c650-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="0c650-463">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="0c650-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="0c650-464">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="0c650-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="0c650-465">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="0c650-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="0c650-466">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0c650-466">Verify the following operations:</span></span>

* <span data-ttu-id="0c650-467">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="0c650-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="0c650-468">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="0c650-469">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="0c650-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="0c650-470">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="0c650-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="0c650-471">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="0c650-472">Benutzer</span><span class="sxs-lookup"><span data-stu-id="0c650-472">User</span></span>                | <span data-ttu-id="0c650-473">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="0c650-473">Seeded by the app</span></span> | <span data-ttu-id="0c650-474">Optionen</span><span class="sxs-lookup"><span data-stu-id="0c650-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="0c650-475">Nein</span><span class="sxs-lookup"><span data-stu-id="0c650-475">No</span></span>                | <span data-ttu-id="0c650-476">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="0c650-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="0c650-477">Ja</span><span class="sxs-lookup"><span data-stu-id="0c650-477">Yes</span></span>               | <span data-ttu-id="0c650-478">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="0c650-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="0c650-479">Ja</span><span class="sxs-lookup"><span data-stu-id="0c650-479">Yes</span></span>               | <span data-ttu-id="0c650-480">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="0c650-481">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="0c650-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="0c650-482">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="0c650-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="0c650-483">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="0c650-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="0c650-484">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="0c650-484">Create the starter app</span></span>

* <span data-ttu-id="0c650-485">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="0c650-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="0c650-486">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="0c650-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="0c650-487">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0c650-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="0c650-488">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="0c650-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="0c650-489">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="0c650-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="0c650-490">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="0c650-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="0c650-491">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0c650-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="0c650-492">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="0c650-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="0c650-493">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="0c650-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="0c650-494">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="0c650-494">Seed the database</span></span>

<span data-ttu-id="0c650-495">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="0c650-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="0c650-496">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="0c650-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="0c650-497">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="0c650-497">Test that the app seeded the database.</span></span> <span data-ttu-id="0c650-498">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="0c650-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="0c650-499">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0c650-499">Additional resources</span></span>

* [<span data-ttu-id="0c650-500">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="0c650-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="0c650-501">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="0c650-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="0c650-502">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="0c650-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="0c650-503">Benutzerdefinierte Richtlinien basierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="0c650-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
