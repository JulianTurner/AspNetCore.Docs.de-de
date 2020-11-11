---
title: ASP.NET Core-Web-API-Dokumentation mit Swagger/OpenAPI
author: RicoSuter
description: Dieses Tutorial enthält eine exemplarische Vorgehensweise für das Hinzufügen von Swagger, um Dokumentationen und Hilfeseiten für eine Web-API-App zu generieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062453"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="17238-103">ASP.NET Core-Web-API-Dokumentation mit Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="17238-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="17238-104">Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="17238-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="17238-105">Bei Swagger (OpenAPI) handelt es sich um eine sprachunabhängige Spezifikation für das Beschreiben von REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="17238-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="17238-106">Sowohl Computer als auch Menschen können so die Funktionen der REST-API verstehen, ohne Direktzugriff auf den Quellcode zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="17238-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="17238-107">Die Hauptziele sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="17238-107">Its main goals are to:</span></span>

* <span data-ttu-id="17238-108">Minimieren des Arbeitsaufwands, der zum Verbinden von getrennten Diensten erforderlich ist</span><span class="sxs-lookup"><span data-stu-id="17238-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="17238-109">Verringern des Zeitaufwands, der für die genaue Dokumentation eines Diensts erforderlich ist</span><span class="sxs-lookup"><span data-stu-id="17238-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="17238-110">Die zwei OpenAPI-Hauptimplementierungen für .NET sind [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) und [NSwag](https://github.com/RicoSuter/NSwag). Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="17238-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="17238-111">Erste Schritte mit Swashbuckle und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17238-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="17238-112">Erste Schritte mit NSwag und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17238-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="17238-113">OpenApi vs. Swagger</span><span class="sxs-lookup"><span data-stu-id="17238-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="17238-114">Das Swagger-Projekt wurde 2015 an die OpenAPI-Initiative übergeben. Dort wird es nun als „OpenAPI“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="17238-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="17238-115">Beide Namen können austauschbar verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="17238-115">Both names are used interchangeably.</span></span> <span data-ttu-id="17238-116">OpenAPI bezieht sich jedoch auf die Spezifikation.</span><span class="sxs-lookup"><span data-stu-id="17238-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="17238-117">Swagger bezieht sich auf die Familie der Open-Source- und kommerziellen Produkte von SmartBear, die die OpenAPI-Spezifikation verwenden.</span><span class="sxs-lookup"><span data-stu-id="17238-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="17238-118">Verwandte Open-Source-Produkte wie [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator) gehören auch zur Swagger-Familie, obwohl sie nicht von SmartBear veröffentlicht wurden.</span><span class="sxs-lookup"><span data-stu-id="17238-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="17238-119">Kurz gesagt:</span><span class="sxs-lookup"><span data-stu-id="17238-119">In short:</span></span>

* <span data-ttu-id="17238-120">OpenAPI ist eine Spezifikation.</span><span class="sxs-lookup"><span data-stu-id="17238-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="17238-121">Swagger ist ein Tool, das die OpenAPI-Spezifikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="17238-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="17238-122">Beispiele sind OpenAPIGenerator und SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="17238-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="17238-123">OpenAPI-Spezifikation (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="17238-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="17238-124">Die OpenAPI-Spezifikation ist ein Dokument, in dem die Funktionen Ihrer API beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="17238-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="17238-125">Das Dokument basiert auf der XML-Datei und den Attributanmerkungen innerhalb der Controller und Modelle.</span><span class="sxs-lookup"><span data-stu-id="17238-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="17238-126">Es handelt sich dabei um den Hauptteil des OpenAPI-Flows, und dieser wird für die Steuerung von Tools wie SwaggerUI verwendet.</span><span class="sxs-lookup"><span data-stu-id="17238-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="17238-127">Standardmäßig lautet der Name *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="17238-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="17238-128">Hier finden Sie ein Beispiel der OpenAPI-Spezifikation, das aus Gründen der Übersichtlichkeit reduziert wurde:</span><span class="sxs-lookup"><span data-stu-id="17238-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="17238-129">Swagger-Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="17238-129">Swagger UI</span></span>

<span data-ttu-id="17238-130">Die [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) ist eine webbasierte Benutzeroberfläche, die anhand der generierten OpenAPI-Spezifikation Informationen über den Dienst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="17238-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="17238-131">Swashbuckle und NSwag enthalten eine eingebettete Version der Swagger-Benutzeroberfläche, sodass diese in Ihrer ASP.NET Core-App mithilfe eines Registrierungsaufrufs für die Middleware gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="17238-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="17238-132">Die Webbenutzeroberfläche sieht folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="17238-132">The web UI looks like this:</span></span>

![Swagger-Benutzeroberfläche](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="17238-134">Jede öffentliche Aktionsmethode in Ihren Controllern kann über die Benutzeroberfläche getestet werden.</span><span class="sxs-lookup"><span data-stu-id="17238-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="17238-135">Klicken Sie auf einen Methodennamen, um den Abschnitt zu erweitern.</span><span class="sxs-lookup"><span data-stu-id="17238-135">Select a method name to expand the section.</span></span> <span data-ttu-id="17238-136">Fügen Sie die erforderlichen Parameter hinzu, und klicken Sie auf **Probieren Sie es aus!** .</span><span class="sxs-lookup"><span data-stu-id="17238-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Beispiel für einen Swagger-GET-Test](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="17238-138">Die für den Screenshot verwendete Version der Swagger-Benutzeroberfläche ist Version 2.</span><span class="sxs-lookup"><span data-stu-id="17238-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="17238-139">Ein Beispiel für Version 3 finden Sie unter [Pet store example (Beispiel für eine Tierhandlung)](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="17238-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="17238-140">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="17238-140">Next steps</span></span>

* [<span data-ttu-id="17238-141">Erste Schritte mit Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="17238-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="17238-142">Erste Schritte mit NSwag</span><span class="sxs-lookup"><span data-stu-id="17238-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
