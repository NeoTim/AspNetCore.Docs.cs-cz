---
title: Migrace z technologie ASP.NET MVC do ASP.NET Core MVC
author: ardalis
description: Zjistěte, jak začít s migrací projektu aplikace ASP.NET MVC do ASP.NET Core MVC.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a9e2b41b933ed04a23515564892ed1694a4ac4f8
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614393"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrace z technologie ASP.NET MVC do ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), a [Scott Addie](https://scottaddie.com)

Tento článek popisuje, jak začít, migrace projektu aplikace ASP.NET MVC [ASP.NET Core MVC](../mvc/overview.md). V procesu se zvýrazní spousta věcí, které byly změněny z technologie ASP.NET MVC. Migrace z technologie ASP.NET MVC je o více krocích a tento článek se týká počáteční nastavení, základní kontrolerů a zobrazení, statický obsah a závislostí na straně klienta. Další články zahrnují migraci konfigurací a kódem identit v mnoha projektů ASP.NET MVC.

> [!NOTE]
> Čísla verzí v ukázkách, nemusí být aktuální. Budete muset aktualizovat vaše projekty odpovídajícím způsobem.

## <a name="create-the-starter-aspnet-mvc-project"></a>Vytvořit úvodní projektu ASP.NET MVC

Abychom si předvedli upgradu, Začneme tím, že vytvoříte aplikaci ASP.NET MVC. Vytvořte s názvem *WebApp1* tak obor názvů odpovídá projekt ASP.NET Core, vytvoříme v dalším kroku.

![Dialogové okno Visual Studio nový projekt](mvc/_static/new-project.png)

![Dialogové okno nové webové aplikace: Šablona projektu MVC vybrali panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Volitelné:* Změňte název řešení od *WebApp1* k *Mvc5*. Visual Studio zobrazí nový název řešení (*Mvc5*), což usnadňuje zjistit tento projekt z projektu.

## <a name="create-the-aspnet-core-project"></a>Vytvořit projekt ASP.NET Core

Vytvořte nový *prázdný* webové aplikace ASP.NET Core se stejným názvem jako předchozí projekt (*WebApp1*) tedy odpovídat oboru názvů na dva projekty. S stejný obor názvů umožňuje snadnější zkopírovat kód mezi dva projekty. Budete mít k vytvoření tohoto projektu do jiného adresáře než předchozí projekt, který používá stejný název.

![Dialogové okno nového projektu](mvc/_static/new_core.png)

![Dialogové okno nové webové aplikace ASP.NET: Prázdná šablona projektu vybrané panelu šablony ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Volitelné:* Vytvoření nové aplikace ASP.NET Core pomocí *webovou aplikaci* šablony projektu. Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivé uživatelské účty**. Přejmenovat tuto aplikaci k *FullAspNetCore*. Vytvoření projektu ušetříte čas při převodu. Můžete si prohlédnout kód generovaný šablony najdete v článku konečný výsledek nebo zkopírujte kód do projektu převodu. Je také užitečné, pokud jste zaseknout se na krok převodu k porovnání s projektem šablona vytvořena.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurace lokality, aby používala MVC

::: moniker range=">= aspnetcore-2.1"

* Při cílení na .NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) se odkazuje ve výchozím nastavení. Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC. Pokud se zaměřujete na rozhraní .NET Framework, musí být odkazy na balíčky uvedené jednotlivě v souboru projektu.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Při cílení na .NET Core [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) se odkazuje ve výchozím nastavení. Tento balíček obsahuje balíčky běžně používané balíčky aplikací MVC. Pokud se zaměřujete na rozhraní .NET Framework, musí být odkazy na balíčky uvedené jednotlivě v souboru projektu.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* Při cílení na .NET Core nebo .NET Framework, balíčků balíčky běžně používané podle aplikací MVC jsou jednotlivě uvedené v souboru projektu.

::: moniker-end

`Microsoft.AspNetCore.Mvc` je rozhraní ASP.NET Core MVC. `Microsoft.AspNetCore.StaticFiles` je obslužná rutina statický soubor. Modul runtime ASP.NET Core je modulární a musí se explicitně výslovný souhlas se doručování statických souborů (viz [statické soubory](xref:fundamentals/static-files)).

* Otevřít *Startup.cs* soubor a změňte kód tak, aby odpovídala následující:

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

`UseStaticFiles` – Metoda rozšíření přidá obslužnou rutinu statický soubor. Jak už bylo zmíněno dříve, modul runtime ASP.NET je modulární a musí explicitně připojíte k doručování statických souborů. `UseMvc` Přidá metody rozšíření směrování. Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup) a [směrování](xref:fundamentals/routing).

## <a name="add-a-controller-and-view"></a>Přidat kontroler a zobrazení

V této části přidáte minimální kontroler a zobrazení, která bude sloužit jako zástupné symboly pro kontroler ASP.NET MVC a zobrazení, že provedeme migraci v další části.

* Přidat *řadiče* složky.

* Přidat **třída Kontroleru rozhraní** s názvem *HomeController.cs* k *řadiče* složky.

![Přidat novou položku – dialogové okno](mvc/_static/add_mvc_ctl.png)

* Přidat *zobrazení* složky.

* Přidat *zobrazení Domů* složky.

* Přidat **zobrazení Razor** s názvem *Index.cshtml* k *zobrazení Domů* složky.

![Přidat novou položku – dialogové okno](mvc/_static/view.png)

Struktura projektu je zobrazena níže:

![Průzkumník řešení zobrazující soubory a složky WebApp1](mvc/_static/project-structure-controller-view.png)

Nahraďte obsah *Views/Home/Index.cshtml* souboru následujícím kódem:

```html
<h1>Hello world!</h1>
```

Spusťte aplikaci.

![Webová aplikace otevřít v Microsoft Edge](mvc/_static/hello-world.png)

Zobrazit [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview) Další informace.

Když teď máme minimální funkční projekt ASP.NET Core, můžeme začít migrace funkce z projektu ASP.NET MVC. Musíme přejít následující:

* obsah na straně klienta (šablon stylů CSS, písem a skripty)

* kontrolery

* zobrazení

* modely

* Sdružování

* filtry

* Protokol vstup a výstup, Identity (to se provádí v dalším kurzu).

## <a name="controllers-and-views"></a>Zobrazení a kontrolerů

* Kopírování všech metod v ASP.NET MVC `HomeController` k novému `HomeController`. Všimněte si, že v architektuře ASP.NET MVC, návratový typ metody serveru integrovanou šablonu kontroleru akce [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); v ASP.NET Core MVC metody akce vrácené `IActionResult` místo. `ActionResult` implementuje `IActionResult`, takže není nutné změnit návratový typ metody akce.

* Kopírovat *About.cshtml*, *Contact.cshtml*, a *Index.cshtml* Razor zobrazit soubory z projektu ASP.NET MVC do projektu ASP.NET Core.

* Spuštění aplikace ASP.NET Core a testování jednotlivých metod. Jsme jste nemigrovali soubor rozložení nebo styly, takže vykreslené zobrazení obsahovat pouze obsah v zobrazení souborů. Nebudete mít rozložení souboru vygenerovaného odkazy `About` a `Contact` zobrazení, takže budete mít k vyvolání z prohlížeče (nahradit **4492** se číslo portu používané ve vašem projektu).

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![Stránka kontaktu](mvc/_static/contact-page.png)

Všimněte si, absenci stylů a položek nabídky. To Změníme v další části.

## <a name="static-content"></a>Statický obsah

V předchozích verzích rozhraní ASP.NET MVC statický obsah hostovaný z kořenového adresáře webového projektu a byla smíšeného s soubory na straně serveru. V ASP.NET Core je statický obsah hostovaný v *wwwroot* složky. Budete chtít zkopírovat statický obsah ze staré aplikaci ASP.NET MVC *wwwroot* složky v projektu ASP.NET Core. V této ukázkové převodu:

* Kopírování *favicon.ico* soubor z původní projekt MVC tak, aby *wwwroot* složky v projektu ASP.NET Core.

Staré rozhraní ASP.NET MVC používá projekt [Bootstrap](https://getbootstrap.com/) stylů a úložišť spuštění souborů *obsahu* a *skripty* složek. Šablona, která vygeneruje původního projektu ASP.NET MVC, odkazuje na spuštění v souboru rozložení (*Views/Shared/_Layout.cshtml*). Můžete zkopírovat *bootstrap.js* a *bootstrap.css* projektu soubory z technologie ASP.NET MVC *wwwroot* složky v novém projektu. Místo toho přidáme podporu Bootstrap (a dalších knihoven na straně klienta) pomocí CDN v další části.

## <a name="migrate-the-layout-file"></a>Migrujte soubor rozložení

* Kopírovat *soubor _ViewStart.cshtml* souboru z původního projektu ASP.NET MVC *zobrazení* složku do projektu ASP.NET Core *zobrazení* složky. *Soubor _ViewStart.cshtml* nedošlo ke změně souboru v ASP.NET Core MVC.

* Vytvoření *zobrazení/Shared* složky.

* *Volitelné:* Kopírování *_ViewImports.cshtml* z *FullAspNetCore* projekt MVC *zobrazení* složku do projektu ASP.NET Core *zobrazení* složka. Odebrat všechny deklarace oboru názvů v *_ViewImports.cshtml* souboru. *_ViewImports.cshtml* soubor obsahuje obory názvů pro všechny soubory, zobrazení a přináší [pomocných rutin značek](xref:mvc/views/tag-helpers/intro). Pomocné rutiny značek se používají v novém souboru rozložení. *_ViewImports.cshtml* souboru je nového v ASP.NET Core.

* Kopírovat *_Layout.cshtml* souboru z původního projektu ASP.NET MVC *zobrazení/Shared* složku do projektu ASP.NET Core *zobrazení/Shared* složky.

Otevřít *_Layout.cshtml* soubor a proveďte následující změny (dokončený kód je zobrazené dole):

* Nahraďte `@Styles.Render("~/Content/css")` s `<link>` element načíst *bootstrap.css* (viz níže).

* Odebrat `@Scripts.Render("~/bundles/modernizr")`.

* Okomentujte `@Html.Partial("_LoginPartial")` řádku (před a za řádek s `@*...*@`). Další informace najdete v části [migrovat ověřování a identita pro ASP.NET Core](xref:migration/identity)

* Nahraďte `@Scripts.Render("~/bundles/jquery")` s `<script>` – element (viz níže).

* Nahraďte `@Scripts.Render("~/bundles/bootstrap")` s `<script>` – element (viz níže).

Nahrazení kódu pro zahrnutí CSS Bootstrapu:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Nahrazení značky jQuery a zahrnutí Bootstrap JavaScript:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Aktualizovaný *_Layout.cshtml* souboru je uveden níže:

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zobrazte webu v prohlížeči. Teď by měl správně načíst data pomocí očekávané styly na místě.

* *Volitelné:* Můžete chtít zkuste použít nový soubor rozložení. Pro tento projekt můžete zkopírovat soubor rozložení z *FullAspNetCore* projektu. Nový soubor rozložení, který používá [pomocných rutin značek](xref:mvc/views/tag-helpers/intro) a dalších vylepšení.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

Informace o tom, jak nakonfigurovat sdružování a minifikace najdete v tématu [sdružování a Minifikace](../client-side/bundling-and-minification.md).

## <a name="solve-http-500-errors"></a>Řešení chyby HTTP 500

Existuje mnoho problémů, které může způsobit chybovou zprávu 500 protokolu HTTP, které neobsahují žádné informace o příčiny problému. Například pokud *Views/_ViewImports.cshtml* soubor obsahuje obor názvů, který neexistuje v projektu, zobrazí se chyby HTTP 500. Ve výchozím nastavení aplikace ASP.NET Core `UseDeveloperExceptionPage` rozšíření se přidá do `IApplicationBuilder` a spuštěn, když je konfigurace *vývoj*. To je podrobně popsán v následujícím kódu:

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

Neošetřené výjimky ve webové aplikaci ASP.NET Core převede do chybové odpovědi protokolu HTTP 500. Za normálních okolností. Podrobnosti o chybě nejsou součástí tyto odpovědi, aby se zabránilo úniku citlivých informací o serveru. V tématu **na stránce výjimek pro vývojáře** v [zpracování chyb](../fundamentals/error-handling.md) Další informace.

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
