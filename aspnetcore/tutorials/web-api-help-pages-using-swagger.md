---
title: Stránky s OpenAPI s webovým rozhraním API pomocí Swagger/ASP.NET Core
author: RicoSuter
description: V tomto kurzu se dozvíte, jak přidat Swagger, který vygeneruje dokumentaci a stránky s nápovědu pro aplikaci Web API.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: c40aede044c78122a9057613f0eece9acf84df7b
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633991"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Stránky s OpenAPI s webovým rozhraním API pomocí Swagger/ASP.NET Core

Od [Christoph Nienaber](https://twitter.com/zuckerthoben) a [Portoriko Suter](https://blog.rsuter.com/)

Při využívání webového rozhraní API může být porozumění jeho různým metodám pro vývojáře náročné. [Swagger](https://swagger.io/), označovaný také jako [openapi](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránek s nápovědu pro webová rozhraní API. Poskytuje výhody, jako je interaktivní dokumentace, generování klientské sady SDK a zjistitelnost rozhraní API.

V tomto článku se prezentují implementace [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) a [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:

* **Swashbuckle. AspNetCore** je open source projekt pro generování dokumentů swagger pro ASP.NET Core webových rozhraní API.

* **NSwag** je další Open source projekt pro generování dokumentů Swagger a INTEGROVÁNÍ [uživatelského rozhraní Swagger](https://swagger.io/swagger-ui/) nebo [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core webových rozhraní API. NSwag navíc nabízí přístup k vygenerování kódu klienta v jazyce C# a TypeScript pro vaše rozhraní API.

## <a name="what-is-swagger--openapi"></a>Co je Swagger/OpenAPI?

Swagger je specifikace nezávislá jazyka pro popis rozhraní [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API. Projekt Swagger byl darován [openapi iniciativou](https://www.openapis.org/), kde se nyní označuje jako openapi. Oba názvy se používají zaměnitelné; OpenAPI je ale upřednostňovaný. Umožňuje počítačům i člověku pochopit možnosti služby bez přímého přístupu k implementaci (zdrojový kód, přístup k síti, dokumentace). Jedním z cílů je minimalizace množství práce potřebné k připojení nepřidružených služeb. Dalším cílem je zkrátit dobu potřebnou k přesnému zdokumentování služby.

## <a name="openapi-specification-openapijson"></a>Specifikace OpenAPI (openapi.jszapnuté)

Jádro toku OpenAPI je &mdash; ve výchozím nastavení specifikace dokumentu s názvem *openapi.js*. Vygeneruje ho řetěz nástrojů OpenAPI (nebo implementace třetích stran) na základě vaší služby. Popisuje možnosti vašeho rozhraní API a přístup k němu pomocí protokolu HTTP. Řídí uživatelské rozhraní Swagger a používá ho řetěz nástrojů k povolení zjišťování a generování kódu klienta. Tady je příklad specifikace OpenAPI, která se snižuje pro zkrácení:

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

## <a name="swagger-ui"></a>Uživatelské rozhraní Swagger

[Uživatelské rozhraní Swagger](https://swagger.io/swagger-ui/) nabízí webové uživatelské rozhraní, které poskytuje informace o službě pomocí vygenerované specifikace openapi. Swashbuckle i NSwag obsahují vloženou verzi uživatelského rozhraní Swagger, aby se mohla hostovat ve vaší aplikaci ASP.NET Core pomocí volání registrace middlewaru. Webové uživatelské rozhraní vypadá takto:

![Uživatelské rozhraní Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Každou metodu veřejné akce v řadičích můžete testovat z uživatelského rozhraní. Kliknutím na název metody rozbalíte oddíl. Přidejte potřebné parametry a klikněte na tlačítko **vyzkoušet!**.

![Příklad získání testu Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Verze uživatelského rozhraní Swagger použitá pro snímky obrazovky je verze 2. Příklad verze 3 naleznete v tématu [Petstore example](https://petstore.swagger.io/).

## <a name="next-steps"></a>Další kroky

* [Začínáme se službou Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Začínáme se službou NSwag](xref:tutorials/get-started-with-nswag)
