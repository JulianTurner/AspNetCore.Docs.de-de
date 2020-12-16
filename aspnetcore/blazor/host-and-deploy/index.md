---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: a23bee120611ee603305a88dabac76566481fa4a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485887"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a>Hosten und Bereitstellen von ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.
1. Wählen Sie das *Veröffentlichungsziel* aus. Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.
1. Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an. Wählen Sie die Schaltfläche **`Publish`** aus.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie auf **Erstellen** > **In Ordner veröffentlichen**.
1. Bestätigen Sie den Ordner, in dem die veröffentlichten Ressourcen gespeichert werden, und wählen Sie **`Publish`** aus.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Verwenden Sie den Befehl [`dotnet publish`](/dotnet/core/tools/dotnet-publish), um die App mit einer Releasekonfiguration zu veröffentlichen:

```dotnetcli
dotnet publish -c Release
```

---

Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.

Veröffentlichungsspeicherorte:

* Blazor WebAssembly
  * Eigenständig: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht. Zum Bereitstellen der App als statische Website kopieren Sie den Inhalt des Ordners `wwwroot` auf den Host der statischen Website.
  * Gehostet: Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht. Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.
* Blazor Server: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` veröffentlicht. Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.

Die Objekte im Ordner werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="app-base-path"></a>Basispfad einer App

Der *Basispfad einer App* beschreibt den URL-Stammpfad der App. Betrachten Sie die folgende ASP.NET Core-App und die untergeordnete Blazor-App:

* Die ASP.NET Core-App heißt `MyApp`:
  * Die App befindet sich physisch unter `d:/MyApp`.
  * Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.
* Eine Blazor-App mit dem Namen `CoolApp` ist die untergeordnete App von `MyApp`:
  * Die untergeordnete App befindet sich physisch unter `d:/MyApp/CoolApp`.
  * Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.

Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet. Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.

Um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben, wird das `href`-Attribut des `<base>`-Tags auf den relativen Stammpfad in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) festgelegt.

Blazor WebAssembly (`wwwroot/index.html`):

```html
<base href="/CoolApp/">
```

Blazor Server (`Pages/_Host.cshtml`):

```html
<base href="~/CoolApp/">
```

Blazor Server-Apps legen zusätzlich den serverseitigen Basispfad fest, indem sie <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in der Anforderungspipeline `Startup.Configure` der App aufrufen:

```csharp
app.UsePathBase("/CoolApp");
```

Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen. Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen. Ferner wird der Basispfad der App verwendet, um ausgewählte Hyperlinks abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet. Der Router Blazor verarbeitet interne Navigation.

Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App. In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps. Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers der App festgelegt werden.

Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei `Pages/_Host.cshtml` (Blazor Server) oder der Datei `wwwroot/index.html` (Blazor WebAssembly), um den Basispfad der App festzulegen. Legen Sie den Wert des `href`-Attributs auf `/{RELATIVE URL PATH}/` (Blazor WebAssembly) oder `~/{RELATIVE URL PATH}/` (Blazor Server) fest. **Der nachstehende Schrägstrich ist erforderlich.** Der Platzhalter `{RELATIVE URL PATH}` ist der vollständige relative URL-Pfad der App.

Bei einer Blazor WebAssembly-App mit einem relativen URL-Pfad, der nicht zum Stammverzeichnis führt (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*. Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht. **Fügen Sie keinen nachgestellten Schrägstrich ein.** Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Für eine Blazor WebAssembly-App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Die Blazor WebAssembly-App antwortet lokal unter `http://localhost:port/CoolApp`.

**Blazor Server `MapFallbackToPage`-Konfiguration**

Übergeben Sie den folgenden Pfad zu <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

Der Platzhalter `{RELATIVE PATH}` repräsentiert den Pfad auf dem Server (nicht der Stamm). Beispielsweise lautet das Platzhaltersegment `CoolApp`, wenn die URL (nicht der Stamm) zur App `https://{HOST}:{PORT}/CoolApp/` lautet:

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

**Hosten mehrerer Blazor WebAssembly-Apps**

Weitere Informationen zum Hosten mehrerer Blazor WebAssembly-Apps in einer gehosteten Blazor-Lösung finden Sie unter <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.

## <a name="deployment"></a>Bereitstellung

Anleitungen für die Bereitstellung finden Sie in folgenden Themen:

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
