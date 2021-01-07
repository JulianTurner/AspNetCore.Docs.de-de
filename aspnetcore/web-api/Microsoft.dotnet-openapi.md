---
title: Entwickeln von ASP.NET Core-Apps mit OpenAPI
author: ryanbrandenburg
description: Hier wird veranschaulicht, wie Sie das Tool „Microsoft.dotnet-openapi“ verwenden, um Verweise zu OpenAPI-Dateien hinzuzufügen.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972027"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>Entwickeln von ASP.NET Core-Apps mit OpenAPI-Tools

Von Ryan Brandenburg

[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) ist ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools) zum Verwalten von [OpenAPI](https://github.com/OAI/OpenAPI-Specification)-Verweisen innerhalb eines Projekts.

## <a name="installation"></a>Installation

Führen Sie den folgenden Befehl aus, um `Microsoft.dotnet-openapi` zu installieren:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>Hinzufügen

Wenn Sie mit einem der Befehle auf dieser Seite einen OpenAPI-Verweis hinzufügen, wird ein `<OpenApiReference />`-Element ähnlich dem folgenden zur *CSPROJ*-Datei hinzugefügt:

```xml
<OpenApiReference Include="openapi.json" />
```

Der oben genannte Verweis ist erforderlich, damit die App den generierten Clientcode aufrufen kann.

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a>Datei hinzufügen

#### <a name="options"></a>Optionen

| Kurze Option| Lange Option| Beschreibung | Beispiel |
|-------|------|-------|---------|
| -p|--updateProject | Das Projekt, das bearbeitet werden soll. |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--code-generator| Der Codegenerator, der auf den Verweis angewendet werden soll. Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`. Wenn `--code-generator` nicht angegeben ist, werden standardmäßig `NSwagCSharp`-Tools verwendet.|dotnet openapi add file .\OpenApi.json --code-generator
| -h|--help|Zeigt Hilfeinformationen an.|dotnet openapi add file --help|

#### <a name="arguments"></a>Argumente

|  Argument  | BESCHREIBUNG | Beispiel |
|-------------|-------------|---------|
| source-file | Die Quelle, aus der ein Verweis erstellt werden soll. Es muss sich um eine OpenAPI-Datei handeln. |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>URL hinzufügen

#### <a name="options"></a>Optionen

| Kurze Option| Lange Option| Beschreibung | Beispiel |
|-------|------|-------------|---------|
| -p|--updateProject | Das Projekt, das bearbeitet werden soll. |dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -o|--output-file | Speicherort für die lokale Kopie der OpenAPI-Datei. |dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--code-generator| Der Codegenerator, der auf den Verweis angewendet werden soll. Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`. |DotNet OpenAPI Add URL `https://contoso.com/openapi.json` --Code-Generator
| -h|--help|Zeigt Hilfeinformationen an.|dotnet openapi add url --help|

#### <a name="arguments"></a>Argumente

|  Argument  | BESCHREIBUNG | Beispiel |
|-------------|-------------|---------|
| source-URL | Die Quelle, aus der ein Verweis erstellt werden soll. Es muss sich um eine URL handeln. |dotnet openapi add url `https://contoso.com/openapi.json` |

## <a name="remove"></a>Entfernen

Entfernt den OpenAPI-Verweis, der mit dem angegebenen Dateinamen übereinstimmt, aus der *CSPROJ*-Datei. Wenn der OpenAPI-Verweis entfernt wird, werden keine Clients generiert. Lokale *JSON*- und *YAML*-Dateien werden gelöscht.

### <a name="options"></a>Optionen

| Kurze Option| Lange Option| Beschreibung| Beispiel |
|-------|------|------------|---------|
| -p|--updateProject | Das Projekt, das bearbeitet werden soll. |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -h|--help|Zeigt Hilfeinformationen an.|dotnet openapi remove --help|

### <a name="arguments"></a>Argumente

|  Argument  | BESCHREIBUNG| Beispiel |
| ------------|------------|---------|
| source-file | Die Quelle, aus der der Verweis entfernt werden soll. |dotnet openapi remove *.\OpenAPI.json* |

## <a name="refresh"></a>Aktualisieren

Aktualisiert die lokale Version einer Datei, die unter Verwendung der neuesten Download-URL heruntergeladen wurde.

### <a name="options"></a>Optionen

| Kurze Option| Lange Option| Beschreibung | Beispiel |
|-------|------|-------------|---------|
| -p|--updateProject | Das Projekt, das bearbeitet werden soll. | dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json` |
| -h|--help|Zeigt Hilfeinformationen an.|dotnet openapi refresh --help|

### <a name="arguments"></a>Argumente

|  Argument  | BESCHREIBUNG | Beispiel |
| ------------|-------------|---------|
| source-URL | Die URL, aus der der Verweis aktualisiert werden soll. | dotnet openapi refresh `https://contoso.com/openapi.json` |
