---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: 'Erfahren Sie, wie Sie eine ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.NET Core Identity .'
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: accfd46fa72c33976f8af2a39267c993447e036e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051940"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="46bf2-104">Erstellen einer ASP.net Core-Web-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="46bf2-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="46bf2-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="46bf2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="46bf2-106">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="46bf2-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="46bf2-107">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="46bf2-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="46bf2-108">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="46bf2-109">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-109">There are three security groups:</span></span>

* <span data-ttu-id="46bf2-110">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="46bf2-111">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="46bf2-112">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="46bf2-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="46bf2-113">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="46bf2-114">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="46bf2-115">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="46bf2-116">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="46bf2-117">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="46bf2-118">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="46bf2-120">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="46bf2-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="46bf2-122">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="46bf2-122">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="46bf2-124">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="46bf2-125">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="46bf2-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="46bf2-127">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-127">The administrator has all privileges.</span></span> <span data-ttu-id="46bf2-128">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="46bf2-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="46bf2-129">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="46bf2-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="46bf2-130">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="46bf2-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="46bf2-131">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="46bf2-132">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="46bf2-133">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="46bf2-134">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="46bf2-134">Prerequisites</span></span>

<span data-ttu-id="46bf2-135">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-135">This tutorial is advanced.</span></span> <span data-ttu-id="46bf2-136">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-136">You should be familiar with:</span></span>

* [<span data-ttu-id="46bf2-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46bf2-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="46bf2-138">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="46bf2-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="46bf2-139">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="46bf2-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="46bf2-140">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="46bf2-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="46bf2-141">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="46bf2-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="46bf2-142">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="46bf2-142">The starter and completed app</span></span>

<span data-ttu-id="46bf2-143">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="46bf2-144">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="46bf2-145">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="46bf2-145">The starter app</span></span>

<span data-ttu-id="46bf2-146">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="46bf2-147">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="46bf2-148">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="46bf2-148">Secure user data</span></span>

<span data-ttu-id="46bf2-149">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-149">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="46bf2-150">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-150">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="46bf2-151">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-151">Tie the contact data to the user</span></span>

<span data-ttu-id="46bf2-152">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-152">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="46bf2-153">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-153">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="46bf2-154">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="46bf2-154">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="46bf2-155">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-155">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="46bf2-156">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="46bf2-156">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="46bf2-157">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="46bf2-157">Add Role services to Identity</span></span>

<span data-ttu-id="46bf2-158">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="46bf2-158">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="46bf2-159">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="46bf2-159">Require authenticated users</span></span>

<span data-ttu-id="46bf2-160">Legen Sie die Fall Back Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-160">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="46bf2-161">Der obige markierte Code legt die [Fall Back Authentifizierungs Richtlinie](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)fest.</span><span class="sxs-lookup"><span data-stu-id="46bf2-161">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="46bf2-162">Die Fall Back Authentifizierungs Richtlinie erfordert, dass \* *_all_* _-Benutzer authentifiziert werden, mit Ausnahme von Razor Seiten, Controllern oder Aktionsmethoden mit einem Authentifizierungs Attribut.</span><span class="sxs-lookup"><span data-stu-id="46bf2-162">The fallback authentication policy requires \* *_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="46bf2-163">Beispielsweise werden Razor Seiten, Controller oder Aktionsmethoden mit `[AllowAnonymous]` oder `[Authorize(PolicyName="MyPolicy")]` das angewendete Authentifizierungs Attribut anstelle der Fall Back Authentifizierungs Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-163">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="46bf2-164">Die Richtlinie für die Fall Back Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="46bf2-164">The fallback authentication policy:</span></span>

<span data-ttu-id="46bf2-165">_ Wird auf alle Anforderungen angewendet, die nicht explizit eine Authentifizierungs Richtlinie angeben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-165">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="46bf2-166">Bei Anforderungen, die von der Endpunkt Weiterleitung verarbeitet werden, würde dies alle Endpunkte einschließen, für die kein Autorisierungs Attribut angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-166">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="46bf2-167">Bei Anforderungen, die von einer anderen Middleware nach der Autorisierungs Middleware, wie z. b. [statischen Dateien](xref:fundamentals/static-files), verarbeitet werden, wird die Richtlinie auf alle Anforderungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-167">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="46bf2-168">Das Festlegen der Fall Back Authentifizierungs Richtlinie, damit Benutzer authentifiziert werden müssen, schützt neu hinzugefügte Razor Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="46bf2-168">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="46bf2-169">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="46bf2-169">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="46bf2-170">Die- <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> Klasse enthält ebenfalls <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="46bf2-170">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="46bf2-171">`DefaultPolicy`Ist die Richtlinie, die mit dem-Attribut verwendet wird, `[Authorize]` Wenn keine Richtlinie angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="46bf2-171">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="46bf2-172">`[Authorize]` enthält im Gegensatz zu keine benannte Richtlinie `[Authorize(PolicyName="MyPolicy")]` .</span><span class="sxs-lookup"><span data-stu-id="46bf2-172">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="46bf2-173">Weitere Informationen zu Richtlinien finden Sie unter <xref:security/authorization/policies> .</span><span class="sxs-lookup"><span data-stu-id="46bf2-173">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="46bf2-174">Eine alternative Möglichkeit für MVC-Controller und- Razor Seiten, dass alle Benutzer authentifiziert werden müssen, besteht darin, einen Autorisierungs Filter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-174">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="46bf2-175">Im vorangehenden Code wird ein Autorisierungs Filter verwendet, bei dem die Fall Back Richtlinie das Endpunkt Routing verwendet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-175">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="46bf2-176">Das Festlegen der Fall Back Richtlinie ist die bevorzugte Methode, um alle Benutzer zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="46bf2-176">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="46bf2-177">Fügen Sie den Seiten und die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) hinzu, `Index` `Privacy` damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können:</span><span class="sxs-lookup"><span data-stu-id="46bf2-177">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="46bf2-178">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="46bf2-178">Configure the test account</span></span>

<span data-ttu-id="46bf2-179">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="46bf2-179">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="46bf2-180">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-180">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="46bf2-181">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs* ) fest:</span><span class="sxs-lookup"><span data-stu-id="46bf2-181">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="46bf2-182">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="46bf2-182">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="46bf2-183">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-183">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="46bf2-184">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="46bf2-184">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="46bf2-185">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-185">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="46bf2-186">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-186">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="46bf2-187">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="46bf2-187">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="46bf2-188">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-188">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="46bf2-189">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="46bf2-189">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="46bf2-190">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="46bf2-190">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="46bf2-191">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="46bf2-191">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="46bf2-192">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-192">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="46bf2-193">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-193">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="46bf2-194">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-194">Authorization handlers generally:</span></span>

* <span data-ttu-id="46bf2-195">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="46bf2-195">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="46bf2-196">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-196">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="46bf2-197">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-197">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="46bf2-198">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="46bf2-198">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="46bf2-199">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-199">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="46bf2-200">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-200">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="46bf2-201">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="46bf2-201">Create a manager authorization handler</span></span>

<span data-ttu-id="46bf2-202">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="46bf2-202">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="46bf2-203">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-203">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="46bf2-204">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="46bf2-204">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="46bf2-205">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="46bf2-205">Create an administrator authorization handler</span></span>

<span data-ttu-id="46bf2-206">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="46bf2-206">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="46bf2-207">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-207">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="46bf2-208">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-208">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="46bf2-209">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="46bf2-209">Register the authorization handlers</span></span>

<span data-ttu-id="46bf2-210">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-210">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="46bf2-211">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-211">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="46bf2-212">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="46bf2-212">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46bf2-213">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-213">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="46bf2-214">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-214">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="46bf2-215">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="46bf2-215">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="46bf2-216">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="46bf2-216">Support authorization</span></span>

<span data-ttu-id="46bf2-217">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-217">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="46bf2-218">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="46bf2-218">Review the contact operations requirements class</span></span>

<span data-ttu-id="46bf2-219">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="46bf2-219">Review the `ContactOperations` class.</span></span> <span data-ttu-id="46bf2-220">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-220">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="46bf2-221">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="46bf2-221">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="46bf2-222">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="46bf2-222">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="46bf2-223">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="46bf2-223">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="46bf2-224">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="46bf2-224">The preceding code:</span></span>

* <span data-ttu-id="46bf2-225">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-225">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="46bf2-226">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-226">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="46bf2-227">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-227">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="46bf2-228">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="46bf2-228">Update the CreateModel</span></span>

<span data-ttu-id="46bf2-229">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-229">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="46bf2-230">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-230">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="46bf2-231">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="46bf2-231">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="46bf2-232">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="46bf2-232">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="46bf2-233">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="46bf2-233">Update the IndexModel</span></span>

<span data-ttu-id="46bf2-234">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-234">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="46bf2-235">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="46bf2-235">Update the EditModel</span></span>

<span data-ttu-id="46bf2-236">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="46bf2-236">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="46bf2-237">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="46bf2-237">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="46bf2-238">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-238">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="46bf2-239">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="46bf2-239">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="46bf2-240">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-240">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="46bf2-241">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-241">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="46bf2-242">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="46bf2-242">Update the DeleteModel</span></span>

<span data-ttu-id="46bf2-243">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="46bf2-243">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="46bf2-244">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="46bf2-244">Inject the authorization service into the views</span></span>

<span data-ttu-id="46bf2-245">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-245">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="46bf2-246">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="46bf2-246">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="46bf2-247">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-247">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="46bf2-248">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-248">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="46bf2-249">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="46bf2-249">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="46bf2-250">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-250">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="46bf2-251">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-251">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="46bf2-252">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="46bf2-252">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="46bf2-253">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="46bf2-253">Update Details</span></span>

<span data-ttu-id="46bf2-254">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="46bf2-254">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="46bf2-255">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="46bf2-255">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="46bf2-256">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="46bf2-256">Add or remove a user to a role</span></span>

<span data-ttu-id="46bf2-257">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="46bf2-257">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="46bf2-258">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="46bf2-258">Removing privileges from a user.</span></span> <span data-ttu-id="46bf2-259">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="46bf2-259">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="46bf2-260">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-260">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="46bf2-261">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="46bf2-261">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="46bf2-262">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#rau)sind.</span><span class="sxs-lookup"><span data-stu-id="46bf2-262">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="46bf2-263">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="46bf2-263">The following code allows anonymous users.</span></span> <span data-ttu-id="46bf2-264">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-264">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="46bf2-265">Im obigen Code:</span><span class="sxs-lookup"><span data-stu-id="46bf2-265">In the preceding code:</span></span>

* <span data-ttu-id="46bf2-266">Wenn der Benutzer **nicht** authentifiziert ist, `ChallengeResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-266">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="46bf2-267">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-267">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="46bf2-268">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, `ForbidResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-268">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="46bf2-269">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-269">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="46bf2-270">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="46bf2-270">Test the completed app</span></span>

<span data-ttu-id="46bf2-271">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-271">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="46bf2-272">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="46bf2-272">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="46bf2-273">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-273">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="46bf2-274">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="46bf2-274">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="46bf2-275">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="46bf2-275">If the app has contacts:</span></span>

* <span data-ttu-id="46bf2-276">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="46bf2-276">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="46bf2-277">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-277">Restart the app to seed the database.</span></span>

<span data-ttu-id="46bf2-278">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-278">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="46bf2-279">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="46bf2-279">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="46bf2-280">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-280">Sign in to each browser with a different user.</span></span> <span data-ttu-id="46bf2-281">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="46bf2-281">Verify the following operations:</span></span>

* <span data-ttu-id="46bf2-282">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-282">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="46bf2-283">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-283">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="46bf2-284">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-284">Managers can approve/reject contact data.</span></span> <span data-ttu-id="46bf2-285">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="46bf2-285">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="46bf2-286">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-286">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="46bf2-287">Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-287">User</span></span>                | <span data-ttu-id="46bf2-288">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="46bf2-288">Seeded by the app</span></span> | <span data-ttu-id="46bf2-289">Optionen</span><span class="sxs-lookup"><span data-stu-id="46bf2-289">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="46bf2-290">No</span><span class="sxs-lookup"><span data-stu-id="46bf2-290">No</span></span>                | <span data-ttu-id="46bf2-291">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-291">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="46bf2-292">Yes</span><span class="sxs-lookup"><span data-stu-id="46bf2-292">Yes</span></span>               | <span data-ttu-id="46bf2-293">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-293">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="46bf2-294">Yes</span><span class="sxs-lookup"><span data-stu-id="46bf2-294">Yes</span></span>               | <span data-ttu-id="46bf2-295">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-295">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="46bf2-296">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="46bf2-296">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="46bf2-297">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-297">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="46bf2-298">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-298">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="46bf2-299">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="46bf2-299">Create the starter app</span></span>

* <span data-ttu-id="46bf2-300">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="46bf2-300">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="46bf2-301">Erstellen Sie die APP mit **einzelnen Benutzerkonten** .</span><span class="sxs-lookup"><span data-stu-id="46bf2-301">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="46bf2-302">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="46bf2-302">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="46bf2-303">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-303">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="46bf2-304">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-304">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="46bf2-305">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="46bf2-305">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="46bf2-306">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="46bf2-306">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="46bf2-307">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="46bf2-307">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="46bf2-308">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46bf2-308">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="46bf2-309">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-309">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="46bf2-310">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="46bf2-310">Seed the database</span></span>

<span data-ttu-id="46bf2-311">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="46bf2-311">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="46bf2-312">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-312">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="46bf2-313">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="46bf2-313">Test that the app seeded the database.</span></span> <span data-ttu-id="46bf2-314">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-314">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="46bf2-315">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="46bf2-315">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="46bf2-316">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-316">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="46bf2-317">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-317">There are three security groups:</span></span>

* <span data-ttu-id="46bf2-318">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-318">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="46bf2-319">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-319">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="46bf2-320">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="46bf2-320">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="46bf2-321">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-321">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="46bf2-322">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="46bf2-322">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="46bf2-323">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen** bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-323">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="46bf2-324">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-324">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="46bf2-325">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-325">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="46bf2-327">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="46bf2-327">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="46bf2-329">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="46bf2-329">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="46bf2-331">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-331">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="46bf2-332">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="46bf2-332">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="46bf2-334">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-334">The administrator has all privileges.</span></span> <span data-ttu-id="46bf2-335">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="46bf2-335">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="46bf2-336">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="46bf2-336">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="46bf2-337">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="46bf2-337">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="46bf2-338">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-338">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="46bf2-339">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-339">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="46bf2-340">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-340">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="46bf2-341">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="46bf2-341">Prerequisites</span></span>

<span data-ttu-id="46bf2-342">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-342">This tutorial is advanced.</span></span> <span data-ttu-id="46bf2-343">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-343">You should be familiar with:</span></span>

* [<span data-ttu-id="46bf2-344">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="46bf2-344">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="46bf2-345">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="46bf2-345">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="46bf2-346">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="46bf2-346">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="46bf2-347">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="46bf2-347">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="46bf2-348">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="46bf2-348">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="46bf2-349">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="46bf2-349">The starter and completed app</span></span>

<span data-ttu-id="46bf2-350">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-350">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="46bf2-351">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-351">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="46bf2-352">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="46bf2-352">The starter app</span></span>

<span data-ttu-id="46bf2-353">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-353">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="46bf2-354">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-354">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="46bf2-355">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="46bf2-355">Secure user data</span></span>

<span data-ttu-id="46bf2-356">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-356">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="46bf2-357">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-357">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="46bf2-358">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-358">Tie the contact data to the user</span></span>

<span data-ttu-id="46bf2-359">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-359">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="46bf2-360">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-360">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="46bf2-361">`OwnerID` die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="46bf2-361">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="46bf2-362">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-362">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="46bf2-363">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="46bf2-363">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="46bf2-364">Rollen Dienste hinzufügen zu Identity</span><span class="sxs-lookup"><span data-stu-id="46bf2-364">Add Role services to Identity</span></span>

<span data-ttu-id="46bf2-365">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="46bf2-365">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="46bf2-366">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="46bf2-366">Require authenticated users</span></span>

<span data-ttu-id="46bf2-367">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-367">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="46bf2-368">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-368">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="46bf2-369">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-369">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="46bf2-370">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="46bf2-370">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="46bf2-371">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-371">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="46bf2-372">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="46bf2-372">Configure the test account</span></span>

<span data-ttu-id="46bf2-373">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="46bf2-373">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="46bf2-374">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-374">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="46bf2-375">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs* ) fest:</span><span class="sxs-lookup"><span data-stu-id="46bf2-375">Set the password from the project directory (the directory containing *Program.cs* ):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="46bf2-376">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="46bf2-376">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="46bf2-377">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-377">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="46bf2-378">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="46bf2-378">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="46bf2-379">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-379">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="46bf2-380">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-380">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="46bf2-381">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="46bf2-381">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="46bf2-382">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-382">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="46bf2-383">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="46bf2-383">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="46bf2-384">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="46bf2-384">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="46bf2-385">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen `ContactIsOwnerAuthorizationHandler` Sie eine Klasse darin.</span><span class="sxs-lookup"><span data-stu-id="46bf2-385">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="46bf2-386">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-386">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="46bf2-387">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-387">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="46bf2-388">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-388">Authorization handlers generally:</span></span>

* <span data-ttu-id="46bf2-389">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="46bf2-389">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="46bf2-390">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-390">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="46bf2-391">`Task.CompletedTask` ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="46bf2-391">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="46bf2-392">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="46bf2-392">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="46bf2-393">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-393">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="46bf2-394">`ContactIsOwnerAuthorizationHandler` der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-394">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="46bf2-395">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="46bf2-395">Create a manager authorization handler</span></span>

<span data-ttu-id="46bf2-396">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="46bf2-396">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="46bf2-397">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-397">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="46bf2-398">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="46bf2-398">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="46bf2-399">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="46bf2-399">Create an administrator authorization handler</span></span>

<span data-ttu-id="46bf2-400">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="46bf2-400">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="46bf2-401">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="46bf2-401">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="46bf2-402">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-402">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="46bf2-403">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="46bf2-403">Register the authorization handlers</span></span>

<span data-ttu-id="46bf2-404">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-404">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="46bf2-405">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-405">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="46bf2-406">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="46bf2-406">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="46bf2-407">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-407">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="46bf2-408">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-408">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="46bf2-409">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="46bf2-409">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="46bf2-410">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="46bf2-410">Support authorization</span></span>

<span data-ttu-id="46bf2-411">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-411">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="46bf2-412">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="46bf2-412">Review the contact operations requirements class</span></span>

<span data-ttu-id="46bf2-413">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="46bf2-413">Review the `ContactOperations` class.</span></span> <span data-ttu-id="46bf2-414">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-414">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="46bf2-415">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="46bf2-415">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="46bf2-416">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="46bf2-416">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="46bf2-417">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="46bf2-417">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="46bf2-418">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="46bf2-418">The preceding code:</span></span>

* <span data-ttu-id="46bf2-419">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-419">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="46bf2-420">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-420">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="46bf2-421">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-421">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="46bf2-422">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="46bf2-422">Update the CreateModel</span></span>

<span data-ttu-id="46bf2-423">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-423">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="46bf2-424">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-424">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="46bf2-425">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="46bf2-425">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="46bf2-426">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="46bf2-426">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="46bf2-427">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="46bf2-427">Update the IndexModel</span></span>

<span data-ttu-id="46bf2-428">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-428">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="46bf2-429">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="46bf2-429">Update the EditModel</span></span>

<span data-ttu-id="46bf2-430">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="46bf2-430">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="46bf2-431">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="46bf2-431">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="46bf2-432">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-432">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="46bf2-433">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="46bf2-433">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="46bf2-434">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-434">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="46bf2-435">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="46bf2-435">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="46bf2-436">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="46bf2-436">Update the DeleteModel</span></span>

<span data-ttu-id="46bf2-437">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="46bf2-437">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="46bf2-438">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="46bf2-438">Inject the authorization service into the views</span></span>

<span data-ttu-id="46bf2-439">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-439">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="46bf2-440">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="46bf2-440">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="46bf2-441">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-441">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="46bf2-442">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="46bf2-442">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="46bf2-443">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="46bf2-443">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="46bf2-444">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="46bf2-444">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="46bf2-445">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-445">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="46bf2-446">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="46bf2-446">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="46bf2-447">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="46bf2-447">Update Details</span></span>

<span data-ttu-id="46bf2-448">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="46bf2-448">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="46bf2-449">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="46bf2-449">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="46bf2-450">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="46bf2-450">Add or remove a user to a role</span></span>

<span data-ttu-id="46bf2-451">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="46bf2-451">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="46bf2-452">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="46bf2-452">Removing privileges from a user.</span></span> <span data-ttu-id="46bf2-453">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="46bf2-453">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="46bf2-454">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-454">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="46bf2-455">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="46bf2-455">Test the completed app</span></span>

<span data-ttu-id="46bf2-456">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-456">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="46bf2-457">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="46bf2-457">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="46bf2-458">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="46bf2-458">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="46bf2-459">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="46bf2-459">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="46bf2-460">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="46bf2-460">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="46bf2-461">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-461">Restart the app to seed the database.</span></span>

<span data-ttu-id="46bf2-462">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-462">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="46bf2-463">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="46bf2-463">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="46bf2-464">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-464">Sign in to each browser with a different user.</span></span> <span data-ttu-id="46bf2-465">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="46bf2-465">Verify the following operations:</span></span>

* <span data-ttu-id="46bf2-466">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-466">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="46bf2-467">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-467">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="46bf2-468">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-468">Managers can approve/reject contact data.</span></span> <span data-ttu-id="46bf2-469">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="46bf2-469">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="46bf2-470">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-470">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="46bf2-471">Benutzer</span><span class="sxs-lookup"><span data-stu-id="46bf2-471">User</span></span>                | <span data-ttu-id="46bf2-472">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="46bf2-472">Seeded by the app</span></span> | <span data-ttu-id="46bf2-473">Optionen</span><span class="sxs-lookup"><span data-stu-id="46bf2-473">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="46bf2-474">No</span><span class="sxs-lookup"><span data-stu-id="46bf2-474">No</span></span>                | <span data-ttu-id="46bf2-475">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-475">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="46bf2-476">Yes</span><span class="sxs-lookup"><span data-stu-id="46bf2-476">Yes</span></span>               | <span data-ttu-id="46bf2-477">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="46bf2-477">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="46bf2-478">Yes</span><span class="sxs-lookup"><span data-stu-id="46bf2-478">Yes</span></span>               | <span data-ttu-id="46bf2-479">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-479">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="46bf2-480">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="46bf2-480">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="46bf2-481">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-481">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="46bf2-482">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="46bf2-482">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="46bf2-483">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="46bf2-483">Create the starter app</span></span>

* <span data-ttu-id="46bf2-484">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="46bf2-484">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="46bf2-485">Erstellen Sie die APP mit **einzelnen Benutzerkonten** .</span><span class="sxs-lookup"><span data-stu-id="46bf2-485">Create the app with **Individual User Accounts** .</span></span>
  * <span data-ttu-id="46bf2-486">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="46bf2-486">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="46bf2-487">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="46bf2-487">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="46bf2-488">*Modelle/Contact. cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="46bf2-488">Add *Models/Contact.cs* :</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="46bf2-489">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="46bf2-489">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="46bf2-490">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="46bf2-490">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="46bf2-491">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="46bf2-491">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="46bf2-492">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="46bf2-492">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="46bf2-493">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="46bf2-493">Seed the database</span></span>

<span data-ttu-id="46bf2-494">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="46bf2-494">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="46bf2-495">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="46bf2-495">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="46bf2-496">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="46bf2-496">Test that the app seeded the database.</span></span> <span data-ttu-id="46bf2-497">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="46bf2-497">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="46bf2-498">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="46bf2-498">Additional resources</span></span>

* [<span data-ttu-id="46bf2-499">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="46bf2-499">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="46bf2-500">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="46bf2-500">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="46bf2-501">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="46bf2-501">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="46bf2-502">Benutzerdefinierte Richtlinien basierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="46bf2-502">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
