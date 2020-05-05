---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se jedná o funkci ASP.NET MVC, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení).
ms.author: riande
ms.date: 03/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/areas
ms.openlocfilehash: 00071b91f2ef374668b74a41190d49b0be83e817
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774298"
---
# <a name="areas-in-aspnet-core"></a>Oblasti v ASP.NET Core

Od [Dhananjay Kumarem](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Oblasti jsou funkce ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného:

* Obor názvů pro směrování
* Struktura složek pro zobrazení a Razor stránky

Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího `area`parametru trasy,, do `controller` a `action` nebo do Razor stránky. `page`

Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní Razor sadu stránek, řadičů, zobrazení a modelů. Oblast je ve skutečnosti strukturou uvnitř aplikace. Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách. Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně. Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání. Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče Razor , stránky a modely.

Zvažte použití oblastí v projektu, když:

* Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.
* Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)). Ukázka stažení poskytuje základní aplikaci pro oblasti testování.

Pokud používáte Razor stránky, přečtěte si téma [oblasti Razor se stránkami](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro řadiče se zobrazeními

Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:

* [Struktura složky oblasti](#area-folder-structure).
* Řadiče s [`[Area]`](#attribute) atributem k přidružení kontroleru k oblasti:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa oblasti přidaná ke spuštění](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složky oblasti

Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*. Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:

* Název projektu
  * Oblasti
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml
        * Spravovat
          * Soubor Index.cshtml
          * O. cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml

I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů. Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Přidružte kontroler k oblasti.

Řadiče oblastí jsou označeny atributem [ &lbrack;Area&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat trasu oblasti

Trasy oblastí obvykle používají [konvenční směrování](xref:mvc/controllers/routing#cr) namísto [Směrování atributů](xref:mvc/controllers/routing#ar). Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.

`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti. Použití `{area:...}` s `MapControllerRoute`:

* Je nejjednodušším mechanismem přidávání směrování do oblastí.
* Vyhledá všechny řadiče s `[Area("Area name")]` atributem.

Následující kód používá <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> k vytvoření dvou tras pojmenované oblasti:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generování propojení s oblastmi MVC

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) zobrazuje generaci odkazů s určenou oblastí:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Ukázka ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) , které obsahuje:

* Předchozí odkazy.
* Odkazy podobné předchozímu, s `area` výjimkou nejsou zadány.

Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.

Pokud oblast nebo kontroler neurčíte, směrování závisí na [okolních](xref:mvc/controllers/routing#ambient) hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávná propojení se značkou, která neurčuje oblast.

Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml

Chcete-li sdílet společné rozložení pro celou aplikaci, ponechte *_ViewStart. cshtml* v [kořenové složce aplikace](#arf). Další informace najdete v tématu <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Kořenová složka aplikace

Kořenová složka aplikace je složka obsahující *Startup.cs* ve webové aplikaci vytvořené pomocí šablon ASP.NET Core.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

 */Views/_ViewImports. cshtml*, pro MVC a */Pages/_ViewImports. cshtml* pro Razor stránky, se neimportují do zobrazení v oblastech. Pomocí jednoho z následujících přístupů poskytněte importy zobrazení do všech zobrazení:

* Přidejte *_ViewImports. cshtml* do [kořenové složky aplikace](#arf). *_ViewImports. cshtml* v kořenové složce aplikace se bude vztahovat na všechna zobrazení v aplikaci.
* Zkopírujte soubor *_ViewImports. cshtml* do příslušné složky zobrazení v oblasti.

Soubor *_ViewImports. cshtml* obvykle obsahuje [pomocníky](xref:mvc/views/tag-helpers/intro) s příkazy Imports `@using`, a `@inject` . Další informace najdete v tématu [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí složky oblasti, kde jsou uložená zobrazení

Následující kód změní výchozí složku oblasti z `"Areas"` na: `"MyAreas"`

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti se Razor stránkami

Oblasti se Razor stránkami vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace. Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)se používá tato struktura složek:

* Název projektu
  * Oblasti
    * Produkty
      * Stránky
        * _ViewImports
        * Informace
        * Index
    * Služby
      * Stránky
        * Spravovat
          * Informace
          * Index

### <a name="link-generation-with-razor-pages-and-areas"></a>Generování odkazů pomocí Razor stránek a oblastí

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"`):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti. Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.

Když není oblast zadaná, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy. Zvažte například odkazy vygenerované z následujícího kódu:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku `Services` v oblasti. Například `/Services/Manage/` `/Services/Manage/Index`,, nebo `/Services/Manage/About`.
* Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku `/Home`v.
* Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importovat obory názvů a pomocníka značek s _ViewImportsm souborem

Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou Razor stránku ve složce.

Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* . Následující kód ukazuje stránku */Services/Manage/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozím kódu:

* K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.
* [Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující kód ukazuje stránku */Products/about* Razor :

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru *areas/Products/pages/_ViewImports. cshtml* .

Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro Razor oblasti stránek

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.

### <a name="publishing-areas"></a>Oblasti publikování

Všechny soubory *. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, `<Project Sdk="Microsoft.NET.Sdk.Web">` Pokud je součástí souboru *. csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Oblasti jsou funkcí ASP.NET, která slouží k uspořádání souvisejících funkcí do skupiny jako samostatného oboru názvů (pro směrování) a struktury složek (pro zobrazení). Použití oblastí vytvoří hierarchii pro účely směrování přidáním dalšího `area`parametru trasy,, do `controller` a `action` nebo do Razor stránky. `page`

Oblasti představují způsob, jak rozdělit webovou aplikaci ASP.NET Core do menších funkčních skupin, z nichž každá má svou vlastní Razor sadu stránek, řadičů, zobrazení a modelů. Oblast je ve skutečnosti strukturou uvnitř aplikace. Ve webovém projektu ASP.NET Core jsou logické komponenty, jako jsou stránky, model, kontroler a zobrazení, uloženy v různých složkách. Modul runtime ASP.NET Core používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit aplikaci na samostatné oblasti funkcí na úrovni vysoké úrovně. Například aplikace pro elektronické obchodování s více obchodními jednotkami, jako je například rezervace, fakturace a hledání. Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, řadiče Razor , stránky a modely.

Zvažte použití oblastí v projektu, když:

* Aplikace se skládá z několika funkčních komponent na vysoké úrovni, které je možné logicky oddělit.
* Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample)). Ukázka stažení poskytuje základní aplikaci pro oblasti testování.

Pokud používáte Razor stránky, přečtěte si téma [oblasti Razor se stránkami](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro řadiče se zobrazeními

Typická ASP.NET Core webové aplikace s využitím oblastí, řadičů a zobrazení obsahuje následující:

* [Struktura složky oblasti](#area-folder-structure).
* Řadiče s [`[Area]`](#attribute) atributem k přidružení kontroleru k oblasti:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Trasa oblasti přidaná ke spuštění](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složky oblasti

Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*. Pomocí oblastí by struktura složek vypadala podobně jako v následujícím příkladu:

* Název projektu
  * Oblasti
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml
        * Spravovat
          * Soubor Index.cshtml
          * O. cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml

I když je předchozí rozložení typický při použití oblastí, k použití této struktury složek se vyžaduje jenom zobrazení souborů. Zobrazení vyhledávání pro soubor zobrazení se shodnými oblastmi v tomto pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Přidružte kontroler k oblasti.

Řadiče oblastí jsou označeny atributem [ &lbrack;Area&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) :

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat trasu oblasti

Trasy oblastí obvykle používají konvenční směrování namísto směrování atributů. Konvenční směrování je závislé na pořadí. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce směrování, protože jsou konkrétnější než trasy bez oblasti.

`{area:...}`dá se použít jako token v šablonách směrování, pokud je prostor adres URL stejnorodý napříč všemi oblastmi:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

V předchozím kódu `exists` použije omezení, které musí trasa odpovídat oblasti. Použití `{area:...}` je nejjednodušším mechanismem přidávání směrování do oblastí.

Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> k vytvoření dvou tras pojmenované oblasti:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Při použití `MapAreaRoute` s ASP.NET Core 2,2 se podívejte na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/7772).

Další informace najdete v tématu [Směrování oblastí](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generování propojení s oblastmi MVC

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje generaci odkazů s určenou oblastí:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.

Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti. Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti a řadiči.

Pokud oblast nebo kontroler neurčíte, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy.

Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Sdílené rozložení pro oblasti používající _ViewStart soubor. cshtml

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.

### <a name="_viewimportscshtml"></a>_ViewImports. cshtml

Ve svém standardním umístění */Views/_ViewImports. cshtml* se nevztahuje na oblasti. Chcete-li použít `@using`běžné [pomocníky značek](xref:mvc/views/tag-helpers/intro), `@inject` nebo ve vaší oblasti, zajistěte, aby se v [zobrazeních oblastí](xref:mvc/views/layout#importing-shared-directives)používal vhodný *_ViewImports. cshtml* . Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports. cshtml* do kořenového adresáře aplikace.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí složky oblasti, kde jsou uložená zobrazení

Následující kód změní výchozí složku oblasti z `"Areas"` na: `"MyAreas"`

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti se Razor stránkami

Oblasti se Razor stránkami vyžadují `Areas/<area name>/Pages` složku v kořenovém adresáři aplikace. Pro [ukázkovou aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)se používá tato struktura složek:

* Název projektu
  * Oblasti
    * Produkty
      * Stránky
        * _ViewImports
        * Informace
        * Index
    * Služby
      * Stránky
        * Spravovat
          * Informace
          * Index

### <a name="link-generation-with-razor-pages-and-areas"></a>Generování odkazů pomocí Razor stránek a oblastí

Následující kód v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje generaci odkazů se zadanou oblastí (například `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygenerované v předchozím kódu jsou platné kdekoli v aplikaci.

Ukázkový soubor ke stažení obsahuje [částečné zobrazení](xref:mvc/views/partial) obsahující předchozí odkazy a stejné odkazy bez určení oblasti. Na částečné zobrazení se odkazuje v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje vygenerované odkazy. Odkazy vygenerované bez určení oblasti jsou platné pouze v případě, že jsou odkazovány ze stránky ve stejné oblasti.

Když není oblast zadaná, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování odkazů. V mnoha případech ukázkové aplikace pomocí okolních hodnot generuje nesprávné odkazy. Zvažte například odkazy vygenerované z následujícího kódu:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Odkaz vygenerovaný z `<a asp-page="/Manage/About">` je správný pouze v případě, že byl poslední požadavek na stránku `Services` v oblasti. Například `/Services/Manage/` `/Services/Manage/Index`,, nebo `/Services/Manage/About`.
* Odkaz vygenerovaný z `<a asp-page="/About">` je správný pouze v případě, že byl poslední požadavek na stránku `/Home`v.
* Kód pochází z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Importovat obory názvů a pomocníka značek s _ViewImportsm souborem

Soubor *_ViewImports. cshtml* lze přidat do každé složky oblasti *stránky* a importovat tak obor názvů a značku pomocníka značek na každou Razor stránku ve složce.

Vezměte v úvahu oblast *služeb* ukázkového kódu, která neobsahuje soubor *_ViewImports. cshtml* . Následující kód ukazuje stránku */Services/Manage/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozím kódu:

* K určení modelu (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`) je nutné použít plně kvalifikovaný název domény.
* [Pomocník značek](xref:mvc/views/tag-helpers/intro) je povolený nástrojem`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

V ukázkovém stažení oblast produkty obsahuje následující *_ViewImports soubor. cshtml* :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující kód ukazuje stránku */Products/about* Razor :

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím souboru je obor názvů a `@addTagHelper` direktiva importován do souboru pomocí souboru *areas/Products/pages/_ViewImports. cshtml* .

Další informace najdete v tématech [Správa oboru pomocníka značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [Import sdílených direktiv](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro Razor oblasti stránek

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart. cshtml* do kořenové složky aplikace.

### <a name="publishing-areas"></a>Oblasti publikování

Všechny soubory *. cshtml a soubory v adresáři *wwwroot* jsou publikovány ve výstupu, `<Project Sdk="Microsoft.NET.Sdk.Web">` Pokud je součástí souboru *. csproj.
::: moniker-end
