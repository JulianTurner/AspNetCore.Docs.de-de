---
title: Richtlinien basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Richtlinien Handler zum Erzwingen von Autorisierungs Anforderungen in einer ASP.net Core-app erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: 286dc3bcc66b86a2a6b7d3cb7b6052bf7b474aff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060208"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Richtlinien basierte Autorisierung in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet. Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code. Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.

Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen. Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt. Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.

## <a name="iauthorizationservice"></a>IAuthorizationService

Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.

Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:
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

Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:

```csharp
 context.Succeed(requirement)
```

Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:

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

Der folgende Code zeigt ein typisches `ConfigureServices` :

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

Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.

## <a name="apply-policies-to-mvc-controllers"></a>Anwenden von Richtlinien auf MVC-Controller

Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.

Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Anwenden von Richtlinien auf Razor Seiten

Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Richtlinien können * **nicht** _ auf Razor seitenhandlerebene angewendet werden, Sie müssen auf die Seite angewendet werden.

Richtlinien können Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.

## <a name="requirements"></a>Anforderungen

Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten. In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter. Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt. Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist. Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf Basis von _ *und* * behandelt.

> [!NOTE]
> Eine Anforderung muss keine Daten oder Eigenschaften haben.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Autorisierungs Handler

Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren. Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.

Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen. Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist. Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.

### <a name="use-a-handler-for-one-requirement"></a>Verwenden eines Handlers für eine Anforderung

<a name="security-authorization-handler-example"></a>

Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde. Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben. Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet. Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft. Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.

### <a name="use-a-handler-for-multiple-requirements"></a>Verwenden eines Handlers für mehrere Anforderungen

Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind. Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen. Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Handlerregistrierung

Handler werden während der Konfiguration in der Dienste-Sammlung registriert. Zum Beispiel:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.

## <a name="what-should-a-handler-return"></a>Was sollte ein Handler zurückgeben?

Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt. Wie wird der Status "erfolgreich" oder "Fehler" angegeben?

* Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.

* Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.

* Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`

Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen. Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat. Wenn diese Eigenschaft auf festgelegt `false` ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft kurz verknüpft, wenn `context.Fail` aufgerufen wird. `InvokeHandlersAfterFailure` der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.

> [!NOTE]
> Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum sollte ich mehrere Handler für eine Anforderung wünschen?

Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren. Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden. Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie. In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry* , aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind. Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.

## <a name="use-a-func-to-fulfill-a-policy"></a>Verwenden eines Func zum erfüllen einer Richtlinie

Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann. `Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .

Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>Zugreifen auf den MVC-Anforderungs Kontext in Handlern

Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln. Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.

Wenn Sie das Endpunkt Routing verwenden, wird die Autorisierung in der Regel von der Autorisierungs Middleware verarbeitet. In diesem Fall ist die `Resource` Eigenschaft eine Instanz von <xref:Microsoft.AspNetCore.Http.Endpoint> . Der Endpunkt kann verwendet werden, um die zugrunde liegende Ressource zu überprüfen, an die Sie weiterleiten. Zum Beispiel:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Der-Endpunkt bietet keinen Zugriff auf die aktuelle `HttpContext` . Wenn Sie das Endpunkt Routing verwenden, verwenden `IHttpContextAcessor` Sie für den Zugriff `HttpContext` innerhalb eines Autorisierungs Handlers. Weitere Informationen finden Sie unter [Verwenden von HttpContext aus benutzerdefinierten Komponenten](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).

Mit herkömmlichem Routing oder wenn die Autorisierung im Rahmen des Autorisierungs Filters von MVC erfolgt, ist der Wert von `Resource` eine <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Instanz von. Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .

Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch. Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein. Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Erfordert Global, dass alle Benutzer authentifiziert werden.

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet. Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code. Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.

Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen. Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt. Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.

Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:
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

Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:

```csharp
 context.Succeed(requirement)
```

Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:

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

Der folgende Code zeigt ein typisches `ConfigureServices` :

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

Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.

## <a name="apply-policies-to-mvc-controllers"></a>Anwenden von Richtlinien auf MVC-Controller

Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.

Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>Anwenden von Richtlinien auf Razor Seiten

Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Richtlinien können auch Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.

## <a name="requirements"></a>Anforderungen

Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten. In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter. Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt. Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist. Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.

> [!NOTE]
> Eine Anforderung muss keine Daten oder Eigenschaften haben.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Autorisierungs Handler

Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren. Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.

Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen. Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist. Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.

### <a name="use-a-handler-for-one-requirement"></a>Verwenden eines Handlers für eine Anforderung

<a name="security-authorization-handler-example"></a>

Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde. Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben. Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet. Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft. Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.

### <a name="use-a-handler-for-multiple-requirements"></a>Verwenden eines Handlers für mehrere Anforderungen

Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind. Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen. Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Handlerregistrierung

Handler werden während der Konfiguration in der Dienste-Sammlung registriert. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.

## <a name="what-should-a-handler-return"></a>Was sollte ein Handler zurückgeben?

Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt. Wie wird der Status "erfolgreich" oder "Fehler" angegeben?

* Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.

* Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.

* Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`

Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen. Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat. Wenn diese Eigenschaft auf festgelegt `false` ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft kurz verknüpft, wenn `context.Fail` aufgerufen wird. `InvokeHandlersAfterFailure` der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.

> [!NOTE]
> Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum sollte ich mehrere Handler für eine Anforderung wünschen?

Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren. Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden. Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie. In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry* , aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind. Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.

## <a name="use-a-func-to-fulfill-a-policy"></a>Verwenden eines Func zum erfüllen einer Richtlinie

Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann. `Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .

Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>Zugreifen auf den MVC-Anforderungs Kontext in Handlern

Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln. Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.

MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in der- `Resource` Eigenschaft. Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .

Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch. Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein. Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
