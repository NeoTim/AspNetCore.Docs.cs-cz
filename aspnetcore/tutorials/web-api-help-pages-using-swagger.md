---
title: ASP.NET stránky nápovědy Core Web API s Swagger / OpenAPI
author: RicoSuter
description: Tento kurz poskytuje návod přidání Swagger pro generování dokumentace a stránky nápovědy pro aplikaci webového rozhraní API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658471"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Stránky nápovědy pro webové rozhraní API s Swagger / OpenAPI

[Christoph Nienaber](https://twitter.com/zuckerthoben) a [Rico Suter](https://blog.rsuter.com/)

Při využívání webového rozhraní API může být pochopení jeho různých metod pro vývojáře náročné. [Swagger](https://swagger.io/), také známý jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránky nápovědy pro webová rozhraní API. Poskytuje výhody, jako je interaktivní dokumentace, generování sady SDK klienta a zjistitelnost rozhraní API.

V tomto článku jsou prezentovány implementace [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) a [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:

* **Swashbuckle.AspNetCore** je open source projekt pro generování dokumentů Swagger pro ASP.NET core web ová rozhraní API.

* **NSwag** je další open source projekt pro generování dokumentů Swagger a integraci [Swagger UI](https://swagger.io/swagger-ui/) nebo [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core webových API. NSwag navíc nabízí přístupy ke generování klientského kódu Jazyka C# a TypeScript pro vaše rozhraní API.

## <a name="what-is-swagger--openapi"></a>Co je Swagger / OpenAPI?

Swagger je specifikace pro jazyk ověnčený pro popis rozhraní [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API. Projekt Swagger byl darován [openapi iniciativě](https://www.openapis.org/), kde je nyní označován jako OpenAPI. Oba názvy jsou používány zaměnitelně; openapi je však upřednostňováno. Umožňuje počítačům i lidem porozumět možnostem služby bez přímého přístupu k implementaci (zdrojový kód, přístup k síti, dokumentace). Jedním z cílů je minimalizovat množství práce potřebné k připojení odpojené služby. Dalším cílem je zkrátit dobu potřebnou k přesnému zdokumentování služby.

## <a name="swagger-specification-swaggerjson"></a>Specifikace Swagger (swagger.json)

Jádrem toku Swagger je specifikace&mdash;Swagger ve výchozím nastavení, dokument s názvem *swagger.json*. Je generován a nástroj Swagger řetězce (nebo implementace třetích stran) na základě vaší služby. Popisuje možnosti rozhraní API a jak k němu přistupovat pomocí protokolu HTTP. Řídí swagger ui a používá se v řetězci nástrojů povolit zjišťování a generování kódu klienta. Zde je příklad specifikace Swagger, snížená pro stručnost:

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Swagger UI

[Uživatelské uživatelské panelsa](https://swagger.io/swagger-ui/) nabízí webové uživatelské uživatelské místo, které poskytuje informace o službě pomocí generované specifikace Swagger. Swashbuckle i NSwag obsahují vestavěnou verzi ui Swagger, takže může být hostována ve vaší aplikaci ASP.NET Core pomocí registračního hovoru middlewaru. Webové uživatelské uživatelské uživatelské stránky vypadají takto:

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Každá metoda veřejné akce ve vašich řadičích může být testována z ui. Kliknutím na název metody rozbalte oddíl. Přidejte všechny potřebné parametry a klepněte na tlačítko **Vyzkoušet!**.

![Příklad testu Swagger GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Swagger UI verze použitá pro screenshoty je verze 2. Příklad verze 3 najdete v [tématu Petstore example](https://petstore.swagger.io/).

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Začínáme se službou NSwag](xref:tutorials/get-started-with-nswag)
