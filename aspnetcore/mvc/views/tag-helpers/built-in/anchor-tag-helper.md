---
title: Ukotvení pomocné rutiny značky v ASP.NET Core
author: pkellner
description: Objevte atributy ASP.NET Core ukotvení pomocné rutiny značky a roli, kterou každý atribut hraje v rozšíření chování značky jazyka HTML anchor.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/18/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 9fd41b049104e99f1701354c40a05e5ee30e9c2f
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814995"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>Ukotvení pomocné rutiny značky v ASP.NET Core

Podle [Peter Kellner](http://peterkellner.net) a [Scott Addie](https://github.com/scottaddie)

[Ukotvení pomocné rutiny značky](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) zvyšuje standard HTML anchor (`<a ... ></a>`) tak, že přidáte nové atributy značky. Podle konvence mají předponu názvů atributů `asp-`. Prvek vykreslené ukotvení `href` hodnota atributu je určen podle hodnot `asp-` atributy.

Přehled pomocných rutin značek, naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([stažení](xref:index#how-to-download-a-sample))

*SpeakerController* se používá v ukázky v tomto dokumentu:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>Ukotvení pomocné rutiny značky atributy

### <a name="asp-controller"></a>kontroler ASP

[Asp kontroleru](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) atribut přiřadí sloužit ke generování adresy URL kontroleru. Následující kód uvádí všechny přednášející:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

Generovaný kód HTML:

```html
<a href="/Speaker">All Speakers</a>
```

Pokud `asp-controller` je zadán atribut a `asp-action` není výchozí `asp-action` hodnotu akce kontroleru, který je přidružený k aktuálně prováděné zobrazení. Pokud `asp-action` je vynecháno z předchozí kód a ukotvení pomocné rutiny značky se používá v *HomeController*společnosti *Index* zobrazení ( */Home*), je generovaný kód HTML:

```html
<a href="/Home">All Speakers</a>
```

## <a name="asp-action"></a>Akce ASP

[Asp akce](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) hodnota atributu představuje název akce kontroleru součástí generované `href` atribut. Následující kód nastaví generované `href` hodnotu atributu na stránce hodnocení mluvčího:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud ne `asp-controller` atribut zadán, je použit výchozí řadič volání zobrazení provádění aktuálního zobrazení.

Pokud `asp-action` hodnota atributu je `Index`, pak žádná akce je připojena k adrese URL, což vede k vyvolání výchozí `Index` akce. Akce zadané (nebo nastavit na výchozí hodnotu), musí existovat v kontroleru odkazuje `asp-controller`.

### <a name="asp-route-value"></a>ASP - route-{value}

[Asp - route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) atribut umožňuje předponu trasy zástupný znak. Všechny hodnoty zabírá `{value}` zástupný symbol je interpretován jako potenciální parametr trasa. Pokud výchozí trasa není nalezen, tuto předponu trasy, se připojí k generované `href` atribut jako parametr žádosti a hodnotu. V opačném případě je nahrazena v šabloně trasy.

Vezměte v úvahu následující akce kontroleru:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

Pomocí výchozí šablony trasy definované v *Startup.Configure*:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

Zobrazení MVC používá model poskytované akci, následujícím způsobem:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

Výchozí trasa `{id?}` odpovídal zástupný symbol. Generovaný kód HTML:

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

Předpokládejme, že předponu trasy, které nejsou součástí odpovídající směrování šablony, stejně jako u následující zobrazení MVC:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

Následující kód HTML je generována, protože `speakerid` nebyl nalezen v odpovídající trasy:

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

Pokud `asp-controller` nebo `asp-action` nejsou zadané, pak stejnou výchozí zpracování je a potom je `asp-route` atribut.

### <a name="asp-route"></a>ASP trasy

[Trasy asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) atribut se používá pro vytvoření adresy URL propojení přímo s pojmenovanou trasu. Pomocí [směrování atributů](xref:mvc/controllers/routing#attribute-routing), může mít název trasy, jak je znázorněno `SpeakerController` a používat v jeho `Evaluations` akce:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

V následujícím kódu `asp-route` atribut odkazuje na pojmenovanou trasu:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

Ukotvení pomocné rutiny značky vytváří trasu přímo k této akci kontroleru pomocí adresy URL */mluvčího/hodnocení*. Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud `asp-controller` nebo `asp-action` určena kromě `asp-route`, postupu generované nemusí být co očekáváte. Aby se zabránilo konfliktu trasy `asp-route` by neměl být použit s `asp-controller` a `asp-action` atributy.

## <a name="asp-all-route-data"></a>asp-all-route-data

[Asp všechny trasy dat](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) atribut podporuje vytváření slovník párů klíč hodnota. Klíč je název parametru a hodnota je hodnota tohoto parametru.

V následujícím příkladu je slovník inicializován a předána do zobrazení Razor. Alternativně může předávat data pomocí modelu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

Předcházející kód vygeneruje následující kód HTML:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data` Slovníku se sloučí k vytvoření dotazu splnění požadavků přetížené `Evaluations` akce:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

Pokud se ve slovníku všechny klíče shodují parametry trasy, tyto hodnoty jsou nahrazeny v postupu podle potřeby. Neodpovídající hodnoty jsou generovány jako parametry požadavku.

### <a name="asp-fragment"></a>ASP fragment

[Asp fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) atribut definuje fragment adresy URL pro připojení k adrese URL. Ukotvení pomocné rutiny značky přidá znak hash (#). Vezměte v úvahu následující kód:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

Hodnota hash značky jsou užitečné při vytváření aplikací na straně klienta. Jejich lze snadno označení a vyhledávání v jazyce JavaScript, třeba.

### <a name="asp-area"></a>asp-area

[Asp oblasti](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) atribut nastaví název oblasti, která slouží k nastavení odpovídající trasy. Následující příklady znázornění jak `asp-area` atribut způsobí, že přemapování trasy.

### <a name="usage-in-razor-pages"></a>Použití stránek Razor

Oblasti stránek Razor se podporují v ASP.NET Core 2.1 nebo novější.

Vezměte v úvahu následující hierarchii adresářů:

* **{Název projektu}**
  * **wwwroot**
  * **Oblasti**
    * **Relace**
      * **Stránky**
        * *\_ViewStart.cshtml*
        * *Index.cshtml*
        * *Index.cshtml.cs*
  * **Stránky**

Kód tak, aby odkazovaly *relace* oblasti *Index* je stránka Razor:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

Generovaný kód HTML:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Pro podporu oblasti v aplikaci pro stránky Razor, proveďte jednu z následujících postupů v `Startup.ConfigureServices`:
>
> * Nastavte [verze kompatibility](xref:mvc/compatibility-version) 2.1 nebo novější.
> * Nastavte [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) vlastnost `true`:
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

### <a name="usage-in-mvc"></a>Použití v aplikaci MVC

Vezměte v úvahu následující hierarchii adresářů:

* **{Název projektu}**
  * **wwwroot**
  * **Oblasti**
    * **Blogy**
      * **Kontrolery**
        * *HomeController.cs*
      * **Zobrazení**
        * **Domovská stránka**
          * *AboutBlog.cshtml*
          * *Index.cshtml*
        * *\_ViewStart.cshtml*
  * **Kontrolery**

Nastavení `asp-area` "Blogy" předpon adresáři *oblasti/blogy* trasy z přidružených kontrolerů a zobrazení pro tuto značku ukotvení. Kód tak, aby odkazovaly *AboutBlog* zobrazení:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

Generovaný kód HTML:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> Pro podporu oblasti v aplikaci MVC, šablona trasy musí obsahovat odkaz na oblast, pokud existuje. Tato šablona je reprezentována druhý parametr `routes.MapRoute` volání metody *Startup.Configure*:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>protokol ASP

[Protokolu asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) atribut slouží k určení protokol (například `https`) v adrese URL. Příklad:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

Generovaný kód HTML:

```html
<a href="https://localhost/Home/About">About</a>
```

Název hostitele v tomto příkladu je localhost. Ukotvení pomocné rutiny značky používá veřejnou doménu na webu při generování adresy URL.

### <a name="asp-host"></a>asp-host

[Asp hostitele](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) atribut slouží k určení názvu hostitele v adrese URL. Příklad:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

Generovaný kód HTML:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>stránka ASP

[Stránka asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) atribut se používá se stránkami Razor. Můžete nastavit značku ukotvení `href` hodnotu atributu na určitou stránku. Předpony názvu stránky s lomítkem ("/") se vytvoří adresa URL.

Následující příklad ukazuje na účastník stránky Razor:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

Generovaný kód HTML:

```html
<a href="/Attendee">All Attendees</a>
```

`asp-page` Atribut je vzájemně se vylučuje s `asp-route`, `asp-controller`, a `asp-action` atributy. Ale `asp-page` jde použít s `asp-route-{value}` řídit směrování, jak ukazuje následující kód:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>rutina stránky ASP

[Rutina stránky asp](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) atribut se používá se stránkami Razor. Je určena pro odkazování na určitou stránku obslužné rutiny.

Vezměte v úvahu následující rutiny stránky:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

Model stránky přidruženého k tomuto kódu odkazy na `OnGetProfile` obslužná rutina stránky. Poznámka: `On<Verb>` předpona názvu metody obslužné rutiny stránky je vynecháno v `asp-page-handler` hodnotu atributu. Pokud je metoda asynchronní, `Async` přípona je tento parametr vynechán, příliš.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
