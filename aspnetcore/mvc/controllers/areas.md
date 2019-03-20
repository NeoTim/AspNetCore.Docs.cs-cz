---
title: Oblasti v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak oblasti jsou používány pro organizaci související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení) funkce služby technologie ASP.NET MVC.
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 79bc023a7bd00a9d4de375e3cddaafd148251469
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264759"
---
# <a name="areas-in-aspnet-core"></a>Oblasti v ASP.NET Core

Podle [Dhananjay Kumar](https://twitter.com/debug_mode) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Oblasti jsou funkce služby ASP.NET použita k uspořádání související funkce do skupiny jako samostatný obor názvů (pro směrování) a strukturu složek (pro zobrazení). Pomocí oblastí vytvoří hierarchii pro účely směrování tak, že přidáte další parametr trasy, `area`do `controller` a `action` nebo stránky Razor `page`.

Oblasti poskytují způsob, jak dělit základní webové aplikace v ASP.NET do menších skupin funkční, každý s vlastní sadou stránek Razor, řadičů, zobrazení a modely. Oblast je v podstatě strukturu uvnitř aplikace. V projektu webové aplikace ASP.NET Core jsou v různých složkách uchovávají logické komponenty, jako jsou stránky, modelu, Kontroleru a zobrazení. ASP.NET Core runtime používá konvence pojmenování a vytvořit tak relaci mezi těmito součástmi. Pro velké aplikace může být výhodné rozdělit na samostatné vysoké úrovně funkční oblasti aplikace. Například e-commerce aplikace s několika obchodními jednotkami, jako je například checkout, fakturace a hledání. Každá z těchto jednotek mají své vlastní oblasti tak, aby obsahovala zobrazení, kontrolery, Razor Pages a modely.

Zvažte použití oblasti v projektu při:

* Aplikace je tvořeno několika vysoké úrovně funkčnosti součásti, které je možné logicky oddělit.
* Chcete rozdělit aplikace tak, aby každá funkční oblast je možné pracovat nezávisle na sobě.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([stažení](xref:index#how-to-download-a-sample)). Stažení ukázkové poskytuje základní aplikace pro testování oblasti.

Pokud používáte pro stránky Razor, přečtěte si téma [oblasti se stránkami Razor](#areas-with-razor-pages) v tomto dokumentu.

## <a name="areas-for-controllers-with-views"></a>Oblasti pro kontrolery se zobrazeními

Typické webové aplikace ASP.NET Core pomocí oblastí, kontrolerů a zobrazení obsahuje následující prvky:

* [Strukturu složek oblasti](#area-folder-structure).
* Kontrolery upravené pomocí [ &lbrack;oblasti&rbrack; ](#attribute) atribut asociovat řadič se serverem oblasti: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]
* [Oblasti trasa přidaná do spuštění](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a>Struktura složek oblasti

Vezměte v úvahu aplikaci, která má dvě logické skupiny, *produkty* a *služby*. Používat, bude podobný následujícímu strukturu složek:

* Název projektu
  * Oblasti
    * Produkty
      * Kontrolery
        * HomeController.cs
        * ManageController.cs
      * Zobrazení
        * Domů
          * Index.cshtml
        * Správa
          * Index.cshtml
          * About.cshtml
    * Služby
      * Kontrolery
        * HomeController.cs
      * Zobrazení
        * Domů
          * Index.cshtml

Při předchozím rozložení je obvyklá, když pomocí oblastí, zobrazit soubory je potřeba použít tuto strukturu složek. Zobrazení zjišťování vyhledá odpovídající oblast zobrazení soubor v následujícím pořadí:

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

Umístění složky jiných zobrazení, jako jsou *řadiče* a *modely* nemá **není** záleží. Například *řadiče* a *modely* složky nejsou povinné. Obsah *řadiče* a *modely* je kód, který získá kompilovány do knihovny DLL. Obsah *zobrazení* není kompilována, dokud byla podána žádost do tohoto zobrazení.

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a>Asociovat řadič se serverem oblast

Oblast řadiče jsou označeny [ &lbrack;oblasti&rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) atribut:

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a>Přidat oblast směrování

Cesty oblasti obvykle používají konvenční směrování místo směrováním atributů. Konvenční směrování je závislé na pořadí. Obecně platí trasy s oblastmi by měl umístit výše ve směrovací tabulce, jako jsou podrobnější než trasy bez oblasti.

`{area:...}` slouží jako token v šablonách tras Pokud ve všech oblastech je jednotné místo adresy url:

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

V předchozím kódu `exists` platí omezení, že postupu musí odpovídat oblast. Pomocí `{area:...}` je nejjednodušším mechanismus pro přidání směrování do oblasti.

Následující kód používá <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> vytvořit dva pojmenované oblasti trasy:

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

Při použití `MapAreaRoute` 2.2 technologie ASP.NET Core, najdete v článku [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/7772).

Další informace najdete v tématu [oblasti směrování](xref:mvc/controllers/routing#areas).

### <a name="link-generation-with-mvc-areas"></a>Generování odkazů s oblastmi MVC

Následující kód z [vzorku ke stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) zobrazuje odkaz generace k zadané oblasti:

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygeneroval s předchozím kódu jsou platné kdekoli v aplikaci.

Zahrnuje vzorku ke stažení [částečné zobrazení](xref:mvc/views/partial) bez zadání oblasti, která obsahuje výše uvedené odkazy a stejné odkazy. Částečné zobrazení odkazuje [soubor rozložení](xref:mvc/views/layout), takže každé stránky v aplikaci zobrazí vygenerovaný odkazy. Odkazy generované bez zadání oblasti platí pouze při odkazování z stránku ve stejné oblasti a kontroler.

Pokud oblast nebo kontroler není zadána, směrování závisí *okolí* hodnoty. Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů. V mnoha případech pro ukázkovou aplikaci pomocí okolí hodnot generuje nesprávné odkazy.

Další informace najdete v tématu [směrování na akce kontroleru](xref:mvc/controllers/routing).

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a>Sdílené rozložení pro použití souboru soubor _ViewStart.cshtml oblasti

Chcete-li sdílet společný rozložení pro celé aplikace, přesuňte *soubor _ViewStart.cshtml* ke kořenové složce aplikace.

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a>Změna výchozí oblast složky pro uložení zobrazení

Následující kód změní výchozí oblast složku z `"Areas"` k `"MyAreas"`:

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a>Oblasti se stránkami Razor

Vyžadovat oblasti se stránkami Razor a *oblasti /&lt;název oblasti&gt;/stránky* složku v kořenovém adresáři aplikace. Následující strukturu složek se používá s [vzorku ke stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)

* Název projektu
  * Oblasti
    * Produkty
      * Stránky
        * _ViewImports
        * O produktu
        * Index
    * Služby
      * Stránky
        * Správa
          * O produktu
          * Index

### <a name="link-generation-with-razor-pages-and-areas"></a>Generování odkazů se stránkami Razor a oblasti

Následující kód z [vzorku ke stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) zobrazuje odkaz generace k zadané oblasti (například `asp-area="Products"`):

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

Odkazy vygeneroval s předchozím kódu jsou platné kdekoli v aplikaci.

Zahrnuje vzorku ke stažení [částečné zobrazení](xref:mvc/views/partial) bez zadání oblasti, která obsahuje výše uvedené odkazy a stejné odkazy. Částečné zobrazení odkazuje [soubor rozložení](xref:mvc/views/layout), takže každé stránky v aplikaci zobrazí vygenerovaný odkazy. Odkazy generované bez zadání oblasti platí pouze při odkazování z stránku ve stejné oblasti.

Pokud není zadána oblasti, směrování závisí *okolí* hodnoty. Aktuální hodnoty trasy z aktuální požadavek jsou považovány za okolí hodnoty pro generování odkazů. V mnoha případech pro ukázkovou aplikaci pomocí okolí hodnot generuje nesprávné odkazy. Představte si třeba odkazy generované z následující kód:

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

Pro předchozí kód:

* Link nevygeneruje `<a asp-page="/Manage/About">` je správný, pouze když posledního požadavku byla pro stránku v `Services` oblasti. Například `/Services/Manage/`, `/Services/Manage/Index`, nebo `/Services/Manage/About`.
* Link nevygeneruje `<a asp-page="/About">` je správný, pouze když posledního požadavku byla pro stránku v `/Home`.
* Kód je z [vzorku ke stažení](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a>Import oboru názvů a pomocných rutin značek se souborem _ViewImports

A *_ViewImports* soubor lze přidat do každé oblasti *stránky* složce pro import oboru názvů a pomocných rutin značek na každou stránku Razor ve složce.

Vezměte v úvahu *služby* oblasti ukázek kódu, který neobsahuje *_ViewImports* souboru. Následující kód ukazuje */Services/Manage/About* stránky Razor:

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

V předchozím kódu:

* K určení modelu musí použít plně kvalifikovaný název domény (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).
* [Pomocné rutiny značky](xref:mvc/views/tag-helpers/intro) ve jsou povolené `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`

Oblasti produktů ve vzorku ke stažení obsahuje následující *_ViewImports* souboru:

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

Následující kód ukazuje */produkty/o* stránky Razor: [!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

V předchozím soubor, obor názvů a `@addTagHelper` směrnice, je naimportován do souboru funkcí *Areas/Products/Pages/_ViewImports.cshtml* souboru:

Další informace najdete v tématu [Správa pomocné rutiny značky oboru](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) a [direktivy import sdílených](xref:mvc/views/layout#importing-shared-directives).

### <a name="shared-layout-for-razor-pages-areas"></a>Sdílené rozložení pro oblasti stránek Razor

Chcete-li sdílet společný rozložení pro celé aplikace, přesuňte *soubor _ViewStart.cshtml* ke kořenové složce aplikace.

### <a name="publishing-areas"></a>Publikování oblasti

Všechny `*.cshtml` a `wwwroot/**` souborů k publikování do výstupu, kdy `<Project Sdk="Microsoft.NET.Sdk.Web">` je součástí the.csproj* souboru.