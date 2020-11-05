---
title: Neuerungen in ASP.NET Core 3.1
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
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
uid: aspnetcore-3.1
ms.openlocfilehash: 15240df978d9389af08030521adaf491f81d3a77
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051173"
---
# <a name="whats-new-in-aspnet-core-31"></a>Neuerungen in ASP.NET Core 3.1

In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.1 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.

## <a name="partial-class-support-for-no-locrazor-components"></a>Unterstützung von partiellen Klassen für Razor-Komponenten

Razor-Komponenten werden nun als partielle Klassen generiert. Der Code für eine Razor-Komponente kann nun mithilfe einer CodeBehind-Datei geschrieben werden, die als eine partielle Klasse definiert ist, anstatt den gesamten Code für die Komponente in einer einzelnen Datei zu definieren. Weitere Informationen finden Sie unter [Unterstützung von partiellen Klassen](xref:blazor/components/index#partial-class-support).

## <a name="no-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor-Komponententaghilfsprogramm und Übergeben von Parametern an Komponenten der obersten Ebene

In Blazor mit ASP.NET Core 3.0 wurden Komponenten mithilfe eines HTML-Hilfsprogramms (`Html.RenderComponentAsync`) in Seiten und Ansichten gerendert. In ASP.NET Core 3.1 werden Komponenten mithilfe eines neuen Komponententaghilfsprogramms in Seiten oder Ansichten gerendert:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Das HTML-Hilfsprogramm wird in ASP.NET Core 3.1 weiterhin unterstützt, jedoch wird die Verwendung des Komponententaghilfsprogramms empfohlen.

Blazor Server-Apps können nun während dem ersten Rendering Parameter an Komponenten der obersten Ebene übergeben. Zuvor konnten Sie Parameter nur an Komponenten der obersten Ebene mit [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static) übergeben. Ab diesem Release werden sowohl [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) als auch [RenderMode.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) unterstützt. Alle festgelegten Parameterwerte werden als JSON serialisiert und sind in der ersten Antwort enthalten.

So können Sie eine `Counter`-Komponente beispielsweise mit einem Inkrement vorab rendern (`IncrementAmount`):

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

Weitere Informationen finden Sie unter [Integrieren von Komponenten in Razor-Seiten und MVC-Apps](xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps).

## <a name="support-for-shared-queues-in-httpsys"></a>Unterstützung für freigegebene Warteschlangen in HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) unterstützt das Erstellen anonymer Anforderungswarteschlangen. In ASP.NET Core 3.1 wurde die Möglichkeit hinzugefügt, eine benannte HTTP.sys-Anforderungswarteschlange zu erstellen oder Elemente an eine solche Warteschlange anzufügen. Das Erstellen einer benannten HTTP.sys-Anforderungswarteschlange bzw. das Anfügen von Elementen an eine solche ermöglicht Szenarien, in denen der HTTP.sys-Controllerprozess, der die Warteschlange besitzt, unabhängig vom Listenerprozess ist. Diese Unabhängigkeit ermöglicht die Beibehaltung vorhandener Verbindungen und in der Warteschlange eingereihter Anforderungen zwischen Neustarts des Listenerprozesses:

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-no-loccookies"></a>Breaking Changes für SameSite-cookies

Das Verhalten von SameSite-cookies wurde gemäß bevorstehender Browseränderungen geändert. Dies kann sich auf Authentifizierungsszenarios wie Azure AD, OpenIdConnect oder WsFederation auswirken. Weitere Informationen finden Sie unter <xref:security/samesite>.

## <a name="prevent-default-actions-for-events-in-no-locblazor-apps"></a>Verhindern von Standardaktionen für Ereignisse in Blazor-Apps

Verwenden Sie das Direktivenattribut `@on{EVENT}:preventDefault`, um die Standardaktion für ein Ereignis zu verhindern. Im folgenden Beispiel wird die Standardaktion verhindert, welche das Zeichen eines Schlüssels im Textfeld anzeigt:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

Weitere Informationen finden Sie unter [Verhindern von Standardaktionen](xref:blazor/components/event-handling#prevent-default-actions).

## <a name="stop-event-propagation-in-no-locblazor-apps"></a>Beenden der Ereignisweitergabe in Blazor-Apps

Verwenden Sie das Direktivenattribut `@on{EVENT}:stopPropagation`, um die Ereignisweitergabe zu beenden. Im folgenden Beispiel verhindert das Aktivieren des Kontrollkästchens die Weitergabe von Klickereignissen des untergeordneten `<div>`-Elements an das übergeordnete `<div>`-Element:

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

Weitere Informationen finden Sie unter [Beenden der Ereignisweitergabe](xref:blazor/components/event-handling#stop-event-propagation).

## <a name="detailed-errors-during-no-locblazor-app-development"></a>Ausführliche Fehler bei der Entwicklung von Blazor-Apps

Wenn eine Blazor-App während der Entwicklung nicht ordnungsgemäß funktioniert, erhalten Sie nun ausführliche Fehlerinformationen von der App, die Sie beim Beheben des Problems unterstützen. Wenn ein Fehler auftritt, zeigen Blazor-Apps eine goldene Leiste am unteren Rand der Anzeige an:

* Während der Entwicklung leitet die goldene Leiste Sie an die Browserkonsole weiter, in der die Ausnahme angezeigt wird.
* In der Produktion benachrichtigt die goldene Leiste den Benutzer darüber, dass ein Fehler aufgetreten ist, und empfiehlt eine Aktualisierung des Browsers.

Weitere Informationen finden Sie unter [Ausführliche Fehler während der Entwicklung](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development).
