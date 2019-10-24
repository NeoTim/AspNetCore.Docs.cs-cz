---
title: Razor Pages konvence směrování a aplikace v ASP.NET Core
author: guardrex
description: Seznamte se s tím, jak konvence poskytovatelů modelů směrování a aplikací umožňují řídit směrování, zjišťování a zpracování stránky.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/22/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: a0a1eda69da34d1865fd11ef464c3697bcd01eff
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779220"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor Pages konvence směrování a aplikace v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex)

Naučte se používat [konvence směrování stránky a poskytovatele modelů aplikací](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránky v aplikacích Razor Pages.

Pokud potřebujete nakonfigurovat vlastní trasy stránky pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [AddPageRoute konvence](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty směrování nebo přidat parametry do trasy, použijte direktivu `@page` stránky. Další informace najdete v tématu [vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty směrování nebo názvy parametrů. Další informace najdete v tématu [směrování: názvy rezervovaných směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

| Scénář | Ukázka znázorňuje... |
| -------- | --------------------------- |
| [Konvence modelu](#model-conventions)<br><br>Konvence. Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu a hlavičku směrování na stránky aplikace. |
| [Konvence akcí při směrování stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu směrování do stránek ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (filtrovat třídu, lambda výraz nebo objekt pro vytváření filtru)</li></ul> | Umožňuje přidat záhlaví na stránky ve složce, přidat záhlaví na jednu stránku a nakonfigurovat [objekt pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidal hlavičku na stránky aplikace. |

Razor Pages konvence jsou přidány a nakonfigurovány pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> pro <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> v kolekci služeb ve třídě `Startup`. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="route-order"></a>Pořadí směrování

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (odpovídá směrování).

| Za            | Předvídatelně |
| :--------------: | -------- |
| – 1               | Trasa je zpracována před zpracováním jiných tras. |
| 0,8                | Pořadí není zadáno (výchozí hodnota). Při nepřiřazování `Order` (`Order = null`) jsou výchozí hodnoty `Order` trasy na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování směrování. |

Zpracování směrování je zřízené podle konvence:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* Když mají stejné trasy stejné `Order`, bude se nejprve shodovat konkrétní trasa, za kterou následuje méně specifických tras.
* Když se trasy se stejnou `Order` a stejný počet parametrů shodují s adresou URL požadavku, trasy se zpracovávají v pořadí, v jakém jsou přidány do <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>.

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování směrování. Obecně směrování vybírá správnou trasu s odpovídající adresou URL. Pokud musíte nastavit vlastnosti směrovacích `Order` tak, aby správně směrovaly požadavky, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké k údržbě. Vyhledejte zjednodušené schéma směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování tras, které předvádí několik scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout se postupu nastavení `Order` trasy v produkčních aplikacích.

Směrování Razor Pages směrování a řadiče MVC sdílí implementaci. Informace o pořadí směrování v tématech MVC jsou k dispozici při [směrování na akce kontroleru: trasy atributů řazení](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Konvence modelu

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pro přidání [konvencí modelu](xref:mvc/controllers/application-model#conventions) , které se vztahují na Razor Pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidat konvenci modelu směrování na všechny stránky

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu směrování stránky.

Ukázková aplikace přidá šablonu směrování `{globalTemplate?}` na všechny stránky v aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

::: moniker-end

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`. Tím se zajistí následující chování při shodě trasy v ukázkové aplikaci:

* Šablona směrování pro `TheContactPage/{text?}` se přidá později v tématu. Trasa stránky kontaktu má výchozí pořadí `null` (`Order = 0`), takže odpovídá před šablonou směrování `{globalTemplate?}`.
* Šablona směrování `{aboutTemplate?}` je přidána později v tématu. Šabloně `{aboutTemplate?}` je předána `Order` `2`. Když se na stránce o aplikaci žádá `/About/RouteDataValue`, načtou se "RouteDataValue" do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.
* Šablona směrování `{otherPagesTemplate?}` je přidána později v tématu. Šabloně `{otherPagesTemplate?}` je předána `Order` `2`. Pokud se u libovolné stránky ve složce *Pages/OtherPages* požaduje parametr trasy (například `/OtherPages/Page1/RouteDataValue`), RouteDataValue se načte do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.

Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`. Pro výběr správné trasy se spoléhá na směrování.

Razor Pages možnosti, jako je přidání <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, jsou přidány při přidání MVC do kolekce služby v `Startup.ConfigureServices`. Příklad najdete v [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

::: moniker-end

Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue` a Prohlédněte si výsledek:

![Stránka o aplikaci je požadována s segmentem směrování GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu aplikace stránky.

Chcete-li předvést tuto a další konvenci dále v tématu, ukázková aplikace obsahuje třídu `AddHeaderAttribute`. Konstruktor třídy přijímá `name` řetězec a pole `values` řetězců. Tyto hodnoty se používají ve své `OnResultExecuting` metodě pro nastavení hlavičky odpovědi. Úplná třída je uvedena v části [konvence akcí modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace používá třídu `AddHeaderAttribute` k přidání záhlaví, `GlobalHeader`, na všechny stránky v aplikaci:

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

Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán GlobalHeader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidat konvenci modelu obslužné rutiny na všechny stránky

Pomocí <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> do kolekce instancí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, které se použijí během vytváření modelu obslužné rutiny stránky.

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

Výchozí zprostředkovatel modelu směrování odvozený od <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> vyvolá konvence, které jsou navržené tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránky.

### <a name="folder-route-model-convention"></a>Konvence modelu směrování složky

Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro všechny stránky v zadané složce.

Ukázková aplikace používá <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k přidání šablony trasy `{otherPagesTemplate?}` na stránky ve složce *OtherPages* :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

::: moniker-end

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy. Pokud je stránka ve složce *Pages/OtherPages* požadována s hodnotou parametru trasy (například `/OtherPages/Page1/RouteDataValue`), je "RouteDataValue" načteno do `RouteData.Values["globalTemplate"]` (`Order = 1`) a není `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.

Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`. Pro výběr správné trasy se spoléhá na směrování.

Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` a Prohlédněte si výsledek:

![Page1 ve složce OtherPages se vyžádá s segmentem směrování GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu směrování stránky

Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> pro stránku se zadaným názvem.

Ukázková aplikace používá `AddPageRouteModelConvention` k přidání šablony trasy `{aboutTemplate?}` na stránku o aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

::: moniker-end

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, že šablona pro `{globalTemplate?}` (nastavená dříve v tématu `1`) má prioritu první pozice hodnoty dat trasy, když je zadána hodnota jedné trasy. Pokud se stránka o žádosti vyžaduje s hodnotou parametru Route na `/About/RouteDataValue`, načte se RouteDataValue do `RouteData.Values["globalTemplate"]` (`Order = 1`) a ne`RouteData.Values["aboutTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti `Order`.

Pokud je to možné, nenastavte `Order`, což má za následek `Order = 0`. Pro výběr správné trasy se spoléhá na směrování.

Požádejte o stránku ukázky o `localhost:5000/About/GlobalRouteValue/AboutRouteValue` a Prohlédněte si výsledek:

![U segmentů směrování pro GlobalRouteValue a AboutRouteValue se žádá o stránku. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Použití transformátoru parametrů k přizpůsobení cest stránky

Trasy stránky generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Transformátor parametru implementuje `IOutboundParameterTransformer` a transformuje hodnotu parametrů. Například vlastní parametr `SlugifyParameterTransformer` Transformer změní hodnotu trasy `SubscriptionManagement` na `subscription-management`.

Konvence modelu směrování na stránce `PageRouteTransformerConvention` aplikuje transformátor parametrů na složku a název souboru segmentů automaticky generovaných tras stránky v aplikaci. Například soubor Razor Pages v */Pages/SubscriptionManagement/ViewAll.cshtml* by měl přepsané směrování z `/SubscriptionManagement/ViewAll` na `/subscription-management/view-all`.

`PageRouteTransformerConvention` pouze transformuje automaticky generované segmenty cesty stránky, které pocházejí ze složky Razor Pages a názvu souboru. Netransformuje segmenty směrování přidané pomocí direktivy `@page`. Konvence také netransformuje trasy přidané pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>.

@No__t_0 je v `Startup.ConfigureServices` zaregistrován jako možnost:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

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

::: moniker range="= aspnetcore-2.2"

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

Pomocí <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> můžete nakonfigurovat směrování na stránku na zadané cestě stránky. Vygenerované odkazy na stránku používají zadanou trasu. `AddPageRoute` používá k vytvoření trasy `AddPageRouteModelConvention`.

Ukázková aplikace vytvoří trasu, která `/TheContactPage` pro *kontakt. cshtml*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

::: moniker-end

Stránku kontaktů lze také získat na `/Contact` prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránce kontaktů umožňuje, aby byl `text` segment trasy (`{text?}`). Tato stránka zahrnuje také tento volitelný segment v direktivě `@page` pro případ, že návštěvník přistupuje k této stránce `/Contact` trase:

::: moniker range=">= aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

::: moniker-end

Všimněte si, že adresa URL vygenerovaná pro odkaz **kontaktu** na vykreslené stránce odráží aktualizovanou trasu:

![Odkaz na ukázkový kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu na kontakt ve vykresleném kódu HTML znamená, že odkaz href je nastaven na hodnotu "/TheContactPage".](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku kontaktů buď na své běžné trase, `/Contact`, nebo na vlastní trasu `/TheContactPage`. Pokud zadáte další segment `text` trasy, stránka zobrazuje segment zakódovaný ve formátu HTML, který zadáte:

![Prohlížeč Edge – příklad poskytnutí volitelného segmentu "text" trasy "TextValue" v adrese URL Vykreslená stránka ukazuje hodnotu segment "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí poskytovatel modelu stránky, který implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>, vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při sestavování a úpravách scénářů zjišťování a zpracování stránky.

V příkladech v této části používá ukázková aplikace třídu `AddHeaderAttribute`, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

::: moniker-end

Pomocí konvence Ukázka ukazuje, jak použít atribut na všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> můžete vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na instancích <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro všechny stránky v zadané složce.

Ukázka demonstruje použití `AddFolderApplicationModelConvention` přidáním hlavičky, `OtherPagesHeader` na stránky ve složce *OtherPages* v aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

::: moniker-end

Vyžádejte si Page1 stránku ukázky na `localhost:5000/OtherPages/Page1` a Prohlédněte si hlavičky a podívejte se na výsledek:

![Hlavičky odpovědi stránky OtherPages/Page1 ukazují, že byla přidána OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Pomocí <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> vytvořit a přidat <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, který vyvolá akci na <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> pro stránku se zadaným názvem.

Ukázka demonstruje použití `AddPageApplicationModelConvention` přidáním hlavičky, `AboutHeader` na stránku o aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

::: moniker-end

Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o produktu ukazují, že byl přidán AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje zadaný filtr, který se má použít. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako objekt pro vytváření, který vrací filtr:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

::: moniker-end

Model webové aplikace se používá ke kontrole relativní cesty pro segmenty, které vedou ke stránce Page2 ve složce *OtherPages* . Pokud podmínka projde, přidá se hlavička. V takovém případě je `EmptyFilter` použito.

`EmptyFilter` je [Filtr akcí](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že jsou filtry akcí ignorovány Razor Pages, `EmptyFilter` nemá žádný vliv, pokud cesta neobsahuje `OtherPages/Page2`.

Vyžádejte si Page2 stránku ukázky na `localhost:5000/OtherPages/Page2` a Prohlédněte si hlavičky a podívejte se na výsledek:

![OtherPagesPage2Header se přidá do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace objektu pro vytváření filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> nakonfiguruje určený objekt pro vytváření a použije [filtry](xref:mvc/controllers/filters) na všechny Razor Pages.

Ukázková aplikace poskytuje příklad použití [objektu pro vytváření filtru](xref:mvc/controllers/filters#ifilterfactory) přidáním hlavičky, `FilterFactoryHeader` se dvěma hodnotami na stránky aplikace:

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

Požádejte o stránku ukázek o `localhost:5000/About` a Prohlédněte si záhlaví a podívejte se na výsledek:

![Hlavičky odpovědi stránky o stránku zobrazují, že byly přidány dvě hlavičky FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr stránky (IPageFilter)

[Filtry akcí](xref:mvc/controllers/filters#action-filters) MVC se Razor Pages ignorují, protože Razor Pages použít obslužné rutiny. Další typy filtrů MVC jsou k dispozici pro použití: [autorizace](xref:mvc/controllers/filters#authorization-filters), [výjimka](xref:mvc/controllers/filters#exception-filters), [prostředek](xref:mvc/controllers/filters#resource-filters)a [výsledek](xref:mvc/controllers/filters#result-filters). Další informace najdete v tématu [filtry](xref:mvc/controllers/filters) .

Filtr stránky (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) je filtr, který se vztahuje na Razor Pages. Další informace naleznete v tématu [metody Filter pro Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
