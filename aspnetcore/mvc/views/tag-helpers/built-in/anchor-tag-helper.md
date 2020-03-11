---
title: Pomocný objekt příznaku kotvy v ASP.NET Core
author: pkellner
description: Seznamte se s atributy pomocníka značek ukotvení ASP.NET Core a role, kterou každý atribut hraje při rozšiřování chování značky ukotvení HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 6bfbad39115c7823b5677d3c52ca64cfb0683037
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664001"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>Pomocný objekt příznaku kotvy v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net) a [Scott Addie](https://github.com/scottaddie)

[Pomocník značek kotvy](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) vylepšuje standardní značku HTML kotvy (`<a ... ></a>`) přidáním nových atributů. Podle konvence jsou názvy atributů předponou `asp-`. Hodnota atributu `href` prvku vykresleného ukotvení je určena hodnotami atributů `asp-`.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

*SpeakerController* se používá v ukázkách v tomto dokumentu:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>Pomocné atributy značek ukotvení

### <a name="asp-controller"></a>ASP – kontroler

Atribut [kontroleru ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) přiřadí kontroler použitý k VYGENEROVÁNÍ adresy URL. Následující kód uvádí všechny reproduktory:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

Generovaný kód HTML:

```html
<a href="/Speaker">All Speakers</a>
```

Pokud je zadán atribut `asp-controller` a `asp-action` není, výchozí hodnota `asp-action` je akce kontroleru přidružená k aktuálně zpracovávanému zobrazení. Pokud je `asp-action` vynechána v předchozím kódu a pomocník značek Anchor je použit v zobrazení *indexu* ( */Home*) *HomeController*, vygenerovaný kód HTML je:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>ASP – akce

Hodnota atributu [ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) reprezentuje název akce kontroleru obsažený v atributu vygenerovaného `href`. Následující kód nastaví vygenerovanou hodnotu atributu `href` na stránku hodnocení mluvčího:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud není zadán žádný atribut `asp-controller`, je použit výchozí řadič volající zobrazení, které spouští aktuální zobrazení.

Pokud je hodnota atributu `asp-action` `Index`, není k adrese URL připojena žádná akce, což vede k vyvolání výchozí `Index` akci. Zadaná akce (nebo výchozí) musí existovat v řadiči, na který odkazuje `asp-controller`.

### <a name="asp-route-value"></a>ASP-Route-{Value}

Atribut [ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) povoluje předponu trasy se zástupnými znaky. Jakákoli hodnota, která je zabírat `{value}` zástupný symbol, je interpretována jako potenciální parametr trasy. Pokud se výchozí trasa nenajde, připojí se tato předpona trasy k vygenerovanému `href` atributu jako parametr požadavku a hodnota. V opačném případě je nahrazena šablonou směrování.

Vezměte v úvahu následující akci kontroleru:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

S výchozí šablonou směrování definovanou při *spuštění. Nakonfigurujte*:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

Zobrazení MVC používá model, který je poskytován akcí, následovně:

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

Byl spárován odpovídající zástupný symbol `{id?}` výchozí trasy. Generovaný kód HTML:

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

Předpokládat, že předpona trasy není součástí vyhovující šablony směrování, jako u následujícího zobrazení MVC:

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

Následující kód HTML je vygenerován, protože ve shodě trasy nebyl nalezen `speakerid`:

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

Pokud nejsou zadány `asp-controller` nebo `asp-action`, bude stejné výchozí zpracování následováno v atributu `asp-route`.

### <a name="asp-route"></a>ASP – trasa

Atribut [ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) se používá k vytvoření adresy URL odkazující přímo na pojmenovanou trasu. Pomocí [atributů směrování](xref:mvc/controllers/routing#attribute-routing)může být trasa pojmenována, jak je znázorněno v `SpeakerController` a která se používá ve své `Evaluations` akci:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

V následujícím kódu atribut `asp-route` odkazuje na pojmenovanou trasu:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

Pomocník značek ukotvení generuje trasu přímo k této akci kontroleru pomocí adresy URL */Speaker/evaluations*. Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud je kromě `asp-route`zadaná `asp-controller` nebo `asp-action`, vygenerovaná trasa nemusí být to, co očekáváte. Chcete-li se vyhnout konfliktu trasy, `asp-route` by neměl být použit s atributy `asp-controller` a `asp-action`.

### <a name="asp-all-route-data"></a>asp-all-route-data

Atribut [ASP-All-Route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) podporuje vytvoření slovníku párů klíč-hodnota. Klíč je název parametru a hodnota je hodnota parametru.

V následujícím příkladu je slovník inicializován a předán zobrazení Razor. Případně můžete data předávat pomocí modelu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

Předchozí kód generuje následující kód HTML:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data` slovník je sloučený tak, aby vytvořil QueryString splnění požadavků na přetíženou akci `Evaluations`:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

Pokud všechny klíče ve slovníku odpovídají parametrům směrování, tyto hodnoty se podle potřeby nahradí v trase. Jiné neshodné hodnoty jsou vygenerovány jako parametry žádosti.

### <a name="asp-fragment"></a>ASP – fragment

Atribut [fragment ASP –](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) definuje fragment adresy URL pro připojení k adrese URL. Pomocník značek ukotvení přidá znak hash (#). Vezměte v úvahu následující značky:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

Značky hash jsou užitečné při sestavování aplikací na straně klienta. Je možné je použít k snadnému označení a hledání v JavaScriptu, například.

### <a name="asp-area"></a>ASP – oblast

Atribut [Area ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) nastavuje název oblasti použitý k nastavení příslušné trasy. V následujících příkladech je znázorněno, jak atribut `asp-area` způsobuje přemapování tras.

#### <a name="usage-in-razor-pages"></a>Využití v Razor Pages

Razor Pages oblasti jsou podporovány v ASP.NET Core 2,1 nebo novějším.

Vezměte v úvahu následující hierarchii adresářů:

* **{Název projektu}**
  * **wwwroot**
  * **Oblasti**
    * **Rušování**
      * **Stránky**
        * *\_ViewStart. cshtml*
        * *Index. cshtml*
        * *Index.cshtml.cs*
  * **Stránky**

Značka, na kterou se odkazuje na stránku *indexu* oblasti *relací* , je:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

Generovaný kód HTML:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Chcete-li podporovat oblasti aplikace Razor Pages, proveďte v `Startup.ConfigureServices`jednu z následujících akcí:
>
> * Nastavte [verzi kompatibility](xref:mvc/compatibility-version) na 2,1 nebo novější.
> * Nastavte vlastnost [RazorPagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) na `true`:
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a>Využití v MVC

Vezměte v úvahu následující hierarchii adresářů:

* **{Název projektu}**
  * **wwwroot**
  * **Oblasti**
    * **Blogy**
      * **Kontrolery**
        * *HomeController.cs*
      * **Zobrazení**
        * **Domovské**
          * *AboutBlog. cshtml*
          * *Index. cshtml*
        * *\_ViewStart. cshtml*
  * **Kontrolery**

Nastavení `asp-area` na "Blogy" přidělí oblasti adresáře */Blogy* na trasy přidružených řadičů a zobrazení pro tuto značku ukotvení. Značka pro odkaz na zobrazení *AboutBlog* je:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

Generovaný kód HTML:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> Aby bylo možné podporovat oblasti v aplikaci MVC, šablona trasy musí zahrnovat odkaz na oblast, pokud existuje. Tato šablona je reprezentovaná druhým parametrem volání metody `routes.MapRoute` v *Startup. Configure*:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>protokol ASP

Atribut [ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) je určen pro určení protokolu (například `https`) ve vaší adrese URL. Příklad:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

Generovaný kód HTML:

```html
<a href="https://localhost/Home/About">About</a>
```

Název hostitele v příkladu je localhost. Pomocník značek ukotvení používá při generování adresy URL veřejnou doménu webu.

### <a name="asp-host"></a>ASP – hostitel

Atribut [Host ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) slouží k zadání názvu hostitele v adrese URL. Příklad:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

Generovaný kód HTML:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>ASP – stránka

Atribut [stránky ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) se používá s Razor Pages. Použijte ho k nastavení hodnoty atributu `href` značky kotvy na konkrétní stránku. Předpona názvu stránky s lomítkem ("/") vytvoří adresu URL.

Následující ukázka odkazuje na stránku Razor pro daného účastníka:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

Generovaný kód HTML:

```html
<a href="/Attendee">All Attendees</a>
```

Atribut `asp-page` se vzájemně vylučuje pomocí atributů `asp-route`, `asp-controller`a `asp-action`. `asp-page` však lze použít s `asp-route-{value}` k řízení směrování, jak ukazuje následující kód:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>ASP – obslužná rutina stránky

Atribut [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) se používá s Razor Pages. Je určený pro propojení s konkrétními obslužnými rutinami stránky.

Vezměte v úvahu následující obslužnou rutinu stránky:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

Přidružená označení značek modelu stránky k obslužné rutině stránky `OnGetProfile`. Všimněte si, že předpona `On<Verb>` názvu metody obslužné rutiny stránky je v hodnotě atributu `asp-page-handler` vynechána. Je-li metoda asynchronní, je `Async` přípona vynechána.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
