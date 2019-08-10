---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: guardrex
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/08/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: c93f169c422d260f738faba4812861521f383e51
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914980"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor Pages konvence směrování a aplikace v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Naučte se používat konvence směrování stránky [a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.

Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte `@page` direktivu stránky. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: Názvy](xref:fundamentals/routing#reserved-routing-names)rezervovaných směrování.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([stažení](xref:index#how-to-download-a-sample))

| Scénář | Ukázka znázorňuje... |
| -------- | --------------------------- |
| [Konvence modelu](#model-conventions)<br><br>Konvence. Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu a hlavičku směrování na stránky aplikace. |
| [Konvence akcí při směrování stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu směrování do stránek ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</li></ul> | Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace. |

Konvence Razor Pages jsou přidány a konfigurovány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> pro kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

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

| Pořadí            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Pořadí není zadáno (výchozí hodnota). Nepřiřazuje `Order` se`Order = null`() výchozí hodnota `Order` trasy na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování směrování. |

Zpracování směrování je zřízené podle konvence:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, &hellip; 2, n).
* V případě, že trasy `Order`mají stejný směr, je nejdříve porovnána konkrétní trasa, za kterou následuje méně specifických tras.
* Když se trasy se stejným `Order` počtem parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>do.

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování. Obecně směrování vybírá správnou trasu s odpovídající adresou URL. Pokud musíte nastavit vlastnosti směrování `Order` na správně směrované požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké na údržbu. Vyhledejte zjednodušené schéma směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout postup nastavení trasy `Order` v produkčních aplikacích.

Směrování Razor Pages směrování a řadiče MVC sdílí implementaci. Informace o pořadí směrování v tématech MVC jsou k dispozici [při směrování na akce kontroleru: Rozobjednávkuje](xref:mvc/controllers/routing#ordering-attribute-routes)trasy atributů.

## <a name="model-conventions"></a>Konvence modelu

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [modelových konvencí](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidat konvenci modelu směrování na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> přidání kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu směrování stránky.

Ukázková aplikace přidá `{globalTemplate?}` šablonu směrování na všechny stránky v aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

Vlastnost pro je nastavena na `1`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:

* Šablona směrování pro `TheContactPage/{text?}` je přidána později v tématu. Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), `{globalTemplate?}` takže odpovídá před šablonou směrování.
* Šablona `{aboutTemplate?}` směrování se přidá později v tématu. `{aboutTemplate?}` Šabloně je přidělena. `Order` `2` Když se na `/About/RouteDataValue`stránce o žádosti vyžádá, "RouteDataValue" se načte do`Order = 1` `RouteData.Values["globalTemplate"]` () `Order` a `RouteData.Values["aboutTemplate"]` ne`Order = 2`() kvůli nastavení vlastnosti.
* Šablona `{otherPagesTemplate?}` směrování se přidá později v tématu. `{otherPagesTemplate?}` Šabloně je přidělena. `Order` `2` Pokud je u libovolné stránky ve složce *Pages/OtherPages* požadováno zadáním parametru `/OtherPages/Page1/RouteDataValue`trasy (například), "RouteDataValue" se načte do `RouteData.Values["otherPagesTemplate"]` `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne (`Order = 2`) kvůli nastavení `Order` vlastnost.

Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají. Pro výběr správné trasy se spoléhá na směrování.

Možnosti Razor Pages, jako je například <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>přidání, jsou přidány při přidání MVC do kolekce služeb v. `Startup.ConfigureServices` Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> přidání do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během vytváření modelu aplikace stránky.

Chcete-li předvést tuto a další konvenci později v tématu, ukázková `AddHeaderAttribute` aplikace obsahuje třídu. Konstruktor třídy přijímá `name` řetězec `values` a pole řetězců. Tyto hodnoty jsou použity v `OnResultExecuting` metodě pro nastavení hlavičky odpovědi. Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace používá `AddHeaderAttribute` třídu k přidání záhlaví, `GlobalHeader`na všechny stránky v aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

::: moniker-end

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvenci modelu obslužné rutiny na všechny stránky

Použijte <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> přidání do kolekce <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které jsou použity během vytváření modelu obslužné rutiny stránky.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

::: moniker-end

*Startup.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

::: moniker-end

## <a name="page-route-action-conventions"></a>Konvence akcí při směrování stránky

Výchozí zprostředkovatel modelu směrování, který je odvozen od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> konvencí vyvolání, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Konvence modelu směrování složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> Přidání akce, která vyvolá akci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k `{otherPagesTemplate?}` Přidání šablony směrování na stránky ve složce *OtherPages* :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

Vlastnost pro je nastavena na `2`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Tím se zajistí, že šablona `{globalTemplate?}` pro (nastavená výše v tématu `1`na) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka ve složce *Pages/OtherPages* `/OtherPages/Page1/RouteDataValue`požaduje s hodnotou parametru trasy (například), "RouteDataValue" se načte do`Order = 1` `RouteData.Values["globalTemplate"]` () a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) kvůli nastavení `Order` vlastnost.

Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají. Pro výběr správné trasy se spoléhá na směrování.

Vyžádejte si Page1 stránku `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` ukázky a Prohlédněte si výsledek:

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu směrování stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> Přidání akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránce pro stránku se zadaným názvem.

Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony `{aboutTemplate?}` směrování na stránku o aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

Vlastnost pro je nastavena na `2`. <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Tím se zajistí, že šablona `{globalTemplate?}` pro (nastavená výše v tématu `1`na) má prioritu první pozice hodnoty dat trasy při zadání jedné hodnoty trasy. Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, je "RouteDataValue" načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) `Order` a nikoli `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti.

Pokud je to možné, nenastavte `Order`, které `Order = 0`výsledky mají. Pro výběr správné trasy se spoléhá na směrování.

Požádejte o stránku ukázek o stránce `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Použití transformátoru parametrů k přizpůsobení cest stránky

Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Parametr Transformer implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr Transformer `SubscriptionManagement` změní hodnotu Route na `subscription-management`.

Konvence modelu směrování stránky aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci. `PageRouteTransformerConvention` Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` do. `/subscription-management/view-all`

`PageRouteTransformerConvention`transformuje automaticky generované segmenty stránky trasy, které pocházejí z Razor Pages složky a názvu souboru. Netransformuje segmenty směrování přidané s `@page` direktivou. Konvence také netransformuje trasy, <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>které přidal.

Je zaregistrován jako možnost v `Startup.ConfigureServices`: `PageRouteTransformerConvention`

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

::: moniker-end

## <a name="configure-a-page-route"></a>Konfigurace trasy stránky

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci směrování na stránku na zadané cestě stránky. Vygenerované odkazy na stránku používají zadanou trasu. `AddPageRoute`slouží `AddPageRouteModelConvention` k vytvoření trasy.

Ukázková aplikace vytvoří trasu `/TheContactPage` pro *kontakt. cshtml*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

Stránku kontaktů lze také kontaktovat `/Contact` prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku kontaktů umožňuje volitelný `text` segment směrování (`{text?}`). Tato stránka obsahuje také volitelný segment v rámci své `@page` směrnice pro případ, že návštěvník přistupuje ke stránce `/Contact` v cestě:

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku kontaktů buď na své běžné trase, `/Contact`nebo na vlastní `/TheContactPage`trasu. Pokud zadáte další `text` segment směrování, stránka zobrazuje segment, který zakódovaný ve formátu HTML, který zadáte:

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL Vykreslená stránka ukazuje hodnotu segment "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel stránky, který implementuje implementují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.

V příkladech v této části používá `AddHeaderAttribute` ukázková aplikace třídu, která <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>je, která používá hlavičku odpovědi:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> Přidání akce, která vyvolá akci u <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instancí pro všechny stránky v zadané složce.

Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním záhlaví, `OtherPagesHeader`na stránkách ve složce *OtherPages* této aplikace:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

Vyžádejte si ukázkovou stránku Page1 `localhost:5000/OtherPages/Page1` na stránce a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření a <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> Přidání akce, která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránce pro stránku se zadaným názvem.

Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním záhlaví, `AboutHeader`na stránce o produktu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadaný filtr na použití. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* . Pokud podmínka projde, přidá se hlavička. V `EmptyFilter` takovém případě se použije.

`EmptyFilter`je [Filtr akcí](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akcí jsou Razor Pages `EmptyFilter` ignorovány, nemá žádný vliv na zamýšlené, pokud `OtherPages/Page2`cesta neobsahuje.

Vyžádejte si ukázkovou stránku Page2 `localhost:5000/OtherPages/Page2` na stránce a Prohlédněte si záhlaví a podívejte se na výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace objektu pro vytváření filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje určený objekt pro vytváření na použití [filtrů](xref:mvc/controllers/filters) na všechny Razor Pages.

Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader`se dvěma hodnotami na stránkách aplikace:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

::: moniker-end

*AddHeaderWithFactory.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

::: moniker-end

Požádejte o stránku ukázek o stránce `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny. Další typy filtrů MVC jsou k dispozici pro použití: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .

Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages. Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
