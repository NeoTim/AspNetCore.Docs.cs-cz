---
title: Částečná zobrazení v ASP.NET Core
author: ardalis
description: Objevte, jak použít částečné zobrazení k rozdělení souborů velké značek a omezili dvojí společné značky na webových stránkách v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/partial
ms.openlocfilehash: 65da78d6df3f179df9bdfa3a32af8736b71bbac5
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468705"
---
# <a name="partial-views-in-aspnet-core"></a>Částečná zobrazení v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), a [Scott Sauber](https://twitter.com/scottsauber)

Částečné zobrazení je [Razor](xref:mvc/views/razor) souboru označení (*.cshtml*), který vykreslí výstupu protokolu HTML *v rámci* jiný soubor značek je vykreslen výstup.

::: moniker range=">= aspnetcore-2.1"

Termín *částečné zobrazení* se používá při vývoji buď aplikace MVC, ve kterém jsou volány soubory označení *zobrazení*, nebo aplikace stránky Razor, ve kterém jsou volány soubory označení *stránky*. Toto téma odkazuje obecně na zobrazení MVC a stránek Razor pages jako *soubory označení*.

::: moniker-end

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="when-to-use-partial-views"></a>Kdy použít částečné zobrazení

Částečná zobrazení jsou účinný způsob, jak:

* Rozdělte soubory velkých značek na menší součásti.

  Ve velkých složitých značek souborů skládá z několika logických částí, je výhodné pracovat se službou každého jednotlivého samostatný do částečné zobrazení. Kód v souboru označení je spravovat, protože kód obsahuje pouze celkovou strukturu stránky a odkazy na částečné zobrazení.
* Omezili dvojí společný kód obsah značky souborů.

  Při použití stejné prvky kódu v souborech značek částečné zobrazení odebere duplicitní značky obsah do jednoho souboru částečné zobrazení. Při změně kódu v částečné zobrazení aktualizuje vykresleného výstupu souborů značek, které používají částečné zobrazení.

Částečná zobrazení nesmí sloužit k udržování společné prvky rozložení. Společné prvky rozložení musí být zadán v [_Layout.cshtml](xref:mvc/views/layout) soubory.

Není použít částečné zobrazení, ve kterém jsou vyžadována k vykreslení kódu vykreslování komplexní logiku nebo spuštění kódu. Namísto částečné zobrazení, použijte [zobrazení komponenty](xref:mvc/views/view-components).

## <a name="declare-partial-views"></a>Deklarujte částečné zobrazení

::: moniker range=">= aspnetcore-2.0"

Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky (MVC) nebo *stránky* složky (stránky Razor).

V ASP.NET Core MVC, řadič <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení. Obdobné funkce plánujeme přidat v 2.2 technologie ASP.NET Core Razor Pages. V Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> můžete vrátit <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>. Odkazování na a vykreslení částečných zobrazení je popsána v [odkazovat na částečné zobrazení](#reference-a-partial-view) oddílu.

Na rozdíl od zobrazení MVC nebo vykreslení stránky částečné zobrazení nespustí *soubor _ViewStart.cshtml*. Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.

Názvy souborů částečné zobrazení často začínají podtržítkem (`_`). Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení a stránky částečné zobrazení.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Částečné zobrazení je *.cshtml* souboru označení udržována v rámci *zobrazení* složky.

Kontroleru <xref:Microsoft.AspNetCore.Mvc.ViewResult> je schopen vracet částečné zobrazení nebo zobrazení.

Na rozdíl od MVC vykreslení zobrazení, částečná zobrazení nespustí *soubor _ViewStart.cshtml*. Další informace o *soubor _ViewStart.cshtml*, naleznete v tématu <xref:mvc/views/layout>.

Názvy souborů částečné zobrazení často začínají podtržítkem (`_`). Tyto zásady vytváření názvů není povinné, ale pomáhá vizuálně odlišili od zobrazení částečné zobrazení.

::: moniker-end

## <a name="reference-a-partial-view"></a>Odkaz na částečné zobrazení

::: moniker range=">= aspnetcore-2.1"

V souboru kódu existuje několik způsobů, jak odkazovat na částečné zobrazení. Doporučujeme vám, že aplikace použijte jednu z následujících dvou přístupů asynchronní vykreslování:

* [Pomocná rutina částečné značky](#partial-tag-helper)
* [Asynchronous HTML Helper](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

V souboru kódu existují dva způsoby, jak odkazovat na částečné zobrazení:

* [Asynchronous HTML Helper](#asynchronous-html-helper)
* [Synchronní pomocné rutiny HTML](#synchronous-html-helper)

Doporučujeme používat aplikace [asynchronní pomocné rutiny HTML](#asynchronous-html-helper).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a>Pomocná rutina částečné značky

[Pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) vyžaduje ASP.NET Core 2.1 nebo novější.

Pomocná rutina částečné značky vykreslí obsah asynchronně a používá HTML syntaxe:

```cshtml
<partial name="_PartialName" />
```

Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny značky částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:

```cshtml
<partial name="_PartialName.cshtml" />
```

Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace. Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:

**Razor Pages**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

Další informace naleznete v tématu <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

::: moniker-end

### <a name="asynchronous-html-helper"></a>Asynchronous HTML Helper

Při použití pomocné rutiny HTML, nejlepším postupem je použití <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>. `PartialAsync` Vrátí <xref:Microsoft.AspNetCore.Html.IHtmlContent> typ zabalené v <xref:System.Threading.Tasks.Task%601>. Metoda odkazuje jsou očekávané volání s `@` znak:

```cshtml
@await Html.PartialAsync("_PartialName")
```

Pokud přípona souboru je k dispozici, odkazuje na pomocné rutiny HTML částečné zobrazení, který musí být ve stejné složce jako soubor značek volání částečného zobrazení:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

Následující příklad odkazuje částečné zobrazení z kořenového adresáře aplikace. Cesty, které začínají lomítkem tilda (`~/`) nebo lomítko (`/`) najdete v kořenovém adresáři aplikace:

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

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

Následující příklad odkazuje na částečné zobrazení pomocí relativní cesty:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Alternativně můžete vykreslení částečného zobrazení s <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>. Tato metoda nevrací <xref:Microsoft.AspNetCore.Html.IHtmlContent>. Jejich streamování vykresleného výstupu přímo do odpovědi. Protože metoda nevrací výsledek, musí být volána v rámci bloku kódu Razor:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

Protože `RenderPartialAsync` datové proudy vykreslený obsah, poskytuje lepší výkon v některých scénářích. V situacích, kritickém pro výkon srovnávací testy na stránce pomocí obou metod a použít přístup, který generuje rychlejší odezvu.

### <a name="synchronous-html-helper"></a>Synchronní pomocné rutiny HTML

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> a <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> jsou synchronní ekvivalenty `PartialAsync` a `RenderPartialAsync`v uvedeném pořadí. Synchronní ekvivalenty se nedoporučuje, protože existují scénáře, ve kterých zablokování. Synchronní metody jsou určené pro odstranění v budoucí verzi.

> [!IMPORTANT]
> Pokud potřebujete spustit kód, použijte [zobrazení komponenty](xref:mvc/views/view-components) místo částečné zobrazení.

::: moniker range=">= aspnetcore-2.1"

Volání `Partial` nebo `RenderPartial` výsledky v analyzátor upozornění aplikace Visual Studio. Například přítomnost `Partial` provede následující upozornění:

> Použití IHtmlHelper.Partial může způsobit zablokování aplikace. Zvažte použití &lt;částečné&gt; pomocné rutiny značky nebo IHtmlHelper.PartialAsync.

Nahraďte volání `@Html.Partial` s `@await Html.PartialAsync` nebo [pomocná rutina částečné značky](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Další informace o migraci pomocná rutina částečné značky najdete v tématu [migrace ze pomocné rutiny HTML](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).

::: moniker-end

## <a name="partial-view-discovery"></a>Částečné zobrazení zjišťování

Když částečné zobrazení se odkazuje jeho názvem bez přípony souboru, následující umístění budou prohledána v uvedené pořadí:

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

1. Právě probíhá na stránce složky
1. Directory graphu nahoře na stránce složky
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

Částečné zobrazení zjišťování platí následující zásady:

* Částečná zobrazení jinou se stejným názvem souboru jsou povoleny v případě, že částečná zobrazení jsou v různých složkách.
* Při odkazování na částečné zobrazení podle názvu bez přípony souboru a částečné zobrazení je k dispozici ve složce obou volajícího a *Shared* složky, částečné zobrazení ve složce volajícího, jež poskytuje částečného zobrazení. Pokud není k dispozici ve složce volajícího částečného zobrazení, částečného zobrazení je k dispozici z *Shared* složky. Částečné zobrazení v *Shared* složky se nazývají *sdílené částečná zobrazení* nebo *výchozí částečná zobrazení*.
* Částečná zobrazení mohou být *zřetězené*&mdash;částečné zobrazení můžete volat jiné částečné zobrazení, pokud cyklický odkaz není vytvořený ve volání. Relativní cesty jsou vždy relativní vzhledem k aktuální soubor nebyl pro kořenový server WSUS nebo nadřazený soubor.

> [!NOTE]
> A [Razor](xref:mvc/views/razor) `section` definované v částečné zobrazení je skryt nadřazené značky souborů. `section` Bude viditelná jenom pro částečné zobrazení, ve kterém je definována.

## <a name="access-data-from-partial-views"></a>Přístup k datům z částečné zobrazení

Při vytváření instance částečného zobrazení přijme *kopírování* z nadřazeného objektu `ViewData` slovníku. Aktualizace provedené v datech v rámci částečného zobrazení se ukládají do nadřazeného zobrazení. `ViewData` provedené změny v částečné zobrazení při vrátí částečného zobrazení.

Následující příklad ukazuje, jak předat instanci [objektu ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) částečné zobrazení:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

Model lze předat do částečné zobrazení. Vlastní objekt může být tento model. Můžete předat model s `PartialAsync` (vykreslí blok volajícímu) nebo `RenderPartialAsync` (streamování obsahu do výstupu):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

**Razor Pages**

Následující kód v ukázkové aplikaci je z *Pages/ArticlesRP/ReadRP.cshtml* stránky. Tato stránka obsahuje dvě částečné zobrazení. Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení. `ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-19)]

*Pages/Shared/_AuthorPartialRP.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

*Pages/ArticlesRP/_ArticleSectionRP.cshtml* je druhý částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

**MVC**

::: moniker-end

Následující kód v ukazuje ukázkové aplikace *Views/Articles/Read.cshtml* zobrazení. Zobrazení obsahuje dvě částečné zobrazení. Druhý částečné zobrazení předává do modelu a `ViewData` částečného zobrazení. `ViewDataDictionary` Přetížení konstruktoru slouží k předání nového `ViewData` slovníku při zachování stávajících `ViewData` slovníku.

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-19)]

*Views/Shared/_AuthorPartial.cshtml* je první částečné zobrazení odkazuje *ReadRP.cshtml* souboru označení:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

*Views/Articles/_ArticleSection.cshtml* je druhý částečné zobrazení odkazuje *Read.cshtml* souboru označení:

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

Za běhu, jsou vykreslovány částečné do souboru kódu nadřazené vykresleného výstupu, který je vykreslen v rámci sdíleného *_Layout.cshtml*. První částečné zobrazení vykreslí autoři článku název a datum publikování:

> Abraham Lincoln
>
> Toto částečné zobrazení z &lt;cesta k souboru sdílené částečné zobrazení&gt;.
> 19/11/1863 12:00:00 AM

Druhý částečné zobrazení vykreslí části článku:

> Jeden Index části: 0
>
> Čtyři skóre a sedm let před...
>
> Index oddílu dvě: 1
>
> Nyní jsme se zabývají skvělé občanské války, testování...
>
> Index oddílu tři: 2
>
> Ale ve větší smysl, jsme nelze vyhradit...

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
