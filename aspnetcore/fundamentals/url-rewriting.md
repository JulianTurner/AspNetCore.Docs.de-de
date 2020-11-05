---
title: URL-umschreibende Middleware in ASP.NET Core
author: rick-anderson
description: Informationen zum Umschreiben und Umleiten von URL mit URL-umschreibender Middleware in ASP.NET Core-Anwendungen
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: e7bd5f4d61661dd23eb0907f896d0d32b7799aac
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061300"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>URL-umschreibende Middleware in ASP.NET Core

Von [Mikael Mengistu](https://github.com/mikaelm12)

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird das Umschreiben von URLs beschrieben. Außerdem erhalten Sie Anweisungen zur Verwendung der URL-umschreibenden Middleware in ASP.NET Core-Apps.

Bei der URL-Umschreibung werden die Anforderungs-URLs verändert, die auf mindesten einer vordefinierten Regel basieren. Es wird eine Abstraktion zwischen den Speicherorten und Adressen von Ressourcen erstellt, sodass diese nicht eng miteinander verknüpft sind. Die URL-Neuschreibung ist hilfreich in verschiedenen Szenarios wie:

* Kurzzeitiges oder permanentes Verschieben oder Ersetzen von Serverressourcen, sowie Erhalten von stabilen Locators für diese Ressourcen.
* Verteilen der Verarbeitung von Anforderungen auf verschiedene Apps oder Bereiche einer App.
* Entfernen, Hinzufügen oder Umorganisieren von URL-Segmenten bei eingehenden Anforderungen.
* Optimieren von öffentlichen URLs für die Suchmaschinenoptimierung (Search Engine Optimization, SEO).
* Gestatten der Verwendung von angezeigten öffentlichen URLs, um Besuchern zu helfen, den Inhalt vorherzusagen, der durch die Anforderung einer Ressource zurückgegeben wird.
* Umleiten von unsicheren Anforderungen auf sichere Endpunkte.
* Verhindern von Hotlinks zu externen Websites, die eine gehostete statische Ressource auf einer anderen Website verwenden, bei der die Ressource zu ihrem eigenen Inhalt verlinkt wird.

> [!NOTE]
> Wenn Sie URLs umschreiben, kann das negative Auswirkungen auf die Leistung einer App haben. Wenn möglich, sollten Sie so wenig Regeln wie möglich erstellen und darauf achten, dass diese nicht zu kompliziert sind.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Umleiten und Umschreiben von URLs

Der Unterschied zwischen dem *Umleiten* und *Neu schreiben* von URLs ist zwar gering, allerdings haben die beiden Verfahren sehr unterschiedliche Auswirkungen auf die Bereitstellung von Ressourcen für Clients. Die URL-umschreibenden Middleware von ASP.NET Core kann für beide Vorgänge verwendet werden.

Bei der *Umleitung von URLs* findet ein clientseitiger Vorgang statt, bei dem der Client angewiesen wird, auf eine Ressource unter einer anderen Adresse zuzugreifen, als die, die der Client ursprünglich angefordert hat. Dafür ist ein Roundtrip zum Server erforderlich. Die Umleitungs-URL, die an den Client zurückgegeben wird, wird in der Adressleiste des Browsers angezeigt, wenn der Client eine neue Anforderung an die Ressource sendet.

Wenn `/resource` auf `/different-resource`*umgeleitet* wird, sendet der Server die Antwort, dass der Client die Ressource unter `/different-resource` abrufen soll. In der Antwort ist außerdem ein Statuscode enthalten, aus dem entnommen werden kann, ob die Umleitung temporär oder permanent ist.

![Ein Web-API-Dienstendpunkt wurde auf dem Server kurzzeitig von Version 1 (v1) auf Version 2 (v2) geändert. Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“. Der Server sendet eine „302 – Gefunden“-Antwort mit dem neuen, temporären Pfad für Version 2: „/v2/api“. Der Client sendet eine zweite Anforderung an den Dienst unter der Umleitungs-URL. Der Server gibt den Statuscode „200 – OK“ zurück.](url-rewriting/_static/url_redirect.png)

Wenn Anforderungen auf eine andere URL umgeleitet werden, muss angegeben werden, ob die Umleitung temporär oder permanent sein soll. Geben Sie hierzu den Statuscode mit der Antwort an:

* Der Statuscode *301 – Permanent verschoben* wird verwendet, wenn der Ressource eine neue permanente URL zugewiesen wurde, und Sie dem Client die Anweisung geben möchten, dass alle zukünftigen Anforderungen an die Ressource die neue URL verwenden sollen. *Der Client kann die Antwort zwischenspeichern und wiederverwenden, wenn der Statuscode 301 empfangen wird.*

* Der Statuscode *302 – Gefunden* wird verwendet, wenn die Umleitung temporär ist oder sowieso Änderungen vorbehalten sind. Der Statuscode 302 teilt dem Client mit, dass die URL nicht gespeichert und nicht wiederverwendet werden soll.

Weitere Informationen zu Statuscodes finden Sie unter [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Bei der *Neuschreibung einer URL* handelt es sich um einen serverseitigen Vorgang, bei dem eine Ressource von einer anderen Ressourcenadresse, als der vom Client angeforderten, bereitgestellt wird. Wenn eine URL neu geschrieben wird, ist kein Roundtrip zum Server erforderlich. Die neu geschriebene URL wird nicht an den Server zurückgegeben und nicht in der Adressleiste des Browsers angezeigt.

Wenn `/resource` in `/different-resource`*umgeschrieben* wird, ruft der Server die Ressource *intern* ab und gibt sie zurück an `/different-resource`.

Auch wenn der Client die Ressource unter der neu geschriebene URL abrufen kann, erhält er nicht die Information, dass die Ressource unter der umgeschriebenen URL gespeichert ist, wenn er die Anforderung sendet und eine Antwort erhält.

![Ein Web-API-Dienstendpunkt wurde auf dem Server von Version 1 (v1) auf Version 2 (v2) geändert. Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“. Die Anforderungs-URL wird umgeschrieben, damit über den Pfad für Version 2 („/v2/api“) auf den Dienst zugegriffen werden kann. Der Dienst sendet eine Antwort an den Client mit dem Statuscode „200 – OK“.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL-umschreibende Beispiel-App

Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) können Sie die Features der Middleware zur URL-Neuschreibung testen. Die App wendet Umleitungs- und Neuschreibungsregeln an und zeigt die umgeleitete oder neu geschriebene URL für verschiedene Szenarios an.

## <a name="when-to-use-url-rewriting-middleware"></a>Empfohlene Verwendung der URL-umschreibenden Middleware

Sie können Middleware zur URL-Neuschreibung verwenden, wenn die folgenden Ansätze nicht möglich sind:

* [URL Rewrite module with IIS on Windows Server (URL-Neuschreibungsmodul mit IIS auf Windows-Server)](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite module on Apache Server (Apache mod_rewrite-Modul auf Apache-Server)](https://httpd.apache.org/docs/2.4/rewrite/)
* [URL rewriting on Nginx (URL-Neuschreibung auf Nginx)](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Sie können die Middleware auch verwenden, wenn die App auf dem [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als WebListener bezeichnet) gehostet wird.

Die Hauptgründe für die Verwendung der serverbasierten Technologien zur URL-Neuschreibung in IIS, Apache und Nginx sind:

* Die Middleware unterstützt nicht alle Features dieser Module.

  Einige Features der Servermodule funktionieren nicht mit ASP.NET Core-Projekten – z.B. die Einschränkungen `IsFile` und `IsDirectory` des IIS-Neuschreibungsmoduls. Verwenden Sie in diesem Szenario stattdessen die Middleware.
* Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Module überein.

  Benchmarking ist der einzige Weg, um sicher festzustellen, welcher Ansatz die Leistung am meisten beeinträchtigt oder ob die nachlassende Leistung nur geringfügig ist.

## <a name="package"></a>Package

Die URL-umschreibende Middleware wird über das [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)-Paket bereitgestellt, das implizit in ASP.NET Core-Apps enthalten ist.

## <a name="extension-and-options"></a>Erweiterung und Optionen

Legen Sie URL-Neuschreibungs- und -Umleitungsregeln fest, indem Sie eine Instanz der [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions)-Klasse erstellen und Erweiterungsmethoden für jede Neuschreibungsregel hinzufügen. Verketten Sie mehrere Regeln in der Reihenfolge miteinander, in der sie verarbeitet werden sollen. Die `RewriteOptions` werden an die Middleware zur URL-Neuschreibung übergeben, wenn diese mit <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> zu der Anforderungspipeline hinzugefügt wird:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Umleitung einer Nicht-WWW-Anforderung an eine WWW-Anforderung

Drei Optionen ermöglichen der App, Nicht-`www`-Anforderungen an `www` umzuleiten:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Die Anforderung wird dauerhaft an die Unterdomäne `www` umgeleitet, sofern die Anforderung nicht `www` ist. Wird mit dem Statuscode [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) umgeleitet.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Die Anforderung wird an die Unterdomäne `www` umgeleitet, sofern die eingehende Anforderung nicht `www` ist. Wird mit dem Statuscode [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) umgeleitet. Eine Überladung ermöglicht es Ihnen, den Statuscode für die Antwort zu senden. Verwenden Sie ein Feld der <xref:Microsoft.AspNetCore.Http.StatusCodes>-Klasse, um einen Statuscode zuzuweisen.

### <a name="url-redirect"></a>Umleitungs-URL

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>, um Anforderungen umzuleiten. Der erste Parameter enthält Ihren RegEx, damit dieser dem Pfad der eingehenden URL zugeordnet werden kann. Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge. Der dritte Parameter gibt, falls vorhanden, den Statuscode an. Wenn Sie den Statuscode nicht angeben, wird standardmäßig *302 – Gefunden* zurückgegeben, was bedeutet, dass die Ressource kurzzeitig verschoben oder ersetzt wurde.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Aktivieren Sie in Ihrem Browser die Entwicklertools, und senden Sie eine Anforderung an die Beispiel-App mit dem Pfad `/redirect-rule/1234/5678`. Der RegEx stimmt mit dem Anforderungspfad unter `redirect-rule/(.*)` überein, und der Pfad wird durch `/redirected/1234/5678` ersetzt. Die Umleitungs-URL wird mit dem Statuscode *302 – Gefunden* an den Client zurückgesendet. Unter der Umleitungs-URL sendet der Browser eine neue Anforderung, die in dessen Adressleiste angezeigt wird. Da keine Regel in der Beispiel-App mit der Umleitungs-URL übereinstimmt:

* Die zweite Anforderung erhält die Antwort *200 – OK* von der App.
* Der Antworttext zeigt die Umleitungs-URL an.

Wenn eine URL *weitergeleitet* wird, wird ein Roundtrip zum Server ausgelöst.

> [!WARNING]
> Seien Sie vorsichtig, wenn Sie Umleitungsregeln einrichten. Bei jeder Anforderung an die App werden Umleitungsregeln überprüft – auch nach einer Umleitung. Dabei kann schnell aus Versehen eine *Dauerschleife von Umleitungen* entstehen.

Ursprüngliche Anforderung: `/redirect-rule/1234/5678`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect.png)

Der Teil des Ausdruck in Klammern wird als *Erfassungsgruppe* bezeichnet. Der Punkt (`.`) im Ausdruck steht für *Übereinstimmung mit beliebigem Zeichen*. Das Sternchen (`*`) steht für *Übereinstimmung mit dem vorausgehenden Zeichen (keinmal oder mindestens einmal)* . Daher werden die letzten beiden Pfadsegmente der URL (`1234/5678`) von der Erfassungsgruppe erfasst `(.*)`. Alle Werte, die Sie in der Anforderungs-URL nach `redirect-rule/` angeben, werden von dieser Erfassungsgruppe erfasst.

Erfassungsgruppen werden in der Ersetzungszeichenfolge mit dem Dollarzeichen (`$`) in die Zeichenfolge eingefügt. Danach folgt die Sequenznummer der Erfassung. Der erste Wert der Erfassungsgruppe wird mit `$1` abgerufen, der zweite mit `$2`. Dies wird in Sequenzen für die Erfassungsgruppen Ihres RegEx weitergeführt. Nur eine Erfassungsgruppe ist in der Beispiel-App im RegEx der Umleitungsregel enthalten. Das bedeutet, dass es in die Ersetzungszeichenfolge nur eine Gruppe eingefügt wird, nämlich `$1`. Wenn die Regel angewendet wird, ändert sich die URL in `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>URL-Umleitung an einen sicheren Endpunkt

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, um HTTP-Anforderungen auf denselben Host und Pfad mithilfe des HTTPS-Protokolls umzuleiten. Wenn kein Statuscode angegeben wird, wird für die Middleware standardmäßig *302 – Gefunden* zurückgegeben. Wenn kein Port angegeben ist:

* Für die Middleware wird standardmäßig `null` zurückgegeben.
* Das Schema ändert sich in `https` (HTTPS-Protokoll), und der Client hat über Port 443 Zugriff auf die Ressource.

Im folgenden Beispiel wird dargestellt, wie Sie den Statuscode *301 – Permanent verschoben* festlegen und den Port in 5001 ändern.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, um unsichere Anforderungen auf denselben Host und Pfad mit einem sicheren HTTPS-Protokoll auf Port 443 umzuleiten. Die Middleware legt den Statuscode auf *301 – Permanent verschoben* fest.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Wenn Sie eine Umleitung an einen sicheren Endpunkt ohne weitere erforderliche Umleitungsregeln durchführen, wird empfohlen, Middleware für HTTPS-Umleitung zu verwenden. Weitere Informationen finden Sie im Artikel zum [Erzwingen von HTTPS](xref:security/enforcing-ssl#require-https).

Anhand der Beispiel-App wird veranschaulicht, wie `AddRedirectToHttps` oder `AddRedirectToHttpsPermanent` verwendet werden sollen. Fügen Sie die Erweiterungsmethode zu den `RewriteOptions` hinzu. Senden Sie eine unsichere Anforderung an die App unter einer beliebigen URL. Schließen Sie die Sicherheitswarnung des Browsers, in der Sie darüber informiert werden, dass das selbstsignierte Zertifikat nicht vertrauenswürdig ist, oder erstellen sie eine Ausnahme, um dem Zertifikat zu vertrauen.

Ursprüngliche Anforderung über `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect_to_https.png)

Ursprüngliche Anforderung über `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Umschreiben einer URL

Erstellen Sie mithilfe von <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> eine Regel zum Umschreiben von URLs. Der erste Parameter enthält den RegEx, damit dieser der eingehenden URL zugeordnet werden kann. Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge. Der dritte Parameter (`skipRemainingRules: {true|false}`) teilt der Middleware mit, ob sie zusätzliche Umschreibungsregeln überspringen soll, wenn die aktuelle Regel angewendet wird.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Ursprüngliche Anforderung: `/rewrite-rule/1234/5678`

![Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen](url-rewriting/_static/add_rewrite.png)

Das Zirkumflexzeichen (`^`) am Anfang des Ausdrucks bedeutet, dass die Übereinstimmung schon am Anfang des URL-Pfads beginnt.

In dem vorherigen Beispiel zu den Umleitungsregeln, `redirect-rule/(.*)`, beginnt der RegEx nicht mit einem Zirkumflexzeichen (`^`). So kann für eine Übereinstimmung ein beliebiges Zeichen im Pfad vor `redirect-rule/` stehen.

| Pfad                               | Match |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ja   |
| `/my-cool-redirect-rule/1234/5678` | Ja   |
| `/anotherredirect-rule/1234/5678`  | Ja   |

Die Umschreibungsregel (`^rewrite-rule/(\d+)/(\d+)`) stimmt nur mit Pfaden überein, wenn sie mit `rewrite-rule/` beginnen. Beachten Sie die unterschiedliche Übereinstimmung in der folgenden Tabelle:

| Pfad                              | Match |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ja   |
| `/my-cool-rewrite-rule/1234/5678` | Nein    |
| `/anotherrewrite-rule/1234/5678`  | Nein    |

Auf den `^rewrite-rule/`-Teil des Ausdruck folgen zwei Erfassungsgruppen: `(\d+)/(\d+)`. `\d` steht für *Übereinstimmung mit einer Ziffer (Zahl)* . Das Pluszeichen (`+`) steht für *match one or more of the preceding character* (Übereinstimmung mit mindestens einem vorausgehenden Zeichen). Aus diesem Grund muss die URL eine Zahl enthalten, auf die ein Schrägstrich und eine weitere Zahl folgt. Die Erfassungsgruppen werden in die umgeschriebene URL als `$1` und `$2` eingefügt. Über die Ersetzungszeichenfolge der Neuschreibungsregel werden die Erfassungsgruppen in die Abfragezeichenfolge eingefügt. Der angeforderte `/rewrite-rule/1234/5678`-Pfad wird umgeschrieben, um eine Ressource unter `/rewritten?var1=1234&var2=5678` abzurufen. Wenn es in der ursprünglichen Anforderung eine Abfragezeichenfolge gibt, bleibt diese erhalten, wenn die URL umgeschrieben wird.

Es gibt keinen Roundtrip zum Server, um die Ressource abzurufen. Wenn es die Ressource gibt, wird sie abgerufen und dem Client mit dem Statuscode *200 – OK* zurückgegeben. Da der Client nicht umgeleitet wird, ändert sich die URL in der Adressleiste des Browsers nicht. Clients können nicht erkennen, dass ein Vorgang zum erneuten Schreiben einer URL auf dem Server stattgefunden hat.

> [!NOTE]
> Verwenden Sie `skipRemainingRules: true`, wo immer das möglich ist, da das Abgleichen von Regeln rechnerisch sehr aufwendig ist und die Antwortzeit der App erhöht. Führen Sie folgende Schritte aus, um die schnellsten App-Antwortzeiten zu erreichen:
>
> * Sortieren Sie Neuschreibungsregeln von der am häufigsten abgeglichenen Regel zu der am seltensten abgeglichenen Regel.
> * Überspringen Sie die Verarbeitung der übrigen Regeln, wenn es zu einer Übereinstimmung kommt und keine zusätzlichen Regelverarbeitungen erforderlich sind.

### <a name="apache-mod_rewrite"></a>Apache: „mod_rewrite“

Wenden Sie die Apache-Regeln „mod_rewrite“ mit <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> an. Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird. Weitere Informationen zu diesen Regeln finden Sie unter [Apache: „mod_rewrite“](https://httpd.apache.org/docs/2.4/rewrite/).

Zum Lesen der Regeldatei *ApacheModRewrite.txt* wird <xref:System.IO.StreamReader> verwendet:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Die Beispiel-App leitet Anforderungen von `/apache-mod-rules-redirect/(.\*)` auf `/redirected?id=$1` um. Der Antwortstatuscode lautet *302 – Gefunden*.

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

Ursprüngliche Anforderung: `/apache-mod-rules-redirect/1234`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_apache_mod_redirect.png)

Die Middleware unterstützt die folgenden Servervariablen für die Apache „mod_rewrite“:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Regeln zum IIS-Umschreibungsmodul

Um denselben Regelsatz zu verwenden, der für das IIS-Moduls für URL-Neuschreibung gilt, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird. Geben Sie der Middleware nicht die Anweisung, die *web.config* -Datei der App zu verwenden, wenn sie auf der Windows Server-IIS ausgeführt wird. Bei IIS sollten diese Regeln außerhalb der *web.config* -Datei der App gespeichert werden, um Konflikte mit dem IIS-Neuschreibungsmodul zu vermeiden. Weitere Informationen und Beispiele zu den Regeln zum IIS-Umschreibungsmodul finden Sie unter [Using Url Rewrite Module 2.0 (Verwenden des URL-Umschreibungsmoduls 2.0)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) und [URL Rewrite Module Configuration Reference (Konfigurationsreferenz des URL-Umschreibungsmoduls)](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

Zum Lesen der Regeldatei *IISUrlRewrite.xml* wird <xref:System.IO.StreamReader> verwendet:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Die Beispiel-App schreibt Anforderungen von `/iis-rules-rewrite/(.*)` in `/rewritten?id=$1` um. Die Antwort wird an den Client mit dem Statuscode *200 – OK* gesendet.

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

Ursprüngliche Anforderung: `/iis-rules-rewrite/1234`

![Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen](url-rewriting/_static/add_iis_url_rewrite.png)

Wenn Sie über ein aktives IIS-Umschreibungsmodul verfügen, für das die Regeln auf Serverebene konfiguriert sind, die negative Auswirkungen auf Ihre App hätten, können Sie das IIS-Umschreibungsmodul für die App deaktivieren. Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nicht unterstützte Features

Die im Lieferumfang von ASP.NET Core 2.x enthaltene Middleware unterstützt die folgenden Features des IIS-URL-Umschreibungsmoduls nicht:

* Ausgehende Regeln
* Benutzerdefinierte Servervariablen
* Platzhalter
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Unterstützte Servervariablen

Die Middleware unterstützt die folgenden Servervariablen für das IIS-URL-Umschreibungsmodul:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Sie können <xref:Microsoft.Extensions.FileProviders.IFileProvider> auch über <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> abrufen. Über diesen Ansatz können Sie flexibler einen Speicherort für die Dateien der Umschreibungsregeln auswählen. Vergewissern Sie sich, dass die Dateien zu den Umschreibungsregeln auf dem Server unter dem von Ihnen angegebenen Pfad bereitgestellt werden.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Methodenbasierte Regel

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, um Ihre eigene Regellogik in einer Methode zu implementieren. `Add` macht <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> verfügbar, wodurch die Verwendung von <xref:Microsoft.AspNetCore.Http.HttpContext> in Ihrer Methode ermöglicht wird. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) bestimmt, wie die zusätzliche Pipelineverarbeitung erfolgt. Legen Sie den Wert auf eines der <xref:Microsoft.AspNetCore.Rewrite.RuleResult>-Felder fest, die in der folgenden Tabelle beschrieben sind:

| Umschreiben von Kontext – Ergebnisse               | Aktion                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (Standardwert) | Regeln weiter anwenden.                                         |
| `RuleResult.EndResponse`             | Regeln nicht mehr anwenden und Antwort senden.                       |
| `RuleResult.SkipRemainingRules`      | Regeln nicht mehr anwenden, und den Kontext an die nächste Middleware senden. |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

In der Beispiel-App wird eine Methode dargestellt, die Anforderungen für Pfade weiterleitet, die auf *.xml* enden. Wenn eine Anforderung für `/file.xml` erfolgt, wird sie zu `/xmlfiles/file.xml` umgeleitet. Der Statuscode wird auf *301 – Permanent verschoben* festgelegt. Wenn vom Browser eine neue Anforderung für */xmlfiles/file.xml* gesendet wird, wird dem Client die Datei von Middleware für statische Dateien aus dem Ordner *wwwroot/xmlfiles* bereitgestellt. Legen Sie für eine Umleitung den Statuscode der Antwort explizit fest. Andernfalls wird ein Statuscode *200 – OK* zurückgegeben, und die Umleitung findet nicht auf dem Client statt.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Bei diesem Ansatz können auch Anforderungen erneut generiert werden. In der Beispiel-App wird demonstriert, wie der Pfad für eine beliebige Textdateianforderung umgeschrieben wird, um die Textdatei *file.txt* aus dem Ordner *wwwroot* bereitzustellen. Middleware für statische Dateien stellt die Datei bereit, die auf dem aktualisierten Anforderungspfad basiert:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule-basierte Regel

Um Regellogik in einer Klasse zu nutzen, die die <xref:Microsoft.AspNetCore.Rewrite.IRule>-Schnittstelle implementiert, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>. Wenn Sie `IRule` verwenden, können Sie flexibler entscheiden, ob Sie eine methodenbasierte Regel verwenden möchten. Ihre Implementierungsklasse kann einen Konstruktor enthalten, wodurch Sie Parameter für die <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>-Methode übergeben können.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Die Werte für die Parameter in der Beispiel-App für `extension` und `newPath` werden auf verschiedene Bedingungen geprüft. `extension` muss einen Wert enthalten, der *.png* , *.jpg* oder *.gif* entspricht. Wenn `newPath` nicht gültig ist, wird <xref:System.ArgumentException> nicht ausgelöst. Wenn eine Anforderung für *image.png* erfolgt, wird sie zu `/png-images/image.png` umgeleitet. Wenn eine Anforderung für *image.jpg* erfolgt, wird die zu `/jpg-images/image.jpg` umgeleitet. Der Statuscode ist auf *301 – Permanent verschoben* festgelegt, und `context.Result` erhält die Anweisung, Verarbeitungsregeln nicht mehr anzuwenden und die Antwort zu senden.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Ursprüngliche Anforderung: `/image.png`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten der image.png-Datei nachverfolgen](url-rewriting/_static/add_redirect_png_requests.png)

Ursprüngliche Anforderung: `/image.jpg`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten der image.jpg-Datei nachverfolgen](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>RegEx-Beispiele

| Ziel | RegEx-Zeichenfolge und<br>übereinstimmendes Beispiel | Ersetzungszeichenfolge und<br>Ausgabebeispiel |
| ---- | ------------------------------- | -------------------------------------- |
| Umschreiben des Pfads in die Abfragezeichenfolge | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Entfernen des nachgestellten Schrägstrichs | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Erzwingen des nachgestellten Schrägstrichs | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vermeiden des Umschreibens von bestimmten Anforderungen | `^(.*)(?<!\.axd)$` oder `^(?!.*\.axd$)(.*)$`<br>Ja: `/resource.htm`<br>Nein: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Ändern der Anordnung von URL-Segmenten | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Ersetzen von URL-Segmenten | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird das Umschreiben von URLs beschrieben. Außerdem erhalten Sie Anweisungen zur Verwendung der URL-umschreibenden Middleware in ASP.NET Core-Apps.

Bei der URL-Umschreibung werden die Anforderungs-URLs verändert, die auf mindesten einer vordefinierten Regel basieren. Es wird eine Abstraktion zwischen den Speicherorten und Adressen von Ressourcen erstellt, sodass diese nicht eng miteinander verknüpft sind. Die URL-Neuschreibung ist hilfreich in verschiedenen Szenarios wie:

* Kurzzeitiges oder permanentes Verschieben oder Ersetzen von Serverressourcen, sowie Erhalten von stabilen Locators für diese Ressourcen.
* Verteilen der Verarbeitung von Anforderungen auf verschiedene Apps oder Bereiche einer App.
* Entfernen, Hinzufügen oder Umorganisieren von URL-Segmenten bei eingehenden Anforderungen.
* Optimieren von öffentlichen URLs für die Suchmaschinenoptimierung (Search Engine Optimization, SEO).
* Gestatten der Verwendung von angezeigten öffentlichen URLs, um Besuchern zu helfen, den Inhalt vorherzusagen, der durch die Anforderung einer Ressource zurückgegeben wird.
* Umleiten von unsicheren Anforderungen auf sichere Endpunkte.
* Verhindern von Hotlinks zu externen Websites, die eine gehostete statische Ressource auf einer anderen Website verwenden, bei der die Ressource zu ihrem eigenen Inhalt verlinkt wird.

> [!NOTE]
> Wenn Sie URLs umschreiben, kann das negative Auswirkungen auf die Leistung einer App haben. Wenn möglich, sollten Sie so wenig Regeln wie möglich erstellen und darauf achten, dass diese nicht zu kompliziert sind.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="url-redirect-and-url-rewrite"></a>Umleiten und Umschreiben von URLs

Der Unterschied zwischen dem *Umleiten* und *Neu schreiben* von URLs ist zwar gering, allerdings haben die beiden Verfahren sehr unterschiedliche Auswirkungen auf die Bereitstellung von Ressourcen für Clients. Die URL-umschreibenden Middleware von ASP.NET Core kann für beide Vorgänge verwendet werden.

Bei der *Umleitung von URLs* findet ein clientseitiger Vorgang statt, bei dem der Client angewiesen wird, auf eine Ressource unter einer anderen Adresse zuzugreifen, als die, die der Client ursprünglich angefordert hat. Dafür ist ein Roundtrip zum Server erforderlich. Die Umleitungs-URL, die an den Client zurückgegeben wird, wird in der Adressleiste des Browsers angezeigt, wenn der Client eine neue Anforderung an die Ressource sendet.

Wenn `/resource` auf `/different-resource`*umgeleitet* wird, sendet der Server die Antwort, dass der Client die Ressource unter `/different-resource` abrufen soll. In der Antwort ist außerdem ein Statuscode enthalten, aus dem entnommen werden kann, ob die Umleitung temporär oder permanent ist.

![Ein Web-API-Dienstendpunkt wurde auf dem Server kurzzeitig von Version 1 (v1) auf Version 2 (v2) geändert. Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“. Der Server sendet eine „302 – Gefunden“-Antwort mit dem neuen, temporären Pfad für Version 2: „/v2/api“. Der Client sendet eine zweite Anforderung an den Dienst unter der Umleitungs-URL. Der Server gibt den Statuscode „200 – OK“ zurück.](url-rewriting/_static/url_redirect.png)

Wenn Anforderungen auf eine andere URL umgeleitet werden, muss angegeben werden, ob die Umleitung temporär oder permanent sein soll. Geben Sie hierzu den Statuscode mit der Antwort an:

* Der Statuscode *301 – Permanent verschoben* wird verwendet, wenn der Ressource eine neue permanente URL zugewiesen wurde, und Sie dem Client die Anweisung geben möchten, dass alle zukünftigen Anforderungen an die Ressource die neue URL verwenden sollen. *Der Client kann die Antwort zwischenspeichern und wiederverwenden, wenn der Statuscode 301 empfangen wird.*

* Der Statuscode *302 – Gefunden* wird verwendet, wenn die Umleitung temporär ist oder sowieso Änderungen vorbehalten sind. Der Statuscode 302 teilt dem Client mit, dass die URL nicht gespeichert und nicht wiederverwendet werden soll.

Weitere Informationen zu Statuscodes finden Sie unter [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Bei der *Neuschreibung einer URL* handelt es sich um einen serverseitigen Vorgang, bei dem eine Ressource von einer anderen Ressourcenadresse, als der vom Client angeforderten, bereitgestellt wird. Wenn eine URL neu geschrieben wird, ist kein Roundtrip zum Server erforderlich. Die neu geschriebene URL wird nicht an den Server zurückgegeben und nicht in der Adressleiste des Browsers angezeigt.

Wenn `/resource` in `/different-resource`*umgeschrieben* wird, ruft der Server die Ressource *intern* ab und gibt sie zurück an `/different-resource`.

Auch wenn der Client die Ressource unter der neu geschriebene URL abrufen kann, erhält er nicht die Information, dass die Ressource unter der umgeschriebenen URL gespeichert ist, wenn er die Anforderung sendet und eine Antwort erhält.

![Ein Web-API-Dienstendpunkt wurde auf dem Server von Version 1 (v1) auf Version 2 (v2) geändert. Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“. Die Anforderungs-URL wird umgeschrieben, damit über den Pfad für Version 2 („/v2/api“) auf den Dienst zugegriffen werden kann. Der Dienst sendet eine Antwort an den Client mit dem Statuscode „200 – OK“.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL-umschreibende Beispiel-App

Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) können Sie die Features der Middleware zur URL-Neuschreibung testen. Die App wendet Umleitungs- und Neuschreibungsregeln an und zeigt die umgeleitete oder neu geschriebene URL für verschiedene Szenarios an.

## <a name="when-to-use-url-rewriting-middleware"></a>Empfohlene Verwendung der URL-umschreibenden Middleware

Sie können Middleware zur URL-Neuschreibung verwenden, wenn die folgenden Ansätze nicht möglich sind:

* [URL Rewrite module with IIS on Windows Server (URL-Neuschreibungsmodul mit IIS auf Windows-Server)](https://www.iis.net/downloads/microsoft/url-rewrite)
* [Apache mod_rewrite module on Apache Server (Apache mod_rewrite-Modul auf Apache-Server)](https://httpd.apache.org/docs/2.4/rewrite/)
* [URL rewriting on Nginx (URL-Neuschreibung auf Nginx)](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

Sie können die Middleware auch verwenden, wenn die App auf dem [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als WebListener bezeichnet) gehostet wird.

Die Hauptgründe für die Verwendung der serverbasierten Technologien zur URL-Neuschreibung in IIS, Apache und Nginx sind:

* Die Middleware unterstützt nicht alle Features dieser Module.

  Einige Features der Servermodule funktionieren nicht mit ASP.NET Core-Projekten – z.B. die Einschränkungen `IsFile` und `IsDirectory` des IIS-Neuschreibungsmoduls. Verwenden Sie in diesem Szenario stattdessen die Middleware.
* Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Module überein.

  Benchmarking ist der einzige Weg, um sicher festzustellen, welcher Ansatz die Leistung am meisten beeinträchtigt oder ob die nachlassende Leistung nur geringfügig ist.

## <a name="package"></a>Package

Um die Middleware in Ihr Projekt einzuschließen, fügen Sie einen Paketverweis zum [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei ein, die das [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)-Paket enthält.

Wenn Sie das `Microsoft.AspNetCore.App`Metapaket nicht verwenden, fügen Sie dem Paket einen Projekthinweis hinzu`Microsoft.AspNetCore.Rewrite`.

## <a name="extension-and-options"></a>Erweiterung und Optionen

Legen Sie URL-Neuschreibungs- und -Umleitungsregeln fest, indem Sie eine Instanz der [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions)-Klasse erstellen und Erweiterungsmethoden für jede Neuschreibungsregel hinzufügen. Verketten Sie mehrere Regeln in der Reihenfolge miteinander, in der sie verarbeitet werden sollen. Die `RewriteOptions` werden an die Middleware zur URL-Neuschreibung übergeben, wenn diese mit <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> zu der Anforderungspipeline hinzugefügt wird:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Umleitung einer Nicht-WWW-Anforderung an eine WWW-Anforderung

Drei Optionen ermöglichen der App, Nicht-`www`-Anforderungen an `www` umzuleiten:

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Die Anforderung wird dauerhaft an die Unterdomäne `www` umgeleitet, sofern die Anforderung nicht `www` ist. Wird mit dem Statuscode [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) umgeleitet.

* <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Die Anforderung wird an die Unterdomäne `www` umgeleitet, sofern die eingehende Anforderung nicht `www` ist. Wird mit dem Statuscode [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) umgeleitet. Eine Überladung ermöglicht es Ihnen, den Statuscode für die Antwort zu senden. Verwenden Sie ein Feld der <xref:Microsoft.AspNetCore.Http.StatusCodes>-Klasse, um einen Statuscode zuzuweisen.

### <a name="url-redirect"></a>Umleitungs-URL

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>, um Anforderungen umzuleiten. Der erste Parameter enthält Ihren RegEx, damit dieser dem Pfad der eingehenden URL zugeordnet werden kann. Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge. Der dritte Parameter gibt, falls vorhanden, den Statuscode an. Wenn Sie den Statuscode nicht angeben, wird standardmäßig *302 – Gefunden* zurückgegeben, was bedeutet, dass die Ressource kurzzeitig verschoben oder ersetzt wurde.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Aktivieren Sie in Ihrem Browser die Entwicklertools, und senden Sie eine Anforderung an die Beispiel-App mit dem Pfad `/redirect-rule/1234/5678`. Der RegEx stimmt mit dem Anforderungspfad unter `redirect-rule/(.*)` überein, und der Pfad wird durch `/redirected/1234/5678` ersetzt. Die Umleitungs-URL wird mit dem Statuscode *302 – Gefunden* an den Client zurückgesendet. Unter der Umleitungs-URL sendet der Browser eine neue Anforderung, die in dessen Adressleiste angezeigt wird. Da keine Regel in der Beispiel-App mit der Umleitungs-URL übereinstimmt:

* Die zweite Anforderung erhält die Antwort *200 – OK* von der App.
* Der Antworttext zeigt die Umleitungs-URL an.

Wenn eine URL *weitergeleitet* wird, wird ein Roundtrip zum Server ausgelöst.

> [!WARNING]
> Seien Sie vorsichtig, wenn Sie Umleitungsregeln einrichten. Bei jeder Anforderung an die App werden Umleitungsregeln überprüft – auch nach einer Umleitung. Dabei kann schnell aus Versehen eine *Dauerschleife von Umleitungen* entstehen.

Ursprüngliche Anforderung: `/redirect-rule/1234/5678`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect.png)

Der Teil des Ausdruck in Klammern wird als *Erfassungsgruppe* bezeichnet. Der Punkt (`.`) im Ausdruck steht für *Übereinstimmung mit beliebigem Zeichen*. Das Sternchen (`*`) steht für *Übereinstimmung mit dem vorausgehenden Zeichen (keinmal oder mindestens einmal)* . Daher werden die letzten beiden Pfadsegmente der URL (`1234/5678`) von der Erfassungsgruppe erfasst `(.*)`. Alle Werte, die Sie in der Anforderungs-URL nach `redirect-rule/` angeben, werden von dieser Erfassungsgruppe erfasst.

Erfassungsgruppen werden in der Ersetzungszeichenfolge mit dem Dollarzeichen (`$`) in die Zeichenfolge eingefügt. Danach folgt die Sequenznummer der Erfassung. Der erste Wert der Erfassungsgruppe wird mit `$1` abgerufen, der zweite mit `$2`. Dies wird in Sequenzen für die Erfassungsgruppen Ihres RegEx weitergeführt. Nur eine Erfassungsgruppe ist in der Beispiel-App im RegEx der Umleitungsregel enthalten. Das bedeutet, dass es in die Ersetzungszeichenfolge nur eine Gruppe eingefügt wird, nämlich `$1`. Wenn die Regel angewendet wird, ändert sich die URL in `/redirected/1234/5678`.

### <a name="url-redirect-to-a-secure-endpoint"></a>URL-Umleitung an einen sicheren Endpunkt

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, um HTTP-Anforderungen auf denselben Host und Pfad mithilfe des HTTPS-Protokolls umzuleiten. Wenn kein Statuscode angegeben wird, wird für die Middleware standardmäßig *302 – Gefunden* zurückgegeben. Wenn kein Port angegeben ist:

* Für die Middleware wird standardmäßig `null` zurückgegeben.
* Das Schema ändert sich in `https` (HTTPS-Protokoll), und der Client hat über Port 443 Zugriff auf die Ressource.

Im folgenden Beispiel wird dargestellt, wie Sie den Statuscode *301 – Permanent verschoben* festlegen und den Port in 5001 ändern.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, um unsichere Anforderungen auf denselben Host und Pfad mit einem sicheren HTTPS-Protokoll auf Port 443 umzuleiten. Die Middleware legt den Statuscode auf *301 – Permanent verschoben* fest.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Wenn Sie eine Umleitung an einen sicheren Endpunkt ohne weitere erforderliche Umleitungsregeln durchführen, wird empfohlen, Middleware für HTTPS-Umleitung zu verwenden. Weitere Informationen finden Sie im Artikel zum [Erzwingen von HTTPS](xref:security/enforcing-ssl#require-https).

Anhand der Beispiel-App wird veranschaulicht, wie `AddRedirectToHttps` oder `AddRedirectToHttpsPermanent` verwendet werden sollen. Fügen Sie die Erweiterungsmethode zu den `RewriteOptions` hinzu. Senden Sie eine unsichere Anforderung an die App unter einer beliebigen URL. Schließen Sie die Sicherheitswarnung des Browsers, in der Sie darüber informiert werden, dass das selbstsignierte Zertifikat nicht vertrauenswürdig ist, oder erstellen sie eine Ausnahme, um dem Zertifikat zu vertrauen.

Ursprüngliche Anforderung über `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect_to_https.png)

Ursprüngliche Anforderung über `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Umschreiben einer URL

Erstellen Sie mithilfe von <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> eine Regel zum Umschreiben von URLs. Der erste Parameter enthält den RegEx, damit dieser der eingehenden URL zugeordnet werden kann. Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge. Der dritte Parameter (`skipRemainingRules: {true|false}`) teilt der Middleware mit, ob sie zusätzliche Umschreibungsregeln überspringen soll, wenn die aktuelle Regel angewendet wird.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Ursprüngliche Anforderung: `/rewrite-rule/1234/5678`

![Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen](url-rewriting/_static/add_rewrite.png)

Das Zirkumflexzeichen (`^`) am Anfang des Ausdrucks bedeutet, dass die Übereinstimmung schon am Anfang des URL-Pfads beginnt.

In dem vorherigen Beispiel zu den Umleitungsregeln, `redirect-rule/(.*)`, beginnt der RegEx nicht mit einem Zirkumflexzeichen (`^`). So kann für eine Übereinstimmung ein beliebiges Zeichen im Pfad vor `redirect-rule/` stehen.

| Pfad                               | Match |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Ja   |
| `/my-cool-redirect-rule/1234/5678` | Ja   |
| `/anotherredirect-rule/1234/5678`  | Ja   |

Die Umschreibungsregel (`^rewrite-rule/(\d+)/(\d+)`) stimmt nur mit Pfaden überein, wenn sie mit `rewrite-rule/` beginnen. Beachten Sie die unterschiedliche Übereinstimmung in der folgenden Tabelle:

| Pfad                              | Match |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Ja   |
| `/my-cool-rewrite-rule/1234/5678` | Nein    |
| `/anotherrewrite-rule/1234/5678`  | Nein    |

Auf den `^rewrite-rule/`-Teil des Ausdruck folgen zwei Erfassungsgruppen: `(\d+)/(\d+)`. `\d` steht für *Übereinstimmung mit einer Ziffer (Zahl)* . Das Pluszeichen (`+`) steht für *match one or more of the preceding character* (Übereinstimmung mit mindestens einem vorausgehenden Zeichen). Aus diesem Grund muss die URL eine Zahl enthalten, auf die ein Schrägstrich und eine weitere Zahl folgt. Die Erfassungsgruppen werden in die umgeschriebene URL als `$1` und `$2` eingefügt. Über die Ersetzungszeichenfolge der Neuschreibungsregel werden die Erfassungsgruppen in die Abfragezeichenfolge eingefügt. Der angeforderte `/rewrite-rule/1234/5678`-Pfad wird umgeschrieben, um eine Ressource unter `/rewritten?var1=1234&var2=5678` abzurufen. Wenn es in der ursprünglichen Anforderung eine Abfragezeichenfolge gibt, bleibt diese erhalten, wenn die URL umgeschrieben wird.

Es gibt keinen Roundtrip zum Server, um die Ressource abzurufen. Wenn es die Ressource gibt, wird sie abgerufen und dem Client mit dem Statuscode *200 – OK* zurückgegeben. Da der Client nicht umgeleitet wird, ändert sich die URL in der Adressleiste des Browsers nicht. Clients können nicht erkennen, dass ein Vorgang zum erneuten Schreiben einer URL auf dem Server stattgefunden hat.

> [!NOTE]
> Verwenden Sie `skipRemainingRules: true`, wo immer das möglich ist, da das Abgleichen von Regeln rechnerisch sehr aufwendig ist und die Antwortzeit der App erhöht. Führen Sie folgende Schritte aus, um die schnellsten App-Antwortzeiten zu erreichen:
>
> * Sortieren Sie Neuschreibungsregeln von der am häufigsten abgeglichenen Regel zu der am seltensten abgeglichenen Regel.
> * Überspringen Sie die Verarbeitung der übrigen Regeln, wenn es zu einer Übereinstimmung kommt und keine zusätzlichen Regelverarbeitungen erforderlich sind.

### <a name="apache-mod_rewrite"></a>Apache: „mod_rewrite“

Wenden Sie die Apache-Regeln „mod_rewrite“ mit <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> an. Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird. Weitere Informationen zu diesen Regeln finden Sie unter [Apache: „mod_rewrite“](https://httpd.apache.org/docs/2.4/rewrite/).

Zum Lesen der Regeldatei *ApacheModRewrite.txt* wird <xref:System.IO.StreamReader> verwendet:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

Die Beispiel-App leitet Anforderungen von `/apache-mod-rules-redirect/(.\*)` auf `/redirected?id=$1` um. Der Antwortstatuscode lautet *302 – Gefunden*.

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

Ursprüngliche Anforderung: `/apache-mod-rules-redirect/1234`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen](url-rewriting/_static/add_apache_mod_redirect.png)

Die Middleware unterstützt die folgenden Servervariablen für die Apache „mod_rewrite“:

* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* TIME
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Regeln zum IIS-Umschreibungsmodul

Um denselben Regelsatz zu verwenden, der für das IIS-Moduls für URL-Neuschreibung gilt, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird. Geben Sie der Middleware nicht die Anweisung, die *web.config* -Datei der App zu verwenden, wenn sie auf der Windows Server-IIS ausgeführt wird. Bei IIS sollten diese Regeln außerhalb der *web.config* -Datei der App gespeichert werden, um Konflikte mit dem IIS-Neuschreibungsmodul zu vermeiden. Weitere Informationen und Beispiele zu den Regeln zum IIS-Umschreibungsmodul finden Sie unter [Using Url Rewrite Module 2.0 (Verwenden des URL-Umschreibungsmoduls 2.0)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) und [URL Rewrite Module Configuration Reference (Konfigurationsreferenz des URL-Umschreibungsmoduls)](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

Zum Lesen der Regeldatei *IISUrlRewrite.xml* wird <xref:System.IO.StreamReader> verwendet:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

Die Beispiel-App schreibt Anforderungen von `/iis-rules-rewrite/(.*)` in `/rewritten?id=$1` um. Die Antwort wird an den Client mit dem Statuscode *200 – OK* gesendet.

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

Ursprüngliche Anforderung: `/iis-rules-rewrite/1234`

![Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen](url-rewriting/_static/add_iis_url_rewrite.png)

Wenn Sie über ein aktives IIS-Umschreibungsmodul verfügen, für das die Regeln auf Serverebene konfiguriert sind, die negative Auswirkungen auf Ihre App hätten, können Sie das IIS-Umschreibungsmodul für die App deaktivieren. Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Nicht unterstützte Features

Die im Lieferumfang von ASP.NET Core 2.x enthaltene Middleware unterstützt die folgenden Features des IIS-URL-Umschreibungsmoduls nicht:

* Ausgehende Regeln
* Benutzerdefinierte Servervariablen
* Platzhalter
* LogRewrittenUrl

#### <a name="supported-server-variables"></a>Unterstützte Servervariablen

Die Middleware unterstützt die folgenden Servervariablen für das IIS-URL-Umschreibungsmodul:

* CONTENT_LENGTH
* CONTENT_TYPE
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Sie können <xref:Microsoft.Extensions.FileProviders.IFileProvider> auch über <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> abrufen. Über diesen Ansatz können Sie flexibler einen Speicherort für die Dateien der Umschreibungsregeln auswählen. Vergewissern Sie sich, dass die Dateien zu den Umschreibungsregeln auf dem Server unter dem von Ihnen angegebenen Pfad bereitgestellt werden.
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Methodenbasierte Regel

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, um Ihre eigene Regellogik in einer Methode zu implementieren. `Add` macht <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> verfügbar, wodurch die Verwendung von <xref:Microsoft.AspNetCore.Http.HttpContext> in Ihrer Methode ermöglicht wird. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) bestimmt, wie die zusätzliche Pipelineverarbeitung erfolgt. Legen Sie den Wert auf eines der <xref:Microsoft.AspNetCore.Rewrite.RuleResult>-Felder fest, die in der folgenden Tabelle beschrieben sind:

| Umschreiben von Kontext – Ergebnisse               | Aktion                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| `RuleResult.ContinueRules` (Standardwert) | Regeln weiter anwenden.                                         |
| `RuleResult.EndResponse`             | Regeln nicht mehr anwenden und Antwort senden.                       |
| `RuleResult.SkipRemainingRules`      | Regeln nicht mehr anwenden, und den Kontext an die nächste Middleware senden. |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

In der Beispiel-App wird eine Methode dargestellt, die Anforderungen für Pfade weiterleitet, die auf *.xml* enden. Wenn eine Anforderung für `/file.xml` erfolgt, wird sie zu `/xmlfiles/file.xml` umgeleitet. Der Statuscode wird auf *301 – Permanent verschoben* festgelegt. Wenn vom Browser eine neue Anforderung für */xmlfiles/file.xml* gesendet wird, wird dem Client die Datei von Middleware für statische Dateien aus dem Ordner *wwwroot/xmlfiles* bereitgestellt. Legen Sie für eine Umleitung den Statuscode der Antwort explizit fest. Andernfalls wird ein Statuscode *200 – OK* zurückgegeben, und die Umleitung findet nicht auf dem Client statt.

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

Bei diesem Ansatz können auch Anforderungen erneut generiert werden. In der Beispiel-App wird demonstriert, wie der Pfad für eine beliebige Textdateianforderung umgeschrieben wird, um die Textdatei *file.txt* aus dem Ordner *wwwroot* bereitzustellen. Middleware für statische Dateien stellt die Datei bereit, die auf dem aktualisierten Anforderungspfad basiert:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

*RewriteRules.cs* :

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>IRule-basierte Regel

Um Regellogik in einer Klasse zu nutzen, die die <xref:Microsoft.AspNetCore.Rewrite.IRule>-Schnittstelle implementiert, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>. Wenn Sie `IRule` verwenden, können Sie flexibler entscheiden, ob Sie eine methodenbasierte Regel verwenden möchten. Ihre Implementierungsklasse kann einen Konstruktor enthalten, wodurch Sie Parameter für die <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*>-Methode übergeben können.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

Die Werte für die Parameter in der Beispiel-App für `extension` und `newPath` werden auf verschiedene Bedingungen geprüft. `extension` muss einen Wert enthalten, der *.png* , *.jpg* oder *.gif* entspricht. Wenn `newPath` nicht gültig ist, wird <xref:System.ArgumentException> nicht ausgelöst. Wenn eine Anforderung für *image.png* erfolgt, wird sie zu `/png-images/image.png` umgeleitet. Wenn eine Anforderung für *image.jpg* erfolgt, wird die zu `/jpg-images/image.jpg` umgeleitet. Der Statuscode ist auf *301 – Permanent verschoben* festgelegt, und `context.Result` erhält die Anweisung, Verarbeitungsregeln nicht mehr anzuwenden und die Antwort zu senden.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

Ursprüngliche Anforderung: `/image.png`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten der image.png-Datei nachverfolgen](url-rewriting/_static/add_redirect_png_requests.png)

Ursprüngliche Anforderung: `/image.jpg`

![Browserfenster mit Entwicklertools, die die Anforderungen und Antworten der image.jpg-Datei nachverfolgen](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>RegEx-Beispiele

| Ziel | RegEx-Zeichenfolge und<br>übereinstimmendes Beispiel | Ersetzungszeichenfolge und<br>Ausgabebeispiel |
| ---- | ------------------------------- | -------------------------------------- |
| Umschreiben des Pfads in die Abfragezeichenfolge | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Entfernen des nachgestellten Schrägstrichs | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Erzwingen des nachgestellten Schrägstrichs | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Vermeiden des Umschreibens von bestimmten Anforderungen | `^(.*)(?<!\.axd)$` oder `^(?!.*\.axd$)(.*)$`<br>Ja: `/resource.htm`<br>Nein: `/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Ändern der Anordnung von URL-Segmenten | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Ersetzen von URL-Segmenten | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Reguläre Ausdrücke in .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/articles/standard/base-types/quick-ref)
* [Apache: „mod_rewrite“](https://httpd.apache.org/docs/2.4/rewrite/)
* [Using Url Rewrite Module 2.0 (for IIS) (Verwenden des URL-Umschreibungsmoduls 2.0 (für IIS))](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [URL Rewrite Module Configuration Reference (Konfigurationsreferenz für das URL-Umschreibungsmodul)](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Forum zum IIS-URL-Umschreibungsmodul](https://forums.iis.net/1152.aspx)
* [Einfache Strukturierung von URLs](https://support.google.com/webmasters/answer/76329?hl=en)
* [10 URL Rewriting Tips and Tricks (10 Tipps zum Umschreiben von URL)](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [To slash or not to slash (Schrägstriche setzen oder nicht setzen)](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
