---
title: Richtlinien basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Richtlinien Handler zum Erzwingen von Autorisierungs Anforderungen in einer ASP.net Core-app erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
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
uid: security/authorization/policies
ms.openlocfilehash: 140969c80191c9549bc19507b9c4818b88fed0d1
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636802"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="13851-103">Richtlinien basierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="13851-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="13851-104">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="13851-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="13851-105">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="13851-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="13851-106">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="13851-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="13851-107">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="13851-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="13851-108">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="13851-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="13851-109">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="13851-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="13851-110">Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="13851-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="13851-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="13851-111">IAuthorizationService</span></span>

<span data-ttu-id="13851-112">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="13851-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="13851-113">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="13851-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="13851-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="13851-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="13851-115">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="13851-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="13851-116">Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="13851-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="13851-117">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="13851-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="13851-118">Der folgende Code zeigt ein typisches `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13851-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="13851-119">Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="13851-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="13851-120">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="13851-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="13851-121">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="13851-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="13851-122">Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="13851-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="13851-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="13851-124">Anwenden von Richtlinien auf Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="13851-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="13851-125">Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet.</span><span class="sxs-lookup"><span data-stu-id="13851-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="13851-126">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="13851-127">Richtlinien können ***nicht*** auf Razor seitenhandlerebene angewendet werden, sondern müssen auf die Seite angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="13851-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="13851-128">Richtlinien können Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="13851-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="13851-129">Anforderungen</span><span class="sxs-lookup"><span data-stu-id="13851-129">Requirements</span></span>

<span data-ttu-id="13851-130">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="13851-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="13851-131">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter.</span><span class="sxs-lookup"><span data-stu-id="13851-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="13851-132">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="13851-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="13851-133">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="13851-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="13851-134">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="13851-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="13851-135">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="13851-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="13851-136">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="13851-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="13851-137">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="13851-137">Authorization handlers</span></span>

<span data-ttu-id="13851-138">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="13851-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="13851-139">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="13851-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="13851-140">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="13851-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="13851-141">Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="13851-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="13851-142">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="13851-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="13851-143">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="13851-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="13851-144">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="13851-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="13851-145">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="13851-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="13851-146">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="13851-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="13851-147">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="13851-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="13851-148">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="13851-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="13851-149">Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="13851-150">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="13851-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="13851-151">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="13851-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="13851-152">Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="13851-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="13851-153">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="13851-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="13851-154">Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="13851-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="13851-155">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="13851-155">Handler registration</span></span>

<span data-ttu-id="13851-156">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="13851-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="13851-157">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="13851-158">Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="13851-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="13851-159">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="13851-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="13851-160">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="13851-160">What should a handler return?</span></span>

<span data-ttu-id="13851-161">Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="13851-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="13851-162">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="13851-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="13851-163">Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="13851-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="13851-164">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="13851-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="13851-165">Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="13851-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="13851-166">Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="13851-167">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="13851-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="13851-168">Wenn diese Eigenschaft auf festgelegt `false` ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft kurz verknüpft, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="13851-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="13851-169">`InvokeHandlersAfterFailure` der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="13851-170">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="13851-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="13851-171">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="13851-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="13851-172">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="13851-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="13851-173">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="13851-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="13851-174">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="13851-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="13851-175">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="13851-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="13851-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="13851-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="13851-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="13851-179">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="13851-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="13851-180">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="13851-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="13851-181">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="13851-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="13851-182">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="13851-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="13851-183">`Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="13851-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="13851-184">Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="13851-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="13851-185">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="13851-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="13851-186">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="13851-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="13851-187">Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="13851-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="13851-188">Wenn Sie das Endpunkt Routing verwenden, wird die Autorisierung in der Regel von der Autorisierungs Middleware verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="13851-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="13851-189">In diesem Fall ist die `Resource` Eigenschaft eine Instanz von <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="13851-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="13851-190">Der Endpunkt kann verwendet werden, um die zugrunde liegende Ressource zu überprüfen, an die Sie weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="13851-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="13851-191">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="13851-192">Der-Endpunkt bietet keinen Zugriff auf die aktuelle `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="13851-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="13851-193">Wenn Sie das Endpunkt Routing verwenden, verwenden `IHttpContextAcessor` Sie für den Zugriff `HttpContext` innerhalb eines Autorisierungs Handlers.</span><span class="sxs-lookup"><span data-stu-id="13851-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="13851-194">Weitere Informationen finden Sie unter [Verwenden von HttpContext aus benutzerdefinierten Komponenten](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="13851-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="13851-195">Mit herkömmlichem Routing oder wenn die Autorisierung im Rahmen des Autorisierungs Filters von MVC erfolgt, ist der Wert von `Resource` eine <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Instanz von.</span><span class="sxs-lookup"><span data-stu-id="13851-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="13851-196">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .</span><span class="sxs-lookup"><span data-stu-id="13851-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="13851-197">Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="13851-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="13851-198">Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="13851-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="13851-199">Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="13851-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="13851-200">Erfordert Global, dass alle Benutzer authentifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="13851-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="13851-201">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="13851-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="13851-202">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="13851-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="13851-203">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="13851-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="13851-204">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="13851-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="13851-205">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="13851-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="13851-206">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="13851-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="13851-207">Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="13851-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="13851-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="13851-208">IAuthorizationService</span></span> 

<span data-ttu-id="13851-209">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="13851-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="13851-210">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="13851-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="13851-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="13851-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="13851-212">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="13851-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="13851-213">Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="13851-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="13851-214">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="13851-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="13851-215">Der folgende Code zeigt ein typisches `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="13851-215">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="13851-216">Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="13851-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="13851-217">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="13851-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="13851-218">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="13851-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="13851-219">Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="13851-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="13851-220">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="13851-221">Anwenden von Richtlinien auf Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="13851-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="13851-222">Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet.</span><span class="sxs-lookup"><span data-stu-id="13851-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="13851-223">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="13851-224">Richtlinien können auch Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="13851-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="13851-225">Anforderungen</span><span class="sxs-lookup"><span data-stu-id="13851-225">Requirements</span></span>

<span data-ttu-id="13851-226">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="13851-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="13851-227">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter.</span><span class="sxs-lookup"><span data-stu-id="13851-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="13851-228">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="13851-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="13851-229">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="13851-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="13851-230">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="13851-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="13851-231">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="13851-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="13851-232">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="13851-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="13851-233">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="13851-233">Authorization handlers</span></span>

<span data-ttu-id="13851-234">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="13851-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="13851-235">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="13851-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="13851-236">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="13851-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="13851-237">Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="13851-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="13851-238">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="13851-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="13851-239">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="13851-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="13851-240">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="13851-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="13851-241">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="13851-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="13851-242">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="13851-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="13851-243">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="13851-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="13851-244">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="13851-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="13851-245">Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="13851-246">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="13851-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="13851-247">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="13851-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="13851-248">Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="13851-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="13851-249">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="13851-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="13851-250">Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="13851-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="13851-251">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="13851-251">Handler registration</span></span>

<span data-ttu-id="13851-252">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="13851-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="13851-253">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="13851-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="13851-254">Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="13851-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="13851-255">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="13851-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="13851-256">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="13851-256">What should a handler return?</span></span>

<span data-ttu-id="13851-257">Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="13851-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="13851-258">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="13851-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="13851-259">Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="13851-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="13851-260">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="13851-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="13851-261">Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="13851-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="13851-262">Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="13851-263">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="13851-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="13851-264">Wenn diese Eigenschaft auf festgelegt `false` ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft kurz verknüpft, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="13851-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="13851-265">`InvokeHandlersAfterFailure` der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="13851-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="13851-266">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="13851-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="13851-267">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="13851-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="13851-268">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="13851-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="13851-269">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="13851-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="13851-270">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="13851-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="13851-271">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="13851-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="13851-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="13851-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="13851-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="13851-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="13851-275">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="13851-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="13851-276">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="13851-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="13851-277">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="13851-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="13851-278">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="13851-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="13851-279">`Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="13851-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="13851-280">Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="13851-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="13851-281">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="13851-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="13851-282">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="13851-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="13851-283">Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="13851-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="13851-284">MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in der- `Resource` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="13851-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="13851-285">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .</span><span class="sxs-lookup"><span data-stu-id="13851-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="13851-286">Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="13851-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="13851-287">Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="13851-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="13851-288">Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="13851-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
