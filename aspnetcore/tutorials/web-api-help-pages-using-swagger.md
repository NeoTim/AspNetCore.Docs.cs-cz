---
title: Stránky s OpenAPI s webovým rozhraním API pomocí Swagger/ASP.NET Core
author: RicoSuter
description: V tomto kurzu se dozvíte, jak přidat Swagger, který vygeneruje dokumentaci a stránky s nápovědu pro aplikaci Web API.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
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
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021975"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="253b5-103">Stránky s OpenAPI s webovým rozhraním API pomocí Swagger/ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="253b5-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="253b5-104">Od [Christoph Nienaber](https://twitter.com/zuckerthoben) a [Portoriko Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="253b5-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="253b5-105">Při využívání webového rozhraní API může být porozumění jeho různým metodám pro vývojáře náročné.</span><span class="sxs-lookup"><span data-stu-id="253b5-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="253b5-106">[Swagger](https://swagger.io/), označovaný také jako [openapi](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránek s nápovědu pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="253b5-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="253b5-107">Poskytuje výhody, jako je interaktivní dokumentace, generování klientské sady SDK a zjistitelnost rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="253b5-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="253b5-108">V tomto článku se prezentují implementace [swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) a [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:</span><span class="sxs-lookup"><span data-stu-id="253b5-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="253b5-109">**Swashbuckle. AspNetCore** je open source projekt pro generování dokumentů swagger pro ASP.NET Core webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="253b5-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="253b5-110">**NSwag** je další Open source projekt pro generování dokumentů Swagger a INTEGROVÁNÍ [uživatelského rozhraní Swagger](https://swagger.io/swagger-ui/) nebo [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core webových rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="253b5-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="253b5-111">NSwag navíc nabízí přístup k vygenerování kódu klienta v jazyce C# a TypeScript pro vaše rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="253b5-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="253b5-112">Co je Swagger/OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="253b5-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="253b5-113">Swagger je specifikace nezávislá jazyka pro popis rozhraní [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API.</span><span class="sxs-lookup"><span data-stu-id="253b5-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="253b5-114">Projekt Swagger byl darován [openapi iniciativou](https://www.openapis.org/), kde se nyní označuje jako openapi.</span><span class="sxs-lookup"><span data-stu-id="253b5-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="253b5-115">Oba názvy se používají zaměnitelné; OpenAPI je ale upřednostňovaný.</span><span class="sxs-lookup"><span data-stu-id="253b5-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="253b5-116">Umožňuje počítačům i člověku pochopit možnosti služby bez přímého přístupu k implementaci (zdrojový kód, přístup k síti, dokumentace).</span><span class="sxs-lookup"><span data-stu-id="253b5-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="253b5-117">Jedním z cílů je minimalizace množství práce potřebné k připojení nepřidružených služeb.</span><span class="sxs-lookup"><span data-stu-id="253b5-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="253b5-118">Dalším cílem je zkrátit dobu potřebnou k přesnému zdokumentování služby.</span><span class="sxs-lookup"><span data-stu-id="253b5-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="253b5-119">Specifikace OpenAPI (openapi.jszapnuté)</span><span class="sxs-lookup"><span data-stu-id="253b5-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="253b5-120">Jádro toku OpenAPI je &mdash; ve výchozím nastavení specifikace dokumentu s názvem *openapi.js*.</span><span class="sxs-lookup"><span data-stu-id="253b5-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="253b5-121">Vygeneruje ho řetěz nástrojů OpenAPI (nebo implementace třetích stran) na základě vaší služby.</span><span class="sxs-lookup"><span data-stu-id="253b5-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="253b5-122">Popisuje možnosti vašeho rozhraní API a přístup k němu pomocí protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="253b5-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="253b5-123">Řídí uživatelské rozhraní Swagger a používá ho řetěz nástrojů k povolení zjišťování a generování kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="253b5-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="253b5-124">Tady je příklad specifikace OpenAPI, která se snižuje pro zkrácení:</span><span class="sxs-lookup"><span data-stu-id="253b5-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="253b5-125">Uživatelské rozhraní Swagger</span><span class="sxs-lookup"><span data-stu-id="253b5-125">Swagger UI</span></span>

<span data-ttu-id="253b5-126">[Uživatelské rozhraní Swagger](https://swagger.io/swagger-ui/) nabízí webové uživatelské rozhraní, které poskytuje informace o službě pomocí vygenerované specifikace openapi.</span><span class="sxs-lookup"><span data-stu-id="253b5-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="253b5-127">Swashbuckle i NSwag obsahují vloženou verzi uživatelského rozhraní Swagger, aby se mohla hostovat ve vaší aplikaci ASP.NET Core pomocí volání registrace middlewaru.</span><span class="sxs-lookup"><span data-stu-id="253b5-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="253b5-128">Webové uživatelské rozhraní vypadá takto:</span><span class="sxs-lookup"><span data-stu-id="253b5-128">The web UI looks like this:</span></span>

![Uživatelské rozhraní Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="253b5-130">Každou metodu veřejné akce v řadičích můžete testovat z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="253b5-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="253b5-131">Kliknutím na název metody rozbalíte oddíl.</span><span class="sxs-lookup"><span data-stu-id="253b5-131">Click a method name to expand the section.</span></span> <span data-ttu-id="253b5-132">Přidejte potřebné parametry a klikněte na tlačítko **vyzkoušet!**.</span><span class="sxs-lookup"><span data-stu-id="253b5-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Příklad získání testu Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="253b5-134">Verze uživatelského rozhraní Swagger použitá pro snímky obrazovky je verze 2.</span><span class="sxs-lookup"><span data-stu-id="253b5-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="253b5-135">Příklad verze 3 naleznete v tématu [Petstore example](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="253b5-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="253b5-136">Další kroky</span><span class="sxs-lookup"><span data-stu-id="253b5-136">Next steps</span></span>

* [<span data-ttu-id="253b5-137">Začínáme se službou Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="253b5-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="253b5-138">Začínáme se službou NSwag</span><span class="sxs-lookup"><span data-stu-id="253b5-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
