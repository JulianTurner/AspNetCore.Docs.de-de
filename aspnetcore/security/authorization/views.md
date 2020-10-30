---
title: Ansichts basierte Autorisierung in ASP.net Core MVC
author: rick-anderson
description: In diesem Dokument wird veranschaulicht, wie der Autorisierungs Dienst in einer ASP.net Core Ansicht eingefügt und verwendet wird Razor .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: b3d6e595aa08208f2bf9e95d7070cf9c24802b62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061326"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>Ansichts basierte Autorisierung in ASP.net Core MVC

Ein Entwickler möchte eine Benutzeroberfläche häufig auf der Grundlage der aktuellen Benutzeridentität anzeigen, ausblenden oder anderweitig ändern. Sie können über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)in MVC-Ansichten auf den Autorisierungs Dienst zugreifen. Um den Autorisierungs Dienst in eine Razor Ansicht einzufügen, verwenden Sie die- `@inject` Direktive:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

Wenn Sie den Autorisierungs Dienst in jeder Ansicht verwenden möchten, platzieren Sie die `@inject` Direktive in der Datei " *_ViewImports. cshtml* " des Verzeichnisses " *views* ". Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).

Verwenden Sie den injizierten Autorisierungs Dienst, um `AuthorizeAsync` genau die gleiche Weise aufzurufen, die Sie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)überprüfen würden:

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

In einigen Fällen ist die Ressource Ihr Ansichts Modell. Rufen `AuthorizeAsync` Sie auf genau dieselbe Weise auf wie bei der [ressourcenbasierten Autorisierung](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

Im vorangehenden Code wird das Modell als Ressource übermittelt, die von der Richtlinien Auswertung berücksichtigt werden sollte.

> [!WARNING]
> Verlassen Sie sich nicht auf das Umschalten der Sichtbarkeit der Benutzeroberflächen Elemente Ihrer APP als alleinige Autorisierungs Überprüfung. Wenn Sie ein UI-Element ausblenden, kann der Zugriff auf die zugeordnete Controller Aktion nicht vollständig verhindert werden Sehen Sie sich beispielsweise die Schaltfläche im vorangehenden Code Ausschnitt an. Ein Benutzer kann die `Edit` Aktionsmethode aufrufen, wenn er weiß, dass die relative Ressourcen-URL */Document/Edit/1* lautet. Aus diesem Grund sollte die `Edit` Aktionsmethode eine eigene Autorisierungs Überprüfung durchführen.
