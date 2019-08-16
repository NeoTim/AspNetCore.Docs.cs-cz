---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 08/16/2019
uid: mvc/controllers/areas
ms.openlocfilehash: d0af3092776ee09469c879fffd3047c50b1a59b4
ms.sourcegitcommit: 4cb0c7e74355f2e87c60e2a196f842b937247a99
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69545804"
---
# <a name="areas-in-aspnet-core"></a>Oblasti v ASP.NET Core

Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení). Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího `area`parametru trasy,, do `controller` a `action` nebo na stránku `page`Razor.

Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní sadu Razor Pages, řadičů, zobrazení a modelů. Oblast je ve skutečnosti strukturou uvnitř aplikace. Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách. Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně. Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání. Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče, Razor Pages a modely.

Zvažte použití oblastí v projektu, když:

* Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.
* Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([stažení](xref:index#how-to-download-a-sample)). Ukázka stažení poskytuje základní aplikaci pro oblasti testování.

Pokud používáte Razor Pages, přečtěte si téma [oblasti s Razor Pages](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro řadiče se zobrazeními

Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:

* [Struktura složky oblasti](#area-folder-structure).
* Řadiče upravené pomocí [ &lbrack;atributu Area&rbrack; ](#attribute) k přidružení kontroleru k oblasti:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa oblasti přidaná ke spuštění](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složky oblasti

Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*. Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:

* Název projektu
  * Oblasti
    * Produktech
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Zobrazení
        * Domů
          * Index.cshtml
        * Správy
          * Index.cshtml
          * O. cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Index.cshtml

I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů. Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

Umístění nezobrazených složek, jako jsou *řadiče* a *modely* , **nezáleží**. Například složka *řadiče* a *modely* není vyžadována. Obsah *řadičů* a *modelů* je kód, který je zkompilován do knihovny DLL. Obsah *zobrazení* není zkompilován, dokud není proveden požadavek na toto zobrazení.

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Přidružte kontroler k oblasti.

Řadiče oblastí jsou označeny [ &lbrack;atributem Area&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat trasu oblasti

Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů. Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.

`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti. Použití `{area:...}` je nejjednodušším mechanismem přidávání směrování do oblastí.

Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Při použití `MapAreaRoute` s ASP.NET Core 2,2 se podívejte na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/7772).

Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generování propojení s oblastmi MVC

Následující kód v ukázkovém souboru [ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.

Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti. Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.

Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.

Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Sdílené rozložení pro oblasti s použitím souboru _ViewStart. cshtml

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

Ve svém standardním umístění se */views/_ViewImports.cshtml* nevztahuje na oblasti. Chcete-li [](xref:mvc/views/tag-helpers/intro)použít běžné pomocníky `@using`značek, `@inject` nebo ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal správný soubor *_ViewImports. cshtml* . Pokud chcete stejné chování ve všech zobrazeních, přesuňte */views/_ViewImports.cshtml* do kořenového adresáře aplikace.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí složky oblasti, kde jsou uložená zobrazení

Následující kód změní výchozí složku oblasti z `"Areas"` na: `"MyAreas"`

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti s Razor Pages

Oblasti s Razor Pages v kořenu aplikace vyžadují *<area name>oblast nebo složku/Pages* . Pro [ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:

* Název projektu
  * Oblasti
    * Produktech
      * Stránky
        * _ViewImports
        * O produktu
        * Index
    * Služby
      * Stránky
        * Správy
          * O produktu
          * Index

### <a name="link-generation-with-razor-pages-and-areas"></a>Vytváření odkazů pomocí Razor Pages a oblastí

Následující kód v ukázkovém souboru [ke stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.

Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti. Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.

Když není oblast zadaná, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy. Zvažte například odkazy vygenerované z následujícího kódu:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku `Services` v oblasti. Například `/Services/Manage/` `/Services/Manage/About`, ,nebo.`/Services/Manage/Index`
* Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku `/Home`v.
* Kód pochází z ukázkového [stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importovat obory názvů a pomocníka značek pomocí souboru _ViewImports

Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou stránku Razor ve složce.

Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* . Následující kód ukazuje stránku */Services/Manage/about* Razor:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozím kódu:

* K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.
* [Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

V ukázkovém stažení oblast produkty obsahuje následující soubor *_ViewImports. cshtml* :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující kód ukazuje stránku */Products/about* Razor:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru areas */Products/Pages/_ViewImports. cshtml* .

Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro Razor Pages oblasti

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.

### <a name="publishing-areas"></a>Oblasti publikování

Všechny soubory *. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, `<Project Sdk="Microsoft.NET.Sdk.Web">` Pokud je součástí souboru *. csproj.
