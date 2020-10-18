---
title: Anpassen des Verhaltens von authorizationmiddleware
author: pranavkm
ms.author: prkrishn
description: In diesem Artikel wird erläutert, wie die Ergebnis Behandlung von authorizationmiddleware angepasst wird.
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156767"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="38da0-103">Anpassen des Verhaltens von authorizationmiddleware</span><span class="sxs-lookup"><span data-stu-id="38da0-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="38da0-104">Anwendungen können einen registrieren `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` , um die Art und Weise anzupassen, in der die Middleware die Autorisierungs Ergebnisse behandelt.</span><span class="sxs-lookup"><span data-stu-id="38da0-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="38da0-105">Anwendungen können die angepasste Middleware für Folgendes verwenden:</span><span class="sxs-lookup"><span data-stu-id="38da0-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="38da0-106">Gibt angepasste Antworten zurück.</span><span class="sxs-lookup"><span data-stu-id="38da0-106">Return customized responses.</span></span>
* <span data-ttu-id="38da0-107">Verbessern Sie die Standard Abfrage, oder verbieten Sie Antworten.</span><span class="sxs-lookup"><span data-stu-id="38da0-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="38da0-108">Der folgende Code zeigt ein Beispiel für einen Autorisierungs Handler, der eine benutzerdefinierte Antwort für bestimmte Arten von Autorisierungs Fehlern zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="38da0-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="38da0-109">Registrieren `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="38da0-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->