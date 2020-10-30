---
title: Zweck Zeichenfolgen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Zweck Zeichenfolgen in den ASP.net Core Datenschutz-APIs verwendet werden.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 9a55131714b23909da5d00b73607078b295a1c4d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060806"
---
# <a name="purpose-strings-in-aspnet-core"></a>Zweck Zeichenfolgen in ASP.net Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenten, die nutzen, `IDataProtectionProvider` müssen einen eindeutigen *Verwendungs* Parameter an die- `CreateProtector` Methode übergeben. Der *Parameter* "zwecks" ist der Sicherheit des Datenschutzsystems, da es die Isolation zwischen kryptografieconsumern ermöglicht, auch wenn die Stamm Kryptografieschlüssel identisch sind.

Wenn ein Consumer einen Zweck angibt, wird die Zweck Zeichenfolge zusammen mit den Stamm-Kryptografieschlüsseln verwendet, um kryptografische Unterschlüssel abzuleiten, die für diesen Consumer eindeutig sind. Dadurch wird der Consumer von allen anderen kryptografieverbrauchern in der Anwendung isoliert: keine andere Komponente kann seine Nutzlasten lesen, und er kann keine Nutzlasten anderer Komponenten lesen. Diese Isolation rendert außerdem alle möglichen Angriffs Kategorien für die Komponente.

![Beispiel für Zweck Diagramm](purpose-strings/_static/purposes.png)

In der obigen Abbildung `IDataProtector` **können** die Instanzen A und B nicht die Nutzlasten anderer Benutzer, sondern nur Ihre eigenen Nutzlasten lesen.

Die Zweck Zeichenfolge muss nicht geheim sein. Es sollte einfach so eindeutig sein, dass keine andere gut verhaltene Komponente jemals die gleiche Zweck Zeichenfolge bereitstellen wird.

>[!TIP]
> Die Verwendung des Namespace und Typnamens der Komponente, die die Datenschutz-APIs nutzt, ist eine gute Faustregel, da diese Informationen in der Praxis niemals in Konflikt stehen.
>
>Eine von Configuration Manager erstellte Komponente, die für das Minting von bearertoken zuständig ist, kann "" als Zweck Zeichenfolge "" "" als Zweck Zeichenfolge verwenden. Oder-noch besser: Es kann "" als Zweck Zeichenfolge "" verwendet werden. Das Anfügen der Versionsnummer ermöglicht einer zukünftigen Version die Verwendung von "fitoso. Security. bearertoken. v2" als Zweck, und die verschiedenen Versionen werden vollständig voneinander isoliert, soweit Nutzlasten erfolgen.

Da es sich bei dem Parameter "zwecks" um `CreateProtector` ein Zeichen folgen Array handelt, könnte der obige Wert stattdessen als angegeben werden `[ "Contoso.Security.BearerToken", "v1" ]` . Dies ermöglicht das Einrichten einer Hierarchie von Zwecken und eröffnet die Möglichkeit von Szenarien mit mehreren Mandanten mit dem Datenschutzsystem.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Komponenten sollten nicht vertrauenswürdige Benutzereingaben nicht als alleinige Quelle für die Eingabe Quelle für die Zweck Kette zulassen.
>
>Stellen Sie sich z. b. eine Komponente "" "" ". Wenn die Secure Messaging-Komponente aufzurufen `CreateProtector([ username ])` versucht, könnte ein böswilliger Benutzer ein Konto mit dem Benutzernamen "", "" "" "" "," "" "," "" "" "" "". `CreateProtector([ "Contoso.Security.BearerToken" ])`
>
>Eine bessere Verwendungs Kette für die Messaging Komponente wäre `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])` , was eine ordnungsgemäße Isolation bereitstellt.

Die vom-Verhalten und dem Verhalten von, und bereitgestellte Isolation `IDataProtectionProvider` `IDataProtector` lautet wie folgt:

* Für ein bestimmtes `IDataProtectionProvider` Objekt erstellt die `CreateProtector` Methode ein Objekt, das `IDataProtector` eindeutig an das Objekt gebunden ist, `IDataProtectionProvider` das es erstellt hat, und den Zweck-Parameter, der an die Methode übergeben wurde.

* Der Zweck Parameter darf nicht NULL sein. (Wenn Zwecke als Array angegeben wird, bedeutet dies, dass das Array nicht die Länge 0 (null) aufweisen darf und dass alle Elemente des Arrays ungleich NULL sein müssen.) Ein leerer Zeichen folgen Zweck ist technisch zulässig, wird jedoch nicht empfohlen.

* Zwei Zweck Argumente sind nur dann gleichwertig, wenn Sie dieselben Zeichen folgen (mit einem Ordinalvergleich) in derselben Reihenfolge enthalten. Ein Argument mit einem einzelnen Zweck entspricht dem entsprechenden Array von Einzelelement Zwecken.

* Zwei `IDataProtector` Objekte sind gleichwertig, wenn Sie aus gleichwertigen `IDataProtectionProvider` Objekten mit äquivalenten Parametern erstellt werden.

* Für ein bestimmtes `IDataProtector` Objekt gibt ein-Rückruf `Unprotect(protectedData)` den ursprünglichen `unprotectedData` if und nur dann zurück, wenn `protectedData := Protect(unprotectedData)` für ein entsprechendes- `IDataProtector` Objekt.

> [!NOTE]
> Der Fall, in dem eine Komponente absichtlich eine Zweck Zeichenfolge auswählt, die bekanntermaßen mit einer anderen Komponente in Konflikt steht, wird nicht berücksichtigt. Eine solche Komponente würde im Grunde als schädlich angesehen werden, und dieses System ist nicht für die Bereitstellung von Sicherheitsgarantien gedacht, wenn bereits bösartiger Code innerhalb des Arbeitsprozesses ausgeführt wird.
