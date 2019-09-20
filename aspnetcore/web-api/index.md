---
title: Vytváření webových rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Seznamte se se základy vytváření webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2019
uid: web-api/index
ms.openlocfilehash: aab9b848eb6e69055b019c9253c716898e9847e2
ms.sourcegitcommit: a11f09c10ef3d4eeab7ae9ce993e7f30427741c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71149351"
---
# <a name="create-web-apis-with-aspnet-core"></a>Vytváření webových rozhraní API pomocí ASP.NET Core

[Scott Addie](https://github.com/scottaddie) a [Dykstra](https://github.com/tdykstra)

ASP.NET Core podporuje vytváření služeb RESTful, označovaných také jako webová rozhraní API C#, pomocí. Webové rozhraní API zpracovává žádosti pomocí řadičů. *Řadiče* ve webovém rozhraní API jsou třídy, které jsou `ControllerBase`odvozeny z. V tomto článku se dozvíte, jak používat řadiče pro zpracování požadavků webového rozhraní API.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Stažení](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>ControllerBase – třída

Webové rozhraní API se skládá z jedné nebo více tříd kontroleru, které <xref:Microsoft.AspNetCore.Mvc.ControllerBase>jsou odvozeny z. Šablona projektu webového rozhraní API poskytuje kontroler Starter:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Nevytvářejte kontroler webového rozhraní API odvozením z <xref:Microsoft.AspNetCore.Mvc.Controller> třídy. `Controller`je odvozen z `ControllerBase` a přidává podporu pro zobrazení, aby bylo možné zpracovávat webové stránky, nikoli požadavky webového rozhraní API. Toto pravidlo má výjimku: Pokud plánujete použít stejný kontroler pro obě zobrazení a webová rozhraní API, odvodit je od `Controller`.

`ControllerBase` Třída poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování požadavků HTTP. Například `ControllerBase.CreatedAtAction` vrátí stavový kód 201:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Zde je několik příkladů metod, které `ControllerBase` poskytuje.

|Metoda   |Poznámky    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| Vrátí stavový kód 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>|Vrátí stavový kód 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|Vrátí soubor.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|Vyvolá [vazbu modelu](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|Vyvolá [ověření modelu](xref:mvc/models/validation).|

Seznam všech dostupných metod a vlastností naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributy

<xref:Microsoft.AspNetCore.Mvc> Obor názvů poskytuje atributy, které lze použít ke konfiguraci chování řadičů webového rozhraní API a metod akcí. Následující příklad používá atributy k určení podporovaného příkazu akce HTTP a všech známých stavových kódů HTTP, které by mohly být vráceny:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Tady je několik příkladů atributů, které jsou k dispozici.

|Atribut|Poznámky|
|---------|-----|
|[Cestě](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Určuje vzor adresy URL pro kontroler nebo akci.|
|[Zapisovat](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Určuje předponu a vlastnosti, které se mají zahrnout do vazby modelu.|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifikuje akci, která podporuje příkaz akce HTTP GET.|
|[Spotřebovává](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Určuje datové typy, které akce akceptuje.|
|[Uslyší](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Určuje datové typy, které akce vrátí.|

Seznam, který obsahuje dostupné atributy, najdete v tématu <xref:Microsoft.AspNetCore.Mvc> obor názvů.

## <a name="apicontroller-attribute"></a>ApiController – atribut

Atribut [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) lze použít pro třídu kontroleru a povolit následující dogmatickým chování specifické pro rozhraní API:

* [Požadavek na směrování atributu](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození zdrojového parametru vazby](#binding-source-parameter-inference)
* [Multipart/form-odvození požadavku na data](#multipartform-data-request-inference)
* [Podrobnosti o problémech stavových kódů chyb](#problem-details-for-error-status-codes)

Tyto funkce vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,1 nebo novější.

### <a name="attribute-on-specific-controllers"></a>Atribut na určitých řadičích

`[ApiController]` Atribut lze použít pro konkrétní řadiče, jako v následujícím příkladu, ze šablony projektu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atribut na více řadičích

Jedním z přístupů k použití atributu na více než jednom řadiči je vytvoření vlastní třídy základního kontroleru s poznámkou s `[ApiController]` atributem. Následující příklad ukazuje vlastní základní třídu a řadič, který je z něj odvozen:

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atribut na sestavení

Pokud je [verze kompatibility](xref:mvc/compatibility-version) nastavena na 2,2 nebo novější, `[ApiController]` atribut lze použít na sestavení. Anotace tímto způsobem aplikuje chování webového rozhraní API na všechny řadiče v sestavení. Neexistuje žádný způsob, jak odhlásit jednotlivé řadiče. Použijte atribut na úrovni sestavení pro deklaraci oboru názvů obklopující `Startup` třídu:

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a>Požadavek na směrování atributu

`[ApiController]` Atribut dělá směrování požadavku. Příklad:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>pomocí `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> nebo `Startup.Configure`v.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> definovaných <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> v `Startup.Configure`nebo.

::: moniker-end

### <a name="automatic-http-400-responses"></a>Automatické odpovědi HTTP 400

`[ApiController]` Atribut způsobuje chyby ověření modelu automaticky a odpověď HTTP 400. V důsledku toho následující kód není zbytečný v metodě akce:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC používá <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> filtr akcí k provedení předchozí kontroly.

### <a name="default-badrequest-response"></a>Výchozí odpověď důvodu chybného požadavku

V případě kompatibility verze 2,1 je <xref:Microsoft.AspNetCore.Mvc.SerializableError>výchozí typ odpovědi pro odpověď HTTP 400. Následující text žádosti je příkladem serializovaného typu:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

V případě kompatibility verze 2,2 nebo novější je <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>výchozí typ odpovědi pro odpověď HTTP 400. Následující text žádosti je příkladem serializovaného typu:

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

`ValidationProblemDetails` Typ:

* Poskytuje strojově čitelný formát pro zadávání chyb v odpovědích webového rozhraní API.
* Vyhovuje [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

### <a name="log-automatic-400-responses"></a>Protokolovat automatické odpovědi 400

Přečtěte si, [Jak protokolovat automatické odpovědi 400 na chyby ověření modelu (ASPNET/AspNetCore. Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Zakázat automatickou odpověď 400

Chcete-li zakázat automatické chování 400, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> vlastnost na `true`hodnotu. Do `Startup.ConfigureServices`následujícího pole přidejte následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Odvození zdrojového parametru vazby

Zdrojový atribut vazby definuje umístění, kde je nalezena hodnota parametru akce. Existují následující zdrojové atributy vazby:

|Atribut|Zdroj vazby |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Text požadavku |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Data formuláře v textu žádosti |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Hlavička žádosti |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parametr řetězce dotazu žádosti |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Směrovat data z aktuální žádosti |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Služba požadavku byla vložena jako parametr akce. |

> [!WARNING]
> Nepoužívejte `[FromRoute]` , pokud hodnoty mohou `%2f` obsahovat (to `/`znamená). `%2f`nedá se odsekvencit na `/`. Použijte `[FromQuery]` , pokud hodnota může obsahovat `%2f`.

Bez atributů nebo `[FromQuery]`atributů vazby, ASP.NET Core modul runtime se pokusí použít komplexní pořadač objektového modelu. `[ApiController]` Pořadač komplexního objektového modelu získává data od zprostředkovatelů hodnot v definovaném pořadí.

V následujícím příkladu `[FromQuery]` atribut označuje `discontinuedOnly` , že hodnota parametru je uvedena v řetězci dotazu adresy URL požadavku:

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` Atribut použije odvozená pravidla pro výchozí zdroje dat parametrů akce. Tato pravidla vám umožní určit ruční identifikaci zdrojů vazby použitím atributů u parametrů akce. Pravidla odvození zdroje vazby se chovají následujícím způsobem:

* `[FromBody]`je odvozený pro parametry komplexního typu. Výjimka na `[FromBody]` odvozený pravidlo je jakýkoli komplexní, vestavěný typ se speciálním významem, <xref:Microsoft.AspNetCore.Http.IFormCollection> například a <xref:System.Threading.CancellationToken>. Kód odvození zdroje vazby ignoruje tyto speciální typy.
* `[FromForm]`je odvozený pro parametry akce typu <xref:Microsoft.AspNetCore.Http.IFormFile> a. <xref:Microsoft.AspNetCore.Http.IFormFileCollection> Není odvozený pro žádné jednoduché nebo uživatelsky definované typy.
* `[FromRoute]`je odvozen pro všechny názvy parametrů akce, které odpovídají parametru v šabloně trasy. Když parametr akce odpovídá víc než jedna trasa, bude se brát v úvahu `[FromRoute]`jakákoli hodnota trasy.
* `[FromQuery]`je odvozen pro všechny ostatní parametry akce.

### <a name="frombody-inference-notes"></a>Poznámky k odvozování FromBody

`[FromBody]`není odvozen pro jednoduché typy, jako například `string` nebo. `int` Proto by měl být atributpoužitprojednoduchétypy,pokudjetatofunkcepotřebná.`[FromBody]`

Pokud má akce více než jeden parametr vázaný z těla požadavku, je vyvolána výjimka. Například všechny následující signatury metody Action způsobují výjimku:

* `[FromBody]`odvozeno v obou případech, protože se jedná o komplexní typy.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]`atribut na jednom, odvozený na druhém, protože se jedná o komplexní typ.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]`atribut na obou.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> V ASP.NET Core 2,1 jsou parametry typu kolekce, jako jsou seznamy a pole, nesprávně odvozeny jako `[FromQuery]`. `[FromBody]` Atribut by měl být použit pro tyto parametry, pokud mají být vázány z textu žádosti. Toto chování se opravuje v ASP.NET Core 2,2 nebo novějším, kde jsou parametry typu kolekce odvoditelné z těla ve výchozím nastavení.

::: moniker-end

### <a name="disable-inference-rules"></a>Zakázat odvození pravidel

Chcete-li zakázat odvození zdroje vazby <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> , `true`nastavte na. Do `Startup.ConfigureServices`tohoto pole přidejte následující kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Multipart/form-odvození požadavku na data

Atribut použije odvození pravidla, pokud je parametr Action opatřen poznámkou s atributem [[FromForm].](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) `[ApiController]` Typ `multipart/form-data` obsahu žádosti je odvozený.

Chcete-li zakázat výchozí chování, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> vlastnost na `true` hodnotu `Startup.ConfigureServices`v:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

## <a name="problem-details-for-error-status-codes"></a>Podrobnosti o problémech stavových kódů chyb

Pokud je verze kompatibility 2,2 nebo novější, MVC transformuje výsledek chyby (výsledek s kódem stavu 400 nebo vyšší) na výsledek <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. Typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobností o chybě čitelné počítačem v odpovědi HTTP. `ProblemDetails`

V akci kontroleru zvažte následující kód:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Metoda vytvoří stavový kód HTTP 404 `ProblemDetails` s tělem. `NotFound` Příklad:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Zakázat odpověď ProblemDetails

Automatické vytváření `ProblemDetails` instance je zakázáno, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors*> Pokud je vlastnost nastavena na `true`hodnotu. Do `Startup.ConfigureServices`tohoto pole přidejte následující kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
