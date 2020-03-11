---
title: Vytváření webových rozhraní API pomocí ASP.NET Core
author: scottaddie
description: Seznamte se se základy vytváření webového rozhraní API v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666003"
---
# <a name="create-web-apis-with-aspnet-core"></a>Vytváření webových rozhraní API pomocí ASP.NET Core

[Scott Addie](https://github.com/scottaddie) a [Dykstra](https://github.com/tdykstra)

ASP.NET Core podporuje vytváření služeb RESTful, označovaných také jako webová rozhraní API C#, pomocí. Webové rozhraní API zpracovává žádosti pomocí řadičů. *Řadiče* ve webovém rozhraní API jsou třídy, které jsou odvozeny z `ControllerBase`. V tomto článku se dozvíte, jak používat řadiče pro zpracování požadavků webového rozhraní API.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples). ([Stažení](xref:index#how-to-download-a-sample)).

## <a name="controllerbase-class"></a>ControllerBase – třída

Webové rozhraní API se skládá z jedné nebo více tříd kontroleru odvozených z <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Šablona projektu webového rozhraní API poskytuje kontroler Starter:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

Nevytvářejte kontroler webového rozhraní API odvozením z třídy <xref:Microsoft.AspNetCore.Mvc.Controller>. `Controller` se odvozují z `ControllerBase` a přidává podporu pro zobrazení, takže je možné zpracovávat webové stránky, nikoli požadavky webového rozhraní API. Toto pravidlo má výjimku: Pokud plánujete použít stejný kontroler pro obě zobrazení a webová rozhraní API, odvozuje je od `Controller`.

Třída `ControllerBase` poskytuje mnoho vlastností a metod, které jsou užitečné pro zpracování požadavků HTTP. `ControllerBase.CreatedAtAction` například vrátí stavový kód 201:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

Zde je několik příkladů metod, které `ControllerBase` poskytuje.

|Metoda   |Poznámky:    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| Vrátí stavový kód 400.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|Vrátí stavový kód 404.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|Vrátí soubor.|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|Vyvolá [vazbu modelu](xref:mvc/models/model-binding).|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|Vyvolá [ověření modelu](xref:mvc/models/validation).|

Seznam všech dostupných metod a vlastností naleznete v tématu <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.

## <a name="attributes"></a>Atributy

Obor názvů <xref:Microsoft.AspNetCore.Mvc> poskytuje atributy, které lze použít ke konfiguraci chování řadičů webového rozhraní API a metod akcí. Následující příklad používá atributy k určení podporovaného příkazu akce HTTP a všech známých stavových kódů HTTP, které by mohly být vráceny:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

Tady je několik příkladů atributů, které jsou k dispozici.

|Atribut|Poznámky:|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |Určuje vzor adresy URL pro kontroler nebo akci.|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |Určuje předponu a vlastnosti, které se mají zahrnout do vazby modelu.|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |Identifikuje akci, která podporuje příkaz akce HTTP GET.|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|Určuje datové typy, které akce akceptuje.|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|Určuje datové typy, které akce vrátí.|

Seznam, který obsahuje dostupné atributy, najdete v oboru názvů <xref:Microsoft.AspNetCore.Mvc>.

## <a name="apicontroller-attribute"></a>ApiController – atribut

Atribut [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) lze použít pro třídu kontroleru, aby bylo možné povolit následující dogmatickým chování specifické pro rozhraní API:

::: moniker range=">= aspnetcore-2.2"

* [Požadavek na směrování atributu](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození zdrojového parametru vazby](#binding-source-parameter-inference)
* [Multipart/form-odvození požadavku na data](#multipartform-data-request-inference)
* [Podrobnosti o problémech stavových kódů chyb](#problem-details-for-error-status-codes)

*Podrobnosti o problému pro stavové kódy chyb* vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,2 nebo novější. Ostatní funkce vyžadují verzi kompatibility 2,1 nebo novější.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [Požadavek na směrování atributu](#attribute-routing-requirement)
* [Automatické odpovědi HTTP 400](#automatic-http-400-responses)
* [Odvození zdrojového parametru vazby](#binding-source-parameter-inference)
* [Multipart/form-odvození požadavku na data](#multipartform-data-request-inference)

Tyto funkce vyžadují [kompatibilitu verze](xref:mvc/compatibility-version) 2,1 nebo novější.

::: moniker-end

### <a name="attribute-on-specific-controllers"></a>Atribut na určitých řadičích

Atribut `[ApiController]` lze použít pro konkrétní řadiče, jako v následujícím příkladu, ze šablony projektu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a>Atribut na více řadičích

Jedním z přístupů k použití atributu na více než jednom řadiči je vytvoření vlastní třídy základního kontroleru s poznámkou s atributem `[ApiController]`. Následující příklad ukazuje vlastní základní třídu a řadič, který je z něj odvozen:

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a>Atribut na sestavení

Je-li [verze kompatibility](xref:mvc/compatibility-version) nastavena na 2,2 nebo vyšší, lze atribut `[ApiController]` použít na sestavení. Anotace tímto způsobem aplikuje chování webového rozhraní API na všechny řadiče v sestavení. Neexistuje žádný způsob, jak odhlásit jednotlivé řadiče. Použijte atribut na úrovni sestavení pro deklaraci oboru názvů obklopující třídu `Startup`:

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

Atribut `[ApiController]` dělá směrování požadavku. Příklad:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure`.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

Akce jsou nepřístupné prostřednictvím [konvenčních tras](xref:mvc/controllers/routing#conventional-routing) definovaných <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> nebo <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> v `Startup.Configure`.

::: moniker-end

## <a name="automatic-http-400-responses"></a>Automatické odpovědi HTTP 400

Atribut `[ApiController]` způsobuje, že chyby ověření modelu automaticky aktivují odpověď HTTP 400. V důsledku toho následující kód není zbytečný v metodě akce:

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

ASP.NET Core MVC používá filtr akcí <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> k provedení předchozí kontroly.

### <a name="default-badrequest-response"></a>Výchozí odpověď důvodu chybného požadavku

V případě kompatibility verze 2,1 je výchozí typ odpovědi pro odpověď HTTP 400 <xref:Microsoft.AspNetCore.Mvc.SerializableError>. Následující text žádosti je příkladem serializovaného typu:

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

Typ `ValidationProblemDetails`:

* Poskytuje strojově čitelný formát pro zadávání chyb v odpovědích webového rozhraní API.
* Vyhovuje [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807).

::: moniker-end

### <a name="log-automatic-400-responses"></a>Protokolovat automatické odpovědi 400

Přečtěte si, [Jak protokolovat automatické odpovědi 400 na chyby ověření modelu (ASPNET/AspNetCore. Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).

### <a name="disable-automatic-400-response"></a>Zakázat automatickou odpověď 400

Chcete-li zakázat automatické chování 400, nastavte vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> na hodnotu `true`. Do `Startup.ConfigureServices`přidejte následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a>Odvození zdrojového parametru vazby

Zdrojový atribut vazby definuje umístění, kde je nalezena hodnota parametru akce. Existují následující zdrojové atributy vazby:

|Atribut|Zdroj vazby |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | Text požadavku |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | Data formuláře v textu žádosti |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | Hlavička požadavku |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | Parametr řetězce dotazu žádosti |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | Směrovat data z aktuální žádosti |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | Služba požadavku byla vložena jako parametr akce. |

> [!WARNING]
> Nepoužívejte `[FromRoute]`, pokud hodnoty mohou obsahovat `%2f` (`/`). `%2f` nebude `/`. Použijte `[FromQuery]`, pokud hodnota může obsahovat `%2f`.

Bez atributu `[ApiController]` nebo vazby zdrojových atributů jako `[FromQuery]`se ASP.NET Core modul runtime pokusí použít komplexní pořadač objektového modelu. Pořadač komplexního objektového modelu získává data od zprostředkovatelů hodnot v definovaném pořadí.

V následujícím příkladu atribut `[FromQuery]` označuje, že hodnota parametru `discontinuedOnly` je uvedena v řetězci dotazu adresy URL požadavku:

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Atribut `[ApiController]` aplikuje odvozená pravidla pro výchozí zdroje dat parametrů akce. Tato pravidla vám umožní určit ruční identifikaci zdrojů vazby použitím atributů u parametrů akce. Pravidla odvození zdroje vazby se chovají následujícím způsobem:

* `[FromBody]` je odvozený pro parametry komplexního typu. Výjimka pro pravidlo odvození `[FromBody]` je jakékoli komplexní, vestavěný typ se speciálním významem, jako je například <xref:Microsoft.AspNetCore.Http.IFormCollection> a <xref:System.Threading.CancellationToken>. Kód odvození zdroje vazby ignoruje tyto speciální typy.
* `[FromForm]` je odvozeno od parametrů akce typu <xref:Microsoft.AspNetCore.Http.IFormFile> a <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. Není odvozený pro žádné jednoduché nebo uživatelsky definované typy.
* `[FromRoute]` se odvozuje pro všechny názvy parametrů akcí, které odpovídají parametru v šabloně směrování. Pokud parametr akce odpovídá víc než jedna trasa, hodnota trasy se považuje za `[FromRoute]`.
* `[FromQuery]` se odvozuje pro všechny ostatní parametry akce.

### <a name="frombody-inference-notes"></a>Poznámky k odvozování FromBody

`[FromBody]` nejsou odvozené pro jednoduché typy, jako je `string` nebo `int`. Proto by měl atribut `[FromBody]` použít pro jednoduché typy, pokud je tato funkce potřebná.

Pokud má akce více než jeden parametr vázaný z těla požadavku, je vyvolána výjimka. Například všechny následující signatury metody Action způsobují výjimku:

* `[FromBody]` odvozená v obou případech, protože se jedná o komplexní typy.

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* `[FromBody]` atribut na jednom, odvozený na druhém, protože se jedná o komplexní typ.

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* atribut `[FromBody]` v obou.

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> V ASP.NET Core 2,1 jsou parametry typu kolekce, jako jsou seznamy a pole, nesprávně odvozeny jako `[FromQuery]`. Atribut `[FromBody]` by měl být použit pro tyto parametry, pokud mají být vázány z textu žádosti. Toto chování se opravuje v ASP.NET Core 2,2 nebo novějším, kde jsou parametry typu kolekce odvoditelné z těla ve výchozím nastavení.

::: moniker-end

### <a name="disable-inference-rules"></a>Zakázat odvození pravidel

Chcete-li zakázat odvození zdroje vazby, nastavte <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> na `true`. Do `Startup.ConfigureServices`přidejte následující kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a>Multipart/form-odvození požadavku na data

Atribut `[ApiController]` aplikuje pravidlo odvození, pokud je parametr akce opatřen poznámkou s atributem [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) . Je odvozený typ obsahu žádosti o `multipart/form-data`.

Chcete-li zakázat výchozí chování, nastavte vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> na `true` v `Startup.ConfigureServices`:

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

## <a name="problem-details-for-error-status-codes"></a>Podrobnosti o problémech stavových kódů chyb

Pokud je verze kompatibility 2,2 nebo novější, MVC transformuje výsledek chyby (výsledek s kódem stavu 400 nebo vyšší) na výsledek s <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>. `ProblemDetails` typ je založen na [specifikaci RFC 7807](https://tools.ietf.org/html/rfc7807) pro poskytování podrobností o chybě čitelné počítačem v odpovědi HTTP.

V akci kontroleru zvažte následující kód:

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

Metoda `NotFound` generuje stavový kód HTTP 404 s `ProblemDetails` tělo. Příklad:

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a>Zakázat odpověď ProblemDetails

Automatické vytváření `ProblemDetails` stavových kódů chyb je zakázáno, pokud je vlastnost <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> nastavena na `true`. Do `Startup.ConfigureServices`přidejte následující kód:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a>Definujte podporované typy obsahu požadavků pomocí atributu [resupporteds].

Ve výchozím nastavení akce podporuje všechny dostupné typy obsahu žádostí. Například pokud je aplikace nakonfigurována tak, aby podporovala [vstupní formátovací](xref:mvc/models/model-binding#input-formatters)moduly JSON a XML, akce podporuje více typů obsahu, včetně `application/json` a `application/xml`.

Atribut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) ] umožňuje akci omezit podporované typy obsahu požadavků. Použijte atribut `[Consumes]` pro akci nebo kontroler a určete jeden nebo více typů obsahu:

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

V předchozím kódu akce `CreateProduct` určuje typ obsahu `application/xml`. Požadavky směrované na tuto akci musí určovat `Content-Type` záhlaví `application/xml`. V případě požadavků, které nespecifikují hlavičku `Content-Type` `application/xml` je výsledkem [Nepodporovaná odpověď typu média 415](https://developer.mozilla.org/docs/Web/HTTP/Status/415) .

Atribut `[Consumes]` také umožňuje, aby akce ovlivnila svůj výběr na základě typu obsahu příchozího požadavku, a to použitím omezení typu. Vezměte v úvahu v následujícím příkladu:

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

V předchozím kódu je `ConsumesController` nakonfigurován tak, aby zpracovávala požadavky odeslané na adresu URL `https://localhost:5001/api/Consumes`. Obě akce kontroleru `PostJson` a `PostForm`zpracovávají požadavky POST pomocí stejné adresy URL. Bez atributu `[Consumes]`, který používá omezení typu, je vyvolána výjimka nejednoznačné shody.

Atribut `[Consumes]` se aplikuje na obě akce. Akce `PostJson` zpracovává požadavky odeslané pomocí `Content-Type` záhlaví `application/json`. Akce `PostForm` zpracovává požadavky odeslané pomocí `Content-Type` záhlaví `application/x-www-form-urlencoded`. 

## <a name="additional-resources"></a>Další zdroje

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
