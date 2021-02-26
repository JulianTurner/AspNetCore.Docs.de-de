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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552032"
---
```console
npm run release
```

<span data-ttu-id="7e8bc-101">Dieser Befehl generiert die clientseitigen Objekte an, die bereitgestellt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="7e8bc-102">Die Objekte werden im Ordner *wwwroot* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="7e8bc-103">Webpack hat die folgenden Aufgaben durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="7e8bc-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="7e8bc-104">Die Inhalte des Verzeichnis *wwwroot* wurden bereinigt.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="7e8bc-105">TypeScript wurde in JavaScript konvertiert. Dieser Prozess ist als *Transpilierung* bekannt.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="7e8bc-106">Der generierte JavaScript-Code wurde gekürzt, um die Dateigröße zu reduzieren. Dieser Prozess ist als *Minimierung* bekannt.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="7e8bc-107">Die verarbeiteten JavaScript-, CSS- und HTML-Dateien wurden aus *src* in das Verzeichnis *wwwroot* kopiert.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="7e8bc-108">Die folgenden Elemente wurden in die Datei *wwwroot/index.html* eingefügt:</span><span class="sxs-lookup"><span data-stu-id="7e8bc-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="7e8bc-109">Ein `<link>`-Tag, das auf die Datei *wwwroot/main.\<hash\>.css* verweist.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="7e8bc-110">Dieses Tag wird unmittelbar vor dem schließenden Tag `</head>` platziert.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="7e8bc-111">Ein `<script>`-Tag, das auf die minimierte Datei *wwwroot/main.\<hash\>.js* verweist.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="7e8bc-112">Dieses Tag wird unmittelbar vor dem schließenden Tag `</body>` platziert.</span><span class="sxs-lookup"><span data-stu-id="7e8bc-112">This tag is placed immediately before the closing `</body>` tag.</span></span>