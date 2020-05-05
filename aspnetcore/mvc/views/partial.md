---
title: Částečná zobrazení v ASP.NET Core
author: ardalis
description: Zjistíte, jak použít částečná zobrazení k rozdělení velkých souborů s označením a jak omezit duplicity běžných značek mezi webovými stránkami v ASP.NET Corech aplikacích.
ms.author: riande
ms.custom: mvc
ms.date: 06/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/partial
ms.openlocfilehash: 1bce6b9cdc876062b050eae6eb3c4acf0127ce92
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777121"
---
# <a name="partial-views-in-aspnet-core"></a>Částečná zobrazení v ASP.NET Core

[Steve Smith](https://ardalis.com/), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT)a [Scott Sauber](https://twitter.com/scottsauber)

Částečné zobrazení je soubor [Razor](xref:mvc/views/razor) označení (*. cshtml*), který vykresluje výstup HTML *v rámci* dalšího vykresleného výstupu souboru s označením.

::: moniker range=">= aspnetcore-2.1"

Termín *částečné zobrazení* se používá při vývoji aplikace MVC, kde se soubory značek nazývají *zobrazení*nebo aplikace Razor stránky, kde se soubory značek nazývají *stránky*. Toto téma obecně odkazuje na zobrazení MVC a Razor stránky stránek jako *soubory značek*.

::: moniker-end

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Kdy použít částečná zobrazení

Částečná zobrazení představují efektivní způsob:

* Rozdělte velké soubory s označením na menší součásti.

  Ve velkém komplexním souboru označení složeném z několika logických částí je výhodná práce s každou částí izolovanou do částečného zobrazení. Kód v souboru označení lze spravovat, protože značky obsahují pouze celkovou strukturu stránky a odkazy na částečná zobrazení.
* Snižte duplicitu společného obsahu značek napříč soubory značek.

  Když se stejné prvky značek používají napříč soubory značek, částečné zobrazení odstraní duplikaci obsahu kódu do jednoho souboru částečného zobrazení. Při změně kódu v částečném zobrazení aktualizuje Vykreslený výstup souborů označení, které používají částečné zobrazení.

Částečná zobrazení by se neměla používat k údržbě běžných prvků rozložení. V souboru [_Layout. cshtml](xref:mvc/views/layout) by měly být zadány běžné prvky rozložení.

Nepoužívejte částečné zobrazení, kde je pro vykreslení značky nutná složitá logika vykreslování nebo provádění kódu. Místo částečného zobrazení použijte [zobrazení komponenty](xref:mvc/views/view-components).

## <a name="declare-partial-views"></a>Deklarovat částečná zobrazení

::: moniker range=">= aspnetcore-2.0"

Částečné zobrazení je soubor s označením *. cshtml* udržovaný ve složce *zobrazení* (MVC) nebo ve složce *stránek* (Razor stránky).

V ASP.NET Core MVC může řadič <xref:Microsoft.AspNetCore.Mvc.ViewResult> vracet buď zobrazení, nebo částečné zobrazení. Na Razor stránkách <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> může vracet částečné zobrazení reprezentované jako <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> objekt. Odkazování na částečné zobrazení a jejich vykreslování je popsané v části [Reference k částečnému zobrazení](#reference-a-partial-view) .

Na rozdíl od zobrazení MVC nebo vykreslování stránky neběží částečné zobrazení *_ViewStart. cshtml*. Další informace o *_ViewStart. cshtml*naleznete v tématu <xref:mvc/views/layout>.

Názvy souborů částečného zobrazení často začínají podtržítkem (`_`). Tato konvence pojmenování není povinná, ale pomáhá vizuálně odlišit částečná zobrazení ze zobrazení a stránek.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Částečné zobrazení je soubor označení *. cshtml* spravovaný ve složce *views* .

Řadič <xref:Microsoft.AspNetCore.Mvc.ViewResult> může vracet buď zobrazení, nebo částečné zobrazení. Odkazování na částečné zobrazení a jejich vykreslování je popsané v části [Reference k částečnému zobrazení](#reference-a-partial-view) .

Na rozdíl od vykreslování zobrazení MVC neběží částečné zobrazení *_ViewStart. cshtml*. Další informace o *_ViewStart. cshtml*naleznete v tématu <xref:mvc/views/layout>.

Názvy souborů částečného zobrazení často začínají podtržítkem (`_`). Tato konvence pojmenování není povinná, ale pomáhá vizuálně odlišit částečná zobrazení ze zobrazení.

::: moniker-end

## <a name="reference-a-partial-view"></a>Odkaz na částečné zobrazení

::: moniker range=">= aspnetcore-2.0"

### <a name="use-a-partial-view-in-a-razor-pages-pagemodel"></a>Použití částečného zobrazení na Razor stránkách PageModel

V ASP.NET Core 2,0 nebo 2,1, následující metoda obslužné rutiny vykresluje částečné zobrazení * \_AuthorPartialRP. cshtml* na odpověď:

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

V ASP.NET Core 2,2 nebo novější, metoda obslužné rutiny může alternativně <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Partial*> zavolat metodu pro vytvoření `PartialViewResult` objektu:

[!code-csharp[](partial/sample/PartialViewsSample/Pages/DiscoveryRP.cshtml.cs?name=snippet_OnGetPartial)]

::: moniker-end

### <a name="use-a-partial-view-in-a-markup-file"></a>Použití částečného zobrazení v souboru označení

::: moniker range=">= aspnetcore-2.1"

V souboru označení existuje několik způsobů, jak odkazovat na částečné zobrazení. Doporučujeme, aby aplikace používaly jeden z následujících přístupů k asynchronnímu vykreslování:

* [Pomocná rutina částečné značky](#partial-tag-helper)
* [Asynchronní pomocník HTML](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

V souboru označení existují dva způsoby, jak odkazovat na částečné zobrazení:

* [Asynchronní pomocník HTML](#asynchronous-html-helper)
* [Synchronní pomocník HTML](#synchronous-html-helper)

Doporučujeme, aby aplikace používaly [asynchronní NÁPOVĚDU HTML](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Pomocná rutina částečné značky

[Pomocník pro částečnou značku](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) vyžaduje ASP.NET Core 2,1 nebo novější.

Pomocník pro částečnou značku vykresluje obsah asynchronně a používá syntaxi podobnou HTML:

```cshtml
<partial name="_PartialName" />
```

Pokud je k dispozici Přípona souboru, pomocník značek odkazuje na částečné zobrazení, které musí být ve stejné složce jako soubor označení, který volá částečné zobrazení:

```cshtml
<partial name="_PartialName.cshtml" />
```

Následující příklad odkazuje na částečné zobrazení z kořene aplikace. Cesty, které začínají znakem tildy (`~/`) nebo lomítkem (`/`), odkazují na kořen aplikace:

**RazorStránky**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

Následující příklad odkazuje na částečné zobrazení s relativní cestou:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Asynchronní pomocník HTML

Při použití pomocníka jazyka HTML je nejvhodnější použít <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync`Vrátí <xref:Microsoft.AspNetCore.Html.IHtmlContent> typ zabalený v <xref:System.Threading.Tasks.Task%601>. Na metodu se odkazuje pomocí předpony očekávaného volání s `@` znakem:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Pokud je k dispozici Přípona souboru, pomocník HTML odkazuje na částečné zobrazení, které musí být ve stejné složce jako soubor označení, který volá částečné zobrazení:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

Následující příklad odkazuje na částečné zobrazení z kořene aplikace. Cesty, které začínají znakem tildy (`~/`) nebo lomítkem (`/`), odkazují na kořen aplikace:

::: moniker range=">= aspnetcore-2.1"

**RazorStránky**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

Následující příklad odkazuje na částečné zobrazení s relativní cestou:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Alternativně můžete vykreslit částečné zobrazení pomocí <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>. Tato metoda nevrací <xref:Microsoft.AspNetCore.Html.IHtmlContent>. Streamuje Vykreslený výstup přímo k odpovědi. Vzhledem k tomu, že metoda nevrací výsledek, musí být volána v Razor rámci bloku kódu:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Vzhledem `RenderPartialAsync` k tomu, že streamy vykreslují obsah, poskytuje v některých scénářích lepší výkon. V situacích, které kritické pro výkon, srovnávací testy stránky pomocí obou přístupů a používejte přístup, který generuje rychlejší odpověď.

### <a name="synchronous-html-helper"></a>Synchronní pomocník HTML

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*>a <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> jsou synchronní ekvivalenty `PartialAsync` a `RenderPartialAsync`v uvedeném pořadí. Synchronní ekvivalenty se nedoporučují, protože existují scénáře, ve kterých se zablokují. Synchronní metody jsou zaměřené na odebrání v budoucí verzi.

> [!IMPORTANT]
> Pokud potřebujete spustit kód, použijte místo částečného zobrazení [komponentu zobrazení](xref:mvc/views/view-components) .

::: moniker range=">= aspnetcore-2.1"

Volání `Partial` nebo `RenderPartial` výsledky v upozornění analyzátoru sady Visual Studio. Například přítomnost `Partial` poskytne následující zprávu upozornění:

> Použití IHtmlHelper. Partial může způsobit zablokování aplikace. Zvažte použití &lt;pomocníka s částečnou&gt; značkou nebo IHtmlHelper. PartialAsync.

Nahraďte volání `@Html.Partial` pomocí `@await Html.PartialAsync` nebo [částečného pomocníka značek](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Další informace o migraci pomocníka částečné značky najdete v tématu [migrace z pomocníka jazyka HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Zjišťování částečného zobrazení

Pokud se na částečné zobrazení odkazuje pomocí názvu bez přípony souboru, prohledávají se v uvedeném pořadí následující umístění:

::: moniker range=">= aspnetcore-2.1"

**RazorStránky**

1. Aktuálně prováděná složka stránky
1. Adresář grafu nad složkou stránky
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

Následující konvence se vztahují na zjišťování částečného zobrazení:

* Různá částečná zobrazení se stejným názvem souboru jsou povolena, pokud jsou částečná zobrazení v různých složkách.
* Při odkazování na částečné zobrazení podle názvu bez přípony souboru a částečného zobrazení se nachází ve složce volajícího i ve *sdílené* složce, částečné zobrazení ve složce volajícího poskytuje částečné zobrazení. Pokud se částečné zobrazení nenachází ve složce volajícího, částečné zobrazení se poskytuje ze *sdílené* složky. Částečná zobrazení ve *sdílené* složce se nazývají *sdílená částečná zobrazení* nebo *výchozí částečná zobrazení*.
* Částečná zobrazení mohou být *zřetězena*&mdash;z částečného zobrazení, pokud cyklický odkaz není vytvořen voláními. Relativní cesty jsou vždycky relativní vzhledem k aktuálnímu souboru, ne ke kořenu nebo nadřazenému souboru.

> [!NOTE]
> [Razor](xref:mvc/views/razor) Definice v částečném zobrazení je neviditelná pro nadřazené soubory `section` značek. `section` Je viditelný pouze pro částečné zobrazení, ve kterém je definováno.

## <a name="access-data-from-partial-views"></a>Přístup k datům z částečných zobrazení

Když je vytvořena instance částečného zobrazení, obdrží *kopii* nadřazeného `ViewData` slovníku. Aktualizace dat v rámci částečného zobrazení nejsou trvale uložené v nadřazeném zobrazení. `ViewData`změny v částečném zobrazení jsou po vrácení částečného zobrazení ztraceny.

Následující příklad ukazuje, jak předat instanci [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) do částečného zobrazení:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Model můžete předat do částečného zobrazení. Modelem může být vlastní objekt. Můžete předat model s `PartialAsync` (vykreslí blok obsahu volajícímu) nebo `RenderPartialAsync` (streamuje obsah do výstupu):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**RazorStránky**

Následující značky v ukázkové aplikaci jsou ze stránky *stránky/ArticlesRP/ReadRP. cshtml* . Stránka obsahuje dvě částečná zobrazení. Druhé částečné zobrazení projde v modelu a `ViewData` částečném zobrazení. Přetížení `ViewDataDictionary` konstruktoru se používá k předání nového `ViewData` slovníku při zachování existujícího `ViewData` slovníku.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-20)]

*Pages/Shared/_AuthorPartialRP. cshtml* je první částečné zobrazení, na které odkazuje soubor s označením *ReadRP. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP. cshtml* je druhé částečné zobrazení, na které odkazuje soubor s označením *ReadRP. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

Následující kód v ukázkové aplikaci zobrazuje zobrazení */články/čtení. cshtml* . Zobrazení obsahuje dvě částečná zobrazení. Druhé částečné zobrazení projde v modelu a `ViewData` částečném zobrazení. Přetížení `ViewDataDictionary` konstruktoru se používá k předání nového `ViewData` slovníku při zachování existujícího `ViewData` slovníku.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-20)]

Views */Shared/_AuthorPartial. cshtml* je první částečné zobrazení, na které odkazuje soubor s označením *Read. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

Views */articles/_ArticleSection. cshtml* je druhé částečné zobrazení, na které odkazuje soubor s označením *Read. cshtml* :

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

V době běhu jsou fragmenty vykresleny do vykresleného výstupu nadřazeného souboru označení, který je vykreslen v rámci sdíleného *_Layout. cshtml*. První částečné zobrazení vykreslí jméno autora článku a datum publikování:

> Abraham Lincoln
>
> Toto částečné zobrazení ze &lt;sdílené cesty&gt;k souboru částečného zobrazení.
> 11/19/1863 12:00:00 DOP.

Druhé částečné zobrazení vykreslí oddíly článku:

> Jeden index oddílu: 0
>
> Před čtyřmi a sedmi lety...
>
> Oddíl dva rejstřík: 1
>
> Teď jsme se zabývají skvělým občanském válečným testováním...
>
> Oddíl tři rejstřík: 2
>
> Ale ve větším smyslu nemůžeme vyhradit...

## <a name="additional-resources"></a>Další zdroje

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
