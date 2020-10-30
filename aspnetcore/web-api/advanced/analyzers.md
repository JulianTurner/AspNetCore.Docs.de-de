---
title: Verwenden von Analysetools für Web-APIs
author: pranavkm
description: Erfahren Sie mehr über das ASP.NET Core MVC-Web-API-Analysetool-Paket.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
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
uid: web-api/advanced/analyzers
ms.openlocfilehash: cf0415e7d72e21a48db8bbeb4540f05e0b0a4198
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057920"
---
# <a name="use-web-api-analyzers"></a>Verwenden von Analysetools für Web-APIs

ASP.NET Core 2.2 und höher bietet ein MVC-Analysetool-Paket zur Verwendung mit Web-API-Projekten. Die Analysetools arbeiten mit Controllern mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse zusammen und basieren auf [Web-API-Konventionen](xref:web-api/advanced/conventions).

Das Analysetoolpaket unterrichtet Sie über alle Controlleraktionen, auf die Folgendes zutrifft:

* Rückgabe eines nicht deklarierten Statuscodes.
* Rückgabe eines nicht deklarierten Erfolgsergebnisses.
* Dokumentation eines Statuscodes, der nicht zurückgegeben wird.
* Eine explizite Modellüberprüfung ist enthalten.

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a>Verweisen auf das Analysepaket

In ASP.NET Core 3.0 oder höher sind die Analysetools im .NET Core SDK enthalten. Um das Analysetool im Projekt zu aktivieren, schließen Sie die `IncludeOpenAPIAnalyzers`-Eigenschaft in die Projektdatei ein:

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a>Paketinstallation

Installieren Sie das [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers)-NuGet-Paket mit einem der folgenden Ansätze:

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aus dem Fenster **Paket-Manager-Konsole** :
  * Wechseln Sie **View** zu > **Weitere Windows** - > **Paket-Manager-Konsole** anzeigen.
  * Navigieren Sie zu dem Verzeichnis, in dem die *ApiConventions.csproj* -Datei gespeichert ist.
  * Führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* in **Lösungspad** > **Pakete hinzufügen...** .
* Legen Sie die Dropdown- **Datei des Fensters** " **Pakete hinzufügen** " auf "nuget.org" fest.
* Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.
* Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ über den Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie den folgenden Befehl aus:

```dotnetcli
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a>Analysetools für Web-API-Konventionen

OpenAPI-Dokumente enthalten Statuscodes und Antworttypen die von einer Aktion zurückgegeben werden können. In ASP.NET Core MVC werden Attribute wie <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> und <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> verwendet, um eine Aktion zu dokumentieren. Weitere Informationen zum Dokumentieren Ihrer Web-API finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.

Eins der Analysetools in dem Paket untersucht Controller mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse und erkennt Aktionen, deren Antworten nicht vollständig dokumentiert werden. Betrachten Sie das folgenden Beispiel:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

Diese Aktion dokumentiert zwar den HTTP 200-Rückgabetyp „Erfolg“, aber nicht den HTTP 404-Statuscode „Fehler“. Das Analysetool meldet die fehlende Dokumentation für den HTTP 404-Statuscode als Warnung. Es gibt eine Möglichkeit, dieses Problem zu beheben.

![Analysetool mit Warnung](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
