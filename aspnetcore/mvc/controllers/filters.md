---
title: Filtry v ASP.NET Core
author: ardalis
description: Zjistěte, jak filtry fungují a jak je používat v ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 1/15/2019
uid: mvc/controllers/filters
ms.openlocfilehash: fe3082481b51c968fd361dbcc9553c4e35a36f2a
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444347"
---
# <a name="filters-in-aspnet-core"></a>Filtry v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Petr Dykstra](https://github.com/tdykstra/), a [Steve Smith](https://ardalis.com/)

*Filtry* v ASP.NET Core MVC umožňuje spuštění kódu před nebo po určitým fázím v kanálu zpracování požadavků.

 Integrované filtry naložit s úkoly, jako:

 * Autorizace (zabránění přístupu k prostředkům, které uživatel nemá oprávnění pro).
 * Zajištění, že všechny požadavky na použití protokolu HTTPS.
 * Odpověď do mezipaměti (krátký cyklus kanál požadavku na vrátí odpověď uložená v mezipaměti). 

Vlastní filtry lze vytvořit pro zpracování vyskytující aspekty. Filtry můžete předejít duplikování kódu napříč akce. Zpracování filtru výjimek chyby může například konsolidovat zpracování chyb.

[Zobrazit nebo stáhnout ukázky z Githubu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

## <a name="how-filters-work"></a>Jak fungují filtry

Filtry se spouští v rámci *kanálu vyvolání akce MVC*, která se někdy označují jako *filtrovat*.  Spuštění kanálu filtru po MVC vybere akci pro spuštění.

![Žádost se zpracovává prostřednictvím další Middleware, směrování Middleware, výběr akce a kanál vyvolání akce MVC. Zpracování žádosti pokračuje zpět přes výběr akce, Middleware směrování a různé další Middleware než se náplní odpověď zaslána klientovi.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Typy filtrů

Různé fáze v kanálu filtr spuštění jednotlivých typů filtrů.

* [Filtry autorizace](#authorization-filters) spouští jako první a slouží k určení, zda je aktuální uživatel oprávnění pro aktuální požadavek. Pokud požadavek nemá oprávnění, mohou zkrácenou kanálu. 

* [Filtry prostředků](#resource-filters) první má požadavek zpracovat po povolení.  Mohou spouštět kód před rest kanálu filtr, a po zbytek kanálu byla dokončena. Jsou užitečné k implementaci ukládání do mezipaměti nebo jinak zkrácenou filtr kanálu z důvodů výkonu. Spuštění před vazby modelu, takže ovlivňují vazby modelu.

* [Filtry akcí](#action-filters) může spustit kód bezprostředně před a po volání metody jednotlivé akce. Můžete být použít k manipulaci s argumenty předané do akce a výsledek vrácený z akce. Filtry akce nejsou podporovány v stránky Razor.

* [Filtry výjimek](#exception-filters) umožňují použití globálních zásad pro neošetřených výjimek, ke kterým dojde před nic se zapsala do datové části odpovědi.

* [Výsledek filtry](#result-filters) může spustit kód bezprostředně před a po spuštění výsledky jednotlivých akcí. Spouštějí se pouze v případě, že byl úspěšně proveden metody akce. Jsou užitečné pro logiku, která se musí před a za spuštění zobrazení nebo formátovacího modulu.

Následující diagram znázorňuje interakci tyto typy filtr v kanálu filtru.

![Žádost se zpracovává prostřednictvím filtry autorizace, prostředků filtry, vazby modelu, filtry akce, provedení akce a převod výsledek akce, filtry výjimek, filtry výsledků a výsledek spuštění. Na cestě navýšení kapacity pouze zpracuje požadavek výsledek filtry a filtry prostředků než se náplní odpověď zaslána klientovi.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementace

Filtrů podporuje synchronní a asynchronní implementace prostřednictvím různých rozhraní definice. 

Synchronní filtry, které lze spustit kód před a po jejich fázi zřetězení definovat na*fáze*zpracování a na*fáze*spuštění metody. Například `OnActionExecuting` je volána před voláním metody akce a `OnActionExecuted` se volá, když metoda akce vrací.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

Asynchronní filtry definovat jeden na*fáze*ExecutionAsync metody. Tato metoda přebírá *FilterType*ExecutionDelegate delegáta, který provede filtru fázi zřetězení. Například `ActionExecutionDelegate` volání metody akce nebo dalšímu filtru akce a může spustit kód před a po jeho volání.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

Implementovat rozhraní pro několik fází filtru v jednu třídu. Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementuje třída `IActionFilter`, `IResultFilter`a jejich ekvivalenty asynchronní.

> [!NOTE]
> Implementace **buď** synchronní nebo asynchronní verzi rozhraní filtru, ne obojí. Rozhraní framework nejprve zkontroluje a zjistěte, jestli implementuje rozhraní asynchronní filtr, a pokud ano, který volá. Pokud tomu tak není, volá rozhraní synchronní metody. Pokud byste chtěli implementace obě rozhraní na jednu třídu, by byla volána pouze asynchronní metody. Při používání abstraktních tříd jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> by se mělo přepsat pouze metody synchronní nebo asynchronní metody pro každý typ filtru.

### <a name="ifilterfactory"></a>IFilterFactory
[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Proto `IFilterFactory` instance může sloužit jako `IFilterMetadata` instance kdekoli v kanálu filtru. Když rozhraní připraví k vyvolání tohoto filtru, pokusí se vysílat na `IFilterFactory`. Pokud je úspěšná tohoto přetypování [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) metoda je volána k vytvoření `IFilterMetadata` instanci, která bude volána. To poskytuje flexibilní návrhu, protože kanál přesné filtru nemusí být explicitně nastaveno při spuštění aplikace.

Můžete implementovat `IFilterFactory` na vlastní atribut implementace jako jiný přístup k vytváření filtrů:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a>Integrovaný filtr atributy

Rozhraní obsahuje integrované filtry založených na atributech, ke kterým můžete podtřídy a přizpůsobit. Například následující filtr výsledků přidá do odpovědi hlavičku.

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

Atributy Povolit filtry, které přijímají argumenty, jak je znázorněno v příkladu výše. By přidejte tento atribut do metody kontroler nebo akce a zadejte název a hodnotu hlavičky protokolu HTTP:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Výsledkem `Index` akce je znázorněná níže – hlavičky odpovědi jsou zobrazeny v pravém dolním rohu.

![Vývojářské nástroje Microsoft Edge zobrazující hlavičky odpovědi, včetně autora Steve Smith @ardalis](filters/_static/add-header.png)

Některé z rozhraní filtru mít odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.

Atributy filtru:

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

`TypeFilterAttribute` a `ServiceFilterAttribute` jsou vysvětleny [dále v tomto článku](#dependency-injection).

## <a name="filter-scopes-and-order-of-execution"></a>Filtr oborů a pořadí provádění

Filtr lze přidat do kanálu na jeden ze tří *obory*. Pomocí atributu můžete přidat filtr k určité metodě akce a třídu kontroleru. Nebo můžete zaregistrovat filtr globálně pro všechny kontrolery a akce. Filtry jsou přidány globálně tak, že přidáte tak, `MvcOptions.Filters` kolekce v `ConfigureServices`:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a>Výchozí pořadí provádění

Pokud jsou použity více filtry pro konkrétní fázi kanálu, obor Určuje výchozí pořadí spuštění filtrů.  Globální filtry před a za třídy filtrů, které pak před a za metoda filtry. To je někdy označovány jako "Ruština panenky" vnořování, protože každý nárůst v oboru je připojený za předchozí oboru, jako je [vnoření panenky](https://wikipedia.org/wiki/Matryoshka_doll). Obecně získat požadované chování přepsání bez nutnosti explicitně určit řazení.

V důsledku této vnoření *po* spuštění kódu filtrů v obráceném pořadí *před* kódu. Sekvence vypadá takto:

* *Před* kód globálně použité filtry
  * *Před* kód filtrech použitých u řadiče
    * *Před* kód filtrech použitých u metody akce
    * *Po* kód filtrech použitých u metody akce
  * *Po* kód filtrech použitých u řadiče
* *Po* kód globálně použité filtry
  
Tady je příklad, který ukazuje pořadí, ve které filtru jsou volány metody pro synchronní filtrů akce.

| Pořadí | Obor filtru | Filter – metoda |
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

Řečeno jiný způsob, pokud jste uvnitř filtr asynchronní uživatele*fáze*ExecutionAsync metoda, všechny filtry s rozsahem užší spuštění kódu je v zásobníku.

> [!NOTE]
> Každý kontroler, který dědí z `Controller` základní třída zahrnuje `OnActionExecuting` a `OnActionExecuted` metody. Tyto metody zabalte, na kterých běží filtry pro danou akci: `OnActionExecuting` je volána před provedením filtry, a `OnActionExecuted` se zavolá po všechny filtry.

### <a name="overriding-the-default-order"></a>Přepsání výchozího pořadí

Můžete přepsat výchozí pořadí provádění implementací `IOrderedFilter`. Toto rozhraní poskytuje `Order` vlastnost, která má přednost před obor pro určení pořadí spouštění. Filtr s nižší `Order` hodnota nebude mít jeho *před* kód proveden před inicializací filtr s vyšší hodnotou `Order`. Filtr s nižší `Order` hodnota nebude mít jeho *po* kód proveden po tomto filtr s vyšší `Order` hodnotu. Můžete nastavit `Order` vlastnost s použitím parametru konstruktoru:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Pokud máte stejné 3 filtry akce je znázorněno v předchozím příkladu, ale sada `Order` vlastnosti kontroleru a globální filtry 1 a 2 v uvedeném pořadí, bude replikace probíhat pořadí provádění.

| Pořadí | Obor filtru | `Order` Vlastnost | Filter – metoda |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Metoda | 0 | `OnActionExecuting` |
| 2 | Kontroler | 1  | `OnActionExecuting` |
| 3 | Globální | 2  | `OnActionExecuting` |
| 4 | Globální | 2  | `OnActionExecuted` |
| 5 | Kontroler | 1  | `OnActionExecuted` |
| 6 | Metoda | 0  | `OnActionExecuted` |

`Order` Vlastnost trumfy obor při určování pořadí, ve kterém se spustí filtry. Filtry jsou řazeny nejdříve podle pořadí a oboru se používá k rozdělení vazby. Všechny integrované filtry implementovat `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0. Obor pro integrované filtry, určuje pořadí, pokud nenastavíte `Order` na nenulovou hodnotu.

## <a name="cancellation-and-short-circuiting"></a>Zrušení a krátký circuiting

Filtr kanálu v libovolném okamžiku můžete zkrácenou nastavením `Result` vlastnost na `context` parametr předán metodě filtru. Například následující filtr prostředků zabraňuje rest z kanálu provádění.

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

V následujícím kódu jak `ShortCircuitingResourceFilter` a `AddHeader` cílový filtr `SomeResource` metody akce. `ShortCircuitingResourceFilter`:

* Spustí nejdříve, protože je filtr prostředků a `AddHeader` je filtr akce.
* Zkratům rest z kanálu.

Proto `AddHeader` filtr nikdy spuštěn `SomeResource` akce. Toto chování by být stejný Pokud byly použity oba filtry na úrovni metody akce k dispozici `ShortCircuitingResourceFilter` spustil první. `ShortCircuitingResourceFilter` Spustí první z důvodu jeho typ filtru nebo explicitní použití `Order` vlastnost.

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Injektáž závislostí

Filtry je možné přidat podle typu nebo instance. Pokud chcete přidat instanci, instance se použije pro každý požadavek. Pokud přidáte typ, bude mít typ aktivovaných, což znamená, vytvoří se instance pro každý požadavek a všechny závislosti konstruktoru bude vyplněn [injektáž závislostí](../../fundamentals/dependency-injection.md) (DI). Přidání filtru podle typu je ekvivalentní `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.

Filtry, které jsou implementovány jako atributy a přidat přímo do třídy kontroleru nebo metody akce nemůže mít konstruktor závislosti poskytované [injektáž závislostí](../../fundamentals/dependency-injection.md) (DI). Je to proto, že atributy musí mít zadané, kde uplatňují jejich parametry konstruktoru. Jedná se omezení fungování atributy.

Pokud vaše filtry mají závislosti, které potřebujete získat přístup z DI, několika způsoby podporované. Váš filtr se dá použít k metodě třídy nebo akce pomocí jedné z následujících akcí:

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* `IFilterFactory` implementovat na atribut

> [!NOTE]
> Jednu závislost, kterou chcete získat z DI se protokolovač. Ale Vyhněte se vytváření a používání filtrů výhradně pro účely protokolování, protože [funkce protokolování vestavěnou architekturou](xref:fundamentals/logging/index) již poskytnout co potřebujete. Pokud se chystáte přidat protokolování filtry, byste se zaměřit na obchodní domény otázky nebo chování specifické pro filtr, spíše než akce MVC nebo jiné rozhraní události.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Tyto typy implementace filtr služby jsou registrovány v DI. A `ServiceFilterAttribute` načte z DI instance filtru. Přidat `ServiceFilterAttribute` do kontejneru v `Startup.ConfigureServices`, odkazovat v `[ServiceFilter]` atribut:

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Při použití `ServiceFilterAttribute`a nastavte `IsReusable` se o nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku. Rozhraní poskytuje žádné záruky, že vytvoří jedna instance filtru nebo filtr nebudou vyžádána znovu z kontejnerů DI někdy později. Vyhněte se použití `IsReusable` při použití filtr, který závisí na službách s životností jiného než typu singleton.

Pomocí `ServiceFilterAttribute` i bez registrace výsledky typu filtru výjimek:

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

`ServiceFilterAttribute` implementuje `IFilterFactory`. `IFilterFactory` Zpřístupňuje `CreateInstance` metodu pro vytvoření `IFilterMetadata` instance. `CreateInstance` Metoda načte zadaného typu ze služby kontejnerů (DI).

### <a name="typefilterattribute"></a>TypeFilterAttribute

`TypeFilterAttribute` je podobný `ServiceFilterAttribute`, ale jeho typ není přímo z kontejnerů DI přeložit. Vytvoření instance typu pomocí `Microsoft.Extensions.DependencyInjection.ObjectFactory`.

Vzhledem k tomuto rozdílu:

* Typy, které jsou odkazovány pomocí `TypeFilterAttribute` nemusí být nejprve registrováno s kontejnerem.  Mají jejich závislosti pracující na kontejneru. 
* `TypeFilterAttribute` Volitelně může přijímat argumenty konstruktoru typu.

Při použití `TypeFilterAttribute`a nastavte `IsReusable` se o nápovědu, která instance filtru *může* znovu použít mimo byl vytvořen v rámci oboru požadavku. Rozhraní poskytuje žádnou záruku, že jedna instance filtru bude vytvořen. Vyhněte se použití `IsReusable` při použití filtr, který závisí na službách s životností jiného než typu singleton.

Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute`:

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a>IFilterFactory implementovaná v atributu

Pokud máte filtr, který:

* Nevyžaduje žádné argumenty.
* Má konstruktor závislosti, které potřebujete pro vyplnění podle DI.

Můžete použít vlastní pojmenovaného atributu třídy a metody místo `[TypeFilter(typeof(FilterType))]`). Filtr ukazuje, jak to lze provést:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Tento filtr lze použít na třídy nebo metody pomocí `[SampleActionFilter]` syntaxe, místo nutnosti použít `[TypeFilter]` nebo `[ServiceFilter]`.

## <a name="authorization-filters"></a>Filtry autorizace

*Filtry autorizace*:

* Řízení přístupu na metody akce.
* Jsou první filtry, který se spustí v rámci kanálu filtru. 
* Máte před metodou, ale ne po metodě. 

Filtr autorizace byste psát pouze pokud vytváříte vlastní autorizace framework. Preferovat konfigurace zásad autorizace nebo zápis vlastní zásady autorizace přes psaní vlastního filtru. Integrovaný filtr implementace právě zodpovídá za volání autorizace systému.

By neměla vyvolávat výjimky v rámci filtry autorizace, protože nic bude zpracovávat výjimky (filtry výjimek nebude jejich zpracování). Zvažte výzvu, když dojde k výjimce.

Další informace o [autorizace](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Filtry prostředků

* Implementovat buď `IResourceFilter` nebo `IAsyncResourceFilter` rozhraní
* Jejich spuštění zabalí většinu kanálu filtru. 
* Pouze [filtry autorizace](#authorization-filters) spustit před filtry prostředků.

Filtry prostředků jsou užitečné pro zkrácenou většinu práce, kterou provádí požadavek. Například se můžete vyhnout filtr ukládání do mezipaměti při odpovědi v mezipaměti rest z kanálu.

[Krátký circuiting filtr prostředků](#short-circuiting-resource-filter) je uvedeno výše je jeden příklad filtr prostředků. Dalším příkladem je [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

* Ta brání vazby modelu v přístupu k data formuláře. 
* To je užitečné pro nahrávání velkých souborů a chcete zabránit formuláře načítána do paměti.

## <a name="action-filters"></a>Filtry akcí

> [!IMPORTANT]
> Filtry akce provést **není** platí pro stránky Razor. Stránky Razor podporuje <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).

*Filtry akcí*:

* Implementovat buď `IActionFilter` nebo `IAsyncActionFilter` rozhraní.
* Jejich spuštění obklopuje provádění metody akce.

Tady je ukázkový filtr akce:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> Poskytuje následující vlastnosti:

* `ActionArguments` – umožňuje pracovat s vstupy pro akci.
* `Controller` – umožňuje pracovat s instance kontroleru. 
* `Result` -nastavení tohoto zkratům provádění metody akce a filtry následné akce. Došlo k výjimce také zabrání spuštění metody akce a následné filtry, ale je považováno za selhání místo úspěšný výsledek.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> Poskytuje `Controller` a `Result` plus následující vlastnosti:

* `Canceled` -bude hodnota true v případě provedení akce byla zkratována jiný filtr.
* `Exception` -bude být jiná než null, pokud akci nebo filtr následné akce došlo k výjimce. Nastavení této vlastnosti na hodnotu null, efektivně '' výjimku zpracovává, a `Result` budou spuštěny, jako kdyby byly vrátil z metody akce normálně.

Pro `IAsyncActionFilter`, volání `ActionExecutionDelegate`:

* Spustí všechny filtry následné akce a metody akce.
* Vrátí `ActionExecutedContext`. 

Chcete zkrácenou, přiřaďte `ActionExecutingContext.Result` některé výsledek instance a volat `ActionExecutionDelegate`.

Rozhraní poskytuje abstraktní `ActionFilterAttribute` , ke kterým můžete podtřídy. 

Filtr akce slouží k ověření stavu modelu a vrátí všechny chyby, pokud neplatí stavu:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

`OnActionExecuted` Metoda spuštění po metody akce a může naleznete v tématu a práci s výsledky akce prostřednictvím `ActionExecutedContext.Result` vlastnost. `ActionExecutedContext.Canceled` se nastaví na hodnotu true, pokud spuštění akce byla zkratována jiný filtr. `ActionExecutedContext.Exception` Nastaví se hodnota jiná než null Pokud akci nebo filtr následné akce došlo k výjimce. Nastavení `ActionExecutedContext.Exception` na hodnotu null:

* Efektivně '' výjimku zpracovává.
* `ActionExectedContext.Result` je provedeno, jako kdyby byly obvykle vrátil z metody akce.

## <a name="exception-filters"></a>Filtry výjimek

*Filtry výjimek* implementovat buď `IExceptionFilter` nebo `IAsyncExceptionFilter` rozhraní. Umožňuje implementovat zásady pro aplikace pro zpracování běžných chyb. 

Následující vzorek filtru výjimek používá zobrazení chyb vlastní pro vývojáře k zobrazení podrobností o výjimkách, ke kterým dochází, když je aplikace ve vývoji:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

Filtry výjimek:

* Není k dispozici před a po události. 
* Implementace `OnException` nebo `OnExceptionAsync`. 
* Zpracování neošetřených výjimek, ke kterým dochází při vytváření adaptéru [vazby modelu](../models/model-binding.md), filtrů Akce nebo metody akce. 
* Nezachycujte výjimky, ke kterým dochází v prostředku filtry, filtry výsledků nebo MVC výsledek spuštění.

Chcete-li zpracovat výjimku, nastavte `ExceptionContext.ExceptionHandled` vlastnost na hodnotu true nebo zápisu odpovědi. Tím se zastaví šíření výjimky. Filtr výjimek nelze zapnout výjimku do "ÚSPĚCH". Pouze filtru akce můžete to udělat.

> [!NOTE]
> V technologii ASP.NET Core 1.1 odpovědi neodešle, pokud jste nastavili `ExceptionHandled` na hodnotu true **a** zápisu odpovědi. V tomto scénáři ASP.NET Core 1.0 odeslání odpovědi a vrátí ASP.NET Core 1.1.2 1.0 chování. Další informace najdete v tématu [vydat #5594](https://github.com/aspnet/Mvc/issues/5594) v úložišti GitHub. 

Filtry výjimek:

* Jsou vhodné pro zachytávání výjimek, které se vyskytují v rámci akce MVC.
* Nejsou tak flexibilní jako middleware pro zpracování chyb. 

Preferovat middleware pro zpracování výjimek. Použít filtry výjimek pouze tam, kde potřebujete provést zpracování chyb *jinak* založené na MVC akci, která byla vybrána. Vaše aplikace může mít například metody akci pro oba koncové body rozhraní API a pro zobrazení a HTML. Koncové body rozhraní API můžou se zobrazovat informace o chybě jako dokumenty JSON, zatímco akce na základě zobrazení může vrátit chybovou stránku ve formátu HTML.

`ExceptionFilterAttribute` Můžete má rozčlenit do podtříd. 

## <a name="result-filters"></a>Filtry výsledků

* Implementovat buď `IResultFilter` nebo `IAsyncResultFilter` rozhraní.
* Jejich spuštění obklopuje provádění výsledky akce. 

Tady je příklad výsledku filtru, který přidá hlavičku protokolu HTTP.

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Typ výsledku prováděný závisí na dotyčném akce. Akce MVC vrací zobrazení bude zahrnovat všechny razor zpracování v rámci `ViewResult` spouštěna. Metodu rozhraní API může provést některé serializace jako součást provedení výsledku. Další informace o [výsledky akcí](actions.md)

Filtry výsledků jsou vykonány pouze pro úspěšné výsledky – při akci nebo filtrů akce vytvoří výsledek akce. Filtry výsledků nejsou provedeny, když filtry výjimek zpracování výjimky.

`OnResultExecuting` Metoda můžete zkrácenou provedení výsledku akce a filtry následujících výsledků nastavením `ResultExecutingContext.Cancel` na hodnotu true. By měla obecně zapisovat do objektu odpovědi při zkrácení pro zabránění generování odpověď je prázdná. Došlo k výjimce bude:

* Zabraňte spuštění výsledku akce a následné filtry.
* Být považován za spadne, místo aby úspěšný výsledek.

Když `OnResultExecuted` metoda pracuje, odpověď se pravděpodobně poslal klientovi a nedá se změnit dále, (Pokud byla vyvolána výjimka). `ResultExecutedContext.Canceled` se nastaví na hodnotu true, pokud výsledek provedení akce byla zkratována jiný filtr.

`ResultExecutedContext.Exception` Nastaví se hodnota jiná než null Pokud výsledek akce nebo filtr následné výsledků došlo k výjimce. Nastavení `Exception` na null efektivně zpracovává výjimku a brání výjimka z právě znovu MVC dále v kanálu. Když při zpracování výjimky ve výsledku filtru, nemusí být schopni zapisovat data do odpovědi. Pokud výsledek akce vyvolá partway prostřednictvím jeho spuštění, a mít již byla vyprázdněna hlavičky klientovi, neexistuje žádný spolehlivý mechanismus poslat kód selhání.

Pro `IAsyncResultFilter` volání `await next` na `ResultExecutionDelegate` spustí všechny následné výsledek filtry a výsledku akce. Zkrácenou, nastavte `ResultExecutingContext.Cancel` na hodnotu true a nemůžete volat `ResultExectionDelegate`.

Rozhraní poskytuje abstraktní `ResultFilterAttribute` , ke kterým můžete podtřídy. [AddHeaderAttribute](#add-header-attribute) třídy je uvedeno výše je příkladem atribut filtru výsledků.

## <a name="using-middleware-in-the-filter-pipeline"></a>Pomocí middleware v kanálu filtru

Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že jsou před a za provádění všeho, která se dodává později v kanálu. Ale filtry liší od middleware, že jsou součástí MVC, což znamená, že mají přístup ke kontextu MVC a konstrukce.

V technologii ASP.NET Core 1.1 můžete použít middleware v kanálu filtru. Můžete to provést, pokud máte komponenta middlewaru, který potřebuje přístup k MVC směrovací data nebo ten, který se má spustit jenom pro některé řadiče nebo akce.

Použití middlewaru jako filtr, vytvoření typu s `Configure` metodu, která určuje middleware, který chcete vložit do kanálu filtru. Tady je příklad, který používá k navázání aktuální jazykovou verzi pro žádost o middlewaru lokalizace:

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

Pak můžete použít `MiddlewareFilterAttribute` pro spuštění middlewaru pro vybraný kontroler nebo akce nebo globálně:

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Middleware filtry třídí ve stejné fázi kanálu filtr jako prostředek filtry, před navázáním modelu a po zbytek kanálu.

## <a name="next-actions"></a>Další akce

* Zobrazit [metody filtrování pro Razor Pages](xref:razor-pages/filter)
* Můžete experimentovat s filtry, [stáhnout, otestovat a upravit na Githubu ukázky](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).
