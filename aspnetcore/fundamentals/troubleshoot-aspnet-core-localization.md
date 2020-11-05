---
title: Problembehandlung bei der ASP.NET Core-Lokalisierung
author: hishamco
description: Erfahren Sie, wie Sie Probleme mit der Lokalisierung von ASP.NET Core-Apps diagnostizieren können.
ms.author: riande
ms.date: 01/24/2019
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
uid: fundamentals/troubleshoot-aspnet-core-localization
ms.openlocfilehash: 995db4c8c9d0c0f1f77b1fd3665e707975406a7f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053617"
---
# <a name="troubleshoot-aspnet-core-localization"></a>Problembehandlung bei der ASP.NET Core-Lokalisierung

Von [Hisham Bin Ateya](https://github.com/hishamco)

Dieser Artikel enthält Anweisungen für die Diagnose von Problemen mit der Lokalisierung von ASP.NET Core-Apps.

## <a name="localization-configuration-issues"></a>Probleme bei der Lokalisierungskonfiguration

**Reihenfolge der Lokalisierungsmiddleware**  
Die App kann möglicherweise nicht lokalisiert werden, da die Lokalisierungsmiddleware nicht in der erwarteten Reihenfolge angeordnet ist.

Um dieses Problem zu beheben, stellen Sie sicher, dass die Lokalisierungsmiddleware vor der MVC-Middleware registriert wurde. Andernfalls wird die Lokalisierungsmiddleware nicht angewendet.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization(options => options.ResourcesPath = "Resources");

    services.AddMvc();
}
```

**Pfad der Lokalisierungsmiddleware nicht gefunden**

**Unterstützte Kulturen im RequestCultureProvider entsprechen nicht der einmaligen Registrierung**  

## <a name="resource-file-naming-issues"></a>Probleme mit dem Ressourcendateinamen

ASP.NET Core verfügt über vordefinierte Regeln und Richtlinien für die Benennung von Lokalisierungsressourcendateien, die [hier](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) im Detail beschrieben werden.

## <a name="missing-resources"></a>Fehlende Ressourcen

Häufige Ursachen, warum Ressourcen nicht gefundenen werden, sind:

- Ressourcennamen sind entweder in der `resx`-Datei oder der Lokalisierungsanforderung falsch geschrieben.
- Die Ressource fehlt für einige Sprachen in `resx`, ist für andere aber vorhanden.
- Wenn weiterhin Probleme auftreten, überprüfen Sie die Protokollmeldungen der Lokalisierung (auf der `Debug`-Protokollebene) auf weitere Informationen zu den fehlenden Ressourcen.

_**Hinweis:** Bei Verwendung von `CookieRequestCultureProvider` vergewissern Sie sich, dass mit den Kulturen im Lokalisierungscookiewert keine einfachen Anführungszeichen verwendet werden. `c='en-UK'|uic='en-US'` ist beispielsweise ein ungültiger cookiewert, während `c=en-UK|uic=en-US` gültig ist._

## <a name="resources--class-libraries-issues"></a>Probleme mit Ressourcen und Klassenbibliotheken

ASP.NET Core stellt standardmäßig eine Methode zur Verfügung, Klassenbibliotheken zu ermöglichen, ihre Ressourcendateien über [ResourceLocationAttribute](/dotnet/api/microsoft.extensions.localization.resourcelocationattribute?view=aspnetcore-2.1) zu finden.

Folgende Probleme mit Klassenbibliotheken treten häufig auf:
- Das Fehlen von `ResourceLocationAttribute` in einer Klassenbibliothek verhindert, dass `ResourceManagerStringLocalizerFactory` die Ressourcen ermitteln kann.
- Ressourcendateinamen. Weitere Informationen finden Sie unter [Probleme mit dem Ressourcendateinamen](#resource-file-naming-issues).
- Ändern des Stammnamespace der Klassenbibliothek. Weitere Informationen finden Sie unter [Probleme mit dem Stammnamespace](#root-namespace-issues) Abschnitt.

## <a name="customrequestcultureprovider-doesnt-work-as-expected"></a>CustomRequestCultureProvider funktioniert nicht wie erwartet

Die `RequestLocalizationOptions`-Klasse verfügt über drei Standardanbieter:

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

Mit [CustomRequestCultureProvider](/dotnet/api/microsoft.aspnetcore.localization.customrequestcultureprovider?view=aspnetcore-2.1) können Sie anpassen, wie die Lokalisierungskultur in Ihrer App bereitgestellt wird. `CustomRequestCultureProvider` wird verwendet, wenn die Standardanbieter Ihren Anforderungen nicht entsprechen.

- Ein häufiger Grund dafür, dass ein benutzerdefinierter Anbieter nicht ordnungsgemäß funktioniert, besteht darin, dass er nicht der erste Anbieter in der `RequestCultureProviders`-Liste ist. So beheben Sie dieses Problem:

- Fügen Sie den benutzerdefinierten Anbieter wie folgt an der Position 0 in die `RequestCultureProviders` Liste ein:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
```
::: moniker-end

- Verwenden Sie die `AddInitialRequestCultureProvider`-Erweiterungsmethode, um den benutzerdefinierten Anbieter als ersten Anbieter festzulegen.

## <a name="root-namespace-issues"></a>Probleme mit dem Stammnamespace

Wenn der Stammnamespace einer Assembly sich vom Assemblynamen unterscheidet, funktioniert die Lokalisierung standardmäßig nicht. Um dieses Problem zu vermeiden, verwenden Sie [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1), das [hier](xref:fundamentals/localization?view=aspnetcore-2.2#resource-file-naming) im Detail beschrieben wird.

> [!WARNING]
> Dies kann vorkommen, wenn der Name eines Projekts kein gültiger .NET-Bezeichner ist. Beispielsweise verwendet `my-project-name.csproj` den Stammnamespace `my_project_name` und den Assemblynamen `my-project-name`, der zu diesem Fehler führt. 

## <a name="resources--build-action"></a>Ressourcen und Buildaktion

Wenn Sie Ressourcendateien für die Lokalisierung verwenden, ist es wichtig, dass sie über eine geeignete Buildaktion verfügen. Sie sollten eine **eingebettete Ressource** sein, andernfalls kann `ResourceStringLocalizer` diese Ressourcen nicht finden.
