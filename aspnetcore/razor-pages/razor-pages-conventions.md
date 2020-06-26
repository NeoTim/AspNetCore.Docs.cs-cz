---
title: RazorSměrování stránek a konvence aplikací v ASP.NET Core
author: rick-anderson
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 308ca4401289a55e5dba8d61de50644cb2a53433
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405247"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>RazorSměrování stránek a konvence aplikací v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.

Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:mvc/controllers/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

| Scénář | Ukázka znázorňuje... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Konvence. Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu a hlavičku směrování na stránky aplikace. |
| [Konvence akcí při směrování stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu směrování do stránek ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</li></ul> | Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace. |

RazorKonvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Pořadí směrování

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Pořadí není zadáno (výchozí hodnota). Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování směrování. |

Zpracování směrování je zřízené podle konvence:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.
* Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování. Obecně směrování vybírá správnou trasu s odpovídající adresou URL. Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu. Vyhledejte zjednodušené schéma směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.

RazorSměrování stránek a směrování kontroléru MVC sdílí implementaci. Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidat konvenci modelu směrování na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.

Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` . Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:

* Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu. Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.
* `{aboutTemplate?}`Šablona směrování se přidá později v tématu. `{aboutTemplate?}`Šabloně je přidělena `Order` `2` . Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.
* `{otherPagesTemplate?}`Šablona směrování se přidá později v tématu. `{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` . Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

RazorMožnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` . Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.

Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu. Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců. Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi. Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvenci modelu obslužné rutiny na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí při směrování stránky

Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Konvence modelu směrování složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu směrování stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.

Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Použití transformátoru parametrů k přizpůsobení cest stránky

Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .

`PageRouteTransformerConvention`Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci. Například Razor soubor stránek na */Pages/SubscriptionManagement/ViewAll.cshtml* by měl svou trasu přepsanou z `/SubscriptionManagement/ViewAll` do `/subscription-management/view-all` .

`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí ze Razor složky a názvu souboru stránky. Netransformuje segmenty směrování přidané s `@page` direktivou. Konvence také netransformuje trasy, které přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

`PageRouteTransformerConvention`Je zaregistrován jako možnost v `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Konfigurace trasy stránky

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky. Vygenerované odkazy na stránku používají zadanou trasu. `AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.

Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ). Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` . Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL Vykreslená stránka ukazuje hodnotu segment "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.

V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.

Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.

Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* . Pokud podmínka projde, přidá se hlavička. V takovém případě se `EmptyFilter` použije.

`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .

Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace objektu pro vytváření filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.

Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny. Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .

Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky. Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.

Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

| Scénář | Ukázka znázorňuje... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Konvence. Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu a hlavičku směrování na stránky aplikace. |
| [Konvence akcí při směrování stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu směrování do stránek ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</li></ul> | Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace. |

RazorKonvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Pořadí směrování

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Pořadí není zadáno (výchozí hodnota). Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování směrování. |

Zpracování směrování je zřízené podle konvence:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.
* Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování. Obecně směrování vybírá správnou trasu s odpovídající adresou URL. Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu. Vyhledejte zjednodušené schéma směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.

RazorSměrování stránek a směrování kontroléru MVC sdílí implementaci. Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidat konvenci modelu směrování na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.

Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` . Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:

* Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu. Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.
* `{aboutTemplate?}`Šablona směrování se přidá později v tématu. `{aboutTemplate?}`Šabloně je přidělena `Order` `2` . Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.
* `{otherPagesTemplate?}`Šablona směrování se přidá později v tématu. `{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` . Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

RazorMožnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` . Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.

Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu. Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců. Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi. Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvenci modelu obslužné rutiny na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí při směrování stránky

Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Konvence modelu směrování složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu směrování stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.

Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Použití transformátoru parametrů k přizpůsobení cest stránky

Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr Transformer změní `SubscriptionManagement` hodnotu Route na `subscription-management` .

`PageRouteTransformerConvention`Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci. Například Razor soubor stránek na */Pages/SubscriptionManagement/ViewAll.cshtml* by měl svou trasu přepsanou z `/SubscriptionManagement/ViewAll` do `/subscription-management/view-all` .

`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí ze Razor složky a názvu souboru stránky. Netransformuje segmenty směrování přidané s `@page` direktivou. Konvence také netransformuje trasy, které přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> .

`PageRouteTransformerConvention`Je zaregistrován jako možnost v `Startup.ConfigureServices` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Konfigurace trasy stránky

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky. Vygenerované odkazy na stránku používají zadanou trasu. `AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.

Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ). Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` . Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL Vykreslená stránka ukazuje hodnotu segment "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.

V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.

Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.

Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* . Pokud podmínka projde, přidá se hlavička. V takovém případě se `EmptyFilter` použije.

`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .

Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace objektu pro vytváření filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.

Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny. Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .

Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky. Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v Razor aplikacích stránky.

Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

| Scénář | Ukázka znázorňuje... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Konvence. Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu a hlavičku směrování na stránky aplikace. |
| [Konvence akcí při směrování stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu směrování do stránek ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</li></ul> | Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace. |

RazorKonvence stránek jsou přidány a nakonfigurovány pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metody rozšíření pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Pořadí směrování

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídání tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Pořadí není zadáno (výchozí hodnota). Nepřiřazuje se `Order` ( `Order = null` ) výchozí hodnota trasy `Order` na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování směrování. |

Zpracování směrování je zřízené podle konvence:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* V případě, že trasy mají stejný směr `Order` , je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.
* Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování. Obecně směrování vybírá správnou trasu s odpovídající adresou URL. Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu. Vyhledejte zjednodušené schéma směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.

RazorSměrování stránek a směrování kontroléru MVC sdílí implementaci. Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> Přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor stránky.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidat konvenci modelu směrování na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.

Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1` . Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:

* Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu. Trasa stránky kontaktu má výchozí pořadí `null` ( `Order = 0` ), takže odpovídá před `{globalTemplate?}` šablonou směrování.
* `{aboutTemplate?}`Šablona směrování se přidá později v tématu. `{aboutTemplate?}`Šabloně je přidělena `Order` `2` . Když se na stránce o žádosti vyžádá `/About/RouteDataValue` , "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a `RouteData.Values["aboutTemplate"]` ne ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.
* `{otherPagesTemplate?}`Šablona směrování se přidá později v tématu. `{otherPagesTemplate?}`Šabloně je přidělena `Order` `2` . Pokud je pro libovolnou stránku ve složce *Pages/OtherPages* požadováno parametr trasy (například `/OtherPages/Page1/RouteDataValue` ), je hodnota "RouteDataValue" načtena do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

RazorMožnosti stránek, jako je například přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> , jsou přidány při přidání MVC do kolekce služby v nástroji `Startup.ConfigureServices` . Příklad najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.

Chcete-li předvést tuto a další konvenci později v tématu, ukázková aplikace obsahuje `AddHeaderAttribute` třídu. Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců. Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi. Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, na `GlobalHeader` všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvenci modelu obslužné rutiny na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí při směrování stránky

Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Konvence modelu směrování složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání `{otherPagesTemplate?}` šablony směrování na stránky ve složce *OtherPages* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka ve složce *Pages/OtherPages* požaduje s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue` ), "RouteDataValue" se načte do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a ne `RouteData.Values["otherPagesTemplate"]` ( `Order = 2` ) kvůli nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Vyžádejte si Page1 stránku ukázky `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu směrování stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.

Ukázková aplikace používá `AddPageRouteModelConvention` k přidání `{aboutTemplate?}` šablony směrování na stránku o aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>Vlastnost pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2` . Tím se zajistí, že šablona pro `{globalTemplate?}` (nastavená výše v tématu na `1` ) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue` , je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` ( `Order = 1` ) a nikoli `RouteData.Values["aboutTemplate"]` ( `Order = 2` ) z důvodu nastavení `Order` Vlastnosti.

Pokud je to možné, nenastavte `Order` , které výsledky mají `Order = 0` . Pro výběr správné trasy se spoléhá na směrování.

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurace trasy stránky

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky. Vygenerované odkazy na stránku používají zadanou trasu. `AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.

Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování ( `{text?}` ). Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce v `/Contact` cestě:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku kontaktů buď na své běžné trase, `/Contact` nebo na vlastní trasu `/TheContactPage` . Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL Vykreslená stránka ukazuje hodnotu segment "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.

V příkladech v této části používá ukázková aplikace třídu, `AddHeaderAttribute` která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute> , která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.

Ukázka demonstruje použití přidáním `AddFolderApplicationModelConvention` záhlaví, na `OtherPagesHeader` stránkách ve složce *OtherPages* této aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Vyžádejte si ukázkovou stránku Page1 na stránce a prohlédněte `localhost:5000/OtherPages/Page1` si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.

Ukázka demonstruje použití přidáním `AddPageApplicationModelConvention` záhlaví, na `AboutHeader` stránce o produktu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* . Pokud podmínka projde, přidá se hlavička. V takovém případě se `EmptyFilter` použije.

`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akcí jsou ignorovány Razor stránkami, `EmptyFilter` nemá žádný vliv na zamýšlenou cestu, pokud cesta neobsahuje `OtherPages/Page2` .

Vyžádejte si ukázkovou stránku Page2 na stránce a prohlédněte `localhost:5000/OtherPages/Page2` si záhlaví a podívejte se na výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace objektu pro vytváření filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor stránky.

Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, se `FilterFactoryHeader` dvěma hodnotami na stránkách aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC jsou stránky ignorovány Razor , protože Razor stránky používají metody obslužné rutiny. Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .

Filtr stránky ( <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> ) je filtr, který se vztahuje na Razor stránky. Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
