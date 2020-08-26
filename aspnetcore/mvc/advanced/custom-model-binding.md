---
title: Anpassen von Modellbindungen in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr darüber, wie Controlleraktionen durch Modellbindungen direkt mit den Modelltypen in ASP.NET Core arbeiten.
ms.author: riande
ms.date: 01/06/2020
no-loc:
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
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 7e1151b04f39f637f889e690bccc97eb6f0c91ea
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865575"
---
# <a name="custom-model-binding-in-aspnet-core"></a>Anpassen von Modellbindungen in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Steve Smith](https://ardalis.com/) und [Kirk Larkin](https://twitter.com/serpent5)

Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten. Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt. Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Standardmodellbindungseinschränkungen

Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler. Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können. Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können. Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann. Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.

## <a name="model-binding-review"></a>Übersicht: Modellbindung

Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet. Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert. Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert. Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts. Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.

Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden. Betrachten Sie <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren. Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.

### <a name="working-with-the-bytearraymodelbinder"></a>Arbeiten mit ByteArrayModelBinder

Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen. Beispielsweise kann ein Bild als Zeichenfolge codiert werden: Das Beispiel enthält ein Bild als Base64-codierte Zeichenfolge in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/3.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren. Der <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> ordnet `byte[]` Argumente `ByteArrayModelBinder` zu:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        var loggerFactory = context.Services.GetRequiredService<ILoggerFactory>();
        return new ByteArrayModelBinder(loggerFactory);
    }

    return null;
}
```

Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> verwenden.

In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_Post)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:

![Postman](custom-model-binding/images/postman.png "Postman")

Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich. Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/ImageController.cs?name=snippet_SaveProfile&highlight=2)]

## <a name="custom-model-binder-sample"></a>Beispiel für eine benutzerdefinierte Modellbindung

In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:

- Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente
- Abrufen der verknüpften Entität mit Entity Framework Core
- Übergeben der verknüpften Entität als Argument an die Aktionsmethode

In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.

Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=snippet_Class)]

> [!NOTE]
> Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen. Die Klasse stellt keine bewährte Methode für ein Suchszenario dar. Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab. Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.

Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_Get&highlight=2)]

Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=snippet_GetById&highlight=2)]

Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben. Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht. Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben. Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.

Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.

### <a name="implementing-a-modelbinderprovider"></a>Implementieren von ModelBinderProvider

Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren. So werden die integrierten Frameworkbindungen implementiert. Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert. Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`. Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Beachten Sie: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück. `BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI). `AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann. Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.

Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:

[!code-csharp[](custom-model-binding/samples/3.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen. Der erste Anbieter, der einen Binder zurückgibt, der mit dem Eingabe Modell übereinstimmt, wird verwendet. Wenn Sie Ihren Anbieter am Ende der Sammlung hinzufügen, kann dies dazu führen, dass ein integrierter Modell Binder aufgerufen wird, bevor Ihr benutzerdefinierter Binder eine Chance hat. In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er immer für `Author` Aktions Argumente verwendet wird.

### <a name="polymorphic-model-binding"></a>Polymorphe Modellbindung

Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet. Eine benutzerdefinierte polymorphe Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss. Polymorphe Modellbindung:

* Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.
* Erschwert es, Informationen über die gebundenen Modelle zu bekommen.

Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Empfehlungen und bewährte Methoden

Benutzerdefinierte Modellbindungen:

- Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden). Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.
- Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.
- Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. <xref:System.ComponentModel.TypeConverter> ist oft eine sinnvollere Option.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Von [Steve Smith](https://ardalis.com/)

Durch die Modellbindung können Controlleraktionen direkt mit Modelltypen (als Methodenargumente übergeben) statt mit HTTP-Anforderungen arbeiten. Das Zuordnen von Anforderungsdaten zu Anwendungsmodellen wird von Modellbindungen durchgeführt. Entwickler können die integrierten Modellbindungsfunktionen erweitern, indem Sie benutzerdefinierte Modellbindungen implementieren (obwohl Sie normalerweise nicht Ihren eigenen Anbieter schreiben müssen).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="default-model-binder-limitations"></a>Standardmodellbindungseinschränkungen

Die Standardmodellbindungen unterstützen die meisten gängigen .NET Core-Datentypen und erfüllen die Bedürfnisse der meisten Entwickler. Sie erwarten, dass sie textbasierte Eingaben aus der Anforderung direkt an den Modelltyp binden können. Möglicherweise müssen Sie die Eingabe umwandeln, bevor Sie sie binden können. Dies ist z.B. der Fall, wenn Sie über einen Schlüssel verfügen, der zum Suchen von Modelldaten verwendet werden kann. Sie können eine benutzerdefinierte Modellbindung verwenden, um Daten auf Basis des Schlüssel abzurufen.

## <a name="model-binding-review"></a>Übersicht: Modellbindung

Die Modellbindung verwendet spezifische Definitionen für die Typen, die sie verwendet. Ein *einfacher Typ* wird aus einer einzigen Zeichenfolge in der Eingabe konvertiert. Ein *komplexer Typ* wird aus mehreren Eingabewerten konvertiert. Das Framework bestimmt den Unterschied auf Grundlage des Vorhandenseins eines `TypeConverter`-Objekts. Es wird empfohlen, dass Sie einen Typkonverter erstellen, wenn Sie über eine einfache `string` -> `SomeType`-Zuordnung verfügen, die keine externen Ressourcen erfordert.

Bevor Sie Ihre eigene benutzerdefinierte Modellbindung erstellen, ist es sinnvoll, sich vor Augen zu führen, wie vorhandene Modellbindungen implementiert werden. Betrachten Sie <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinder>, das verwendet werden kann, um Base64-codierte Zeichenfolgen in Bytearrays zu konvertieren. Bytearrays werden häufig als Dateien oder Datenbank-BLOB-Felder gespeichert.

### <a name="working-with-the-bytearraymodelbinder"></a>Arbeiten mit ByteArrayModelBinder

Base64-codierte Zeichenfolgen können verwendet werden, um Binärdaten darzustellen. Beispielsweise kann ein Bild als Zeichenfolge codiert werden: Das Beispiel enthält ein Bild als Base64-codierte Zeichenfolge in [Base64String.txt](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/samples/2.x/CustomModelBindingSample/Base64String.txt).

ASP.NET Core MVC kann eine Base64-codierte Zeichenfolge mit `ByteArrayModelBinder` in ein Bytearray konvertieren. Der <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Binders.ByteArrayModelBinderProvider> ordnet `byte[]` Argumente `ByteArrayModelBinder` zu:

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

Wenn Sie Ihre eigenen benutzerdefinierte Modellbindung erstellen, können Sie Ihren eigenen `IModelBinderProvider`-Typ implementieren oder <xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> verwenden.

In folgendem Beispiel wird veranschaulicht, wie Sie mit `ByteArrayModelBinder` eine Base64-codierte Zeichenfolge in `byte[]` konvertieren und in einer Datei speichern können:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post1)]

Sie können eine Base64-codierte Zeichenfolge in diese API-Methode posten, indem Sie ein Tool wie [Postman](https://www.getpostman.com/) verwenden:

![Postman](custom-model-binding/images/postman.png "Postman")

Solange die Bindung Anforderungsdaten an entsprechend benannte Eigenschaften oder Argumente binden kann, ist die Modellbindung erfolgreich. Im folgenden Beispiel wird gezeigt, wie `ByteArrayModelBinder` mit einem Ansichtsmodell verwendet wird:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a>Beispiel für eine benutzerdefinierte Modellbindung

In diesem Abschnitt implementieren wir eine benutzerdefinierte Modellbindung, die folgende Aktionen durchführen kann:

- Konvertieren eingehender Anforderungsdaten in stark typisierte Schlüsselargumente
- Abrufen der verknüpften Entität mit Entity Framework Core
- Übergeben der verknüpften Entität als Argument an die Aktionsmethode

In folgendem Beispiel wird das `ModelBinder`-Attribut auf das `Author`-Modell angewendet:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Data/Author.cs?highlight=6)]

Im oben stehenden Beispiel gibt das `ModelBinder`-Attribut den Typ von `IModelBinder` an, der zur Bindung von `Author`-Aktionsparametern verwendet werden soll.

Die folgende `AuthorEntityBinder`-Klasse bindet `Author`-Parameter durch das Abrufen einer Entität aus der Datenquelle mit Entity Framework Core und einer `authorId`:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> Diese `AuthorEntityBinder`-Klasse soll eine benutzerdefinierte Modellbindung darstellen. Die Klasse stellt keine bewährte Methode für ein Suchszenario dar. Binden Sie die `authorId` für Suchvorgänge, und fragen Sie die Datenbank in einer Aktionsmethode ab. Dadurch werden Fehler bei der Modellbindung von `NotFound`-Fällen unterschieden.

Im folgenden Code wird die Verwendung von `AuthorEntityBinder` in einer Aktionsmethode veranschaulicht:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

Das `ModelBinder`-Attribut kann verwendet werden, um `AuthorEntityBinder` auf Parameter anzuwenden, die nicht die Standardkonventionen verwenden:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

Da der Name des Arguments nicht dem Standard (`authorId`) entspricht, wird er in diesem Beispiel im Parameter mit dem Attribut `ModelBinder` angegeben. Sowohl der Controller als auch die Aktionsmethode ist verglichen mit der Suche nach der Entität in der Aktionsmethode vereinfacht. Die Logik zum Abrufen des Autors mit Entity Framework Core wird in die Modellbindung verschoben. Dies kann zu einer deutlichen Vereinfachung führen, wenn Sie über mehrere Methoden verfügen, die eine Bindung an das `Author`-Modell durchführen.

Sie können das Attribut `ModelBinder` auf einzelne Modelleigenschaften (z.B. ViewModel) oder auf Aktionsmethodenparameter anwenden, um eine bestimmte Modellbindung oder einen bestimmten Modellnamen für genau diesen Typ oder genau diese Aktion anzugeben.

### <a name="implementing-a-modelbinderprovider"></a>Implementieren von ModelBinderProvider

Statt ein Attribut anzuwenden, können Sie auch `IModelBinderProvider` implementieren. So werden die integrierten Frameworkbindungen implementiert. Wenn Sie den Typ angeben, den Ihre Bindung verwendet, geben Sie gleichzeitig auch den Typ der Argumente an, den sie erzeugt, und **nicht** die Eingabe, die Ihre Bindung akzeptiert. Der folgende Bindungsanbieter funktioniert mit `AuthorEntityBinder`. Wenn er der Anbietersammlung von MVC hinzugefügt wird, müssen Sie das Attribut `ModelBinder` nicht für `Author` oder Parameter des Typs `Author` verwenden.

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> Beachten Sie: Der oben stehende Code gibt ein `BinderTypeModelBinder`-Objekt zurück. `BinderTypeModelBinder` fungiert als Factory für Modellbindungen und ermöglicht Dependency Injection (DI). `AuthorEntityBinder` erfordert, das DI auf Entity Framework Core zugreifen kann. Verwenden Sie `BinderTypeModelBinder`, wenn Ihre Modellbindung Dienste von DI erfordert.

Fügen Sie einen benutzerdefinierten Modellbindungsanbieter in `ConfigureServices` hinzu, um ihn verwenden zu können:

[!code-csharp[](custom-model-binding/samples/2.x/CustomModelBindingSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

Beim Überprüfen von Modellbindungen wird die Anbieterauflistung von oben nach unten durchlaufen. Der erste Anbieter, der eine Bindung zurückgibt, wird verwendet. Wenn Sie Ihren Anbieter am Ende der Auflistung hinzufügen, kann es passieren, dass ein integrierter Modellbindung aufgerufen wird, bevor Ihre benutzerdefinierte Bindung an die Reihe kommt. In diesem Beispiel wird der benutzerdefinierte Anbieter am Anfang der Auflistung hinzugefügt, um sicherzustellen, dass er auch tatsächlich für `Author`-Aktionsargumente verwendet wird.

### <a name="polymorphic-model-binding"></a>Polymorphe Modellbindung

Das Binden an verschiedene Modelle abgeleiteter Typen wird als polymorphe Modellbindung bezeichnet. Eine benutzerdefinierte polymorphe Modellbindung ist erforderlich, wenn der Anforderungswert an den spezifischen abgeleiteten Modelltyp gebunden werden muss. Polymorphe Modellbindung:

* Ist nicht typisch für eine Rest-API, die für die Interoperabilität mit allen Sprachen konzipiert ist.
* Erschwert es, Informationen über die gebundenen Modelle zu bekommen.

Wenn eine App jedoch eine polymorphe Modellbindung erfordert, könnte eine Implementierung wie der folgende Code aussehen:

[!code-csharp[](custom-model-binding/samples/3.x/PolymorphicModelBindingSample/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a>Empfehlungen und bewährte Methoden

Benutzerdefinierte Modellbindungen:

- Sollten nicht versuchen, Statuscodes festzulegen oder Ergebnisse zurückzugeben (z.B. 404 – Nicht gefunden). Wenn die Modellbindung fehlschlägt, sollte ein [Aktionsfilter](xref:mvc/controllers/filters) oder Logik innerhalb der Aktionsmethode selbst den Fehler behandeln.
- Sind besonders beim Eliminieren von wiederholendem Code und übergreifenden Belangen aus Aktionsmethoden nützlich.
- Sollten normalerweise nicht dazu verwendet werden, eine Zeichenfolge in einen benutzerdefinierten Typ zu konvertieren. <xref:System.ComponentModel.TypeConverter> ist oft eine sinnvollere Option.

::: moniker-end
