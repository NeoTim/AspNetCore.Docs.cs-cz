---
title: Konvence směrování a aplikačních stránky Razor v ASP.NET Core
author: guardrex
description: Zjistěte, jak směrování a aplikační konvence zprostředkovatele modelu můžete ovládací prvek stránky směrování, zjišťování a zpracování.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/27/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 5cfcae5cffd5d9484ca64c3885b838ae0a2b4a0d
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346512"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Konvence směrování a aplikačních stránky Razor v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Další informace o použití stránky [směrování a aplikační model poskytovatele konvence](xref:mvc/controllers/application-model#conventions) řídit směrování stránky, zjišťování a zpracování v aplikacích pro stránky Razor.

Pokud potřebujete nakonfigurovat vlastní stránku trasy pro jednotlivé stránky, konfigurace směrování pro ně [AddPageRoute konvence](#configure-a-page-route) je popsáno dále v tomto tématu.

Zadejte trasy stránku, přidat segmenty směrování nebo parametry trasu, můžete na stránce `@page` směrnice. Další informace najdete v tématu [trasy vlastní](xref:razor-pages/index#custom-routes).

Existují vyhrazených slov, která nejde použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([stažení](xref:index#how-to-download-a-sample))

| Scénář | Ukázce... |
| -------- | --------------------------- |
| [Vytváření modelu](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidáte šablonu trasy a záhlaví stránek vaší aplikace. |
| [Stránka trasy akce konvence](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidáte šablonu trasy na stránky ve složce a jednu stránku. |
| [Konvence akce modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (třídy filtru, výraz lambda nebo objekt pro vytváření filtru)</li></ul> | Přidat záhlaví stránky ve složce, přidat hlavičku do jediné stránce a nakonfigurovat [filtr factory](xref:mvc/controllers/filters#ifilterfactory) přidat záhlaví stránek vaší aplikace. |

Vytváření stránek Razor přidávají a konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> metodu rozšíření k <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> na kolekci služby v `Startup` třídy. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>Pořadí trasy

Zadejte trasy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (trasy párování).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasy se zpracuje dříve, než jsou zpracovány jiným trasám. |
| 0                | Není zadáno pořadí (výchozí hodnota). Není přiřazení `Order` (`Order = null`) výchozí hodnoty trasy `Order` na hodnotu 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování trasy. |

Podle konvence pokládáme stav zpracování trasy:

* Trasy se zpracovávají v pořadí (hodnota -1, 0, 1, 2, &hellip; n).
* Když trasy mají stejné `Order`, nejvíce určené směrování je nalezena shoda, nejprve následovaný specifické pro less trasy.
* Když tras se stejnou `Order` a stejný počet parametrů odpovídat adrese URL žádosti, trasy se zpracovávají v pořadí, ve kterém se přidají do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Pokud je to možné Vyhněte se v závislosti na zavedených postupu zpracování objednávky. Obecně platí směrování vybere správné směrování s odpovídajícími adresy URL. Pokud je nutné nastavit trasy `Order` vlastnosti pro směrování požadavků správně, směrování schéma aplikace je pravděpodobně matoucí pro klienty a křehkými udržovat. Které se snaží zjednodušit směrování schéma aplikace. Ukázková aplikace vyžaduje explicitní trasy zpracování objednávky k předvedení několik scénáře použití jedné aplikace. Nicméně, měli byste se pokusit postup nastavení trasy, aby `Order` v produkčních aplikacích.

Stránky Razor směrování a směrování sdílení řadiče MVC implementace. Informace o pořadí trasy v tématech MVC je k dispozici na [směrování na akce kontroleru: Pořadí trasy atributů](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Vytváření modelu

Přidat delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidat [modelu konvencí](xref:mvc/controllers/application-model#conventions) , která platí pro stránky Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidání vytváření modelu trasu pro všechny stránky

Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> instancí, které se použijí během postupu stránky model konstrukce.

Ukázková aplikace přidá `{globalTemplate?}` šablona trasy pro všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`. Tím se zajistí následující trasu odpovídající chování v ukázkové aplikaci:

* Šablona trasy pro `TheContactPage/{text?}` přidat později v tomto tématu. Trasa stránku kontaktu má výchozí pořadí `null` (`Order = 0`), tak, aby odpovídala před `{globalTemplate?}` šablonu trasy.
* `{aboutTemplate?}` Trasy šablona se přidá později v tomto tématu. `{aboutTemplate?}` Šablony je uveden `Order` z `2`. Když se na stránce o požaduje za `/About/RouteDataValue`, "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.
* `{otherPagesTemplate?}` Trasy šablona se přidá později v tomto tématu. `{otherPagesTemplate?}` Šablony je uveden `Order` z `2`. Když všechny stránky v *stránek/OtherPages* složky je požadováno se parametr trasy (například `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.

Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`. Spolehněte se na směrování k výběru správné směrování.

Možnosti stránky Razor, jako je například přidávání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, se přidají, když MVC se přidá do kolekce služby `Startup.ConfigureServices`. Příklad najdete v tématu [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Žádosti o stránku ukázky na `localhost:5000/About/GlobalRouteValue` a zkontrolujte výsledek:

![Na stránce o žádá s segment GlobalRouteValue trasy. Na vykreslené stránce se zobrazí, že hodnota data trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidejte aplikaci modelu konvence pro všechny stránky

Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> stránce instance, které se použijí při vytváření modelu aplikace.

Abychom si předvedli to a jiné konvence později v tomto tématu, obsahuje ukázkovou aplikaci `AddHeaderAttribute` třídy. Konstruktor třídy přijímá `name` řetězec a `values` pole řetězců. Tyto hodnoty jsou použity v jeho `OnResultExecuting` metoda nastavit hlavičku odpovědi. Úplné třídy je zobrazena ve [stránce modelu akce konvence](#page-model-action-conventions) dále v tomto tématu.

Tato ukázková aplikace používá `AddHeaderAttribute` třídy přidat záhlaví `GlobalHeader`, na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:

![Hlavičky odpovědi na stránce o ukazují, že byly přidány GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvence model obslužné rutiny pro všechny stránky

Použití <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> ke kolekci <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> stránce instance, které se použijí během konstrukce model obslužné rutiny.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Stránka trasy akce konvence

Výchozího zprostředkovatele modelu trasy, která je odvozena z <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá smluv, které poskytují body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Složka trasy modelu konvence

Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Tato ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> přidat `{otherPagesTemplate?}` šablonu trasy na stránky *OtherPages* složky:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. To zajistí, že šablona pro `{globalTemplate?}` (dříve v tématu, které chcete nastavit `1`) je prioritu pro první data trasy hodnotu pozice, pokud je zadaná hodnota jednu trasu. Pokud stránku *stránek/OtherPages* složky je požadováno s hodnotu parametru trasy (například `/OtherPages/Page1/RouteDataValue`), "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.

Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`. Spolehněte se na směrování k výběru správné směrování.

Požadavek ukázky Page1 stránku na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a zkontrolujte výsledek:

![Page1 ve složce OtherPages žádá segmentu směrování GlobalRouteValue a OtherPagesRouteValue. Na vykreslené stránce ukazuje, že v metodě OnGet stránky jsou zachyceny hodnot dat trasy.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Stránka trasy modelu konvence

Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> stránky se zadaným názvem.

Tato ukázková aplikace používá `AddPageRouteModelConvention` přidat `{aboutTemplate?}` šablonu trasy ke stránce About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. To zajistí, že šablona pro `{globalTemplate?}` (dříve v tématu, které chcete nastavit `1`) je prioritu pro první data trasy hodnotu pozice, pokud je zadaná hodnota jednu trasu. Pokud je hodnotou parametr trasy na požadované stránku o `/About/RouteDataValue`, "RouteDataValue" je načten do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení `Order` vlastnost.

Kdykoli je to možné, nemají nastavený `Order`, což má za následek `Order = 0`. Spolehněte se na směrování k výběru správné směrování.

Žádosti o stránku ukázky na `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a zkontrolujte výsledek:

![Stránka je požadováno se segmenty směrování pro GlobalRouteValue a AboutRouteValue. Na vykreslené stránce ukazuje, že v metodě OnGet stránky jsou zachyceny hodnot dat trasy.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Použití transformátoru parametr k přizpůsobení stránky trasy

Použití transformeru parametr lze přizpůsobit stránky trasy generovaných ASP.NET Core. Parametr transformer implementuje `IOutboundParameterTransformer` a transformuje hodnoty parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformer změny `SubscriptionManagement` trasy hodnota, která má `subscription-management`.

`PageRouteTransformerConvention` Stránky trasy modelu úmluvy transformátoru parametr pro složku a soubor segmenty název trasy pro automaticky generované stránky v aplikaci. Například soubor Razor Pages na */Pages/SubscriptionManagement/ViewAll.cshtml* bude mít jeho trasu přepsán z `/SubscriptionManagement/ViewAll` k `/subscription-management/view-all`.

`PageRouteTransformerConvention` pouze transformuje automaticky generované segmentů směrování stránky, které pocházejí ze stránky Razor složku a název souboru. To nebude transformace segmenty směrování přidána s `@page` směrnice. Úmluvy nebude transformovat trasy přidal <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

`PageRouteTransformerConvention` Je zaregistrovaný jako možnost v `Startup.ConfigureServices`:

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

## <a name="configure-a-page-route"></a>Konfigurace stránky trasy

Použití <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> trasy na stránku konfigurace v cestě zadané stránky. Vygenerovaný odkazy na stránce použít zadanou trasu. `AddPageRoute` používá `AddPageRouteModelConvention` k vytvoření trasy.

Ukázková aplikace vytvoří trasu k `/TheContactPage` pro *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Na stránce kontaktu lze dosáhnout také za `/Contact` prostřednictvím jeho výchozí trasy.

Vlastní trasy ukázkovou aplikaci na stránku kontaktujte umožňuje volitelně `text` segment směrování (`{text?}`). Na stránce také zahrnuje tato volitelná segment v jeho `@page` direktiv v případě, že na stránce návštěvníka má přístup k jeho `/Contact` trasy:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL se vygeneruje pro **kontakt** odkaz na vykreslené stránce odráží aktualizovaný trasy:

![Ukázkový odkaz na aplikaci obraťte se na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu kontakt v zobrazený HTML označuje, že je odkaz nastavený na "/ TheContactPage.](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku nástroje kontakt na buď jeho běžný trasu `/Contact`, nebo vlastní trasy `/TheContactPage`. Pokud zadáte další `text` trasy segmentu, na stránce se zobrazí segment kódovaný jazykem HTML, že zadáte:

![Příklad prohlížeče Edge poskytnutí segment trasy volitelné 'text' 'TextValue"v adrese URL. Na vykreslené stránce zobrazuje hodnota 'text' segmentu.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akce modelu stránky

Výchozího zprostředkovatele modelu stránky, která implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> vyvolá smluv, které poskytují body rozšiřitelnosti pro konfiguraci stránky modely. Tato konvence jsou užitečné při vytváření a úprava stránky zjišťování a scénáře zpracování.

Příklady v této části, se ukázková aplikace používá `AddHeaderAttribute` třídy, která je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, aplikovaná hlavičky odpovědi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvencí vzorek ukazuje, jak použít atribut na všechny stránky ve složce a jednu stránku.

**Složky aplikace modelu konvence**

Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> instance pro všechny stránky v zadané složce.

Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř *OtherPages* složku aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Požadavek ukázky Page1 stránku na `localhost:5000/OtherPages/Page1` a zkontrolujte záhlaví, abyste viděli výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Stránka aplikace modelu konvence**

Použití <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> , která vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> stránky se zadaným názvem.

Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, ke stránce About:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:

![Hlavičky odpovědi na stránce o ukazují, že byly přidány AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Nakonfiguruje zadaný filtr použít. Můžete implementovat filtr třídy, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován jako objekt factory, který vrátí filtr pozadí:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model stránky aplikace se používá ke kontrole relativní cestu pro segmenty, které vedou na stránku strany Page2 *OtherPages* složky. Pokud bude podmínka splněna, se přidá hlavičku. Pokud ne, `EmptyFilter` platí.

`EmptyFilter` je [filtr akce](xref:mvc/controllers/filters#action-filters). Protože filtrů Akce ignorovány pomocí Razor Pages `EmptyFilter` operace tak, jak má, pokud cesta neobsahuje `OtherPages/Page2`.

Požadavek ukázky strany Page2 stránku na `localhost:5000/OtherPages/Page2` a zkontrolujte záhlaví, abyste viděli výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro strany Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace továrny filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> Nastaví zadaný objekt factory použít [filtry](xref:mvc/controllers/filters) na všechny stránky Razor.

Ukázkovou aplikaci poskytuje příklad použití [filtr factory](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvě hodnoty a stránek vaší aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Žádosti o stránku ukázky na `localhost:5000/About` a zkontrolujte záhlaví, abyste viděli výsledek:

![Hlavičky odpovědi na stránce o ukazují, že byly přidány dva FilterFactoryHeader záhlaví.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

MVC [filtrů Akce](xref:mvc/controllers/filters#action-filters) ignorují podle stránky Razor, protože stránky Razor pomocí metody obslužné rutiny. Jiné typy filtrů MVC jsou k dispozici pro použití: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředků](xref:mvc/controllers/filters#resource-filters), a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) tématu.

Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který platí pro stránky Razor. Další informace najdete v tématu [metody filtrování pro Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* [Konvence autorizace Razor Pages](xref:security/authorization/razor-pages-authorization)
