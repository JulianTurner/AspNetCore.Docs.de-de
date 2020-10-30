---
title: Image-Taghilfsprogramm in ASP.NET Core
author: pkellner
description: Veranschaulicht die Arbeit mit dem Image-Taghilfsprogramm.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: edc6971ac42756504b5b371ba509d7a657a0c396
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061118"
---
# <a name="image-tag-helper-in-aspnet-core"></a>Image-Taghilfsprogramm in ASP.NET Core

Von [Peter Kellner](https://peterkellner.net)

Das Image-Taghilfsprogramm optimiert das `<img>`-Tag für die Bereitstellung des Cache-Busting-Verhaltens bei statischen Bilddateien.

Eine Cache-Busting-Zeichenfolge ist ein eindeutiger Wert, der den Hash der statischen Bilddatei darstellt, der an die Asset-URL angefügt ist. Die eindeutige Zeichenfolge fordert Clients (und einige Proxys) auf, das Bild erneut vom Hostwebserver zu laden, und nicht über den Cache des Clients.

Wenn es sich bei der Bildquelle (`src`) um eine statische Datei auf dem Hostwebserver handelt, gilt Folgendes:

* Eine eindeutige Cache-Busting-Zeichenfolge wird als Abfrageparameter an die Bildquelle angefügt.
* Eine eindeutige Anforderungs-URL wird erstellt, die den aktualisierten Anforderungsparameter enthält, wenn die Datei auf dem Hostwebserver geändert wird.

Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

## <a name="image-tag-helper-attributes"></a>Attribute von Image-Taghilfsprogrammen

### <a name="src"></a>src

Um das Image-Taghilfsprogramm zu aktivieren, wird das `src`-Attribut im Element `<img>` benötigt.

Die Bildquelle (`src`) muss auf eine physische statische Datei auf dem Server verweisen. Wenn `src` ein Remote-URI ist, wird nicht der Parameter für die Cache-Busting-Abfragezeichenfolge generiert.

### <a name="asp-append-version"></a>asp-append-version

Wenn `asp-append-version` zusätzlich mit einem `true`-Wert sowie einem `src`-Attribut angegeben wird, wird das Image-Taghilfsprogramm aufgerufen.

Im folgenden Beispiel wird ein Image-Taghilfsprogramm verwendet:

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

Wenn die statische Datei im Verzeichnis */wwwroot/images/* vorhanden ist, ähnelt der erstellte HTML-Code folgendem (der Hash wird abweichen):

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

Der Wert, der dem Parameter `v` zugewiesen ist, ist der Hashwert der Datei *asplogo.png* auf dem Datenträger. Wenn der Webserver den Lesezugriff auf die statische Datei, auf die verwiesen wird, nicht erhalten kann, werden im gerenderten Markup keine `v`-Parameter dem Attribut `src` zugefügt.

## <a name="hash-caching-behavior"></a>Hashverhalten beim Zwischenspeichern

Das Image-Taghilfsprogramm verwendet den Cacheanbieter auf dem lokalen Webserver, um den berechneten `Sha512`-Hash einer angegebenen Datei zu speichern. Wenn die Datei mehrmals angefordert wird, wird der Hash nicht neu berechnet. Der Cache wird durch einen Datei-Watcher ungültig gemacht, der an die Datei angefügt wird, wenn der `Sha512`-Hash der Datei berechnet wird. Wenn die Datei auf dem Datenträger geändert wird, wird ein neuer Hash berechnet und zwischengespeichert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/memory>
