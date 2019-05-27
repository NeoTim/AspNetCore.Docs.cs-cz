---
title: Filtry v ASP.NET Core
author: ardalis
description: Zjistěte, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 5/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: cdf121b97396cb23103d49cd141b9ef19b8c0cc6
ms.sourcegitcommit: e1623d8279b27ff83d8ad67a1e7ef439259decdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2019
ms.locfileid: "66223026"
---
# <a name="filters-in-aspnet-core"></a>Filtry v ASP.NET Core

Podle [Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra/), a [Steve Smith](https://ardalis.com/)

*Filtry* v ASP.NET Core umožňují kód ke spuštění před nebo po určitým fázím v kanálu zpracování požadavků.

Integrované filtry naložit s úkoly, jako:

* Autorizace (zabránění přístupu k prostředkům, které uživatel nemá oprávnění pro).
* Odpověď do mezipaměti (krátký cyklus kanál požadavku na vrátí odpověď uložená v mezipaměti).

Vlastní filtry lze vytvořit pro zpracování vyskytující aspekty. Příklady související aspekty: zpracování chyb, ukládání do mezipaměti, konfiguraci, ověření a protokolování.  Filtry předejít duplikování kódu. Zpracování filtru výjimek chyby může například konsolidovat zpracování chyb.

Tento dokument se vztahuje na stránky Razor, kontrolery rozhraní API a řadiče se zobrazeními.

[Zobrazení nebo stažení ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Jak fungují filtry

Filtry se spouští v rámci *kanálu vyvolání akce ASP.NET Core*, která se někdy označují jako *filtrovat*.  Spuštění kanálu filtru po ASP.NET Core vybere akci pro spuštění.

![Další Middleware, směrování Middleware, výběr akce a vyvolání kanálu ASP.NET Core akce zpracování žádosti. Zpracování žádosti pokračuje zpět přes výběr akce, Middleware směrování a různé další Middleware než se náplní odpověď zaslána klientovi.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrů

Různé fáze v kanálu filtr spuštění jednotlivých typů filtrů:

* [Filtry autorizace](#authorization-filters) spouští jako první a slouží k určení, zda je uživatel autorizován pro daný požadavek. Filtry autorizace zkrácenou kanálu, pokud žádost neoprávněná.

* [Filtry prostředků](#resource-filters):

  * Po povolení spusťte.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> můžete spustit kód před filtr zbytku. Například `OnResourceExecuting` může spustit kód před navázáním modelu.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> můžete spustit kód po dokončení rest z kanálu.

* [Filtry akcí](#action-filters) může spustit kód bezprostředně před a po volání metody jednotlivé akce. Můžete být použít k manipulaci s argumenty předané do akce a výsledek vrácený z akce. Filtry akce jsou **není** podporované v stránky Razor.

* [Filtry výjimek](#exception-filters) umožňují použití globálních zásad pro neošetřených výjimek, ke kterým dojde před nic se zapsala do datové části odpovědi.

* [Výsledek filtry](#result-filters) může spustit kód bezprostředně před a po spuštění výsledky jednotlivých akcí. Spouštějí se pouze v případě, že byl úspěšně proveden metody akce. Jsou užitečné pro logiku, která se musí před a za spuštění zobrazení nebo formátovacího modulu.

Následující diagram znázorňuje interakci typy filtr v kanálu filtru.

![Žádost se zpracovává prostřednictvím filtry autorizace, prostředků filtry, vazby modelu, filtry akce, provedení akce a převod výsledek akce, filtry výjimek, filtry výsledků a výsledek spuštění. Na cestě navýšení kapacity pouze zpracuje požadavek výsledek filtry a filtry prostředků než se náplní odpověď zaslána klientovi.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementace

Filtrů podporuje synchronní a asynchronní implementace prostřednictvím různých rozhraní definice.

Synchronní filtry můžete spuštění kódu před (`On-Stage-Executing`) a po provedení (`On-Stage-Executed`) jejich fázi zřetězení. Například `OnActionExecuting` je volána před voláním metody akce. `OnActionExecuted` je volána po vrácení metody akce.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Definování asynchronní filtry `On-Stage-ExecutionAsync` metody:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

V předchozím kódu `SampleAsyncActionFilter` má <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`), který provede metodu akce.  Každá z `On-Stage-ExecutionAsync` metody přijímají `FilterType-ExecutionDelegate` fázi zřetězení tohoto filtru, který se spustí.

### <a name="multiple-filter-stages"></a>Více fázích filtru

Rozhraní pro více fázích filtru je možné implementovat do jedné třídy. Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje třída `IActionFilter`, `IResultFilter`a jejich ekvivalenty asynchronní.

Implementace **buď** synchronní nebo asynchronní verze rozhraní filtru, **není** obojí. Modul runtime nejprve zkontroluje zobrazit, pokud filtr, implementuje rozhraní asynchronní, a pokud ano, který volá. Pokud tomu tak není, volá rozhraní synchronní metody. Pokud se synchronní a asynchronní rozhraní jsou implementovány v jedné třídy, se nazývá asynchronní metody. Při používání abstraktních tříd jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> přepsání pouze metody synchronní nebo asynchronní metody pro každý typ filtru.

### <a name="built-in-filter-attributes"></a>Integrovaný filtr atributy

ASP.NET Core zahrnuje integrované filtry založených na atributech, které mohou být rozčlenění a přizpůsobit. Například následující filtr výsledků přidá do odpovědi hlavičku:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Atributy Povolit filtry, které přijímají argumenty, jak je znázorněno v předchozím příkladu. Použít `AddHeaderAttribute` metodě kontroler nebo akce a zadejte název a hodnotu hlavičky protokolu HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Některé z rozhraní filtru mít odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.

Atributy filtru:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Filtr oborů a pořadí provádění

Filtr lze přidat do kanálu na jeden ze tří *obory*:

* Pomocí atributu na akci.
* Pomocí atributu na kontroleru.
* Globálně pro všechny kontrolery a akce, jak je znázorněno v následujícím kódu:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Předchozí kód přidá globálně pomocí tří filtry [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) kolekce.

### <a name="default-order-of-execution"></a>Výchozí pořadí provádění

Pokud jsou použity více filtry pro konkrétní fázi kanálu, obor Určuje výchozí pořadí spuštění filtrů.  Globální filtry před a za třídy filtrů, které pak před a za metoda filtry.

V důsledku filtru vnoření *po* spuštění kódu filtrů v obráceném pořadí *před* kódu. Pořadí filtru:

* *Před* kód globálních filtrů.
  * *Před* kód filtrů kontroleru.
    * *Před* kód filtrů metody akce.
    * *Po* kód filtrů metody akce.
  * *Po* kód filtrů kontroleru.
* *Po* kód globálních filtrů.
  
Následující příklad, který znázorňuje pořadí, ve které filtru jsou volány metody pro synchronní akce filtry.

| Sequence | Obor filtru | Filter – metoda |
|:--------:|:------------:|:-------------:|
| 1 | Globální | `OnActionExecuting` |
| 2 | Kontroler | `OnActionExecuting` |
| 3 | Metoda | `OnActionExecuting` |
| 4 | Metoda | `OnActionExecuted` |
| 5 | Kontroler | `OnActionExecuted` |
| 6 | Globální | `OnActionExecuted` |

Zobrazí se toto pořadí:

* Metoda filtr je vnořená v rámci kontroleru filtru.
* Filtr kontroleru je vnořená v rámci globálních filtrů.

### <a name="controller-and-razor-page-level-filters"></a>Filtry na úrovni kontroleru a stránky Razor

Každý kontroler, který dědí z <xref:Microsoft.AspNetCore.Mvc.Controller> základní třída zahrnuje [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), a [ Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` metody. Tyto metody:

* Zabalení, na kterých běží filtry pro danou akci.
* `OnActionExecuting` je volána před provedením akce filtry.
* `OnActionExecuted` je volána po všech filtrů akce.
* `OnActionExecutionAsync` je volána před provedením akce filtry. Kód ve filtru po `next` běží po metodě akce.

Například v ukázce stahování `MySampleActionFilter` platí globálně ve spuštění.

`TestController`:

* Se vztahuje `SampleActionFilterAttribute` (`[SampleActionFilter]`) k `FilterTest2` akce:
* Přepíše `OnActionExecuting` a `OnActionExecuted`.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Přejděte na `https://localhost:5001/Test/FilterTest2` spustí následující kód:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Stránky Razor, naleznete v tématu [filtry stránky Razor implementace přepsáním metody filtr](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Přepsání výchozího pořadí

Výchozí pořadí provádění lze přepsat pomocí implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>. `IOrderedFilter` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před obor pro určení pořadí spouštění. Filtr s nižší `Order` hodnotu:

* Spuštění *před* kódu před inicializací filtr s vyšší hodnotou `Order`.
* Spuštění *po* kódu po tomto filtr s vyšší `Order` hodnotu.

`Order` Vlastnost lze nastavit pomocí parametru konstruktoru:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Vezměte v úvahu stejné filtry akce 3 v předchozím příkladu. Pokud `Order` řadiče a globální filtry je nastavena na 1 a 2 v uvedeném pořadí, je obrácený pořadí provádění.

| Sequence | Obor filtru | `Order` Vlastnost | Filter – metoda |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Metoda | 0 | `OnActionExecuting` |
| 2 | Kontroler | 1  | `OnActionExecuting` |
| 3 | Globální | 2  | `OnActionExecuting` |
| 4 | Globální | 2  | `OnActionExecuted` |
| 5 | Kontroler | 1  | `OnActionExecuted` |
| 6 | Metoda | 0  | `OnActionExecuted` |

`Order` Vlastnost přepíše obor při určování pořadí, ve které filtry spustit. Filtry jsou řazeny nejdříve podle pořadí a oboru se používá k rozdělení vazby. Všechny integrované filtry implementovat `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0. Obor pro integrované filtry, určuje pořadí, není-li `Order` nastaven na nenulovou hodnotu.

## <a name="cancellation-and-short-circuiting"></a>Zrušení a krátký cyklus

Filtr kanálu můžete short-circuited nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnost na <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr předán metodě filtru. Například následující filtr prostředků zabraňuje rest z kanálu provádění:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

V následujícím kódu jak `ShortCircuitingResourceFilter` a `AddHeader` cílový filtr `SomeResource` metody akce. `ShortCircuitingResourceFilter`:

* Spustí nejdříve, protože je filtr prostředků a `AddHeader` je filtr akce.
* Zkratům rest z kanálu.

Proto `AddHeader` filtr nikdy spuštěn `SomeResource` akce. Toto chování by být stejný Pokud byly použity oba filtry na úrovni metody akce k dispozici `ShortCircuitingResourceFilter` spustil první. `ShortCircuitingResourceFilter` Spustí první z důvodu jeho typ filtru nebo explicitní použití `Order` vlastnost.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injektáž závislostí

Filtry je možné přidat podle typu nebo instance. Pokud je přidána instance, se pro každý požadavek používá tuto instanci. Pokud se přidá typ, je aktivovat typu. Aktivovat typ filtru znamená, že:

* Pro každý požadavek je vytvořena instance.
* Všechny závislosti konstruktoru se vyplní podle [injektáž závislostí](xref:fundamentals/dependency-injection) (DI).

Filtry, které jsou implementovány jako atributy a přidat přímo do třídy kontroleru nebo metody akce nemůže mít konstruktor závislosti poskytované [injektáž závislostí](xref:fundamentals/dependency-injection) (DI). Závislosti konstruktoru nemůže poskytovat DI, protože:

* Atributy musí mít zadané, kde uplatňují jejich parametry konstruktoru. 
* Jedná se omezení fungování atributy.

Následující filtry podpory poskytované DI závislosti konstruktoru:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno v atributu.

Předchozí filtry můžete použít pro metodu kontroler nebo akce:

Protokolovací nástroje jsou k dispozici DI. Ale Vyhněte se vytváření a používání filtrů výhradně pro účely protokolování. [Vestavěnou architekturou protokolování](xref:fundamentals/logging/index) obvykle poskytuje toho, co je potřeba pro protokolování. Přidat do filtrů protokolování:

* Byste se zaměřit na aspekty obchodní domény nebo chování specifické pro filtr.
* By měl **není** protokolu akcí nebo jiné rozhraní události. Integrované filtry akce a framework události protokolu.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Tyto typy implementace filtr služby jsou registrované ve `ConfigureServices`. A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> načte z DI instance filtru.

Následující kód ukazuje `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

V následujícím kódu `AddHeaderResultServiceFilter` se přidá do kontejnerů DI:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtr ze sloupce DI:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Poskytuje nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku. Modul runtime ASP.NET Core nezaručuje:

  * Vytvoří se jedna instance filtru.
  * Filtr nebudou vyžádána znovu z kontejnerů DI někdy později.

* Se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` načte zadaného typu z DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, ale jeho typ není přímo z kontejnerů DI přeložit. Vytvoření instance typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.

Protože `TypeFilterAttribute` nejsou přímo z kontejnerů DI přeložit typy:

* Typy, které jsou odkazovány pomocí `TypeFilterAttribute` nemusíte být registrována pomocí kontejnerů DI.  Mají jejich závislosti podle kontejnerů DI splněny.
* `TypeFilterAttribute` Volitelně může přijímat argumenty konstruktoru typu.

Při použití `TypeFilterAttribute`a nastavte `IsReusable` se o nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku. Modul runtime ASP.NET Core poskytuje žádnou záruku, že jedna instance filtru bude vytvořen. `IsReusable` se nemá používat s filtr, který závisí na službách s životností jiného než typu singleton.

Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtry autorizace

Filtry autorizace:

* Běží první filtry v kanálu filtru.
* Řízení přístupu na metody akce.
* Máte před metodou, ale ne po metodě.

Filtry autorizace vyžadují framework vlastní autorizace. Preferovat konfigurace zásad autorizace nebo zápis vlastní zásady autorizace přes psaní vlastního filtru. Filtr autorizace integrovaná:

* Volání autorizace systému.
* Nepovolí požadavky.

Proveďte **není** vyvolat výjimky v rámci filtry autorizace:

* Tato výjimka není ošetřena.
* Filtry výjimek nebude zpracování výjimky.

Zvažte výzvu, když dojde k výjimce v filtr autorizace.

Další informace o [autorizace](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry prostředků

Filtry prostředků:

* Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> rozhraní.
* Spuštění zabalí většinu kanálu filtru.
* Pouze [filtry autorizace](#authorization-filters) spustit před filtry prostředků.

Filtry prostředků jsou užitečné pro zkrácenou většinu kanálu. Například filtr ukládání do mezipaměti se můžete vyhnout zbytku na přístup do mezipaměti.

Příklady prostředků filtru:

* [Short-circuiting filtr prostředků](#short-circuiting-resource-filter) uvedenému výše.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Zabraňuje vazby modelu v přístupu k data formuláře.
  * Používá se pro nahrávání velkých souborů zabránit načítána do paměti data formuláře.

## <a name="action-filters"></a>Filtry akcí

> [!IMPORTANT]
> Filtry akce provést **není** platí pro stránky Razor. Stránky Razor podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).

Filtry akcí:

* Implementovat buď <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> rozhraní.
* Jejich spuštění obklopuje provádění metody akce.

Následující kód ukazuje ukázkový filtr akce:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – umožňuje načíst vstupní hodnoty pro metodu akce.
* <xref:Microsoft.AspNetCore.Mvc.Controller> – umožňuje manipulaci s instance kontroleru.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> -nastavení `Result` zkratům provádění metody akce a filtry následné akce.

Došlo k výjimce v metodě akce:

* Zabraňuje spuštění následujícími filtry.
* Na rozdíl od nastavení `Result`, je považováno za selhání místo úspěšný výsledek.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, pokud spuštění akce byla zkratována jiný filtr.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> Bez-hodnota null, pokud akci nebo filtr dříve spuštění akce došlo k výjimce. Nastavení této vlastnosti na hodnotu null:

  * Efektivně ošetří výjimku.
  * `Result` je provedeno, jako kdyby byla vrácena z metody akce.

Pro `IAsyncActionFilter`, volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:

* Spustí všechny filtry následné akce a metody akce.
* Vrátí `ActionExecutedContext`.

Chcete-li zkrácenou, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> do výsledku instance a volat `next` ( `ActionExecutionDelegate`).

Rozhraní poskytuje abstraktní <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , který může být rozčlenit do podtříd.

`OnActionExecuting` Filtr akce lze provádět:

* Ověření stavu modelu.
* Vrátí chybu, pokud stav není platný.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted` Metoda spustí po metodě akce:

* A můžete zobrazit a pracovat s výsledky akce prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> vlastnost.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastavena na hodnotu true, pokud spuštění akce byla zkratována jiný filtr.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu než null, pokud akci nebo filtr následné akce došlo k výjimce. Nastavení `Exception` na hodnotu null:

  * Efektivně zpracovává výjimku.
  * `ActionExecutedContext.Result` je provedeno, jako kdyby byly obvykle vrátil z metody akce.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtry výjimek

Filtry výjimek:

* Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Je možné implementovat zásady pro zpracování běžných chyb.

Následující vzorek filtru výjimek zobrazení vlastní chybové používá k zobrazení podrobností o výjimkách, ke kterým dochází, když je aplikace ve vývoji:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Filtry výjimek:

* Není k dispozici před a po události.
* Implementace <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Zpracování neošetřených výjimek, ke kterým dochází v stránky Razor nebo vytváření řadiče [vazby modelu](xref:mvc/models/model-binding), filtrů Akce nebo metody akce.
* Proveďte **není** zachytit výjimky, ke kterým dochází v prostředku filtry, filtry výsledků nebo provedení výsledku MVC.

Ke zpracování výjimky, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost `true` nebo zápisu odpovědi. Tím se zastaví šíření výjimky. Filtr výjimek nelze zapnout výjimku do "ÚSPĚCH". Pouze filtru akce můžete to udělat.

Filtry výjimek:

* Jsou vhodné pro zachytávání výjimek, které se vyskytují v rámci akce.
* Nejsou tak flexibilní jako middleware pro zpracování chyb.

Preferovat middleware pro zpracování výjimek. Výjimky použijte filtry pouze tehdy, pokud zpracování chyb *se liší* v závislosti na způsobu akce je volána. Aplikace může mít například metody akci pro oba koncové body rozhraní API a pro zobrazení a HTML. Koncové body rozhraní API můžou se zobrazovat informace o chybě jako dokumenty JSON, zatímco akce na základě zobrazení může vrátit chybovou stránku ve formátu HTML.

## <a name="result-filters"></a>Filtry výsledků

Filtry výsledků:

* Implementujte rozhraní:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Jejich spuštění obklopuje provádění výsledky akce.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter a IAsyncResultFilter

Následující kód ukazuje výsledek filtr, který přidá hlavičku protokolu HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Typ výsledku prováděný závisí na akci. Akce vrácení zobrazení bude zahrnovat všechny razor zpracování v rámci <xref:Microsoft.AspNetCore.Mvc.ViewResult> spouštěna. Metodu rozhraní API může provést některé serializace jako součást provedení výsledku. Další informace o [výsledky akcí](xref:mvc/controllers/actions)

Filtry výsledků jsou vykonány pouze pro úspěšné výsledky – při akci nebo filtrů akce vytvoří výsledek akce. Filtry výsledků nejsou provedeny, když filtry výjimek zpracování výjimky.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> Metoda můžete zkrácenou provedení výsledku akce a filtry následujících výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> k `true`. Zápis do objektu odpovědi při zkrácení pro zabránění generování odpověď je prázdná. Došlo k výjimce `IResultFilter.OnResultExecuting` bude:

* Zabraňte spuštění výsledku akce a následné filtry.
* Být považován za spadne, místo aby úspěšný výsledek.

Když <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> metoda spuštění:

* Odpověď pravděpodobně byl odeslán do klienta a nedá se změnit.
* Pokud došlo k výjimce, nejsou zasílány text odpovědi.

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

`ResultExecutedContext.Canceled` je nastavena na `true` Pokud provedení výsledku akce byla zkratována jiný filtr.

`ResultExecutedContext.Exception` je nastavena na hodnotu než null, pokud výsledek akce nebo filtr následné výsledků došlo k výjimce. Nastavení `Exception` na null efektivně zpracovává výjimku a brání výjimka z právě znovu ASP.NET Core později v kanálu. Neexistuje žádný spolehlivý způsob při zpracování výjimky v filtr výsledků zapsat data do odpovědi. Pokud hlavičky mají klientovi vyprazdňuje, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus poslat kód selhání.

Pro <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, volání `await next` na <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné výsledek filtry a výsledku akce. Zkrácenou, nastavte [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) k `true` a nemůžete volat `ResultExecutionDelegate`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Rozhraní poskytuje abstraktní `ResultFilterAttribute` , který může být rozčlenit do podtříd. [AddHeaderAttribute](#add-header-attribute) uvedená třída dříve je příkladem atribut filtru výsledků.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> rozhraní deklarovat <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementace, která spustí pro všechny výsledky akce. Filtr platí pro všechny výsledky akce, pokud:

* <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> Nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> platí a zkratům odpovědi.
* Filtr výjimek zpracovává výjimku vytvářením výsledek akce.

Filtry pro jiné než `IExceptionFilter` a `IAuthorizationFilter` není zkrácenou `IAlwaysRunResultFilter` a `IAsyncAlwaysRunResultFilter`.

Například následující filtr vždy spustí a nastaví výsledek akce (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) s *422 nezpracované Entity* stavový kód, pokud selže vyjednávání obsahu:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Proto `IFilterFactory` instance může sloužit jako `IFilterMetadata` instance kdekoli v kanálu filtru. Když modul runtime se připravuje k vyvolání filtr, pokusí se vysílat na `IFilterFactory`. Pokud je úspěšná tohoto přetypování <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metoda je volána k vytvoření `IFilterMetadata` instanci, která je vyvolána. To poskytuje flexibilní návrhu, protože kanál přesné filtru nemusí být explicitně nastaveno při spuštění aplikace.

`IFilterFactory` je možné implementovat jako jiný přístup k vytváření filtrů pomocí implementace vlastního atributu:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Předchozí kód můžete otestovat spuštěním [stáhněte si ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Vyvolání vývojářských nástrojů F12.
* Přejděte na `https://localhost:5001/Sample/HeaderWithFactory`

Vývojářské nástroje F12 pomáhají zobrazit následující hlavičky odpovědi přidal ukázkový kód:

* **Autor:** `Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **Interní:** `My header`

Předchozí kód vytvoří **interní:** `My header` hlavičky odpovědi.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementovaná v atributu

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtry, které implementují `IFilterFactory` jsou užitečné pro filtry, které:

* Předávání parametrů nevyžadují.
* Mají konstruktor závislosti, které potřebujete pro vyplnění podle DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` Zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` načte zadaného typu ze služby kontejnerů (DI).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

V předchozím kódu, upravení metody `[SampleActionFilter]` je upřednostňovaný způsob použití `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Pomocí middleware v kanálu filtru

Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že jsou před a za provádění všeho, která se dodává později v kanálu. Ale filtry liší od middleware, že jsou součástí modulu runtime ASP.NET Core, což znamená, že mají přístup ke kontextu ASP.NET Core a konstrukce.

Použití middlewaru jako filtr, vytvoření typu s `Configure` metodu, která určuje middlewaru, který má být vložení do kanálu filtru. Lokalizace middleware pro aktuální jazykovou verzi pro žádost o navázání v následujícím příkladu:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Použití <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> pro spuštění middlewaru:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Middleware filtry třídí ve stejné fázi kanálu filtr jako prostředek filtry, před navázáním modelu a po zbytek kanálu.

## <a name="next-actions"></a>Další akce

* Zobrazit [metody filtrování pro Razor Pages](xref:razor-pages/filter)
* Můžete experimentovat s filtry, [stažení, otestovat a upravit na Githubu ukázky](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).
