---
title: Filtry v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: eeae167286e793ecd5a547cea0142cf7d8014ece
ms.sourcegitcommit: c0a15ab8549cb729731a0fdf1d7da0b7feaa11ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671779"
---
# <a name="filters-in-aspnet-core"></a>Filtry v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)

*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.

Předdefinované filtry zpracovávají úlohy, jako například:

* Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).
* Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).

Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek. Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.  Filtry zabraňují duplikování kódu. Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.

Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními. Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index). Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:

* Komponenta je vložena do stránky nebo zobrazení.
* Stránka nebo kontroler nebo zobrazení používá filtr.

[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Jak fungují filtry

Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*. Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce. Zpracování žádosti pokračuje zpět pomocí výběru akcí, middlewaru směrování a jiného middleware před odesláním odpovědi klientovi.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrů

Každý typ filtru se spustí v jiné fázi kanálu filtru:

* [Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek. Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.

* [Filtry prostředků](#resource-filters):

  * Spusťte po autorizaci.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> spustí kód před zbytek kanálu filtru. Například `OnResourceExecuting` spustí kód před vazbou modelu.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> spustí kód po dokončení zbývající části kanálu.

* [Filtry akcí](#action-filters):

  * Spustit kód bezprostředně před a po volání metody Action.
  * Může změnit argumenty předané do akce.
  * Může změnit výsledek vrácený z akce.
  * Nejsou **na** stránkách podporovány Razor .

* [Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.

* [Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce. Spustí se pouze v případě, že metoda akce byla úspěšně provedena. Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.

Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků. Na cestě je požadavek zpracován pouze pomocí filtrů výsledků a filtrů prostředků před tím, než se stane odpověď odeslanou klientovi.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementace

Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.

Synchronní filtry spouštějí kód před a po fázi zřetězení. Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> je volána po návratu metody Action.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

V předchozím kódu je [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) funkcí nástroje v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu. Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.

### <a name="multiple-filter-stages"></a>Několik fází filtru

Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy. Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:

* Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí. Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá. V takovém případě volá metody synchronního rozhraní. Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda. Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je, přepište pouze synchronní metody nebo asynchronní metody pro každý typ filtru.

### <a name="built-in-filter-attributes"></a>Předdefinované atributy filtru

ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené. Například následující filtr výsledků přidá hlavičku do odpovědi:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu. Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) . V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.

Následující kód implementuje `ActionFilterAttribute` :

* Přečte název a název z konfiguračního systému. Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.
* Přidá název a název do hlavičky odpovědi.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options). Například z *appsettings.js* souboru:

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

V `StartUp.ConfigureServices` :

* `PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.
* `MyActionFilterAttribute`Přidá se do kontejneru služby.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

Následující kód ukazuje `PositionOptions` třídu:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.

Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Filtry nelze použít na Razor metody obslužné rutiny stránky. Lze je použít buď na Razor model stránky, nebo globálně.

Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.

Atributy filtru:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Filtrovat obory a pořadí provádění

Filtr lze přidat do kanálu v jednom ze tří *oborů*:

* Použití atributu u akce kontroleru. Atributy filtru nelze použít na Razor stránky metod obslužných rutin.
* Použití atributu na řadiči nebo na Razor stránce.
* Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Výchozí pořadí provádění

Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.  Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.

V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem. Pořadí filtru:

* *Před* kódem globálních filtrů.
  * *Před* kódem kontroleru a Razor filtru stránky.
    * Kód *před* filtry metody Action.
    * *Po* kódu filtry metody Action.
  * *Po* kódu filtru řadiče a Razor stránky.
* *Po* kódu globálních filtrů.
  
Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.

| Sequence | Rozsah filtru | Filter – metoda |
|:--------:|:------------:|:-------------:|
| 1 | Globální | `OnActionExecuting` |
| 2 | Kontroler nebo Razor Stránka| `OnActionExecuting` |
| 3 | Metoda | `OnActionExecuting` |
| 4 | Metoda | `OnActionExecuted` |
| 5 | Kontroler nebo Razor Stránka | `OnActionExecuted` |
| 6 | Globální | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filtry na úrovni řadiče

Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` . Tyto metody:

* Zabalte filtry, které se spouštějí pro danou akci.
* `OnActionExecuting` je volána před jakýmkoli filtrem akce.
* `OnActionExecuted` se volá po všech filtrech akcí.
* `OnActionExecutionAsync` je volána před jakýmkoli filtrem akce. Kód v filtru po `next` spuštění po metodě Action.

Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.

Pomocná rutina `TestController`:

* Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.
* Přepisuje `OnActionExecuting` a `OnActionExecuted` .

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` . Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody. Pořadí je vysvětleno v další části.

RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Přepsání výchozího pořadí

Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> . `IOrderedFilter` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění. Filtr s nižší `Order` hodnotou:

* Spustí *před* kódem filtr s vyšší hodnotou `Order` .
* Spustí *za* kódem za filtr s vyšší `Order` hodnotou.

`Order`Vlastnost je nastavena s parametrem konstruktoru:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Vezměte v úvahu dva filtry akcí v následujícím kontroleru:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

Globální filtr je přidaný v `StartUp.ConfigureServices` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

Tři filtry jsou spouštěny v následujícím pořadí:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny. Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor. Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0. Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.

V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru. Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Zrušení a zkrácení okruhů

Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter. Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action. Pomocná rutina `ShortCircuitingResourceFilter`:

* Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.
* Krátké okruhy zbývajícího kanálu.

Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` . Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první. `ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a>Injektáž závislostí

Filtry lze přidat podle typu nebo podle instance. Pokud je přidána instance, bude tato instance použita pro každý požadavek. Pokud je přidán typ, je aktivován typ. Filtr aktivovaný typu znamená:

* Instance se vytvoří pro každý požadavek.
* Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).

Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di). Pomocí DI nelze poskytnout závislosti konstruktoru:

* V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány. 
* Toto je omezení způsobu fungování atributů.

Následující filtry podporují závislosti konstruktoru poskytované od DI:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno u atributu.

Předchozí filtry lze použít pro metodu kontroleru nebo akce:

Protokolovací nástroje jsou k dispozici z DI. Ale nevytvářejte a používejte filtry čistě pro účely protokolování. [Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování. Protokolování přidáno do filtrů:

* Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.
* Neměli **byste** protokolovat akce nebo jiné události rozhraní. Vestavěné filtry protokolují akce a události rozhraní.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` . <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.

Následující kód ukazuje `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci. Modul runtime ASP.NET Core nezaručuje:

  * Vytvoří se jedna instance filtru.
  * Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.

* Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` Načte zadaný typ z DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di. Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .

Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:

* Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.  Mají své závislosti splněné kontejnerem DI.
* `TypeFilterAttribute` může volitelně přijmout argumenty konstruktoru pro typ.

Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci. Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.

* Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.

Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtry autorizace

Filtry autorizace:

* Jsou první filtry spouštěny v kanálu filtru.
* Řízení přístupu k metodám akcí.
* Před metodou, ale ne po metodě.

Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní. Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru. Vestavěný autorizační filtr:

* Volá autorizační systém.
* Neautorizuje požadavky.

Negenerovat **výjimky** v rámci autorizačních filtrů:

* Výjimka nebude zpracována.
* Filtry výjimek nebudou zpracovávat výjimku.

Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.

Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry prostředků

Filtry prostředků:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .
* Provádění zabalí většinu kanálu filtru.
* Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .

Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu. Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.

Příklady filtru prostředků:

* [Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Zabraňuje vazbě modelu v přístupu k datům formuláře.
  * Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.

## <a name="action-filters"></a>Filtry akcí

Filtry **akcí se nevztahují** na Razor stránky. Razor Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).

Filtry akcí:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .
* Jejich spuštění obklopuje provádění metod akcí.

Následující kód ukazuje filtr vzorové akce:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – povolí čtení vstupů do metody Action.
* <xref:Microsoft.AspNetCore.Mvc.Controller> – povolí manipulaci s instancí kontroleru.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> – nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.

Vyvolání výjimky v metodě akce:

* Zabraňuje spuštění dalších filtrů.
* Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, pokud bylo provedení akce zkráceno jiným filtrem.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> -Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí. Nastavení této vlastnosti na hodnotu null:

  * Efektivně zpracovává výjimku.
  * `Result` je spuštěn, jako kdyby byl vrácen z metody Action.

V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Provede všechny následné filtry akcí a metodu Action.
* Vrací objekt `ActionExecutedContext`.

Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).

Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.

`OnActionExecuting`Filtr akcí lze použít k těmto akcím:

* Ověří stav modelu.
* Pokud je stav neplatný, vrátí se chyba.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Řadiče s poznámkou s `[ApiController]` atributem automaticky ověří stav modelu a vrátí odpověď 400. Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).

`OnActionExecuted`Metoda se spustí za metodou akce:

* A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku. Nastavení `Exception` na hodnotu null:

  * Efektivně zpracovává výjimku.
  * `ActionExecutedContext.Result` je spuštěn, jako kdyby byl vrácen normálně z metody Action.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtry výjimek

Filtry výjimek:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .
* Dá se použít k implementaci běžných zásad zpracování chyb.

Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Následující kód testuje filtr výjimky:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Filtry výjimek:

* Nemusíte mít události před a po.
* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .
* Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.
* **Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.

Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď. Tím se zastaví šíření výjimky. Filtr výjimek nemůže vypnout výjimku na "úspěch". To může provést pouze filtr akcí.

Filtry výjimek:

* Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.
* Nejsou tak flexibilní jako middleware při zpracování chyb.

Preferovat middleware pro zpracování výjimek. Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána. Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML. Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.

## <a name="result-filters"></a>Filtry výsledků

Filtry výsledků:

* Implementace rozhraní:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Jejich spuštění obklopuje provádění výsledků akcí.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter a IAsyncResultFilter

Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Typ výsledku, který se má provést, závisí na akci. Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> . Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku. Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).

Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce. Filtry výsledků nejsou provedeny v těchto případech:

* Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.
* Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` . Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi. Vyvolání výjimky v `IResultFilter.OnResultExecuting` :

* Zabraňuje spuštění výsledků akce a následných filtrů.
* Se považuje za selhání namísto úspěšného výsledku.

Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi. Pokud byla odpověď již odeslána klientovi, nelze ji změnit.

`ResultExecutedContext.Canceled` je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.

`ResultExecutedContext.Exception` je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku. Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu. Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků. Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.

V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce. Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd. Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce. To zahrnuje výsledky akcí, které vytvořil:

* Filtry autorizace a filtry prostředků, které mají krátký okruh.
* Filtry výjimek.

Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> . Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru. Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` . Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána. To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.

`IFilterFactory` může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Filtr je použit v následujícím kódu:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Vyvolejte vývojářské nástroje F12.
* Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.

Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:

* **Autor:**`Rick Anderson`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **interní:**`My header`

Předchozí kód vytvoří hlavičku **interního:** `My header` Response.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementované u atributu

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:

* Nevyžadovat předávání parametrů
* Musí mít závislosti konstruktoru, které musí vyplnit DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` Načte zadaný typ z kontejneru Services (DI).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .

## <a name="using-middleware-in-the-filter-pipeline"></a>Použití middlewaru v kanálu filtru

Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu. Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.

Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru. Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.

## <a name="next-actions"></a>Další akce

* Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).
* Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)

*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.

Předdefinované filtry zpracovávají úlohy, jako například:

* Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).
* Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).

Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek. Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.  Filtry zabraňují duplikování kódu. Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.

Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.

[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Jak fungují filtry

Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.  Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core. Zpracování žádosti pokračuje zpět pomocí výběru akcí, middlewaru směrování a jiného middleware před odesláním odpovědi klientovi.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrů

Každý typ filtru se spustí v jiné fázi kanálu filtru:

* [Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek. Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný

* [Filtry prostředků](#resource-filters):

  * Spusťte po autorizaci.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> může spustit kód před zbytek kanálu filtru. Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> může spustit kód po dokončení zbývající části kanálu.

* [Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce. Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce. Na stránkách se **nepodporují filtry** akcí Razor .

* [Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.

* [Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce. Spustí se pouze v případě, že metoda akce byla úspěšně provedena. Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.

Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků. Na cestě je požadavek zpracován pouze pomocí filtrů výsledků a filtrů prostředků před tím, než se stane odpověď odeslanou klientovi.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementace

Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.

Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení. Například `OnActionExecuting` je volána před voláním metody Action. `OnActionExecuted` je volána po návratu metody Action.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.  Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.

### <a name="multiple-filter-stages"></a>Několik fází filtru

Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy. Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.

Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí. Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá. V takovém případě volá metody synchronního rozhraní. Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda. Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.

### <a name="built-in-filter-attributes"></a>Předdefinované atributy filtru

ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené. Například následující filtr výsledků přidá hlavičku do odpovědi:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu. Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.

Atributy filtru:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Filtrovat obory a pořadí provádění

Filtr lze přidat do kanálu v jednom ze tří *oborů*:

* Použití atributu pro akci.
* Použití atributu na řadiči.
* Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .

### <a name="default-order-of-execution"></a>Výchozí pořadí provádění

Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.  Globální filtry filtr tříd Surround. Filtry tříd obklopují filtry metod Surround.

V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem. Pořadí filtru:

* *Před* kódem globálních filtrů.
  * *Před* kódem filtru kontroleru.
    * Kód *před* filtry metody Action.
    * *Po* kódu filtry metody Action.
  * *Po* kódu filtry kontroleru.
* *Po* kódu globálních filtrů.
  
Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.

| Sequence | Rozsah filtru | Filter – metoda |
|:--------:|:------------:|:-------------:|
| 1 | Globální | `OnActionExecuting` |
| 2 | Kontrolér | `OnActionExecuting` |
| 3 | Metoda | `OnActionExecuting` |
| 4 | Metoda | `OnActionExecuted` |
| 5 | Kontrolér | `OnActionExecuted` |
| 6 | Globální | `OnActionExecuted` |

Tato posloupnost zobrazuje:

* Filtr metod je vnořený do filtru kontroleru.
* Filtr kontroleru je vnořený do globálního filtru.

### <a name="controller-and-no-locrazor-page-level-filters"></a>Filtry na Razor úrovni řadiče a stránky

Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` . Tyto metody:

* Zabalte filtry, které se spouštějí pro danou akci.
* `OnActionExecuting` je volána před jakýmkoli filtrem akce.
* `OnActionExecuted` se volá po všech filtrech akcí.
* `OnActionExecutionAsync` je volána před jakýmkoli filtrem akce. Kód v filtru po `next` spuštění po metodě Action.

Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.

Pomocná rutina `TestController`:

* Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.
* Přepisuje `OnActionExecuting` a `OnActionExecuted` .

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Přepsání výchozího pořadí

Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> . `IOrderedFilter` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění. Filtr s nižší `Order` hodnotou:

* Spustí *před* kódem filtr s vyšší hodnotou `Order` .
* Spustí *za* kódem za filtr s vyšší `Order` hodnotou.

`Order`Vlastnost lze nastavit s parametrem konstruktoru:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu. Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.

| Sequence | Rozsah filtru | `Order` majetek | Filter – metoda |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Metoda | 0 | `OnActionExecuting` |
| 2 | Kontrolér | 1  | `OnActionExecuting` |
| 3 | Globální | 2  | `OnActionExecuting` |
| 4 | Globální | 2  | `OnActionExecuted` |
| 5 | Kontrolér | 1  | `OnActionExecuted` |
| 6 | Metoda | 0  | `OnActionExecuted` |

`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny. Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor. Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0. Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.

## <a name="cancellation-and-short-circuiting"></a>Zrušení a zkrácení okruhů

Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter. Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action. Pomocná rutina `ShortCircuitingResourceFilter`:

* Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.
* Krátké okruhy zbývajícího kanálu.

Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` . Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první. `ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injektáž závislostí

Filtry lze přidat podle typu nebo podle instance. Pokud je přidána instance, bude tato instance použita pro každý požadavek. Pokud je přidán typ, je aktivován typ. Filtr aktivovaný typu znamená:

* Instance se vytvoří pro každý požadavek.
* Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).

Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di). Pomocí DI nelze poskytnout závislosti konstruktoru:

* V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány. 
* Toto je omezení způsobu fungování atributů.

Následující filtry podporují závislosti konstruktoru poskytované od DI:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementováno u atributu.

Předchozí filtry lze použít pro metodu kontroleru nebo akce:

Protokolovací nástroje jsou k dispozici z DI. Ale nevytvářejte a používejte filtry čistě pro účely protokolování. [Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování. Protokolování přidáno do filtrů:

* Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.
* Neměli **byste** protokolovat akce nebo jiné události rozhraní. Vestavěné filtry protokolují akce a události rozhraní.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` . <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.

Následující kód ukazuje `AddHeaderResultServiceFilter` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):

* Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci. Modul runtime ASP.NET Core nezaručuje:

  * Vytvoří se jedna instance filtru.
  * Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.

* Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` Načte zadaný typ z DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di. Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .

Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:

* Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.  Mají své závislosti splněné kontejnerem DI.
* `TypeFilterAttribute` může volitelně přijmout argumenty konstruktoru pro typ.

Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):
* Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci. Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.

* Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.

Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Filtry autorizace

Filtry autorizace:

* Jsou první filtry spouštěny v kanálu filtru.
* Řízení přístupu k metodám akcí.
* Před metodou, ale ne po metodě.

Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní. Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru. Vestavěný autorizační filtr:

* Volá autorizační systém.
* Neautorizuje požadavky.

Negenerovat **výjimky** v rámci autorizačních filtrů:

* Výjimka nebude zpracována.
* Filtry výjimek nebudou zpracovávat výjimku.

Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.

Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry prostředků

Filtry prostředků:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .
* Provádění zabalí většinu kanálu filtru.
* Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .

Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu. Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.

Příklady filtru prostředků:

* [Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Zabraňuje vazbě modelu v přístupu k datům formuláře.
  * Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.

## <a name="action-filters"></a>Filtry akcí

> [!IMPORTANT]
> Filtry **akcí se nevztahují** na Razor stránky. Razor Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).

Filtry akcí:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .
* Jejich spuštění obklopuje provádění metod akcí.

Následující kód ukazuje filtr vzorové akce:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> – povolí čtení vstupů do metody akce.
* <xref:Microsoft.AspNetCore.Mvc.Controller> – povolí manipulaci s instancí kontroleru.
* <xref:System.Web.Mvc.ActionExecutingContext.Result> – nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.

Vyvolání výjimky v metodě akce:

* Zabraňuje spuštění dalších filtrů.
* Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> -True, pokud bylo provedení akce zkráceno jiným filtrem.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> -Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí. Nastavení této vlastnosti na hodnotu null:

  * Efektivně zpracovává výjimku.
  * `Result` je spuštěn, jako kdyby byl vrácen z metody Action.

V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :

* Provede všechny následné filtry akcí a metodu Action.
* Vrací objekt `ActionExecutedContext`.

Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).

Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.

`OnActionExecuting`Filtr akcí lze použít k těmto akcím:

* Ověří stav modelu.
* Pokud je stav neplatný, vrátí se chyba.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

`OnActionExecuted`Metoda se spustí za metodou akce:

* A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku. Nastavení `Exception` na hodnotu null:

  * Efektivně zpracovává výjimku.
  * `ActionExecutedContext.Result` je spuštěn, jako kdyby byl vrácen normálně z metody Action.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Filtry výjimek

Filtry výjimek:

* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> . 
* Dá se použít k implementaci běžných zásad zpracování chyb.

Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Filtry výjimek:

* Nemusíte mít události před a po.
* Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .
* Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.
* **Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.

Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď. Tím se zastaví šíření výjimky. Filtr výjimek nemůže vypnout výjimku na "úspěch". To může provést pouze filtr akcí.

Filtry výjimek:

* Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.
* Nejsou tak flexibilní jako middleware při zpracování chyb.

Preferovat middleware pro zpracování výjimek. Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána. Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML. Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.

## <a name="result-filters"></a>Filtry výsledků

Filtry výsledků:

* Implementace rozhraní:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Jejich spuštění obklopuje provádění výsledků akcí.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter a IAsyncResultFilter

Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Typ výsledku, který se má provést, závisí na akci. Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> . Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku. Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).

Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce. Filtry výsledků nejsou provedeny v těchto případech:

* Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.
* Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.

<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` . Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi. Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:

* Zabraňte provádění výsledků akce a dalších filtrů.
* Být považována za selhání namísto úspěšného výsledku.

Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi. Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.

`ResultExecutedContext.Canceled` je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.

`ResultExecutedContext.Exception` je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku. Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu. Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků. Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.

V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce. Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd. Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter

<xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce. To zahrnuje výsledky akcí, které vytvořil:

* Filtry autorizace a filtry prostředků, které mají krátký okruh.
* Filtry výjimek.

Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> . Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru. Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` . Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána. To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.

`IFilterFactory` může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):

* Vyvolejte vývojářské nástroje F12.
* Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.

Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:

* **Autor:**`Joe Smith`
* **globaladdheader:**`Result filter added to MvcOptions.Filters`
* **interní:**`My header`

Předchozí kód vytvoří hlavičku **interního:** `My header` Response.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>IFilterFactory implementované u atributu

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:

* Nevyžadovat předávání parametrů
* Musí mít závislosti konstruktoru, které musí vyplnit DI.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> . `IFilterFactory` zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance. `CreateInstance` Načte zadaný typ z kontejneru Services (DI).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .

## <a name="using-middleware-in-the-filter-pipeline"></a>Použití middlewaru v kanálu filtru

Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu. Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.

Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru. Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.

## <a name="next-actions"></a>Další akce

* Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).
* Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
