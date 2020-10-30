---
title: Konfiguration zu ASP.net Core migrieren
author: ardalis
description: Erfahren Sie, wie Sie die Konfiguration von einem ASP.NET-MVC-Projekt zu einem ASP.net Core MVC-Projekt migrieren.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/configuration
ms.openlocfilehash: d84204c8c791bfaf36432462cde3a42c294c7966
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059792"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="43d91-103">Konfiguration zu ASP.net Core migrieren</span><span class="sxs-lookup"><span data-stu-id="43d91-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="43d91-104">Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="43d91-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="43d91-105">Im vorherigen Artikel haben wir begonnen, [ein ASP.NET MVC-Projekt zu ASP.net Core MVC zu migrieren](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="43d91-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="43d91-106">In diesem Artikel wird die Konfiguration migriert.</span><span class="sxs-lookup"><span data-stu-id="43d91-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="43d91-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43d91-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="43d91-108">Einrichtungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="43d91-108">Setup configuration</span></span>

<span data-ttu-id="43d91-109">ASP.net Core verwendet nicht mehr die Dateien " *Global. asax* " und " *web.config* ", die in früheren Versionen von ASP.NET verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="43d91-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="43d91-110">In früheren Versionen von ASP.net wurde die Anwendungsstart Logik in eine- `Application_StartUp` Methode in " *Global. asax* " eingefügt.</span><span class="sxs-lookup"><span data-stu-id="43d91-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax* .</span></span> <span data-ttu-id="43d91-111">Später wurde in ASP.NET MVC eine *Startup.cs* -Datei in das Stammverzeichnis des Projekts eingefügt. und es wurde aufgerufen, als die Anwendung gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="43d91-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="43d91-112">ASP.net Core hat diesen Ansatz vollständig übernommen, indem er die gesamte Start Logik in der *Startup.cs* -Datei platziert hat.</span><span class="sxs-lookup"><span data-stu-id="43d91-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="43d91-113">Die *web.config* Datei wurde auch in ASP.net Core ersetzt.</span><span class="sxs-lookup"><span data-stu-id="43d91-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="43d91-114">Die Konfiguration selbst kann nun im Rahmen des in *Startup.cs* beschriebenen Verfahrens zum Starten der Anwendung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="43d91-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs* .</span></span> <span data-ttu-id="43d91-115">Die Konfiguration kann weiterhin XML-Dateien verwenden, aber in der Regel ASP.net Core-Projekte Konfigurationswerte in eine JSON-formatierte Datei, z *:::no-loc(appsettings.json):::* . b..</span><span class="sxs-lookup"><span data-stu-id="43d91-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="43d91-116">ASP.net Core Konfigurationssystem kann auch problemlos auf Umgebungsvariablen zugreifen, die einen sichereren [und stabileren Speicherort](xref:security/app-secrets) für Umgebungs spezifische Werte bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="43d91-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="43d91-117">Dies gilt insbesondere für Geheimnisse wie Verbindungs Zeichenfolgen und API-Schlüssel, die nicht in die Quell Code Verwaltung eingecheckt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="43d91-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="43d91-118">Weitere Informationen zur Konfiguration in ASP.net Core finden Sie unter [Konfiguration](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="43d91-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="43d91-119">In diesem Artikel beginnen wir mit dem teilweise migrierten ASP.net Core Projekt aus [dem vorherigen Artikel](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="43d91-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="43d91-120">Fügen Sie der *Startup.cs* -Datei, die sich im Stammverzeichnis des Projekts befindet, den folgenden Konstruktor und die folgende Eigenschaft hinzu, um die Konfiguration einzurichten:</span><span class="sxs-lookup"><span data-stu-id="43d91-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="43d91-121">Beachten Sie, dass die *Startup.cs* -Datei zu diesem Zeitpunkt nicht kompiliert werden kann, da wir weiterhin die folgende Anweisung hinzufügen müssen `using` :</span><span class="sxs-lookup"><span data-stu-id="43d91-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="43d91-122">Fügen Sie dem Stamm *:::no-loc(appsettings.json):::* des Projekts mithilfe der entsprechenden Element Vorlage eine Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="43d91-122">Add an *:::no-loc(appsettings.json):::* file to the root of the project using the appropriate item template:</span></span>

![AppSettings-JSON hinzufügen](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="43d91-124">Konfigurationseinstellungen von web.config migrieren</span><span class="sxs-lookup"><span data-stu-id="43d91-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="43d91-125">Das ASP.NET-MVC-Projekt enthielt die erforderliche Daten bankverbindungs Zeichenfolge in *web.config* im- `<connectionStrings>` Element.</span><span class="sxs-lookup"><span data-stu-id="43d91-125">Our ASP.NET MVC project included the required database connection string in *web.config* , in the `<connectionStrings>` element.</span></span> <span data-ttu-id="43d91-126">In unserem ASP.net Core Projekt werden diese Informationen in der *:::no-loc(appsettings.json):::* Datei gespeichert.</span><span class="sxs-lookup"><span data-stu-id="43d91-126">In our ASP.NET Core project, we are going to store this information in the *:::no-loc(appsettings.json):::* file.</span></span> <span data-ttu-id="43d91-127">Öffnen *:::no-loc(appsettings.json):::* Sie, und beachten Sie, dass Sie bereits Folgendes umfasst:</span><span class="sxs-lookup"><span data-stu-id="43d91-127">Open *:::no-loc(appsettings.json):::* , and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/:::no-loc(appsettings.json):::?highlight=4)]

<span data-ttu-id="43d91-128">Ändern Sie in der oben gezeigten markierten Zeile den Namen der Datenbank von **_CHANGE_ME** in den Namen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="43d91-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="43d91-129">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="43d91-129">Summary</span></span>

<span data-ttu-id="43d91-130">ASP.net Core platziert alle Start Logik für die Anwendung in einer einzelnen Datei, in der die erforderlichen Dienste und Abhängigkeiten definiert und konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="43d91-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="43d91-131">Sie ersetzt die *web.config* Datei durch ein flexibles Konfigurations Feature, das eine Vielzahl von Dateiformaten, z. b. JSON, und Umgebungsvariablen nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="43d91-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
