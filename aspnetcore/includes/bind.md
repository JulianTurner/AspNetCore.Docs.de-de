---
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
ms.openlocfilehash: 30baab0649268f4abf0dbd6c99dfeef3f43d0054
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536293"
---
Die bevorzugte Methode für das Lesen zugehöriger Konfigurationswerte ist die Verwendung des [Optionsmusters](xref:fundamentals/configuration/options). Um z. B. die folgenden Konfigurationswerte zu lesen:

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

Erstellen Sie die folgende neue `PositionOptions`-Klasse:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Eine Optionsklasse:

* Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.
* Alle öffentlichen Lese-/Schreibeigenschaften des Typs sind gebunden.
* Felder werden ***nicht*** gebunden. Im vorangehenden Code ist `Position` nicht gebunden. Die `Position`-Eigenschaft wird verwendet, sodass die Zeichenfolge `"Position"` nicht in der App hartcodiert werden muss, wenn die Klasse an einen Konfigurationsanbieter gebunden wird.

Der folgende Code

* Ruft [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) auf, um die `PositionOptions`-Klasse an den `Position`-Abschnitt zu binden.
* Zeigt die `Position`-Konfigurationsdaten an.

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück. `ConfigurationBinder.Get<T>` ist möglicherweise praktischer als die Verwendung von `ConfigurationBinder.Bind`. Der folgende Code zeigt die Verwendung von `ConfigurationBinder.Get<T>` mit der `PositionOptions`-Klasse:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.

Eine alternative Vorgehensweise bei der Verwendung des **Optionsmusters** besteht darin, den `Position`-Abschnitt zu binden und ihn zum [Dependency-Injection-Dienstcontainer](xref:fundamentals/dependency-injection) hinzuzufügen. Im folgenden Code wird `PositionOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

Mithilfe des vorangehenden Codes liest der folgende Code die Positionsoptionen:

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

Im vorangehenden Code werden Änderungen an der JSON-Konfigurationsdatei nach dem Start der App ***nicht*** gelesen. Verwenden Sie [IOptionsSnapshot](xref:fundamentals/configuration/options#ios), um Änderungen lesen zu können, nachdem die App gestartet wurde.
