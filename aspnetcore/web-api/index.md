---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Naučte se základy vytváření webových rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/08/2019
uid: web-api/index
ms.openlocfilehash: 4f9c334f74dd2a8b7c31c7a42703fa361ccf9139
ms.sourcegitcommit: 91cc1f07ef178ab709ea42f8b3a10399c970496e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622800"
---
# <a name="create-web-apis-with-aspnet-core"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core

Podle [Scott Addie](https://github.com/scottaddie) a [Petr Dykstra](https://github.com/tdykstra)

Podporuje vytváření služby RESTful, označované také jako webová rozhraní API, pomocí ASP.NET Core C#. Webové rozhraní API pro zpracování žádostí, používá řadiče. *Řadiče* ve webovém rozhraní API jsou třídy, které jsou odvozeny z `ControllerBase`. Tento článek ukazuje, jak používat kontrolery pro zpracování požadavků rozhraní API.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Stažení](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>Třída ControllerBase

Webové rozhraní API má jednu nebo více tříd kontroleru, které jsou odvozeny z <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Například šablony projektu webového rozhraní API vytvoří kontroler hodnoty:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=3)]

Nevytvářejte odvozením z kontroleru webového rozhraní API <xref:Microsoft.AspNetCore.Mvc.Controller> třídy. `Controller` je odvozen od `ControllerBase` a přidává podporu pro zobrazení, tak, aby byl pro zpracování webové stránky, webové rozhraní API požaduje.  Existuje výjimka tohoto pravidla: Pokud máte v plánu používat stejný kontrolér pro zobrazení a rozhraní API, jsou odvozeny z `Controller`.

`ControllerBase` Třída poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování žádostí HTTP. Například `ControllerBase.CreatedAtAction` vrátí 201 stavový kód:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=8-9)]

 Tady jsou některé další příklady metod, které `ControllerBase` poskytuje.

|Metoda  |Poznámky  |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>| Vrátí stavový kód 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> |Vrátí stavový kód 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile*>|Vrátí soubor.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>|Vyvolá [vazby modelu](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel*>|Vyvolá [ověření modelu](xref:mvc/models/validation).|

Seznam všech dostupných metod a vlastností najdete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributy

<xref:Microsoft.AspNetCore.Mvc> Obor názvů obsahuje atributy, které můžete použít ke konfiguraci chování kontrolerů webového rozhraní API a metod akcí. Následující příklad používá atributů k určení metoda HTTP přijetí a vrátil kód stavu:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Tady jsou některé další příklady atributy, které jsou k dispozici.

|Atribut|Poznámky|
|---------|-----|
|[[Trasy]](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Určuje vzor adresy URL pro kontroler nebo akce.|
|[[Vazba]](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Určuje předponu a vlastnosti, které chcete zahrnout pro vazbu modelu.|
|[[HttpGet]](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Určuje akci, která podporuje metodu GET protokolu HTTP.|
|[[Využívá]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Určuje typy dat, které přijímá akci.|
|[[Vytváří]](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Určuje typy dat, které vrátí akci.|

Seznam, který obsahuje dostupné atributy, najdete v článku <xref:Microsoft.AspNetCore.Mvc> oboru názvů.

## <a name="apicontroller-attribute"></a>Atribut objektu ApiController

[[Objektu ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atribut lze použít pro třídu kontroleru k povolení chování pro konkrétní rozhraní API:

* [Atribut směrování požadavků](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození parametr zdroje vazby](#binding-source-parameter-inference)
* [Odvození multipart/formulář data požadavku](#multipartform-data-request-inference)
* [Podrobnosti o problému pro stavové kódy chyb](#problem-details-for-error-status-codes)

Tyto funkce vyžadují [verze kompatibility](<xref:mvc/compatibility-version>) 2.1 nebo novější.

### <a name="apicontroller-on-specific-controllers"></a>Objektu ApiController na konkrétní řadiče

`[ApiController]` Atribut lze použít pro konkrétní řadiče, jako v následujícím příkladu ze šablony projektu:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=2)]

### <a name="apicontroller-on-multiple-controllers"></a>Objektu ApiController na víc řadičích

Jeden ze způsobů použití atributu na více než jeden řadič, je vytvořit třídu vlastní základní kontroler opatřen poznámkou `[ApiController]` atribut. Tady je příklad ukazující vlastní základní třídu a kontroler, který je odvozen z něj:

[!code-csharp[](index/samples/2.x/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_Inherit)]

### <a name="apicontroller-on-an-assembly"></a>Objektu ApiController na sestavení

Pokud [verze kompatibility](<xref:mvc/compatibility-version>) je nastavená na verzi 2.2 nebo novější, `[ApiController]` atribut lze použít k sestavení. Poznámka tímto způsobem použít chování webového rozhraní API do všech řadičů v sestavení. Neexistuje žádný způsob, jak se odhlásit pro jednotlivé řadiče. Atribut úrovně sestavení, který chcete použít `Startup` třídy, jak je znázorněno v následujícím příkladu:

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

## <a name="attribute-routing-requirement"></a>Atribut směrování požadavků

`ApiController` Atribut díky směrování požadavku atributů. Příklad:

[!code-csharp[](index/samples/2.x/Controllers/ValuesController.cs?name=snippet_Signature&highlight=1)]

Akce jsou nedostupné přes [trasy konvenční](xref:mvc/controllers/routing#conventional-routing) určené <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> v `Startup.Configure`.

## <a name="automatic-http-400-responses"></a>Automatické odpovědi HTTP 400

`ApiController` Atribut umožňuje ověření chyby modelu automaticky odpovědi na trigger HTTP 400. V důsledku toho není nutné v metodě akce následující kód:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

### <a name="default-badrequest-response"></a>Výchozí odpověď chybného požadavku 

Kompatibilita verzí 2.2 nebo novější, je výchozí typ odpovědi pro odpovědi HTTP 400 <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. `ValidationProblemDetails` Typ v souladu s [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).

Chcete-li změnit výchozí odpověď <xref:Microsoft.AspNetCore.Mvc.SerializableError>, nastavte `SuppressUseValidationProblemDetailsForInvalidModelStateResponses` vlastnost `true` v `Startup.ConfigureServices`, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,9)]

### <a name="customize-badrequest-response"></a>Přizpůsobení chybného požadavku odpovědi

Chcete-li přizpůsobit odpověď, která je výsledkem chyba ověřování, použijte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory>. Přidejte následující zvýrazněný kód po `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureBadRequestResponse&highlight=3-20)]

### <a name="log-automatic-400-responses"></a>Protokolovat 400 automatické odpovědi

Zobrazit [protokolování automatické 400 odpovědi na chyby ověření modelu (aspnet/AspNetCore.Docs #12157)](https://github.com/aspnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400"></a>Zakázat automatické 400

Chcete-li zakázat automatické 400 chování, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> vlastnost `true`. Přidejte následující zvýrazněný kód v `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

## <a name="binding-source-parameter-inference"></a>Odvození parametr zdroje vazby

Zdrojový atribut vazby definuje umístění, ve kterém není nalezena hodnota parametru akce. Existují následující atributy zdroje vazby:

|Atribut|Zdroje připojení |
|---------|---------|
|[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Text požadavku |
|[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Data formuláře v textu požadavku |
|[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Hlavička požadavku |
|[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Požádat o parametru řetězce dotazu |
|[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Data trasy z aktuální žádosti |
|[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Žádost o služby, vložený jako parametru akce |

> [!WARNING]
> Nepoužívejte `[FromRoute]` při může obsahovat hodnoty `%2f` (to znamená `/`). `%2f` nebude znaků bez řídících k `/`. Použití `[FromQuery]` Pokud hodnota může obsahovat `%2f`.

Bez `[ApiController]` vazbu atribut nebo atributy zdroje, jako jsou `[FromQuery]`, modul runtime ASP.NET Core se pokusí použít komplexní objekt vazače modelu. Vazač modelu komplexní objekt si vyžádá data z zprostředkovatele hodnot v zadaném pořadí.

V následujícím příkladu `[FromQuery]` atribut označuje, že `discontinuedOnly` je zadána hodnota parametru v řetězci dotazu v adrese URL požadavku:

[!code-csharp[](index/samples/2.x/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

`[ApiController]` Atributu, platí pravidla odvozování pro zdroje dat výchozí parametry akce. Tato pravidla můžete uložit z museli ručně určení zdroje připojení použitím atributy k parametrům akce. Vazba zdrojové odvození, kterou pravidla chování následujícím způsobem:

* `[FromBody]` pro parametry komplexní typ je odvozen. Výjimkou `[FromBody]` odvozené pravidlo je libovolný integrované, komplexní typ zvláštní význam, jako například <xref:Microsoft.AspNetCore.Http.IFormCollection> a <xref:System.Threading.CancellationToken>. Zdrojový kód odvození vazby ignoruje tyto speciální typy. 
* `[FromForm]` pro parametry akce typu je odvozený <xref:Microsoft.AspNetCore.Http.IFormFile> a <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Není odvodit pro jednoduché nebo uživatelem definované typy.
* `[FromRoute]` je odvozen pro název parametru žádné akce odpovídající parametr v šabloně trasy. Když víc tras odpovídá parametru akce, je považován za libovolnou hodnotu trasy `[FromRoute]`.
* `[FromQuery]` je odvozen pro všechny ostatní parametry akce.

### <a name="frombody-inference-notes"></a>Poznámky k odvození FromBody

`[FromBody]` není odvodit pro jednoduché typy, jako například `string` nebo `int`. Proto `[FromBody]` atribut by měl použít pro jednoduché typy, které tuto funkci je potřeba.

Když akce má více než jeden parametr vázán z textu požadavku, je vyvolána výjimka. Například všechny následující podpisy metod akce způsobit výjimku:

* `[FromBody]` u obou odvodit, protože jsou komplexní typy.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]` atribut na jednom, protože se jedná o komplexní typ odvodit na druhé.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* `[FromBody]` atribut u obou.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

> [!NOTE]
> V ASP.NET Core 2.1 parametry typu kolekce, jako je například seznamy a pole jsou odvozeny nesprávně jako `[FromQuery]`. `[FromBody]` Atributu se použije pro tyto parametry, pokud mají být vázány z textu požadavku. Toto chování je opraveno v ASP.NET Core 2.2 nebo vyšší, ve kterém jsou parametry typu kolekce odvodit vázat z textu ve výchozím nastavení.

### <a name="disable-inference-rules"></a>Zakázat odvozených pravidel

Chcete-li zakázat odvození zdroj vazby, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> k `true`. Přidejte následující kód do `Startup.ConfigureServices` po `services.AddMvc().SetCompatibilityVersion`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

## <a name="multipartform-data-request-inference"></a>Odvození multipart/formulář data požadavku

`[ApiController]` Atributu platí odvozené pravidlo, pokud parametr akce je opatřen poznámkou [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) atribut: `multipart/form-data` žádosti je odvozený typ obsahu.

Chcete-li výchozí chování zakázat, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> k `true` v `Startup.ConfigureServices`, jak je znázorněno v následujícím příkladu:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

## <a name="problem-details-for-error-status-codes"></a>Podrobnosti o problému pro stavové kódy chyb

Pokud je kompatibility verzí 2.2 nebo vyšší, MVC transformuje chybného výsledku (výsledek s stavový kód 400 nebo vyšší) k výsledku s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. `ProblemDetails` Typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobnosti o chybě Strojově čitelný v odpovědi HTTP.

Vezměte v úvahu následující kód do kontroleru akce:

[!code-csharp[](index/samples/2.x/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Odpověď HTTP pro `NotFound` má stavový kód 404 s `ProblemDetails` textu. Příklad:

```json
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="customize-problemdetails-response"></a>Přizpůsobení ProblemDetails odpovědi

Použití `ClientErrorMapping` vlastnosti ke konfiguraci obsah `ProblemDetails` odpovědi. Například následující kód aktualizace `type` vlastnost obdržíte kód odpovědi 404:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=10-11)]

### <a name="disable-problemdetails-response"></a>Zakázat ProblemDetails odezvy

Automatická tvorba `ProblemDetails` vypnutá při `SuppressMapClientErrors` je nastavena na `true`. Přidejte následující kód do `Startup.ConfigureServices`:

[!code-csharp[](index/samples/2.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

## <a name="additional-resources"></a>Další zdroje 

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
