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
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="270a6-103">ASP.NET Stránky nápovědy pro webové rozhraní API s Swagger / OpenAPI</span><span class="sxs-lookup"><span data-stu-id="270a6-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="270a6-104">[Christoph Nienaber](https://twitter.com/zuckerthoben) a [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="270a6-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="270a6-105">Při využívání webového rozhraní API může být pochopení jeho různých metod pro vývojáře náročné.</span><span class="sxs-lookup"><span data-stu-id="270a6-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="270a6-106">[Swagger](https://swagger.io/), také známý jako [OpenAPI](https://www.openapis.org/), řeší problém generování užitečné dokumentace a stránky nápovědy pro webová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="270a6-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="270a6-107">Poskytuje výhody, jako je interaktivní dokumentace, generování sady SDK klienta a zjistitelnost rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="270a6-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="270a6-108">V tomto článku jsou prezentovány implementace [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) a [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:</span><span class="sxs-lookup"><span data-stu-id="270a6-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="270a6-109">**Swashbuckle.AspNetCore** je open source projekt pro generování dokumentů Swagger pro ASP.NET core web ová rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="270a6-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="270a6-110">**NSwag** je další open source projekt pro generování dokumentů Swagger a integraci [Swagger UI](https://swagger.io/swagger-ui/) nebo [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core webových API.</span><span class="sxs-lookup"><span data-stu-id="270a6-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="270a6-111">NSwag navíc nabízí přístupy ke generování klientského kódu Jazyka C# a TypeScript pro vaše rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="270a6-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="270a6-112">Co je Swagger / OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="270a6-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="270a6-113">Swagger je specifikace pro jazyk ověnčený pro popis rozhraní [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API.</span><span class="sxs-lookup"><span data-stu-id="270a6-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="270a6-114">Projekt Swagger byl darován [openapi iniciativě](https://www.openapis.org/), kde je nyní označován jako OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="270a6-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="270a6-115">Oba názvy jsou používány zaměnitelně; openapi je však upřednostňováno.</span><span class="sxs-lookup"><span data-stu-id="270a6-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="270a6-116">Umožňuje počítačům i lidem porozumět možnostem služby bez přímého přístupu k implementaci (zdrojový kód, přístup k síti, dokumentace).</span><span class="sxs-lookup"><span data-stu-id="270a6-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="270a6-117">Jedním z cílů je minimalizovat množství práce potřebné k připojení odpojené služby.</span><span class="sxs-lookup"><span data-stu-id="270a6-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="270a6-118">Dalším cílem je zkrátit dobu potřebnou k přesnému zdokumentování služby.</span><span class="sxs-lookup"><span data-stu-id="270a6-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="270a6-119">Specifikace Swagger (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="270a6-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="270a6-120">Jádrem toku Swagger je specifikace&mdash;Swagger ve výchozím nastavení, dokument s názvem *swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="270a6-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="270a6-121">Je generován a nástroj Swagger řetězce (nebo implementace třetích stran) na základě vaší služby.</span><span class="sxs-lookup"><span data-stu-id="270a6-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="270a6-122">Popisuje možnosti rozhraní API a jak k němu přistupovat pomocí protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="270a6-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="270a6-123">Řídí swagger ui a používá se v řetězci nástrojů povolit zjišťování a generování kódu klienta.</span><span class="sxs-lookup"><span data-stu-id="270a6-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="270a6-124">Zde je příklad specifikace Swagger, snížená pro stručnost:</span><span class="sxs-lookup"><span data-stu-id="270a6-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="270a6-125">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="270a6-125">Swagger UI</span></span>

<span data-ttu-id="270a6-126">[Uživatelské uživatelské panelsa](https://swagger.io/swagger-ui/) nabízí webové uživatelské uživatelské místo, které poskytuje informace o službě pomocí generované specifikace Swagger.</span><span class="sxs-lookup"><span data-stu-id="270a6-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="270a6-127">Swashbuckle i NSwag obsahují vestavěnou verzi ui Swagger, takže může být hostována ve vaší aplikaci ASP.NET Core pomocí registračního hovoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="270a6-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="270a6-128">Webové uživatelské uživatelské uživatelské stránky vypadají takto:</span><span class="sxs-lookup"><span data-stu-id="270a6-128">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="270a6-130">Každá metoda veřejné akce ve vašich řadičích může být testována z ui.</span><span class="sxs-lookup"><span data-stu-id="270a6-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="270a6-131">Kliknutím na název metody rozbalte oddíl.</span><span class="sxs-lookup"><span data-stu-id="270a6-131">Click a method name to expand the section.</span></span> <span data-ttu-id="270a6-132">Přidejte všechny potřebné parametry a klepněte na tlačítko **Vyzkoušet!**.</span><span class="sxs-lookup"><span data-stu-id="270a6-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Příklad testu Swagger GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="270a6-134">Swagger UI verze použitá pro screenshoty je verze 2.</span><span class="sxs-lookup"><span data-stu-id="270a6-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="270a6-135">Příklad verze 3 najdete v [tématu Petstore example](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="270a6-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="270a6-136">Další kroky</span><span class="sxs-lookup"><span data-stu-id="270a6-136">Next steps</span></span>

* [<span data-ttu-id="270a6-137">Začínáme se službou Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="270a6-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="270a6-138">Začínáme se službou NSwag</span><span class="sxs-lookup"><span data-stu-id="270a6-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
