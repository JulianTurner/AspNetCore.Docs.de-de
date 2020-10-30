---
title: JsonPatch in ASP.NET Core-Web-API
author: rick-anderson
description: Erfahren Sie, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: da507974b88c21de22e2c7a56950943207565138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060546"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch in ASP.NET Core-Web-API

Von [Tom Dykstra](https://github.com/tdykstra) und [Kirk Larkin](https://github.com/serpent5)

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.

## <a name="package-installation"></a>Paketinstallation

Führen Sie die folgenden Schritte aus, um die JSON-Patchunterstützung in der APP zu aktivieren:

1. Installieren Sie das [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) nuget-Paket.
1. Aktualisieren Sie die- `Startup.ConfigureServices` Methode des Projekts, um aufzurufen <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> . Zum Beispiel:

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

`AddNewtonsoftJson` ist mit den folgenden MVC-Dienstregistrierungsmethoden kompatibel:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a>JSON Patch, addnewtonweichjson und System.Text.Js

`AddNewtonsoftJson` ersetzt die `System.Text.Json` -basierten Eingabe-und Ausgabe Formatierer, die zum Formatieren des **gesamten** JSON-Inhalts verwendet werden. Um Unterstützung für JSON-Patch mithilfe von hinzuzufügen und `Newtonsoft.Json` die anderen Formatierer unverändert zu lassen, aktualisieren Sie die-Methode des Projekts `Startup.ConfigureServices` wie folgt:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

Der vorangehende Code erfordert das `Microsoft.AspNetCore.Mvc.NewtonsoftJson` -Paket und die folgenden- `using` Anweisungen:

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a>PATCH HTTP-Anforderungsmethode

Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren. Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.

## <a name="json-patch"></a>JSON Patch

Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen. Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen* . Jeder Vorgang identifiziert eine bestimmte Art von Änderung. Beispiele für solche Änderungen sind das Hinzufügen eines Array Elements oder das Ersetzen eines Eigenschafts Werts.

Die folgenden JSON-Dokumente stellen z. b. eine Ressource, ein JSON-Patch-Dokument für die Ressource und das Ergebnis der Anwendung der patchvorgänge dar.

### <a name="resource-example"></a>Ressourcenbeispiel

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON Patch-Beispiel

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Für den oben stehenden JSON-Code gilt:

* Die `op`-Eigenschaft gibt den Typ des Vorgangs an.
* Die `path`-Eigenschaft gibt das zu aktualisierende Element an.
* Die `value`-Eigenschaft stellt den neuen Wert bereit.

### <a name="resource-after-patch"></a>Ressource nach dem Patch

So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Die Änderungen, die durch Anwenden eines JSON-Patch-Dokuments auf eine Ressource vorgenommen werden, sind atomarisch. Wenn ein Vorgang in der Liste fehlschlägt, wird kein Vorgang in der Liste angewendet.

## <a name="path-syntax"></a>Pfadsyntax

Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf. Beispiel: `"/address/zipCode"`.

Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben. Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`. Bis `add` zum Ende eines Arrays verwenden Sie einen Bindestrich ( `-` ) anstelle einer Indexnummer: `/addresses/-` .

### <a name="operations"></a>Operations

Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):

|Vorgang  | Hinweise |
|-----------|--------------------------------|
| `add`     | Hinzufügen einer Eigenschaft oder eines Arrayelements. Für vorhandene Eigenschaft: set value.|
| `remove`  | Entfernen einer Eigenschaft oder eines Arrayelements. |
| `replace` | Identisch mit `remove`, gefolgt von `add` an gleicher Stelle. |
| `move`    | Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle. |
| `copy`    | Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle. |
| `test`    | Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.|

## <a name="json-patch-in-aspnet-core"></a>JSON-Patch in ASP.net Core

Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.

## <a name="action-method-code"></a>Aktionsmethodencode

Eine Aktionsmethode für JSON Patch in einem API-Controller:

* Ist versehen mit dem `HttpPatch`-Attribut.
* Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.
* Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.

Hier sehen Sie ein Beispiel:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Dieser Code aus der Beispiel-APP funktioniert mit dem folgenden `Customer` Modell:

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Die Beispielaktionsmethode:

* Erstellt ein Objekt vom Typ `Customer`.
* Wendet den Patch an.
* Gibt das Ergebnis wird im Textkörper der Antwort zurück.

In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.

### <a name="model-state"></a>Modellstatus

Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert. Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen. Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamische Objekte

Im folgenden Aktionsmethoden Beispiel wird gezeigt, wie ein Patch auf ein dynamisches Objekt angewendet wird:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Add-Vorgang (Hinzufügen)

* Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.
* Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.
* Wenn `path` auf einen nicht vorhandenen Speicherort verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.

Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Remove-Vorgang (Entfernen)

* Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.
* Wenn `path` auf eine Eigenschaft verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:
    * Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.
    * Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.

Im folgenden Beispiel-Patch-Dokument wird `CustomerName` auf NULL festgelegt und löscht Folgendes `Orders[0]` :

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Replace-Vorgang (Ersetzen)

Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.

Im folgenden Beispiel-Patch-Dokument wird der Wert von festgelegt `CustomerName` und `Orders[0]` durch ein neues- `Order` Objekt ersetzt:

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Move-Vorgang (Verschieben)

* Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.
* Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.
* Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.
  * Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.

Das folgende Patch-Dokumentbeispiel:

* Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.
* Legt `Orders[0].OrderName` auf Null fest.
* Verschiebt `Orders[1]` vor `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Copy-Vorgang (Kopieren)

Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.

Das folgende Patch-Dokumentbeispiel:

* Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.
* Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test-Vorgang (Testen)

Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl. In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.

Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.

Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Abrufen des Codes

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))

Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP-Methode: `PATCH`
* Header: `Content-Type: application/json-patch+json`
* Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [IETF RFC 5789 PATCH-Methodenspezifikation](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch-Pfadformatspezifikation](https://tools.ietf.org/html/rfc6901)
* [JSON Patch-Dokumentation](https://jsonpatch.com/). Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.
* [ASP.NET Core JSON Patch-Quellcode](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.

## <a name="patch-http-request-method"></a>PATCH HTTP-Anforderungsmethode

Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren. Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.

## <a name="json-patch"></a>JSON Patch

Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen. Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen* . Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.

Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.

### <a name="resource-example"></a>Ressourcenbeispiel

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>JSON Patch-Beispiel

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

Für den oben stehenden JSON-Code gilt:

* Die `op`-Eigenschaft gibt den Typ des Vorgangs an.
* Die `path`-Eigenschaft gibt das zu aktualisierende Element an.
* Die `value`-Eigenschaft stellt den neuen Wert bereit.

### <a name="resource-after-patch"></a>Ressource nach dem Patch

So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.

## <a name="path-syntax"></a>Pfadsyntax

Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf. Beispiel: `"/address/zipCode"`.

Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben. Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`. Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.

### <a name="operations"></a>Operations

Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):

|Vorgang  | Hinweise |
|-----------|--------------------------------|
| `add`     | Hinzufügen einer Eigenschaft oder eines Arrayelements. Für vorhandene Eigenschaft: set value.|
| `remove`  | Entfernen einer Eigenschaft oder eines Arrayelements. |
| `replace` | Identisch mit `remove`, gefolgt von `add` an gleicher Stelle. |
| `move`    | Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle. |
| `copy`    | Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle. |
| `test`    | Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch in ASP.NET Core

Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt. Das Paket ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

## <a name="action-method-code"></a>Aktionsmethodencode

Eine Aktionsmethode für JSON Patch in einem API-Controller:

* Ist versehen mit dem `HttpPatch`-Attribut.
* Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.
* Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.

Hier sehen Sie ein Beispiel:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

Die Beispielaktionsmethode:

* Erstellt ein Objekt vom Typ `Customer`.
* Wendet den Patch an.
* Gibt das Ergebnis wird im Textkörper der Antwort zurück.

 In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.

### <a name="model-state"></a>Modellstatus

Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert. Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen. Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Dynamische Objekte

Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>Add-Vorgang (Hinzufügen)

* Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.
* Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.
* Wenn `path` auf einen nicht vorhandenen Speicherort verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.

Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>Remove-Vorgang (Entfernen)

* Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.
* Wenn `path` auf eine Eigenschaft verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:
    * Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.
    * Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.

Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>Replace-Vorgang (Ersetzen)

Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.

Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>Move-Vorgang (Verschieben)

* Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.
* Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.
* Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:
  * Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.
  * Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.

Das folgende Patch-Dokumentbeispiel:

* Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.
* Legt `Orders[0].OrderName` auf Null fest.
* Verschiebt `Orders[1]` vor `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>Copy-Vorgang (Kopieren)

Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.

Das folgende Patch-Dokumentbeispiel:

* Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.
* Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>Test-Vorgang (Testen)

Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl. In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.

Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.

Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Abrufen des Codes

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2) ([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))

Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* HTTP-Methode: `PATCH`
* Header: `Content-Type: application/json-patch+json`
* Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [IETF RFC 5789 PATCH-Methodenspezifikation](https://tools.ietf.org/html/rfc5789)
* [IETF RFC 6902 JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902)
* [IETF RFC 6901 JSON Patch-Pfadformatspezifikation](https://tools.ietf.org/html/rfc6901)
* [JSON Patch-Dokumentation](https://jsonpatch.com/). Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.
* [ASP.NET Core JSON Patch-Quellcode](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
