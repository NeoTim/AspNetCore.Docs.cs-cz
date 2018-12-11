---
title: Migrace z rozhraní ASP.NET Web API pro ASP.NET Core
author: ardalis
description: Zjistěte, jak migrovat implementaci webového rozhraní API z ASP.NET 4.x webového rozhraní API pro ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: 9806c502f8f5244740f9f9614657a40cfaa03314
ms.sourcegitcommit: 1872d2e6f299093c78a6795a486929ffb0bbffff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53216830"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrace z rozhraní ASP.NET Web API pro ASP.NET Core

Podle [Scott Addie](https://twitter.com/scott_addie) a [Steve Smith](https://ardalis.com/)

Rozhraní Web API pro ASP.NET 4.x je služba HTTP, kterou půjde používat celou řadu klientů, včetně prohlížečů a mobilních zařízení. Webové rozhraní API aplikace modelů do jednodušší programovacího modelu říká ASP.NET Core MVC a sjednocuje ASP.NET Core MVC ASP.NET 4.x společnosti. Tento článek popisuje kroky potřebné k migraci z ASP.NET 4.x webového rozhraní API pro ASP.NET Core MVC.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/migration/webapi/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [net-core-prereqs-vs-2.2](../includes/net-core-prereqs-vs-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Projděte si projekt webového rozhraní API technologie ASP.NET 4.x

Jako výchozí bod, tento článek používá *ProductsApp* projekt vytvořený v [Začínáme s ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). V příslušném projektu je nakonfigurovaný Jednoduchý projekt webového rozhraní API technologie ASP.NET 4.x následujícím způsobem.

V *Global.asax.cs*, je provedeno volání `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig` Třídy se nachází v *App_Start* složky a má statickou `Register` metody:

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Tato třída nakonfiguruje [směrováním atributů](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), i když se ve skutečnosti používá v projektu. Nakonfiguruje taky směrovací tabulky, který používá rozhraní ASP.NET Web API. V takovém případě očekává, že webové rozhraní API ASP.NET 4.x adresy URL ve formátu `/api/{controller}/{id}`, s `{id}` je nepovinná.

*ProductsApp* projekt obsahuje jeden kontroler. Kontroler dědí z `ApiController` a obsahuje dvě akce:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`Product` Model používaný `ProductsController` je jednoduchou třídu:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

Následující části ukazují migrace projektu webového rozhraní API pro ASP.NET Core MVC.

## <a name="create-destination-project"></a>Vytvořte cílový projekt

Proveďte následující kroky v sadě Visual Studio:

* Přejděte na **souboru** > **nové** > **projektu** > **ostatní typy projektů**  >  **Řešení sady visual Studio**. Vyberte **prázdné řešení**a řešení nazvěte *WebAPIMigration*. Klikněte na tlačítko **OK** tlačítko.
* Přidat existující *ProductsApp* projektu do řešení.
* Přidat nový **webové aplikace ASP.NET Core** projektu do řešení. Vyberte **.NET Core** cílové rozhraní framework z rozevíracího seznamu a vyberte **API** šablony projektu. Pojmenujte projekt *ProductsCore*a klikněte na tlačítko **OK** tlačítko.

Řešení teď obsahuje dva projekty. Následující části popisují migraci *ProductsApp* obsah projektu *ProductsCore* projektu.

## <a name="migrate-configuration"></a>Migrace konfigurace

ASP.NET Core nepoužívá *App_Start* složky nebo *Global.asax* souboru a *web.config* se přidá soubor na čas publikování. *Startup.cs* je náhradou *Global.asax* a je umístěn v kořenové složce projektu. `Startup` Třída zpracovává všechny úlohy po spuštění aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

V ASP.NET Core MVC směrovací atribut je standardní součástí při <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> je volána `Startup.Configure`. Následující `UseMvc` volání nahradí *ProductsApp* projektu *App_Start/WebApiConfig.cs* souboru:

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrace modelů a kontrolerů

Zkopírovat *ProductApp* projektu kontroleru a používá model. Postupujte podle těchto kroků:

1. Kopírování *Controllers/ProductsController.cs* z původního projektu do nové.
1. Zkopírujte celý *modely* složku z původního projektu do nové.
1. Změnit obory názvů zkopírované soubory tak, aby odpovídaly novým názvem projektu (*ProductsCore*). Upravit `using ProductsApp.Models;` výroky *ProductsController.cs* příliš.

V tomto okamžiku vytváření aplikace výsledkem počet chyb kompilace. Této chybě dojde, protože následující součásti neexistují v ASP.NET Core:

* `ApiController` Třída
* `System.Web.Http` Namespace
* `IHttpActionResult` Rozhraní

Opravte chyby následujícím způsobem:

1. Změna `ApiController` k <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Přidat `using Microsoft.AspNetCore.Mvc;` přeložit `ControllerBase` odkaz.
1. Odstraňte `using System.Web.Http;`.
1. Změnit `GetProduct` návratový typ akce z `IHttpActionResult` k `ActionResult<Product>`.

Zjednodušení `GetProduct` akce `return` příkaz takto:

```csharp
return product;
```

## <a name="configure-routing"></a>Konfigurace směrování

Konfigurace směrování následujícím způsobem:

1. Uspořádání `ProductsController` třídy s následujícími atributy:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Předchozí [[trasy]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atribut nakonfiguruje vzor směrování atributů kontroleru. [[Objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut díky atribut směrování požadavků pro všechny akce v tomto kontroleru.

    Směrování atributů podporuje tokeny, jako například `[controller]` a `[action]`. Za běhu každý token se nahradí názvem kontroler nebo akce, v uvedeném pořadí, ke které byl použit atribut. Tokeny snížit počet magic řetězců v projektu. Tokeny Ujistěte se také trasy zajistila synchronizovanost s odpovídající kontrolery a akce při automatické přejmenování refaktoringy používají.
1. Nastavení režimu kompatibility v projektu na verzi 2.2 technologie ASP.NET Core:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Předchozí změny:

    * Je potřeba použít `[ApiController]` atribut na úrovni kontroleru.
    * Vyjádřit výslovný souhlas pro potenciálně zásadní chování zavedené v ASP.NET Core 2.2.
1. Povolit požadavky HTTP Get `ProductController` akce:
    * Použít [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atribut `GetAllProducts` akce.
    * Použít `[HttpGet("{id}")]` atribut `GetProduct` akce.

Po předchozí změny a odstranění nepoužívaných `using` příkazů *ProductsController.cs* souboru vypadá takto:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Spusťte projekt pro migrované a přejděte do `/api/products`. Zobrazí se úplný seznam tří produktů. Přejděte do `/api/products/1`. Zobrazí se první produktu.

## <a name="compatibility-shim"></a>Překrytí kompatibility

[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) knihovna poskytuje kompatibilitu překrytí pro přesun projekty webového rozhraní API technologie ASP.NET 4.x ASP.NET Core. Překrytí kompatibility rozšiřuje podporu celé řady konvence z rozhraní Web API 2 ASP.NET 4.x ASP.NET Core. Ukázka přenáší dříve v tomto dokumentu je dostatečně základní, že bylo překrytí kompatibility zbytečné. U větších projektů pomocí shimu kompatibility může být užitečná pro dočasné překlenutí této propasti API mezi ASP.NET Core a ASP.NET 4.x webového rozhraní API 2.

Překrytí kompatibility webového rozhraní API je určen se použije jako dočasné opatření pro podporu migrace velkých projektech ASP.NET 4.x webového rozhraní API pro ASP.NET Core. V průběhu času se musí aktualizovat projekty ASP.NET Core využít aniž byste museli spoléhat na překrytí kompatibility.

Kompatibilita funkce obsažené v `Microsoft.AspNetCore.Mvc.WebApiCompatShim` patří:

* Přidá `ApiController` tak, aby základních typů řadičů není potřeba aktualizovat.
* Umožňuje vytvořit vazbu modelu webové rozhraní API – stylu. ASP.NET Core MVC model vazby funkcí podobně, který technologie ASP.NET 4.x MVC 5, ve výchozím nastavení. Změny překrytí kompatibility vazby se víc podobá vytváření vazby modelu 4.x webovým rozhraním API 2 technologie ASP.NET modelu. Komplexní typy jsou automaticky svázán z textu požadavku.
* Rozšiřuje vazby modelu, tak, aby akce kontroleru může mít parametry typu `HttpRequestMessage`.
* Přidá formátování zpráv umožňuje akce vrátí výsledky typu `HttpResponseMessage`.
* Přidá další odpovědi metody, které webové rozhraní API 2 akce může mít používají k předávání odpovědi:
  * `HttpResponseMessage` generátory:
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Výsledek metody akce:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Přidá instanci `IContentNegotiator` aplikaci prvku kontejneru pro vkládání (DI) závislosti a zpřístupňuje obsahu vyjednávání související typy z [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Mezi tyto typy patří `DefaultContentNegotiator` a `MediaTypeFormatter`.

Chcete-li použít překrytí kompatibility:

1. Nainstalujte [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) balíček NuGet.
1. Zaregistrovat překrytí kompatibility služby kontejnerů DI aplikace po zavolání `services.AddMvc().AddWebApiConventions()` v `Startup.ConfigureServices`.
1. Definování webové rozhraní API specifické směruje pomocí `MapWebApiRoute` na `IRouteBuilder` aplikace `IApplicationBuilder.UseMvc` volání.

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
