---
title: Ressourcenbasierte Autorisierung in ASP.net Core
author: scottaddie
description: Erfahren Sie, wie Sie die ressourcenbasierte Autorisierung in einer ASP.net Core-App implementieren, wenn ein Autorisierungs Attribut nicht ausreicht.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
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
uid: security/authorization/resourcebased
ms.openlocfilehash: 202ca681a66ddf7f729d8835e2f77da846583df1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060169"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Ressourcenbasierte Autorisierung in ASP.net Core

Autorisierungs Strategie hängt von der Ressource ab, auf die zugegriffen wird. Stellen Sie sich ein Dokument mit der Eigenschaft Autor vor. Nur der Autor darf das Dokument aktualisieren. Folglich muss das Dokument aus dem Datenspeicher abgerufen werden, bevor die Autorisierungs Auswertung erfolgen kann.

Die Attribut Auswertung erfolgt vor der Datenbindung und vor der Ausführung des Seiten Handlers oder der Aktion, die das Dokument lädt. Aus diesen Gründen genügt die deklarative Autorisierung mit einem- `[Authorize]` Attribut nicht. Sie können stattdessen eine benutzerdefinierte Autorisierungs Methode aufrufen, die &mdash; als *imperative Autorisierung* bezeichnet wird.

::: moniker range=">= aspnetcore-3.0"
[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/3_0) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/2_2) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples/1_1) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).
::: moniker-end

[Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten](xref:security/authorization/secure-data) enthält eine Beispiel-APP, die die ressourcenbasierte Autorisierung verwendet.

## <a name="use-imperative-authorization"></a>Imperative Autorisierung verwenden

Die Autorisierung wird als [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) -Dienst implementiert und in der-Dienst Auflistung innerhalb der- `Startup` Klasse registriert. Der Dienst wird über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in Seiten Handler oder Aktionen bereitgestellt.

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` verfügt über zwei `AuthorizeAsync` Methoden Überladungen: einen, der die Ressource annimmt, und den Richtlinien Namen und den anderen, der die Ressource annimmt, und eine Liste der auszuwertenden Anforderungen.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

Im folgenden Beispiel wird die zu sichernden Ressource in ein benutzerdefiniertes- `Document` Objekt geladen. Eine `AuthorizeAsync` Überladung wird aufgerufen, um zu bestimmen, ob der aktuelle Benutzer das angegebene Dokument bearbeiten darf. Eine benutzerdefinierte editpolicy-Autorisierungs Richtlinie wird in die Entscheidung einbezogen. Weitere Informationen zum Erstellen von Autorisierungs Richtlinien finden Sie unter [benutzerdefinierte Richtlinien basierte Autorisierung](xref:security/authorization/policies) .

> [!NOTE]
> In den folgenden Codebeispielen wird vorausgesetzt, dass die-Authentifizierung ausgeführt wurde und die- `User` Eigenschaft

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Schreiben eines ressourcenbasierten Handlers

Das Schreiben eines Handlers für die ressourcenbasierte Autorisierung unterscheidet sich nicht wesentlich vom [Schreiben eines einfachen Anforderungs Handlers](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Erstellen Sie eine benutzerdefinierte Anforderungs Klasse, und implementieren Sie eine Anforderungs Handler-Klasse. Weitere Informationen zum Erstellen einer Anforderungs Klasse finden Sie unter [Anforderungen](xref:security/authorization/policies#requirements).

Die Handlerklasse gibt sowohl die Anforderung als auch den Ressourcentyp an. Ein Handler, der eine `SameAuthorRequirement` -und eine-Ressource nutzt, folgt z. b. `Document` :

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

Stellen Sie sich vor, dass im vorherigen Beispiel `SameAuthorRequirement` ein Sonderfall einer generischen- `SpecificAuthorRequirement` Klasse ist. Die- `SpecificAuthorRequirement` Klasse (nicht angezeigt) enthält eine `Name` Eigenschaft, die den Namen des Autors darstellt. Die- `Name` Eigenschaft kann auf den aktuellen Benutzer festgelegt werden.

Registrieren Sie die Anforderung und den Handler in `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=4-8,10)]
::: moniker-end

 ::: moniker range=">= aspnetcore-2.0 < aspnetcore-3.0"
[!code-csharp[](resourcebased/samples/2_2/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

::: moniker range="<= aspnetcore-1.1"
[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]
::: moniker-end

### <a name="operational-requirements"></a>Betriebsanforderungen

Wenn Sie Entscheidungen auf der Grundlage der Ergebnisse von CRUD-Vorgängen (erstellen, lesen, aktualisieren, löschen) treffen, verwenden Sie die Hilfsklasse [operationauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) . Diese Klasse ermöglicht das Schreiben eines einzelnen Handlers anstelle einer einzelnen Klasse für jeden Vorgangstyp. Um es zu verwenden, geben Sie einige Vorgangs Namen an:

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Der Handler wird wie folgt implementiert, wobei eine `OperationAuthorizationRequirement` Anforderung und eine `Document` Ressource verwendet werden:

 ::: moniker range=">= aspnetcore-2.0"
[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Der vorherige Handler überprüft den Vorgang mit der Ressource, der Identität des Benutzers und der-Eigenschaft der Anforderung `Name` .

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a>Herausforderungen und verbieten mit einem funktionstüchtigen Ressourcen Handler

In diesem Abschnitt wird gezeigt, wie die Ergebnisse "Challenge" und "verboten" verarbeitet werden und wie sich die Herausforderung und die unter

Um einen Ressourcen Handler für operative Ressourcen aufzurufen, geben Sie den Vorgang an, wenn `AuthorizeAsync` Sie in Ihrem Seiten Handler oder einer Aktion aufrufen. Im folgenden Beispiel wird ermittelt, ob der authentifizierte Benutzer das angegebene Dokument anzeigen darf.

> [!NOTE]
> In den folgenden Codebeispielen wird vorausgesetzt, dass die-Authentifizierung ausgeführt wurde und die- `User` Eigenschaft

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/3_0/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Wenn die Autorisierung erfolgreich ist, wird die Seite zum Anzeigen des Dokuments zurückgegeben. Wenn die Autorisierung fehlschlägt, der Benutzer aber authentifiziert ist, wird bei der Rückgabe der `ForbidResult` Authentifizierungs Middleware, bei der die Autorisierung `ChallengeResult`Wenn eine Authentifizierung durchgeführt werden muss, wird eine zurückgegeben. Bei interaktiven Browser Clients kann es sinnvoll sein, den Benutzer an eine Anmeldeseite umzuleiten.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/1_1/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Wenn die Autorisierung erfolgreich ist, wird die Sicht für das Dokument zurückgegeben. Wenn die Autorisierung fehlschlägt, wird bei der Rückgabe `ChallengeResult` von alle Authentifizierungs Middleware benachrichtigt, dass die Autorisierung fehlgeschlagen ist, und die Middleware kann die entsprechende Eine entsprechende Antwort könnte einen 401-oder 403-Statuscode zurückgeben. Bei interaktiven Browser Clients könnte dies bedeuten, dass der Benutzer an eine Anmeldeseite umgeleitet wird.

::: moniker-end
