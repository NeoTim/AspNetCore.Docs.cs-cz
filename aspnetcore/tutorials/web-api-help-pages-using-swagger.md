---
title: Stránky nápovědy ASP.NET Core webového rozhraní API ve Swaggeru / OpenAPI
author: RicoSuter
description: Tento kurz poskytuje návod k přidávání Swagger generovat dokumentaci a stránky aplikace webového rozhraní API nápovědy.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658471"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core webové rozhraní API nápovědy stránky ve Swaggeru / OpenAPI

Od [Christoph Nienaber](https://twitter.com/zuckerthoben) a [Portoriko Suter](https://blog.rsuter.com/)

Při využívání webové rozhraní API, může vývojář náročné Principy jeho různé metody. [Swagger](https://swagger.io/), označovaný také jako [openapi](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránek s nápovědu pro webová rozhraní API. Poskytuje výhody, jako je například interaktivní dokumentace, generování klientských sad SDK a rozhraní API zjistitelnost.

V tomto článku se prezentují implementace [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) a [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:

* **Swashbuckle. AspNetCore** je open source projekt pro generování dokumentů swagger pro ASP.NET Core webových rozhraní API.

* **NSwag** je další Open source projekt pro generování dokumentů Swagger a INTEGROVÁNÍ [uživatelského rozhraní Swagger](https://swagger.io/swagger-ui/) nebo [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core webových rozhraní API. Kromě toho službou NSwag nabízí přístupy ke generování jazyka C# a TypeScript klientský kód pro vaše rozhraní API.

## <a name="what-is-swagger--openapi"></a>Co je Swagger / OpenAPI?

Swagger je specifikace nezávislá jazyka pro popis rozhraní [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API. Projekt Swagger byl darován [openapi iniciativou](https://www.openapis.org/), kde se nyní označuje jako openapi. Oba názvy jsou používány Zaměnitelně; je však upřednostňované OpenAPI. Umožňuje počítačům a lidí na široké možnosti produktu služby bez jakékoli přímý přístup k implementaci (zdrojový kód, přístup k síti, dokumentace ke službě). Chcete-li minimalizovat množství práce potřebné pro připojení služby zrušeným přidružením je jedním z cílů. Jiné cílem je omezit množství času potřebného k přesně dokumentu služby.

## <a name="swagger-specification-swaggerjson"></a>Specifikace swagger (swagger.json)

Jádrem toku Swagger je specifikace Swagger&mdash;ve výchozím nastavení dokument s názvem *Swagger. JSON*. Vygenerovala Swagger nástroj řetězu (nebo implementace jiných výrobců jeho) založené na vaši službu. Popisuje možnosti přístupu k němu přes HTTP a rozhraní API. Řídí uživatelské rozhraní Swagger a používá řetězce nástrojů na povolit generování kódu klienta a zjišťování. Tady je příklad specifikace Swagger pro zkrácení snížit:

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

## <a name="swagger-ui"></a>Uživatelské rozhraní swagger

[Uživatelské rozhraní Swagger](https://swagger.io/swagger-ui/) nabízí webové uživatelské rozhraní, které poskytuje informace o službě pomocí vygenerované specifikace Swagger. Swashbuckle a službou NSwag obsahovat vložený verzi uživatelské rozhraní Swagger, tak, že je možné hostovat ve vaší aplikaci ASP.NET Core pomocí volání registrace middlewaru. Webové uživatelské rozhraní vypadá takto:

![Uživatelské rozhraní swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Každá metoda public akce ve vašich kontrolerech můžete otestovat v uživatelském rozhraní. Klikněte na název metody, rozbalte v části. Přidejte potřebné parametry a klikněte na tlačítko **vyzkoušet!** .

![Příklad Swagger získat testu](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Uživatelské rozhraní Swagger verze použitá pro snímky obrazovky je verze 2. Příklad verze 3 naleznete v tématu [Petstore example](https://petstore.swagger.io/).

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Začínáme se službou NSwag](xref:tutorials/get-started-with-nswag)
