---
title: Oblasti v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak jsou oblasti ASP.NET funkce MVC používaná k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení).
ms.author: riande
ms.date: 03/21/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 8859bc52416ff657036198c73f63b8b0a0201e11
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625933"
---
# <a name="areas-in-aspnet-core"></a>Oblasti v ASP.NET jádru

Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Oblasti jsou ASP.NET funkce používaná k uspořádání souvisejících funkcí do skupiny jako samostatná funkce:

* Obor názvů pro směrování.
* Struktura složek pro zobrazení a holicí strojek stránky.

Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` dalšího `action` parametru `page`trasy , `area`na stránku razor nebo .

Oblasti poskytují způsob, jak rozdělit ASP.NET základní webové aplikace do menších funkčních skupin, z nichž každá má vlastní sadu razor stránky, ovladače, zobrazení a modely. Oblast je efektivně struktura uvnitř aplikace. Ve webovém projektu ASP.NET Core jsou logické součásti, jako jsou stránky, model, řadič a zobrazení, uloženy v různých složkách. ASP.NET Core runtime používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velkou aplikaci může být výhodné rozdělit aplikaci do samostatných oblastí funkcí na vysoké úrovni. Například aplikace pro elektronické obchodování s více organizačními jednotkami, jako je pokladna, fakturace a vyhledávání. Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, ovladače, stránky Razor a modely.

Zvažte použití oblastí v projektu, když:

* Aplikace je tvořena více funkčními součástmi na vysoké úrovni, které lze logicky oddělit.
* Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([jak stáhnout](xref:index#how-to-download-a-sample)). Ukázka stahování poskytuje základní aplikaci pro testovací oblasti.

Pokud používáte Razor Pages, přečtěte si [téma Oblasti se stránkami s břitvou](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro regulátory se zobrazeními

Typická webová aplikace ASP.NET Core používající oblasti, ovladače a zobrazení obsahuje následující:

* [Struktura složek oblasti](#area-folder-structure).
* Řadiče s [`[Area]`](#attribute) atributem přidružit řadič s oblastí:

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Oblastní trasa přidaná ke spuštění](#add-area-route):

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složek oblasti

Zvažte aplikaci, která má dvě logické *skupiny, produkty* a *služby*. Pomocí oblastí by struktura složek byla podobná následujícímu:

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
          * O souboru O.cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml

Zatímco předchozí rozložení je typické při použití oblasti, pouze soubory zobrazení jsou nutné použít tuto strukturu složek. Zobrazení hledání hledání odpovídajícího souboru zobrazení oblasti v následujícím pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Přidružení řadiče k oblasti

Oblastní kontroloři jsou označeni atributem [ &lbrack;Area:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat oblast trasy

Trasy oblasti obvykle používají [konvenční směrování](xref:mvc/controllers/routing#cr) spíše než [směrování atributů](xref:mvc/controllers/routing#ar). Konvenční směrování je závislé na objednávce. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.

`{area:...}`lze použít jako token v šablonách tras, pokud je místo url jednotné ve všech oblastech:

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

V předchozím kódu `exists` použije omezení, že trasa musí odpovídat oblasti. Použití `{area:...}` `MapControllerRoute`s :

* Je nejméně složitý mechanismus přidání směrování do oblastí.
* Porovná všechny řadiče `[Area("Area name")]` s atributem.

Následující kód <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> používá k vytvoření dvou pojmenovaných tras oblasti:

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

Další informace naleznete v tématu [Area routing](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Propojení generování s oblastmi MVC

Následující kód z [ukázkové stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ukazuje generování propojení se zadanou oblastí:

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje:

* Předchozí odkazy.
* Odkazy podobné předchozí except `area` není zadán.

Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy. Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti a kontroleru.

Pokud není zadána oblast nebo řadič, směrování závisí na [okolních](xref:mvc/controllers/routing#ambient) hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V mnoha případech pro ukázkovou aplikaci, pomocí okolníhodnoty generuje nesprávné odkazy se značkami, které neurčuje oblast.

Další informace naleznete v [tématu Směrování k akcím řadiče](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Sdílené rozložení pro oblasti používající soubor _ViewStart.cshtml

Chcete-li sdílet společné rozložení pro celou aplikaci, ponechejte *_ViewStart.cshtml* v [kořenové složce aplikace](#arf). Další informace najdete v tématu <xref:mvc/views/layout>.

<a name="arf"></a>

### <a name="application-root-folder"></a>Kořenová složka aplikace

Kořenová složka aplikace je složka obsahující *Startup.cs* ve webové aplikaci vytvořené pomocí šablon ASP.NET Core.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

 */Views/_ViewImports.cshtml*, pro MVC a */Pages/_ViewImports.cshtml* pro Razor Pages, se neimportuje do zobrazení v oblastech. Pomocí jednoho z následujících přístupů můžete zajistit import zobrazení do všech zobrazení:

* Přidejte *_ViewImports.cshtml* do [kořenové složky aplikace](#arf). Pro všechna zobrazení v aplikaci se bude vztahovat *_ViewImports.cshtml* v kořenové složce aplikace.
* Zkopírujte soubor *_ViewImports.cshtml* do příslušné složky zobrazení pod oblastmi.

Soubor *_ViewImports.cshtml* obvykle obsahuje tag [helpery](xref:mvc/views/tag-helpers/intro) importy `@using`a `@inject` příkazy. Další informace naleznete [v tématu Import sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí složky oblastí, ve které jsou uložena zobrazení

Následující kód změní výchozí složku `"Areas"` `"MyAreas"`oblastí z do :

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti s břitvou stránky

Oblasti s Razor `Areas/<area name>/Pages` Pages vyžadují složku v kořenovém adresáři aplikace. Následující struktura složek se používá s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):

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

### <a name="link-generation-with-razor-pages-and-areas"></a>Propojení generování se stránkami a oblastmi Razor

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) ukazuje generování propojení se `asp-area="Products"`zadanou oblastí (například ):

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti. Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy. Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti.

Pokud oblast není zadána, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy. Zvažte například odkazy generované z následujícího kódu:

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Odkaz generovaný `<a asp-page="/Manage/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `Services` oblasti. Například `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`nebo .
* Odkaz generovaný `<a asp-page="/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.
* Kód je z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Import oboru názvů a pomocníků značek pomocí _ViewImports souboru

Do každé složky *Stránky* s adresou domény a pomocníků značek lze přidat soubor *_ViewImports.cshtml* a importovat tak obor názvů a pomocníky značek do každé stránky Razor ve složce.

Zvažte oblast *Služby* ukázkového kódu, která neobsahuje soubor *_ViewImports.cshtml.* Následující značky ukazují *stránku /Services/Manage/About* Razor:

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozí chrupu:

* K určení modelu`@model RPareas.Areas.Services.Pages.Manage.AboutModel`( ).
* [Pomocné spoje](xref:mvc/views/tag-helpers/intro) tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

V ukázkovém stažení obsahuje oblast Produkty následující soubor *_ViewImports.cshtml:*

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující značky ukazují */Products/About* Razor Page:

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím souboru je obor `@addTagHelper` názvů a směrnice importovány do souboru souborem *Oblasti/Produkty/Stránky/_ViewImports.cshtml.*

Další informace naleznete [v tématu Správa oboru pomocné spoje značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a import [sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro oblasti břitvy stránek

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.

### <a name="publishing-areas"></a>Publikační oblasti

Všechny soubory *.cshtml a soubory v adresáři *wwwroot* jsou publikovány do výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` jsou zahrnuty do souboru *.csproj.
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Oblasti jsou ASP.NET funkce používaná k uspořádání souvisejících funkcí do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení). Použití oblastí vytvoří hierarchii pro účely směrování přidáním `controller` dalšího `action` parametru `page`trasy , `area`na stránku razor nebo .

Oblasti poskytují způsob, jak rozdělit ASP.NET základní webové aplikace do menších funkčních skupin, z nichž každá má vlastní sadu razor stránky, ovladače, zobrazení a modely. Oblast je efektivně struktura uvnitř aplikace. Ve webovém projektu ASP.NET Core jsou logické součásti, jako jsou stránky, model, řadič a zobrazení, uloženy v různých složkách. ASP.NET Core runtime používá konvence pojmenování k vytvoření vztahu mezi těmito součástmi. Pro velkou aplikaci může být výhodné rozdělit aplikaci do samostatných oblastí funkcí na vysoké úrovni. Například aplikace pro elektronické obchodování s více organizačními jednotkami, jako je pokladna, fakturace a vyhledávání. Každá z těchto jednotek má svou vlastní oblast, která obsahuje zobrazení, ovladače, stránky Razor a modely.

Zvažte použití oblastí v projektu, když:

* Aplikace je tvořena více funkčními součástmi na vysoké úrovni, které lze logicky oddělit.
* Chcete rozdělit aplikaci tak, aby každá funkční oblast mohla pracovat nezávisle.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([jak stáhnout](xref:index#how-to-download-a-sample)). Ukázka stahování poskytuje základní aplikaci pro testovací oblasti.

Pokud používáte Razor Pages, přečtěte si [téma Oblasti se stránkami s břitvou](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro regulátory se zobrazeními

Typická webová aplikace ASP.NET Core používající oblasti, ovladače a zobrazení obsahuje následující:

* [Struktura složek oblasti](#area-folder-structure).
* Řadiče s [`[Area]`](#attribute) atributem přidružit řadič s oblastí:

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* [Oblastní trasa přidaná ke spuštění](#add-area-route):

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složek oblasti

Zvažte aplikaci, která má dvě logické *skupiny, produkty* a *služby*. Pomocí oblastí by struktura složek byla podobná následujícímu:

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
          * O souboru O.cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Soubor Index.cshtml

Zatímco předchozí rozložení je typické při použití oblasti, pouze soubory zobrazení jsou nutné použít tuto strukturu složek. Zobrazení hledání hledání odpovídajícího souboru zobrazení oblasti v následujícím pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Přidružení řadiče k oblasti

Oblastní kontroloři jsou označeni atributem [ &lbrack;Area:&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat oblast trasy

Trasy oblasti obvykle používají konvenční směrování spíše než směrování atributů. Konvenční směrování je závislé na objednávce. Obecně platí, že trasy s oblastmi by měly být umístěny dříve v tabulce tras, protože jsou konkrétnější než trasy bez oblasti.

`{area:...}`lze použít jako token v šablonách tras, pokud je místo url jednotné ve všech oblastech:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

V předchozím kódu `exists` použije omezení, že trasa musí odpovídat oblasti. Použití `{area:...}` je nejméně složitý mechanismus přidání směrování do oblastí.

Následující kód <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> používá k vytvoření dvou pojmenovaných tras oblasti:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Při `MapAreaRoute` použití s ASP.NET Core 2.2, podívejte se na [tento problém GitHub](https://github.com/dotnet/AspNetCore/issues/7772).

Další informace naleznete v tématu [Area routing](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Propojení generování s oblastmi MVC

Následující kód z [ukázkové stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ukazuje generování propojení se zadanou oblastí:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy generované s předchozím kódem jsou platné kdekoli v aplikaci.

Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti. Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy. Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti a kontroleru.

Pokud není zadána oblast nebo řadič, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy.

Další informace naleznete v [tématu Směrování k akcím řadiče](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a>Sdílené rozložení pro oblasti používající soubor _ViewStart.cshtml

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.

### <a name="_viewimportscshtml"></a>_ViewImports.cshtml

Ve svém standardním umístění */Views/_ViewImports.cshtml* se nevztahuje na oblasti. Chcete-li používat běžné [pomocné složky značek](xref:mvc/views/tag-helpers/intro) `@using`, nebo `@inject` ve vaší oblasti, zajistěte, aby [se pro zobrazení oblasti vztahoval](xref:mvc/views/layout#importing-shared-directives)správný soubor *_ViewImports.cshtml* . Pokud chcete stejné chování ve všech zobrazeních, přesuňte */Views/_ViewImports.cshtml* do kořenového adresáře aplikace.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí složky oblastí, ve které jsou uložena zobrazení

Následující kód změní výchozí složku `"Areas"` `"MyAreas"`oblastí z do :

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti s břitvou stránky

Oblasti s Razor `Areas/<area name>/Pages` Pages vyžadují složku v kořenovém adresáři aplikace. Následující struktura složek se používá s [ukázkovou aplikací](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):

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

### <a name="link-generation-with-razor-pages-and-areas"></a>Propojení generování se stránkami a oblastmi Razor

Následující kód z [ukázkového stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) ukazuje generování propojení se `asp-area="Products"`zadanou oblastí (například ):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy generované s předchozím kódem jsou platné kdekoli v aplikaci.

Ukázkové stažení obsahuje [částečné zobrazení,](xref:mvc/views/partial) které obsahuje předchozí odkazy a stejné odkazy bez určení oblasti. Částečné zobrazení je odkazováno v [souboru rozložení](xref:mvc/views/layout), takže každá stránka v aplikaci zobrazuje generované odkazy. Odkazy generované bez určení oblasti jsou platné pouze při odkazování ze stránky ve stejné oblasti.

Pokud oblast není zadána, směrování závisí na *okolních* hodnotách. Aktuální hodnoty trasy aktuálního požadavku jsou považovány za okolní hodnoty pro generování propojení. V mnoha případech pro ukázkovou aplikaci pomocí okolních hodnot generuje nesprávné odkazy. Zvažte například odkazy generované z následujícího kódu:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Odkaz generovaný `<a asp-page="/Manage/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `Services` oblasti. Například `/Services/Manage/`, `/Services/Manage/Index`, `/Services/Manage/About`nebo .
* Odkaz generovaný `<a asp-page="/About">` z je správný pouze v případě, že poslední požadavek byl pro stránku v `/Home`.
* Kód je z [ukázky ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a>Import oboru názvů a pomocníků značek pomocí _ViewImports souboru

Do každé složky *Stránky* s adresou domény a pomocníků značek lze přidat soubor *_ViewImports.cshtml* a importovat tak obor názvů a pomocníky značek do každé stránky Razor ve složce.

Zvažte oblast *Služby* ukázkového kódu, která neobsahuje soubor *_ViewImports.cshtml.* Následující značky ukazují *stránku /Services/Manage/About* Razor:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozí chrupu:

* K určení modelu`@model RPareas.Areas.Services.Pages.Manage.AboutModel`( ).
* [Pomocné spoje](xref:mvc/views/tag-helpers/intro) tagů jsou povoleny`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

V ukázkovém stažení obsahuje oblast Produkty následující soubor *_ViewImports.cshtml:*

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující značky ukazují */Products/About* Razor Page:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím souboru je obor `@addTagHelper` názvů a směrnice importovány do souboru souborem *Oblasti/Produkty/Stránky/_ViewImports.cshtml.*

Další informace naleznete [v tématu Správa oboru pomocné spoje značek](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a import [sdílených směrnic](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro oblasti břitvy stránek

Chcete-li sdílet společné rozložení pro celou aplikaci, přesuňte *_ViewStart.cshtml* do kořenové složky aplikace.

### <a name="publishing-areas"></a>Publikační oblasti

Všechny soubory *.cshtml a soubory v adresáři *wwwroot* jsou publikovány do výstupu, pokud `<Project Sdk="Microsoft.NET.Sdk.Web">` jsou zahrnuty do souboru *.csproj.
::: moniker-end
