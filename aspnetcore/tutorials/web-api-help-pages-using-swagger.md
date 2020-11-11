---
title: ASP.NET Core-Web-API-Dokumentation mit Swagger/OpenAPI
author: RicoSuter
description: Dieses Tutorial enthält eine exemplarische Vorgehensweise für das Hinzufügen von Swagger, um Dokumentationen und Hilfeseiten für eine Web-API-App zu generieren.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062453"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>ASP.NET Core-Web-API-Dokumentation mit Swagger/OpenAPI

Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://blog.rsuter.com/)

Bei Swagger (OpenAPI) handelt es sich um eine sprachunabhängige Spezifikation für das Beschreiben von REST-APIs. Sowohl Computer als auch Menschen können so die Funktionen der REST-API verstehen, ohne Direktzugriff auf den Quellcode zu benötigen. Die Hauptziele sind die folgenden:

* Minimieren des Arbeitsaufwands, der zum Verbinden von getrennten Diensten erforderlich ist
* Verringern des Zeitaufwands, der für die genaue Dokumentation eines Diensts erforderlich ist

Die zwei OpenAPI-Hauptimplementierungen für .NET sind [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) und [NSwag](https://github.com/RicoSuter/NSwag). Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Swashbuckle und ASP.NET Core](xref:tutorials/get-started-with-swashbuckle)
* [Erste Schritte mit NSwag und ASP.NET Core](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi vs. Swagger

Das Swagger-Projekt wurde 2015 an die OpenAPI-Initiative übergeben. Dort wird es nun als „OpenAPI“ bezeichnet. Beide Namen können austauschbar verwendet werden. OpenAPI bezieht sich jedoch auf die Spezifikation. Swagger bezieht sich auf die Familie der Open-Source- und kommerziellen Produkte von SmartBear, die die OpenAPI-Spezifikation verwenden. Verwandte Open-Source-Produkte wie [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator) gehören auch zur Swagger-Familie, obwohl sie nicht von SmartBear veröffentlicht wurden.

Kurz gesagt:

* OpenAPI ist eine Spezifikation.
* Swagger ist ein Tool, das die OpenAPI-Spezifikation verwendet. Beispiele sind OpenAPIGenerator und SwaggerUI.

## <a name="openapi-specification-openapijson"></a>OpenAPI-Spezifikation (openapi.json)

Die OpenAPI-Spezifikation ist ein Dokument, in dem die Funktionen Ihrer API beschrieben werden. Das Dokument basiert auf der XML-Datei und den Attributanmerkungen innerhalb der Controller und Modelle. Es handelt sich dabei um den Hauptteil des OpenAPI-Flows, und dieser wird für die Steuerung von Tools wie SwaggerUI verwendet. Standardmäßig lautet der Name *openapi.json*. Hier finden Sie ein Beispiel der OpenAPI-Spezifikation, das aus Gründen der Übersichtlichkeit reduziert wurde:

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

## <a name="swagger-ui"></a>Swagger-Benutzeroberfläche

Die [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) ist eine webbasierte Benutzeroberfläche, die anhand der generierten OpenAPI-Spezifikation Informationen über den Dienst bereitstellt. Swashbuckle und NSwag enthalten eine eingebettete Version der Swagger-Benutzeroberfläche, sodass diese in Ihrer ASP.NET Core-App mithilfe eines Registrierungsaufrufs für die Middleware gehostet werden kann. Die Webbenutzeroberfläche sieht folgendermaßen aus:

![Swagger-Benutzeroberfläche](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Jede öffentliche Aktionsmethode in Ihren Controllern kann über die Benutzeroberfläche getestet werden. Klicken Sie auf einen Methodennamen, um den Abschnitt zu erweitern. Fügen Sie die erforderlichen Parameter hinzu, und klicken Sie auf **Probieren Sie es aus!** .

![Beispiel für einen Swagger-GET-Test](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Die für den Screenshot verwendete Version der Swagger-Benutzeroberfläche ist Version 2. Ein Beispiel für Version 3 finden Sie unter [Pet store example (Beispiel für eine Tierhandlung)](https://petstore.swagger.io/).

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Erste Schritte mit NSwag](xref:tutorials/get-started-with-nswag)
