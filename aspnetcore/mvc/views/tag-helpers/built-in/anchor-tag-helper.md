---
title: Pomocný objekt příznaku kotvy v ASP.NET Core
author: pkellner
description: Seznamte se s atributy pomocníka značek ukotvení ASP.NET Core a role, kterou každý atribut hraje při rozšiřování chování značky ukotvení HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 25f1716b5c80492edb72bc9555954c9c93c02a49
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018763"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>Pomocný objekt příznaku kotvy v ASP.NET Core

Od [Petra Kellner](https://peterkellner.net) a [Scott Addie](https://github.com/scottaddie)

[Pomocník značek kotvy](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) vylepšuje standardní značku kotvy HTML ( `<a ... ></a>` ) přidáním nových atributů. Podle konvence jsou názvy atributů předponou `asp-` . Hodnota atributu vykresleného ukotvení elementu `href` je určena hodnotami `asp-` atributů.

Přehled pomocníků značek naleznete v tématu <xref:mvc/views/tag-helpers/intro> .

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

Pokud `asp-controller` je atribut zadán a `asp-action` není, výchozí `asp-action` hodnota je akce kontroleru přidružená k aktuálně zpracovávanému zobrazení. Pokud `asp-action` je tento parametr vynechán v předchozím kódu a pomocník značek Anchor je použit v zobrazení indexu *HomeController*( *Index* */Home*), vygenerovaný kód HTML je:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>ASP – akce

Hodnota atributu [ASP-Action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) představuje název akce kontroleru, který je součástí generovaného `href` atributu. Následující kód nastaví vygenerovanou `href` hodnotu atributu na stránku hodnocení mluvčího:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud `asp-controller` není zadán žádný atribut, je použit výchozí řadič volající zobrazení, které spouští aktuální zobrazení.

Pokud `asp-action` je hodnota atributu `Index` , pak se k adrese URL nepřipojí žádná akce, která by vedla k vyvolání výchozí `Index` akce. Zadaná akce (nebo výchozí) musí existovat v řadiči, na který odkazuje `asp-controller` .

### <a name="asp-route-value"></a>ASP-Route-{Value}

Atribut [ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) povoluje předponu trasy se zástupnými znaky. Jakákoli hodnota, která je v `{value}` zástupném symbolu, se interpretuje jako potenciální parametr trasy. Pokud se výchozí trasa nenajde, připojí se tato předpona trasy k vygenerovanému `href` atributu jako parametr požadavku a hodnota. V opačném případě je nahrazena šablonou směrování.

Vezměte v úvahu následující akci kontroleru:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

S výchozí šablonou směrování definovanou v *Startup.Configurovat*:

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

`{id?}`Byl spárován zástupný symbol výchozí trasy. Generovaný kód HTML:

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

Následující kód HTML je vygenerován, protože nebyl `speakerid` nalezen v rámci odpovídajícího postupu:

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

Pokud není `asp-controller` `asp-action` zadán parametr nebo, pak stejné výchozí zpracování je následováno v `asp-route` atributu.

### <a name="asp-route"></a>ASP – trasa

Atribut [ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) se používá k vytvoření adresy URL odkazující přímo na pojmenovanou trasu. Pomocí [atributů směrování](xref:mvc/controllers/routing#attribute-routing)může být trasa pojmenována tak, jak je znázorněna v `SpeakerController` a použitá ve své `Evaluations` akci:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

V následujícím kódu `asp-route` odkazuje atribut na pojmenovanou trasu:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

Pomocník značek ukotvení generuje trasu přímo k této akci kontroleru pomocí adresy URL */Speaker/evaluations*. Generovaný kód HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Pokud `asp-controller` `asp-action` je zadána kromě `asp-route` , vygenerovaná trasa nemusí být to, co očekáváte. Chcete-li se vyhnout konfliktu trasy, `asp-route` neměli byste použít `asp-controller` s `asp-action` atributy a.

### <a name="asp-all-route-data"></a>ASP-All-Route-data

Atribut [ASP-All-Route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) podporuje vytvoření slovníku párů klíč-hodnota. Klíč je název parametru a hodnota je hodnota parametru.

V následujícím příkladu je slovník inicializován a předán do Razor zobrazení. Případně můžete data předávat pomocí modelu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

Předchozí kód generuje následující kód HTML:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data`Slovník je sloučený tak, aby vytvořil QueryString splnění požadavků na přetíženou `Evaluations` akci:

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

Atribut [Area ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) nastavuje název oblasti použitý k nastavení příslušné trasy. V následujících příkladech je znázorněno, jak `asp-area` atribut způsobuje přemapování tras.

#### <a name="usage-in-no-locrazor-pages"></a>Použití na Razor stránkách

RazorOblasti stránek jsou podporovány v ASP.NET Core 2,1 nebo novějším.

Vezměte v úvahu následující hierarchii adresářů:

* **{Název projektu}**
  * **wwwroot**
  * **Oblasti**
    * **Relace**
      * **Stránky**
        * *\_ViewStart. cshtml*
        * *Soubor Index.cshtml*
        * *Index.cshtml.cs*
  * **Stránky**

Značka, na kterou se odkazuje na stránku *indexu* oblasti *relací* Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

Generovaný kód HTML:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Chcete-li podporovat oblasti v Razor aplikaci Pages, proveďte jednu z následujících `Startup.ConfigureServices` akcí:
>
> * Nastavte [verzi kompatibility](xref:mvc/compatibility-version) na 2,1 nebo novější.
> * Vlastnost [ Razor PagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) nastavte na `true` :
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
        * **Domů**
          * *AboutBlog. cshtml*
          * *Soubor Index.cshtml*
        * *\_ViewStart. cshtml*
  * **Kontrolery**

Nastavení `asp-area` na "Blogy" přidělí oblasti adresáře */Blogy* na trasy přidružených řadičů a zobrazení pro tuto značku ukotvení. Značka pro odkaz na zobrazení *AboutBlog* je:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

Generovaný kód HTML:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> Aby bylo možné podporovat oblasti v aplikaci MVC, šablona trasy musí zahrnovat odkaz na oblast, pokud existuje. Tato šablona je reprezentovaná druhým parametrem `routes.MapRoute` volání metody v *Startup.Configurovat*:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>protokol ASP

Atribut [ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) je určen pro určení protokolu (například `https` ) v adrese URL. Například:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

Generovaný kód HTML:

```html
<a href="https://localhost/Home/About">About</a>
```

Název hostitele v příkladu je localhost. Pomocník značek ukotvení používá při generování adresy URL veřejnou doménu webu.

### <a name="asp-host"></a>ASP – hostitel

Atribut [Host ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) slouží k zadání názvu hostitele v adrese URL. Například:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

Generovaný kód HTML:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>ASP – stránka

Atribut [stránky ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) se používá se Razor stránkami. Použijte ho k nastavení hodnoty atributu kotvicí značky `href` na konkrétní stránku. Předpona názvu stránky s lomítkem ("/") vytvoří adresu URL.

Následující ukázka odkazuje na stránku účastníka Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

Generovaný kód HTML:

```html
<a href="/Attendee">All Attendees</a>
```

`asp-page`Atribut se vzájemně vylučuje pomocí `asp-route` atributů, a `asp-controller` `asp-action` . Lze však `asp-page` použít s `asp-route-{value}` pro řízení směrování, jak ukazuje následující kód:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>ASP – obslužná rutina stránky

Atribut [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) se používá se Razor stránkami. Je určený pro propojení s konkrétními obslužnými rutinami stránky.

Vezměte v úvahu následující obslužnou rutinu stránky:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

Přidružená označení odkazujícího modelu stránky k `OnGetProfile` obslužné rutině stránky Poznámka: `On<Verb>` Předpona názvu metody obslužné rutiny stránky je vynechána v `asp-page-handler` hodnotě atributu. Je-li metoda asynchronní, `Async` je přípona vynechána.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

Generovaný kód HTML:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Další materiály

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
