---
title: Migrace z webového rozhraní API ASP.NET do ASP.NET Core
author: ardalis
description: Naučte se migrovat implementaci webového rozhraní API z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: migration/webapi
ms.openlocfilehash: c68cf83f427f53b110075168c6d5e4d021808782
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881144"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrace z webového rozhraní API ASP.NET do ASP.NET Core

[Scottem Addie](https://twitter.com/scott_addie) a [Steve Smith](https://ardalis.com/)

Webové rozhraní API ASP.NET 4. x je služba HTTP, která dosahuje široké škály klientů, včetně prohlížečů a mobilních zařízení. ASP.NET Core sjednocuje ASP.NET 4. x MVC a model aplikace webového rozhraní API do jednoduššího programovacího modelu označovaného jako ASP.NET Core MVC. Tento článek popisuje kroky potřebné k migraci z webového rozhraní API ASP.NET 4. x na ASP.NET Core MVC.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Kontrola projektu webového rozhraní API ASP.NET 4. x

Jako výchozí bod Tento článek používá projekt *ProductsApp* vytvořený v [Začínáme s webovým rozhraním API 2 pro ASP.NET](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). V tomto projektu je jednoduchý projekt webového rozhraní API ASP.NET 4. x nakonfigurovaný takto.

V *Global.asax.cs*je učiněno volání `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

Třída `WebApiConfig` se nachází ve *app_start* složce a má statickou `Register` metodu:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Tato třída konfiguruje [Směrování atributů](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), i když není ve skutečnosti použita v projektu. Také nakonfiguruje směrovací tabulku, která je používána webovým rozhraním API ASP.NET. V takovém případě webový rozhraní API ASP.NET 4. x očekává, že adresy URL budou odpovídat formátu `/api/{controller}/{id}``{id}` volitelné.

Projekt *ProductsApp* zahrnuje jeden kontroler. Kontroler dědí z `ApiController` a obsahuje dvě akce:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`Product` model používaný `ProductsController` je jednoduchá třída:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

Následující části demonstrují migraci projektu webového rozhraní API do ASP.NET Core MVC.

## <a name="create-destination-project"></a>Vytvořit cílový projekt

Proveďte následující kroky v aplikaci Visual Studio:

* Do **souboru** > **nový** > **projektu** > **Další typy projektů** > řešení sady **Visual Studio**. Vyberte **prázdné řešení**a pojmenujte řešení *WebAPIMigration*. Klikněte na tlačítko **OK**.
* Přidejte existující projekt *ProductsApp* do řešení.
* Přidejte do řešení nový projekt **webové aplikace ASP.NET Core** . V rozevíracím seznamu vyberte cílové rozhraní **.NET Core** a vyberte šablonu projektu **rozhraní API** . Pojmenujte projekt *ProductsCore*a klikněte na tlačítko **OK** .

Řešení teď obsahuje dva projekty. Následující části vysvětlují migraci obsahu projektu *ProductsApp* do projektu *ProductsCore* .

## <a name="migrate-configuration"></a>Migrace konfigurace

ASP.NET Core nepoužívá složku *app_start* nebo soubor *Global. asax* a v době publikování je přidán soubor *Web. config* . *Startup.cs* je náhradou za *Global. asax* a nachází se v kořenovém adresáři projektu. Třída `Startup` zpracovává všechny úlohy při spuštění aplikace. Další informace najdete v tématu <xref:fundamentals/startup>.

Ve ASP.NET Core MVC je směrování atributů standardně zahrnuté v případě, že je <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> voláno v `Startup.Configure`. Následující `UseMvc` volání nahradí *app_start soubor/webapiconfig.cs* projektu *ProductsApp* :

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrace modelů a řadičů

Zkopírujte přes kontroler projektu *ProductApp* a model, který používá. Postupujte podle těchto kroků:

1. Zkopírujte *Controllers/ProductsController. cs* z původního projektu na nový.
1. Zkopírujte celou složku *modely* z původního projektu do nového.
1. Změňte obory názvů kopírovaných souborů tak, aby odpovídaly novému názvu projektu (*ProductsCore*). Upravte také příkaz `using ProductsApp.Models;` v *ProductsController.cs* .

V tomto okamžiku sestavíte aplikaci v důsledku řady chyb kompilace. K chybám dochází, protože následující komponenty v ASP.NET Core neexistují:

* Třída `ApiController`
* Obor názvů `System.Web.Http`
* Rozhraní `IHttpActionResult`

Opravte chyby následujícím způsobem:

1. Změna `ApiController` k <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Přidejte `using Microsoft.AspNetCore.Mvc;` pro vyřešení odkazu `ControllerBase`.
1. Odstraňte `using System.Web.Http;`.
1. Změňte návratový typ `GetProduct` akce z `IHttpActionResult` na `ActionResult<Product>`.

Zjednodušte příkaz `GetProduct` akce `return` následujícím způsobem:

```csharp
return product;
```

## <a name="configure-routing"></a>Konfigurace směrování

Nakonfigurujte směrování následujícím způsobem:

1. Označte třídu `ProductsController` s následujícími atributy:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Předchozí atribut [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) nakonfiguruje vzor směrování atributů řadiče. Atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) způsobuje, že atribut směruje požadavek pro všechny akce v tomto kontroleru.

    Směrování atributů podporuje tokeny, například `[controller]` a `[action]`. Za běhu je každý token nahrazen názvem kontroleru nebo akce, v uvedeném pořadí, na který byl atribut použit. Tokeny omezují počet řetězců Magic v projektu. Tokeny také zajistí, aby trasy zůstaly synchronizované s odpovídajícími kontroléry a akcemi při použití refaktoringu automatického přejmenování.
1. Nastavte režim kompatibility projektu na ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Předchozí změna:

    * Je vyžadován pro použití atributu `[ApiController]` na úrovni řadiče.
    * Výslovný se na potenciálně odrušující chování zavedená v ASP.NET Core 2,2.
1. Povolit požadavky HTTP GET na `ProductController` akce:
    * Použijte atribut [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) pro akci `GetAllProducts`.
    * Použijte atribut `[HttpGet("{id}")]` pro akci `GetProduct`.

Po předchozích změnách a odebrání nepoužívaných `using`ch příkazů vypadá soubor *ProductsController.cs* takto:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Spusťte migrovaný projekt a vyhledejte `/api/products`. Zobrazí se úplný seznam tří produktů. Přejděte do `/api/products/1`. Zobrazí se první produkt.

## <a name="compatibility-shim"></a>Překrytí kompatibility

Knihovna [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) poskytuje překrytí kompatibility pro přesun projektů webového rozhraní API ASP.NET 4. x do ASP.NET Core. Překrytí kompatibility rozšiřuje ASP.NET Core o podporu řady konvencí z webového rozhraní API 2 pro ASP.NET 4. x. Vzorový port dříve v tomto dokumentu je dostatečně základní, protože překrytí kompatibility bylo zbytečné. U větších projektů může použití překrytí kompatibility sloužit k dočasnému přemostění mezer rozhraní API mezi ASP.NET Core a webovým rozhraním API 2 ASP.NET 4. x.

Překrytí kompatibility webového rozhraní API je určeno k použití jako dočasné opatření pro podporu migrace rozsáhlých projektů webového rozhraní API ASP.NET 4. x na ASP.NET Core. V průběhu času by se projekty měly aktualizovat tak, aby používaly ASP.NET Core vzorů, a nemusely se spoléhat na překrytí kompatibility.

Mezi funkce kompatibility zahrnuté v `Microsoft.AspNetCore.Mvc.WebApiCompatShim` patří:

* Přidá typ `ApiController` tak, aby se základní typy řadičů nemusely aktualizovat.
* Povoluje vazbu modelů ve stylu webového rozhraní API. ASP.NET Core vázání modelů MVC funguje podobně jako ASP.NET 4. x MVC 5 ve výchozím nastavení. Překrytí kompatibility mění vazbu modelu tak, aby bylo lépe podobné konvencím vazby modelů ASP.NET 4. x webového rozhraní API 2. Například komplexní typy jsou automaticky svázány z textu žádosti.
* Rozšiřuje vazbu modelu tak, aby akce kontroleru mohly přijímat parametry typu `HttpRequestMessage`.
* Přidá formátovací moduly zpráv umožňující akce vracet výsledky typu `HttpResponseMessage`.
* Přidá další metody odpovědi, které akce webového rozhraní API 2 mohly použít k obsluze odpovědí:
  * generátory `HttpResponseMessage`:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Metody výsledku akce:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Přidá instanci `IContentNegotiator` do kontejneru vkládání závislostí (DI) aplikace a zpřístupní typy související s vyjednáváním obsahu z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Příklady takových typů zahrnují `DefaultContentNegotiator` a `MediaTypeFormatter`.

Použití překrytí kompatibility:

1. Nainstalujte balíček NuGet [Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Zaregistrujte služby překrytí kompatibility s kontejnerem DI aplikace voláním `services.AddMvc().AddWebApiConventions()` v `Startup.ConfigureServices`.
1. Definujte trasy specifické pro webové rozhraní API pomocí `MapWebApiRoute` na `IRouteBuilder` ve volání `IApplicationBuilder.UseMvc` aplikace.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
