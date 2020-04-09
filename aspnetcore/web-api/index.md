---
title: Vytváření webových api s ASP.NET jádrem
author: scottaddie
description: Naučte se základy vytváření webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666003"
---
# <a name="create-web-apis-with-aspnet-core"></a>Vytváření webových api s ASP.NET jádrem

Scott [Addie](https://github.com/scottaddie) a [Tom Dykstra](https://github.com/tdykstra)

ASP.NET Core podporuje vytváření služeb RESTful v jazyce C# (služby RESTful se označují také jako webová rozhraní API). Pro zpracování požadavků používá webové rozhraní API řadiče. *Řadiče* ve webovém rozhraní API `ControllerBase`jsou třídy, které jsou odvozeny z aplikace . Tento článek ukazuje, jak používat řadiče pro zpracování požadavků webového rozhraní API.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>Třída ControllerBase

Webové rozhraní API se skládá z jedné <xref:Microsoft.AspNetCore.Mvc.ControllerBase>nebo více tříd řadiče, které jsou odvozeny z aplikace . Šablona projektu webového rozhraní API poskytuje počáteční řadič:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Nevytvářejte řadič webového rozhraní API odvozením <xref:Microsoft.AspNetCore.Mvc.Controller> z třídy. `Controller`odvozuje `ControllerBase` a přidává podporu pro zobrazení, takže je pro zpracování webových stránek, nikoli webových api požadavků. Existuje výjimka z tohoto pravidla: Pokud plánujete použít stejný řadič pro zobrazení i `Controller`webová api, odvoděte ji z aplikace .

Třída `ControllerBase` poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování požadavků HTTP. Například `ControllerBase.CreatedAtAction` vrátí stavový kód 201:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Zde jsou některé další příklady metod, které `ControllerBase` poskytuje.

|Metoda   |Poznámky    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Vrátí stavový kód 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Vrátí stavový kód 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Vrátí soubor.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Vyvolá [vazbu modelu](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Vyvolá [ověření modelu](xref:mvc/models/validation).|

Seznam všech dostupných metod a <xref:Microsoft.AspNetCore.Mvc.ControllerBase>vlastností naleznete v tématu .

## <a name="attributes"></a>Atributy

Obor <xref:Microsoft.AspNetCore.Mvc> názvů poskytuje atributy, které lze použít ke konfiguraci chování řadičů webového rozhraní API a metod akcí. Následující příklad používá atributy k určení podporovaného slovesa akce HTTP a všech známých stavových kódů HTTP, které by mohly být vráceny:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Zde jsou některé další příklady atributů, které jsou k dispozici.

|Atribut|Poznámky|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Určuje vzorek adresy URL pro řadič nebo akci.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Určuje předponu a vlastnosti, které mají být zahrnuty pro vazbu modelu.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifikuje akci, která podporuje sloveso akce HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Určuje datové typy, které akce přijímá.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Určuje datové typy, které akce vrátí.|

Seznam, který obsahuje dostupné atributy, <xref:Microsoft.AspNetCore.Mvc> naleznete v oboru názvů.

## <a name="apicontroller-attribute"></a>Atribut ApiController

Atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) lze použít pro třídu řadiče povolit následující umíněný, api specifické chování:

::: moniker range=">= aspnetcore-2.2"

* [Požadavek na směrování atributů](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození elementárního parametru vazby](#binding-source-parameter-inference)
* [Odvození žádosti o vícedílných/formulářových dat](#multipartform-data-request-inference)
* [Podrobnosti o problému pro kódy stavu chyby](#problem-details-for-error-status-codes)

Podrobnosti o problému pro funkci *kódů stavu chyby* vyžadují [verzi kompatibility](xref:mvc/compatibility-version) 2.2 nebo novější. Ostatní funkce vyžadují kompatibilní verzi verze 2.1 nebo novější.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Požadavek na směrování atributů](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození elementárního parametru vazby](#binding-source-parameter-inference)
* [Odvození žádosti o vícedílných/formulářových dat](#multipartform-data-request-inference)

Tyto funkce vyžadují [kompatibilní verzi](xref:mvc/compatibility-version) verze 2.1 nebo novější.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Atribut na konkrétnířadiče

Atribut `[ApiController]` lze použít pro konkrétní řadiče, jako v následujícím příkladu ze šablony projektu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atribut na více řadičích

Jeden přístup k použití atributu na více než jeden řadič je vytvořit `[ApiController]` vlastní základní řadič třídy s poznámkou s atributem. Následující příklad ukazuje vlastní základní třídu a řadič, který je z ní odvozen:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atribut v sestavě

Pokud je [verze kompatibility](xref:mvc/compatibility-version) nastavena na `[ApiController]` 2.2 nebo novější, atribut lze použít pro sestavení. Anotace tímto způsobem platí chování webového rozhraní API pro všechny řadiče v sestavení. Neexistuje žádný způsob, jak se odhlásit pro jednotlivé řadiče. Použijte atribut na úrovni sestavení pro deklaraci oboru názvů obklopující třídu: `Startup`

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

## <a name="attribute-routing-requirement"></a>Požadavek na směrování atributů

Atribut `[ApiController]` dělá směrování atributu požadavek. Příklad:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Akce jsou nepřístupné konvenčními [trasami definovanými](xref:mvc/controllers/routing#conventional-routing) `UseEndpoints`v písmenech a), <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Akce jsou nepřístupné konvenčními <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> [trasami definovanými](xref:mvc/controllers/routing#conventional-routing) nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Automatické odpovědi HTTP 400

Atribut `[ApiController]` umožňuje chyby ověření modelu automaticky aktivovat odpověď HTTP 400. V důsledku toho následující kód není nutné v metodě akce:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> používá filtr akce k předchozí kontrole.

### <a name="default-badrequest-response"></a>Výchozí odpověď BadRequest

S verzí kompatibility 2.1 je <xref:Microsoft.AspNetCore.Mvc.SerializableError>výchozím typem odpovědi pro odpověď HTTP 400 . Následující tělo požadavku je příkladem serializovaného typu:

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

S verzí kompatibility 2.2 nebo novější je <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>výchozím typem odpovědi pro odpověď HTTP 400 . Následující tělo požadavku je příkladem serializovaného typu:

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

Typ: `ValidationProblemDetails`

* Poskytuje strojově čitelný formát pro určení chyb v odpovědích webového rozhraní API.
* Vyhovuje [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

### <a name="log-automatic-400-responses"></a>Protokolovat automatické 400 odpovědí

Viz [Jak protokolovat automatické odpovědi 400 na chyby ověření modelu (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Zakázat automatickou odezvu 400

Chcete-li zakázat automatické chování <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> 400, nastavte vlastnost na `true`. V kládání `Startup.ConfigureServices`přidejte následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Odvození elementárního parametru vazby

Atribut zdroje vazby definuje umístění, ve kterém je nalezena hodnota parametru akce. Existují následující atributy zdroje vazby:

|Atribut|Zdroj vazby |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Text požadavku |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Data formuláře v těle požadavku |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Hlavička požadavku |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parametr řetězce dotazu požadavku |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Trasa dat z aktuálního požadavku |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Služba požadavku vstřikovaná jako parametr akce |

> [!WARNING]
> Nepoužívejte, `[FromRoute]` pokud mohou `%2f` hodnoty obsahovat (to znamená). `/` `%2f`nebude bez řídicího `/`a nepodařilo se mu. Použijte, `[FromQuery]` pokud hodnota `%2f`může obsahovat .

Bez `[ApiController]` atributnebo atributy zdroje `[FromQuery]`vazby, jako je ASP.NET Core runtime pokusí použít binder modelu komplexní objekt. Vazba modelu s komplexním objektovým modelem získává data z poskytovatelů hodnot v definovaném pořadí.

V následujícím příkladu `[FromQuery]` atribut označuje, že hodnota parametru `discontinuedOnly` je uvedena v řetězci dotazu adresy URL požadavku:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Atribut `[ApiController]` použije pravidla odvození pro výchozí zdroje dat parametrů akce. Tato pravidla vám ušetří nutnost identifikovat zdroje vazby ručně použitím atributů na parametry akce. Pravidla odvození závazného zdroje se chovají takto:

* `[FromBody]`je odvozen pro parametry komplexního typu. Výjimkou z `[FromBody]` pravidla odvození je jakýkoli složitý předdefinovaný typ se zvláštním <xref:Microsoft.AspNetCore.Http.IFormCollection> <xref:System.Threading.CancellationToken>významem, například a . Kód odvození zdroje vazby ignoruje tyto speciální typy.
* `[FromForm]`je odvozen pro akční parametry <xref:Microsoft.AspNetCore.Http.IFormFile> <xref:Microsoft.AspNetCore.Http.IFormFileCollection>typu a . Není odvozen pro žádné jednoduché nebo uživatelem definované typy.
* `[FromRoute]`je odvozen pro libovolný název parametru akce odpovídající parametru v šabloně trasy. Pokud více než jedna trasa odpovídá parametru `[FromRoute]`akce, je považována za libovolnou hodnotu trasy .
* `[FromQuery]`je odvozen pro všechny ostatní parametry akce.

### <a name="frombody-inference-notes"></a>FromBody odvození poznámky

`[FromBody]`není odvozen o jednoduchých typech, jako `string` jsou nebo `int`. Proto `[FromBody]` atribut by měl být použit pro jednoduché typy, když je potřeba tuto funkci.

Pokud akce má více než jeden parametr vázaný z těla požadavku, je vyvolána výjimka. Například všechny následující podpisy metody akce způsobit výjimku:

* `[FromBody]`odvodit na obou, protože jsou složité typy.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]`atribut na jedné, odvodit na straně druhé, protože je to komplexní typ.

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
> V ASP.NET Core 2.1 jsou parametry typu kolekce, jako jsou `[FromQuery]`seznamy a pole, nesprávně odvozeny jako . Atribut `[FromBody]` by měl být použit pro tyto parametry, pokud mají být vázány z těla požadavku. Toto chování je opraveno v ASP.NET Core 2.2 nebo novější, kde jsou odvozeny parametry typu kolekce, které jsou ve výchozím nastavení vázány z těla.

::: moniker-end

### <a name="disable-inference-rules"></a>Zakázat pravidla odvození

Chcete-li zakázat odvození <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> `true`zdroje vazby, nastavte na . Do tohoto textu `Startup.ConfigureServices`přidejte následující kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Odvození žádosti o vícedílných/formulářových dat

Atribut `[ApiController]` použije pravidlo odvození, když je parametr akce anotován s atributem. [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) Typ `multipart/form-data` obsahu požadavku je odvozen.

Chcete-li zakázat výchozí <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> chování, nastavte vlastnost na `true` v `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a>Podrobnosti o problému pro kódy stavu chyby

Pokud je verze kompatibility 2.2 nebo novější, mvc transformuje výsledek chyby (výsledek se <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>stavovým kódem 400 nebo vyšší) na výsledek s . Typ `ProblemDetails` je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobností o chybě čitelné počítačem v odpovědi HTTP.

Zvažte následující kód v akci řadiče:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Metoda `NotFound` vytváří stavový kód HTTP 404 s `ProblemDetails` tělem. Příklad:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Zakázat odpověď ProblemDetails

Automatické vytváření kódů stavu `ProblemDetails` chyby for <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> je zakázáno, pokud je vlastnost nastavena na . `true` Do tohoto textu `Startup.ConfigureServices`přidejte následující kód:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definování typů obsahu podporovaných požadavků pomocí atributu [Spotřebovává]

Ve výchozím nastavení akce podporuje všechny dostupné typy obsahu požadavku. Pokud je například aplikace nakonfigurována tak, aby podporovala [vstupní formáty](xref:mvc/models/model-binding#input-formatters)JSON i XML , akce podporuje více typů obsahu, včetně `application/json` a `application/xml`.

Atribut [[Spotřebovává]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umožňuje akci omezit typy obsahu podporovaných požadavků. Použijte `[Consumes]` atribut na akci nebo řadič a určete jeden nebo více typů obsahu:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

V předchozím kódu `CreateProduct` akce určuje typ `application/xml`obsahu . Požadavky směrované na tuto akci `Content-Type` musí `application/xml`určit záhlaví . Požadavky, které neurčují `Content-Type` záhlaví `application/xml` výsledku v odpovědi [415 Nepodporovaný typ média.](https://developer.mozilla.org/docs/Web/HTTP/Status/415)

Atribut `[Consumes]` také umožňuje akci ovlivnit jeho výběr na základě typu obsahu příchozí žádosti použitím omezení typu. Uvažujte následující příklad:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

V předchozím kódu `ConsumesController` je nakonfigurován pro zpracování `https://localhost:5001/api/Consumes` požadavků odeslaných na adresu URL. Obě akce řadiče `PostJson` a `PostForm`, zpracování požadavků POST se stejnou adresou URL. Bez `[Consumes]` atributu použití omezení typu je vyvolána výjimka nejednoznačné shody.

Atribut `[Consumes]` je použit pro obě akce. Akce `PostJson` zpracovává požadavky odeslané `Content-Type` s `application/json`hlavičkou . Akce `PostForm` zpracovává požadavky odeslané `Content-Type` s `application/x-www-form-urlencoded`hlavičkou . 

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
