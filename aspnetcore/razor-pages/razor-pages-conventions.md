---
title: Konvence trasy a aplikací Razor Pages v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak konvence poskytovatele trasy a modelu aplikace pomáhají řídit směrování, zjišťování a zpracování stránek.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 6124554d5f9859179edfb5c545cf0b082369c0c9
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642736"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Konvence trasy a aplikací Razor Pages v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.

Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky. Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů. Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:mvc/controllers/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

| Scénář | Vzorek ukazuje ... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu trasy a záhlaví na stránky aplikace. |
| [Konvence akcí trasy stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu trasy na stránky ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</li></ul> | Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace. |

Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

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

## <a name="route-order"></a>Pořadí postupu

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Objednávka není zadána (výchozí hodnota). Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování postupu. |

Zpracování postupu je stanoveno úmluvou:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.
* Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu. Obecně platí, že směrování vybere správnou trasu s párováním adres URL. Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat. Vyhledejte zjednodušení schématu směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.

Razor Pages směrování a MVC řadič směrování sdílet implementaci. Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidání konvence modelu trasy na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.

Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`. Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:

* Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu. Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.
* Šablona `{aboutTemplate?}` trasy je přidána později v tématu. Šablona `{aboutTemplate?}` je `Order` dána . `2` Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).
* Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu. Šablona `{otherPagesTemplate?}` je `Order` dána . `2` Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v . Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.

Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu. Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce. Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi. Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidání konvence modelu obslužné rutiny na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí trasy stránky

Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.

### <a name="folder-route-model-convention"></a>Konvence modelu postupu složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.

Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu trasy stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.

Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Přizpůsobení tras stránek pomocí transformátoru parametrů

Trasy stránek generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .

Konvence `PageRouteTransformerConvention` modelu trasy stránky aplikuje transformátor parametrů na segmenty složek a názvů souborů automaticky generovaných tras stránek v aplikaci. Například soubor Razor Pages na *adrese /Pages/SubscriptionManagement/ViewAll.cshtml* by `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`měl svou trasu přepsánz do .

`PageRouteTransformerConvention`transformuje pouze automaticky generované segmenty trasy stránky, které pocházejí ze složky Razor Pages a názvu souboru. Netransformuje segmenty trasy přidané `@page` se směrnicí. Konvence také netransformuje trasy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>přidané .

Je `PageRouteTransformerConvention` registrován jako možnost `Startup.ConfigureServices`v :

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

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky. Generované odkazy na stránku používají zadanou trasu. `AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.

Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ). Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase . Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL. Vykreslená stránka zobrazuje hodnotu segmentu "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.

Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.

Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.

Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.* Pokud podmínka projde, je přidána hlavička. Pokud ne, `EmptyFilter` použije se.

`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.

Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace výroby filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)

Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr Stránky (IPageFilter)

[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny. K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters). Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)

Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky. Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.

Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky. Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů. Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

| Scénář | Vzorek ukazuje ... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu trasy a záhlaví na stránky aplikace. |
| [Konvence akcí trasy stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu trasy na stránky ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</li></ul> | Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace. |

Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

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

## <a name="route-order"></a>Pořadí postupu

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Objednávka není zadána (výchozí hodnota). Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování postupu. |

Zpracování postupu je stanoveno úmluvou:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.
* Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu. Obecně platí, že směrování vybere správnou trasu s párováním adres URL. Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat. Vyhledejte zjednodušení schématu směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.

Razor Pages směrování a MVC řadič směrování sdílet implementaci. Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidání konvence modelu trasy na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.

Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`. Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:

* Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu. Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.
* Šablona `{aboutTemplate?}` trasy je přidána později v tématu. Šablona `{aboutTemplate?}` je `Order` dána . `2` Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).
* Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu. Šablona `{otherPagesTemplate?}` je `Order` dána . `2` Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v . Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.

Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu. Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce. Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi. Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidání konvence modelu obslužné rutiny na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí trasy stránky

Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.

### <a name="folder-route-model-convention"></a>Konvence modelu postupu složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.

Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu trasy stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.

Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Přizpůsobení tras stránek pomocí transformátoru parametrů

Trasy stránek generované ASP.NET Core lze přizpůsobit pomocí transformátoru parametrů. Parametr transformátor `IOutboundParameterTransformer` implementuje a transformuje hodnotu parametrů. Například vlastní `SlugifyParameterTransformer` parametr transformátor `SubscriptionManagement` změní `subscription-management`hodnotu trasy na .

Konvence `PageRouteTransformerConvention` modelu trasy stránky aplikuje transformátor parametrů na segmenty složek a názvů souborů automaticky generovaných tras stránek v aplikaci. Například soubor Razor Pages na *adrese /Pages/SubscriptionManagement/ViewAll.cshtml* by `/SubscriptionManagement/ViewAll` `/subscription-management/view-all`měl svou trasu přepsánz do .

`PageRouteTransformerConvention`transformuje pouze automaticky generované segmenty trasy stránky, které pocházejí ze složky Razor Pages a názvu souboru. Netransformuje segmenty trasy přidané `@page` se směrnicí. Konvence také netransformuje trasy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>přidané .

Je `PageRouteTransformerConvention` registrován jako možnost `Startup.ConfigureServices`v :

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

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky. Generované odkazy na stránku používají zadanou trasu. `AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.

Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ). Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase . Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL. Vykreslená stránka zobrazuje hodnotu segmentu "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.

Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.

Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.

Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.* Pokud podmínka projde, je přidána hlavička. Pokud ne, `EmptyFilter` použije se.

`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.

Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace výroby filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)

Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr Stránky (IPageFilter)

[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny. K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters). Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)

Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky. Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Přečtěte si, jak používat konvence trasy stránky [a modelu aplikace](xref:mvc/controllers/application-model#conventions) k řízení směrování, zjišťování a zpracování stránek v aplikacích Razor Pages.

Pokud potřebujete nakonfigurovat vlastní trasy stránek pro jednotlivé stránky, nakonfigurujte směrování na stránky pomocí [konvence AddPageRoute](#configure-a-page-route) popsané dále v tomto tématu.

Chcete-li určit trasu stránky, přidat segmenty trasy nebo přidat parametry do trasy, použijte direktivu `@page` stránky. Další informace naleznete [v tématu Vlastní trasy](xref:razor-pages/index#custom-routes).

Existují vyhrazená slova, která nelze použít jako segmenty trasy nebo názvy parametrů. Další informace naleznete v [tématu Směrování: Vyhrazené názvy směrování](xref:fundamentals/routing#reserved-routing-names).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

| Scénář | Vzorek ukazuje ... |
| -------- | --------------------------- |
| [Modelové konvence](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Přidejte šablonu trasy a záhlaví na stránky aplikace. |
| [Konvence akcí trasy stránky](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Přidejte šablonu trasy na stránky ve složce a na jednu stránku. |
| [Konvence akcí modelu stránky](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (třída filtru, výraz lambda nebo továrna filtrů)</li></ul> | Přidejte záhlaví na stránky ve složce, přidejte záhlaví na jednu stránku a nakonfigurujte [továrnu filtrů](xref:mvc/controllers/filters#ifilterfactory) tak, aby přidala záhlaví na stránky aplikace. |

Razor Pages konvence jsou přidány <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> a konfigurovány pomocí metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> kolekci služeb ve `Startup` třídě. Následující příklady konvence jsou vysvětleny dále v tomto tématu:

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

## <a name="route-order"></a>Pořadí postupu

Trasy určují <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro zpracování (párování tras).

| Objednání            | Chování |
| :--------------: | -------- |
| -1               | Trasa je zpracována před zpracováním jiných tras. |
| 0                | Objednávka není zadána (výchozí hodnota). Nepřiřazovat `Order` (`Order = null` `Order` ) výchozí trasu na 0 (nula) pro zpracování. |
| 1, 2, &hellip; n | Určuje pořadí zpracování postupu. |

Zpracování postupu je stanoveno úmluvou:

* Trasy jsou zpracovávány v sekvenčním pořadí (-1, 0, 1, 2, &hellip; n).
* Pokud mají trasy `Order`stejné , nejkonkrétnější trasa se nejprve shoduje s méně specifickými trasami.
* Pokud trasy se `Order` stejným a stejným počtem parametrů odpovídají adrese URL požadavku, jsou trasy zpracovány v <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>pořadí, v jakém jsou přidány do .

Pokud je to možné, vyhněte se v závislosti na zavedeném pořadí zpracování postupu. Obecně platí, že směrování vybere správnou trasu s párováním adres URL. Pokud je nutné `Order` nastavit vlastnosti trasy pro směrování požadavků správně, schéma směrování aplikace je pravděpodobně matoucí pro klienty a křehké udržovat. Vyhledejte zjednodušení schématu směrování aplikace. Ukázková aplikace vyžaduje explicitní pořadí zpracování trasy k předvedení několika scénářů směrování pomocí jedné aplikace. Měli byste se však pokusit vyhnout `Order` se praxi nastavení postupu v produkčních aplikacích.

Razor Pages směrování a MVC řadič směrování sdílet implementaci. Informace o pořadí tras v tématech MVC jsou k dispozici na [adrese Akce Směrování k kontrolorovi: Řazení tras atributů](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modelové konvence

Přidejte delegáta pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> přidání [konvencí modelu,](xref:mvc/controllers/application-model#conventions) které se vztahují k razor pages.

### <a name="add-a-route-model-convention-to-all-pages"></a>Přidání konvence modelu trasy na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu trasy stránky.

Ukázková aplikace `{globalTemplate?}` přidá šablonu trasy na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `1`. Tím zajistíte následující chování při porovnávání tras v ukázkové aplikaci:

* Šablona trasy `TheContactPage/{text?}` pro je přidána později v tématu. Trasa Stránka kontaktu má `null` výchozí`Order = 0`pořadí ( ), `{globalTemplate?}` takže odpovídá před šablonou trasy.
* Šablona `{aboutTemplate?}` trasy je přidána později v tématu. Šablona `{aboutTemplate?}` je `Order` dána . `2` Je-li požadována `/About/RouteDataValue`stránka O , je z `RouteData.Values["globalTemplate"]` `Order = 1`důvodu nastavení `RouteData.Values["aboutTemplate"]` `Order = 2`vlastnosti načtena `Order` hodnota RouteDataValue do položky ( ) a nikoli ( ).
* Šablona `{otherPagesTemplate?}` trasy je přidána později v tématu. Šablona `{otherPagesTemplate?}` je `Order` dána . `2` Pokud je požadována libovolná stránka ve složce *Pages/OtherPages* s parametrem `/OtherPages/Page1/RouteDataValue`trasy `RouteData.Values["globalTemplate"]` (například), "RouteDataValue" je načtena do (`Order = 1`) a ne `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Razor Pages možnosti, <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>jako je například přidání , jsou `Startup.ConfigureServices`přidány při MVC je přidán do kolekce služeb v . Například najdete v [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Vyžádejte si `localhost:5000/About/GlobalRouteValue` stránku O vzorku a zkontrolujte výsledek:

![Stránka Informace je požadována s segmentem trasy GlobalRouteValue. Vykreslená stránka ukazuje, že hodnota dat trasy je zachycena v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Přidání konvence modelu aplikace na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které se použijí během konstrukce modelu aplikace stránky.

Chcete-li demonstrovat toto a další konvence dále `AddHeaderAttribute` v tématu, ukázková aplikace obsahuje třídu. Konstruktor třídy přijímá `name` řetězec `values` a pole řetězce. Tyto hodnoty se `OnResultExecuting` používají v jeho metodě nastavit hlavičku odpovědi. Celá třída je zobrazena v části [Akce modelu stránky](#page-model-action-conventions) dále v tématu.

Ukázková aplikace `AddHeaderAttribute` používá třídu k `GlobalHeader`přidání záhlaví , na všechny stránky v aplikaci:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána globalheader.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Přidání konvence modelu obslužné rutiny na všechny stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> a přidání <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> a do kolekce instancí, které jsou použity během konstrukce modelu obslužné rutiny stránky.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konvence akcí trasy stránky

Výchozí zprostředkovatel modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> který je odvozen od vyvolá ní konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci tras stránek.

### <a name="folder-route-model-convention"></a>Konvence modelu postupu složky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, který <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci pro všechny stránky pod zadanou složkou.

Ukázková aplikace <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> používá `{otherPagesTemplate?}` k přidání šablony trasy na stránky ve složce *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka ve složce *Pages/OtherPages* s `/OtherPages/Page1/RouteDataValue`hodnotou parametru trasy (například), "RouteDataValue" je načtena `RouteData.Values["globalTemplate"]` do (`Order = 1`) a `RouteData.Values["otherPagesTemplate"]` ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si stránku Stránky 1 ukázky a `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` zkontrolujte výsledek:

![Stránka1 ve složce OtherPages je požadována s segmentem trasy GlobalRouteValue a OtherPagesRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konvence modelu trasy stránky

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> vyvolá akci na stránce se zadaným názvem.

Ukázková aplikace `AddPageRouteModelConvention` používá `{aboutTemplate?}` k přidání šablony trasy na stránku Informace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Vlastnost <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> pro <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> je nastavena na `2`. Tím je zajištěno, `{globalTemplate?}` že šablona pro `1`(nastavená dříve v tématu na ) má prioritu pro první pozici hodnoty dat trasy, pokud je poskytnuta jedna hodnota trasy. Pokud je požadována stránka O s `/About/RouteDataValue`hodnotou parametru trasy `RouteData.Values["globalTemplate"]` na`Order = 1`, `RouteData.Values["aboutTemplate"]` "RouteDataValue" je načten do ( ) a ne (`Order = 2`) z důvodu nastavení vlastnosti. `Order`

Pokud je to možné, `Order`nenastavujte , což má za následek `Order = 0`. Při výběru správné trasy se můžete spolehnout na směrování.

Vyžádejte si `localhost:5000/About/GlobalRouteValue/AboutRouteValue` stránku O vzorku a zkontrolujte výsledek:

![O stránce je požadováno s segmenty postupu pro GlobalRouteValue a AboutRouteValue. Vykreslená stránka ukazuje, že hodnoty dat trasy jsou zachyceny v metodě OnGet stránky.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurace trasy stránky

Slouží <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> ke konfiguraci trasy ke stránce na zadané cestě stránky. Generované odkazy na stránku používají zadanou trasu. `AddPageRoute`používá `AddPageRouteModelConvention` k určení trasy.

Ukázková aplikace vytvoří `/TheContactPage` trasu pro *Contact.cshtml*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Na stránku Kontakt lze `/Contact` také dosáhnout prostřednictvím výchozí trasy.

Vlastní trasa ukázkové aplikace na stránku `text` Kontakt umožňuje`{text?}`volitelný segment trasy ( ). Stránka také obsahuje tento volitelný `@page` segment ve své směrnici v `/Contact` případě, že návštěvník přistupuje ke stránce na své trase:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Všimněte si, že adresa URL vygenerovaná pro odkaz **Kontakt** na vykreslené stránce odráží aktualizovanou trasu:

![Ukázkový odkaz Kontakt aplikace na navigačním panelu](razor-pages-conventions/_static/contact-link.png)

![Kontrola odkazu Kontakt v vykresleném KÓDU HTML znamená, že href je nastaven na '/TheContactPage'](razor-pages-conventions/_static/contact-link-source.png)

Navštivte stránku Kontakt na běžné `/Contact`trase , `/TheContactPage`nebo na vlastní trase . Pokud zadáte další `text` segment trasy, stránka zobrazí segment kódovaný HTML, který zadáte:

![Okraj ový příklad poskytnutí volitelného segmentu trasy "text" textvalue v adrese URL. Vykreslená stránka zobrazuje hodnotu segmentu "text".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konvence akcí modelu stránky

Výchozí zprostředkovatel modelu stránky, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> který implementuje vyvolá konvence, které jsou navrženy tak, aby poskytovaly body rozšiřitelnosti pro konfiguraci modelů stránek. Tyto konvence jsou užitečné při vytváření a úpravách scénáře zjišťování a zpracování stránky.

Příklady v této části ukázkové aplikace `AddHeaderAttribute` používá třídu, což je <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, která používá hlavičku odpovědi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Pomocí konvencí ukázka ukazuje, jak použít atribut pro všechny stránky ve složce a na jednu stránku.

**Konvence modelu aplikace složky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání akce, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> která vyvolá akci na instancích pro všechny stránky v zadané složce.

Ukázka ukazuje použití `AddFolderApplicationModelConvention` přidáním záhlaví `OtherPagesHeader`, na stránky uvnitř složky *OtherPages* aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Požádejte ukázkovou stránku `localhost:5000/OtherPages/Page1` Page1 a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce OtherPages/Page1 ukazují, že byla přidána hlavička OtherPagesHeader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konvence modelu aplikace stránky**

Slouží <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> k vytvoření <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> a přidání, která <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> vyvolá akci na stránce se zadaným názvem.

Ukázka ukazuje použití `AddPageApplicationModelConvention` přidáním záhlaví `AboutHeader`, na stránku O:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byla přidána hlavička AboutHeader.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurace filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje použití zadaného filtru. Můžete implementovat třídu filtru, ale ukázková aplikace ukazuje, jak implementovat filtr ve výrazu lambda, který je implementován na pozadí jako továrna, která vrací filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikace stránky se používá ke kontrole relativní cesty pro segmenty, které vedou na stránku Stránka2 ve složce *OtherPages.* Pokud podmínka projde, je přidána hlavička. Pokud ne, `EmptyFilter` použije se.

`EmptyFilter`je [filtr akce](xref:mvc/controllers/filters#action-filters). Vzhledem k tomu, že filtry akce jsou ignorovány Razor Pages, `EmptyFilter` nemá žádný účinek, jak bylo zamýšleno, pokud cesta neobsahuje `OtherPages/Page2`.

Požádejte stránku Page2 `localhost:5000/OtherPages/Page2` ukázky a zkontrolujte záhlaví, abyste zobrazili výsledek:

![OtherPagesPage2Header je přidán do odpovědi pro Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurace výroby filtrů**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>nakonfiguruje zadanou továrnu tak, aby na všechny stránky Razor [použila filtry.](xref:mvc/controllers/filters)

Ukázková aplikace poskytuje příklad použití [továrny filtrů](xref:mvc/controllers/filters#ifilterfactory) přidáním záhlaví `FilterFactoryHeader`, se dvěma hodnotami na stránky aplikace:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Požádejte o stránku `localhost:5000/About` Informace v ukázce a zkontrolujte záhlaví, abyste zobrazili výsledek:

![Záhlaví odpovědí na stránce O stránce ukazují, že byly přidány dvě záhlaví FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC a filtr Stránky (IPageFilter)

[Filtry akce](xref:mvc/controllers/filters#action-filters) MVC jsou razor pages ignorovány, protože Razor Pages používají metody obslužné rutiny. K dispozici jsou další typy filtrů MVC: [Autorizace](xref:mvc/controllers/filters#authorization-filters), [Výjimka](xref:mvc/controllers/filters#exception-filters), [Prostředek](xref:mvc/controllers/filters#resource-filters)a [Výsledek](xref:mvc/controllers/filters#result-filters). Další informace naleznete v tématu [Filtry.](xref:mvc/controllers/filters)

Filtr Stránka<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) je filtr, který se vztahuje na žiletky stránky. Další informace naleznete v [tématu Filter methods for Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
