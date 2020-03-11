---
title: Migrace z ASP.NET MVC na ASP.NET Core MVC
author: ardalis
description: Naučte se, jak začít migrovat projekt ASP.NET MVC na ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661166"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrace z ASP.NET MVC na ASP.NET Core MVC

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Skořepa](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)a [Scott Addie](https://scottaddie.com)

Tento článek ukazuje, jak začít s migrací projektu ASP.NET MVC na [ASP.NET Core MVC](../mvc/overview.md). V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC. Migrace z ASP.NET MVC je proces více kroků a tento článek se zabývá počátečním nastavením, základními řadiči a zobrazeními, statickým obsahem a závislostmi na straně klienta. Další články zahrnují migraci konfigurace a kódu identity nalezeného v mnoha projektech ASP.NET MVC.

> [!NOTE]
> Čísla verzí v ukázkách nemusí být aktuální. Vaše projekty možná budete muset aktualizovat odpovídajícím způsobem.

## <a name="create-the-starter-aspnet-mvc-project"></a>Vytvoření projektu počáteční ASP.NET MVC

K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC. Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu, který jsme vytvořili v dalším kroku.

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*. Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.

## <a name="create-the-aspnet-core-project"></a>Vytvoření projektu ASP.NET Core

Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly. Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty. Tento projekt bude nutné vytvořit v jiném adresáři než předchozí projekt pro použití stejného názvu.

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* . Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**. Přejmenujte tuto aplikaci na *FullAspNetCore*. Vytvořením tohoto projektu ušetříte čas v převodu. Můžete se podívat na kód vygenerovaný šablonou a zobrazit tak konečný výsledek nebo zkopírovat kód do projektu pro převod. Je také užitečné, když se zablokuje na krok převodu pro porovnání s projektem generovaným šablonou.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurace lokality pro použití MVC

::: moniker range=">= aspnetcore-2.1"

* Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) . Tento balíček obsahuje balíčky běžně používané aplikacemi MVC. Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. All Metapackage](xref:fundamentals/metapackage) . Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC. Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Při cílení na rozhraní .NET Core nebo .NET Framework jsou balíčky běžně používané balíčky MVC aplikace uvedeny jednotlivě v souboru projektu.

::: moniker-end

`Microsoft.AspNetCore.Mvc` je rozhraní ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles` je obslužná rutina statického souboru. Modul runtime ASP.NET Core je modulární a musíte explicitně vyjádřit výslovný souhlas s poskytováním statických souborů (viz [statické soubory](xref:fundamentals/static-files)).

* Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

Metoda rozšíření `UseStaticFiles` přidá obslužnou rutinu statického souboru. Jak už bylo zmíněno dříve, modul runtime ASP.NET je modulární a musíte explicitně vyjádřit výslovný souhlas s poskytováním statických souborů. Metoda rozšíření `UseMvc` přidává směrování. Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.

## <a name="add-a-controller-and-view"></a>Přidat kontroler a zobrazení

V této části přidáte minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler ASP.NET MVC a zobrazení, která migrujete v další části.

* Přidejte složku *Controllers* .

* Do složky *Controllers* přidejte **třídu controller** s názvem *HomeController.cs* .

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* Přidejte složku *zobrazení* .

* Přidat *zobrazení/domovskou* složku.

* Přidejte **zobrazení Razor** s názvem *index. cshtml* do složky *views/Home* .

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

Struktura projektu je zobrazena níže:

![Průzkumník řešení zobrazení souborů a složek WebApp1](mvc/_static/project-structure-controller-view.png)

Obsah souboru *views/Home/index. cshtml* nahraďte následujícím způsobem:

```html
<h1>Hello world!</h1>
```

Spusťte aplikaci.

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview) .

Teď, když máme minimální pracovní ASP.NET Core projekt, můžeme začít migrovat funkce z projektu MVC ASP.NET. Musíme přesunout následující:

* obsah na straně klienta (šablony stylů CSS, písma a skripty)

* kontrolery

* views

* modely

* Sdružování

* Filtry

* Přihlaste se a přihlaste se k identitě (Tato práce se provádí v dalším kurzu).

## <a name="controllers-and-views"></a>Řadiče a zobrazení

* Zkopírujte všechny metody z ASP.NET MVC `HomeController` do nového `HomeController`. Všimněte si, že ve službě ASP.NET MVC je návratový typ metody kontroleru vestavěné šablony [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC metody akcí vrátí `IActionResult` místo toho. `ActionResult` implementuje `IActionResult`, takže není nutné měnit návratový typ vašich metod akcí.

* Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.

* Spusťte aplikaci ASP.NET Core a otestujte všechny metody. Zatím jsme nemigrovali soubor rozložení ani styly, takže vykreslená zobrazení obsahují jenom obsah v zobrazení souborů. Nebudete mít odkazy vygenerované tímto souborem rozložení pro `About` a `Contact` zobrazení, takže je budete muset vyvolat z prohlížeče (nahraďte **4492** číslem portu použitým v projektu).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Stránka kontaktu](mvc/_static/contact-page.png)

Všimněte si nedostatku stylů a položek nabídky. To Změníme v další části.

## <a name="static-content"></a>Statický obsah

V předchozích verzích ASP.NET MVC byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru. V ASP.NET Core je statický obsah hostovaný ve složce *wwwroot* . Budete chtít zkopírovat statický obsah ze staré aplikace ASP.NET MVC do složky *wwwroot* v projektu ASP.NET Core. V tomto ukázkovém převodu:

* Zkopírujte soubor *favicon. ico* z původního projektu MVC do složky *wwwroot* v projektu ASP.NET Core.

Starý projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do složek *obsah* a *skripty* . Šablona, která vygenerovala starý projekt ASP.NET MVC, odkazuje na zavedení v souboru rozložení (*views/Shared/_Layout. cshtml*). Můžete zkopírovat soubory *bootstrap. js* a *bootstrap. css* z projektu ASP.NET MVC do složky *wwwroot* v novém projektu. Místo toho přidáme podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v další části.

## <a name="migrate-the-layout-file"></a>Migrovat soubor rozložení

* Zkopírujte soubor *_ViewStart. cshtml* ze staré složky *zobrazení* projektu ASP.NET MVC do složky *zobrazení* projektu ASP.NET Core. Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.

* Vytvoří *zobrazení/sdílenou* složku.

* *Volitelné:* Zkopírujte *_ViewImports. cshtml* ze složky *zobrazení* projektu *FullAspNetCore* MVC do složky *zobrazení* projektu ASP.NET Core. Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* . Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro). V novém souboru rozložení se používají pomocníky značek. Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.

* Zkopírujte soubor *_Layout. cshtml* ze starých *zobrazení nebo sdílené* složky projektu ASP.NET MVC do *zobrazení nebo* sdílené složky projektu ASP.NET Core.

Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):

* Nahraďte `@Styles.Render("~/Content/css")` prvkem `<link>` pro načtení *bootstrap. CSS* (viz níže).

* Odeberte `@Scripts.Render("~/bundles/modernizr")`.

* Odkomentujte `@Html.Partial("_LoginPartial")` čáru (Obklopte řádek `@*...*@`). Další informace najdete v tématu [migrace ověřování a identity pro ASP.NET Core](xref:migration/identity)

* Nahraďte `@Scripts.Render("~/bundles/jquery")` prvkem `<script>` (viz níže).

* Nahraďte `@Scripts.Render("~/bundles/bootstrap")` prvkem `<script>` (viz níže).

Náhradní značky pro spuštění šablony stylů Bootstrap:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Náhradní označení pro zahrnutí a zavedení JavaScriptu:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Aktualizovaný soubor *_Layout. cshtml* je uveden níže:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zobrazí web v prohlížeči. Měl by se teď správně načíst s očekávanými styly.

* *Volitelné:* Možná budete chtít zkusit použít nový soubor rozložení. Pro tento projekt můžete zkopírovat soubor rozložení z projektu *FullAspNetCore* . Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Řešení chyb HTTP 500

Existuje mnoho problémů, které mohou způsobit chybovou zprávu HTTP 500, která neobsahuje žádné informace o zdroji problému. Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, zobrazí se chyba HTTP 500. Ve výchozím nastavení se v aplikacích ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace. Toto je podrobně popsáno v následujícím kódu:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

ASP.NET Core převede neošetřené výjimky ve webové aplikaci na odpovědi na chyby HTTP 500. V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru. Další informace najdete v tématu **použití stránky s výjimkou vývojáře** v tématu [zpracování chyb](../fundamentals/error-handling.md) .

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
