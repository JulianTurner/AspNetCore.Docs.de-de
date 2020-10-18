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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>Anpassen des Verhaltens von authorizationmiddleware

Anwendungen können einen registrieren `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` , um die Art und Weise anzupassen, in der die Middleware die Autorisierungs Ergebnisse behandelt. Anwendungen können die angepasste Middleware für Folgendes verwenden:

* Gibt angepasste Antworten zurück.
* Verbessern Sie die Standard Abfrage, oder verbieten Sie Antworten.

Der folgende Code zeigt ein Beispiel für einen Autorisierungs Handler, der eine benutzerdefinierte Antwort für bestimmte Arten von Autorisierungs Fehlern zurückgibt:

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

Registrieren `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices` :

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->