---
title: Naučte se migrovat z ASP.NET MVC na ASP.NET Core MVC
author: wadepickett
description: Naučte se, jak začít migrovat projekt ASP.NET MVC do ASP.NET Core MVC.
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 6a645d0e5959b4301ee7d2bcfc692f7499574dc4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407320"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a>Migrace z ASP.NET MVC na ASP.NET Core MVC

::: moniker range=">= aspnetcore-3.0"

Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core MVC](xref:mvc/overview). V tomto procesu zvýrazní související změny z ASP.NET MVC.

Migrace z ASP.NET MVC je proces s více kroky. Tento článek popisuje:

* Počáteční nastavení.
* Základní řadiče a zobrazení.
* Statický obsah.
* Závislosti na straně klienta.

Informace o migraci konfigurace a Identity kódu najdete v tématu [migrace konfigurace do ASP.NET Core](xref:migration/configuration) a [migrace ověřování a Identity ASP.NET Core](xref:migration/identity).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a>Vytvoření projektu počáteční ASP.NET MVC

V aplikaci Visual Studio vytvořte ukázkový projekt ASP.NET MVC k migraci:

1. V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
1. Vyberte **ASP.NET webová aplikace (.NET Framework)** a pak vyberte **Další**.
1. Pojmenujte projekt *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku. Vyberte **Vytvořit**.
1. Vyberte **MVC**a pak vyberte **vytvořit**.

## <a name="create-the-aspnet-core-project"></a>Vytvoření projektu ASP.NET Core

Vytvořte nové řešení s novým projektem ASP.NET Core pro migraci na:

1. Spusťte druhou instanci sady Visual Studio.
1. V nabídce **soubor** vyberte možnost **Nový** > **projekt**.
1. Vyberte **Webová aplikace webové jádro ASP.NET** a pak vyberte **Další**.
1. V dialogovém okně **Konfigurovat nový projekt** pojmenujte projekt *WebApp1*.
1. Nastavte umístění na jiný adresář než předchozí projekt pro použití stejného názvu projektu. Použití stejného oboru názvů usnadňuje kopírování kódu mezi dvěma projekty. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,1** . Vyberte šablonu projektu **Webová aplikace (model-zobrazení-kontroler)** a vyberte **vytvořit**.

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a>Konfigurace lokality ASP.NET Core pro použití MVC

V ASP.NET Core 3,0 a novějších projektech .NET Framework již není podporovanou cílovou architekturou. Projekt musí být cílen na .NET Core. ASP.NET Core sdílené rozhraní, které zahrnuje MVC, je součástí instalace modulu runtime .NET Core. Sdílené rozhraní je automaticky odkazováno při použití `Microsoft.NET.Sdk.Web` sady SDK v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Další informace naleznete v tématu [Reference k rozhraní](xref:migration/22-to-30#framework-reference).

V ASP.NET Core `Startup` Třída:

* Nahradí *Global. asax*.
* Zpracuje všechny úlohy při spuštění aplikace.

Další informace naleznete v tématu <xref:fundamentals/startup>.

V projektu ASP.NET Core otevřete soubor *Startup.cs* :

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

Aplikace ASP.NET Core musí souhlasit s funkcemi architektury se middlewarem. Předchozí kód generovaný šablonou přidá tyto služby a middleware:

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>Metoda rozšíření registruje podporu služby MVC pro řadiče, funkce a zobrazení související s rozhraním API. Další informace o možnostech registrace služby MVC najdete v tématu [Registrace služby MVC](xref:migration/22-to-30#mvc-service-registration) .
* <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>Metoda rozšíření přidá obslužnou rutinu statického souboru `Microsoft.AspNetCore.StaticFiles` . `UseStaticFiles`Metoda rozšíření musí být volána před `UseRouting` . Další informace naleznete v tématu <xref:fundamentals/static-files>.
* <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>Metoda rozšíření přidává směrování. Další informace naleznete v tématu <xref:fundamentals/routing>.

Tato existující konfigurace zahrnuje to, co je potřeba k migraci ukázkového projektu ASP.NET MVC. Další informace o ASP.NET Core možností middlewaru naleznete v tématu <xref:fundamentals/startup> .

## <a name="migrate-controllers-and-views"></a>Migrace řadičů a zobrazení

V projektu ASP.NET Core by byla přidána nová prázdná třída kontroleru a třídy zobrazení, která bude sloužit jako zástupné symboly pomocí stejných názvů jako kontrolér a třídy zobrazení v jakémkoli projektu ASP.NET MVC pro migraci z.

Projekt ASP.NET Core *WebApp1* již obsahuje minimální ukázkový kontroler a zobrazení se stejným názvem jako projekt ASP.NET MVC. Ty budou sloužit jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení, která se mají migrovat z projektu ASP.NET MVC *WebApp1* .

1. Zkopírujte metody z ASP.NET MVC, `HomeController` abyste nahradili nové metody ASP.NET Core `HomeController` . Není nutné měnit návratový typ metod akce. Zabudovaná metoda akčního typu metody kontroleru ASP.NET MVC je [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` . `ActionResult`implementuje `IActionResult` .
1. V projektu ASP.NET Core klikněte pravým tlačítkem myši na *zobrazení/domovský* adresář, vyberte možnost **Přidat** > **existující položku**.
1. V dialogovém okně **Přidat existující položku** přejděte do *zobrazení/domovského* adresáře projektu ASP.NET MVC *WebApp1* .
1. Vyberte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení a pak vyberte **Přidat**a nahraďte stávající soubory.

Další informace naleznete v tématech <xref:mvc/controllers/actions> a <xref:mvc/views/overview>.

## <a name="test-each-method"></a>Testování jednotlivých metod

Každý koncový bod řadiče lze otestovat, ale rozložení a styly jsou popsány dále v dokumentu.

1. Spusťte aplikaci ASP.NET Core.
1. Vyvolání vykreslených zobrazení z prohlížeče na běžící ASP.NET Core aplikaci nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core. Například, `https://localhost:44375/home/about`.

## <a name="migrate-static-content"></a>Migrace statického obsahu

V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru. V ASP.NET Core jsou statické soubory uloženy v rámci [webového kořenového](xref:fundamentals/index#web-root) adresáře projektu. Výchozí adresář je *{root obsahu}/wwwroot*, ale může se změnit. Další informace najdete v tématu [statické soubory v ASP.NET Core](xref:fundamentals/static-files#serve-static-files).

Zkopírujte statický obsah z projektu ASP.NET MVC *WebApp1* do adresáře *wwwroot* v projektu ASP.NET Core *WebApp1* :

1. V projektu ASP.NET Core klikněte pravým tlačítkem myši na adresář *wwwroot* , vyberte možnost **Přidat** > **existující položku**.
1. V dialogovém okně **Přidat existující položku** přejděte do projektu ASP.NET MVC *WebApp1* .
1. Vyberte soubor *favicon. ico* a pak vyberte **Přidat**a nahraďte existující soubor.

## <a name="migrate-the-layout-files"></a>Migrace souborů rozložení

Zkopírujte soubory rozložení projektu ASP.NET MVC do projektu ASP.NET Core:

1. V projektu ASP.NET Core klikněte pravým tlačítkem myši na adresář *zobrazení* a vyberte možnost **Přidat** > **existující položku**.
1. V dialogovém okně **Přidat existující položku** přejděte do adresáře *zobrazení* projektu ASP.NET MVC *WebApp1* .
1. Vyberte soubor *_ViewStart. cshtml* a pak vyberte **Přidat**.

Zkopírujte soubory sdíleného rozložení projektu ASP.NET MVC do projektu ASP.NET Core:

1. V projektu ASP.NET Core klikněte pravým tlačítkem na *zobrazení/sdílený* adresář a vyberte **Přidat** > **existující položku**.
1. V dialogovém okně **Přidat existující položku** přejděte do *zobrazení/sdíleného* adresáře projektu ASP.NET MVC *WebApp1* .
1. Vyberte soubor *_Layout. cshtml* a pak vyberte **Přidat**a nahraďte existující soubor.

V projektu ASP.NET Core otevřete *_Layout soubor. cshtml* . Proveďte následující změny, aby odpovídaly dokončenému kódu zobrazenému níže:

Aktualizujte zahrnutí šablon stylů CSS tak, aby odpovídala následujícímu dokončenému kódu:

1. Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).
1. Odebrat `@Scripts.Render("~/bundles/modernizr")` .

Dokončené nahrazující značky pro spuštění šablony stylů spuštění:

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

Aktualizujte zahrnutí JavaScriptu a Bootstrap pro JavaScript tak, aby odpovídalo následujícímu dokončenému kódu:

1. Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).
1. Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).

Hotové nahrazující označení pro zahrnutí a zavedení JavaScriptu:

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

Aktualizovaný soubor *_Layout. cshtml* je uveden níže:

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

Zobrazí web v prohlížeči. Měl by se vykreslovat s očekávanými styly.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

ASP.NET Core je kompatibilní s několika Open Source řešeními a minifikace řešeními, jako je [Weboptimalizátor](https://github.com/ligershark/WebOptimizer) a další podobné knihovny. ASP.NET Core neposkytuje nativní řešení pro sdružování a minifikace. Informace o konfiguraci sdružování a minifikace najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Řešení chyb HTTP 500

Existuje mnoho problémů, které mohou způsobit chybovou zprávu HTTP 500, která neobsahuje žádné informace o zdroji problému. Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500. Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*prostředí. Toto je podrobně popsáno v následujícím kódu:

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500. V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru. Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).

## <a name="next-steps"></a>Další kroky

* <xref:migration/identity>

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core mvc](xref:mvc/overview) 2,2. V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC. Migrace z ASP.NET MVC je proces s více kroky. Tento článek popisuje:

* Počáteční nastavení
* Základní řadiče a zobrazení
* Statický obsah
* Závislosti na straně klienta.

Informace o migraci konfigurace a Identity kódu naleznete v tématu <xref:migration/configuration> a <xref:migration/identity> .

> [!NOTE]
> Čísla verzí v ukázkách nemusí být aktuální, aktualizujte projekty odpovídajícím způsobem.

## <a name="create-the-starter-aspnet-mvc-project"></a>Vytvoření projektu počáteční ASP.NET MVC

K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC. Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku.

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*. Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.

## <a name="create-the-aspnet-core-project"></a>Vytvoření projektu ASP.NET Core

Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly. Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty. Tento projekt vytvořte v jiném adresáři než předchozí projekt, abyste mohli použít stejný název.

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* . Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**. Přejmenujte tuto aplikaci na *FullAspNetCore*. Při vytváření tohoto projektu se šetří čas v převodu. Konečný výsledek lze zobrazit v kódu generovaném šablonou, kód může být zkopírován do projektu pro převod nebo v porovnání s projektem generovaným šablonou.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurace lokality pro použití MVC

* Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) . Tento balíček obsahuje balíčky běžně používané aplikacemi MVC. Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.

`Microsoft.AspNetCore.Mvc`je ASP.NET Core architekturu MVC. `Microsoft.AspNetCore.StaticFiles`je obslužná rutina statického souboru. Aplikace ASP.NET Core výslovně přihlašovat pro middleware, například pro obsluhu statických souborů. Další informace najdete v tématu [statické soubory](xref:fundamentals/static-files).

* Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozšíření přidá obslužnou rutinu statického souboru. Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.

## <a name="add-a-controller-and-view"></a>Přidat kontroler a zobrazení

V této části se přidávají minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení migrované v další části.

* Přidejte adresář *řadičů* .

* Přidejte do adresáře *Controllers* **třídu controller** s názvem *HomeController.cs* .

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* Přidejte adresář *zobrazení* .

* Přidání *zobrazení/domovského* adresáře

* Přidejte ** Razor zobrazení** s názvem *index. cshtml* do *zobrazení/domovského* adresáře.

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

Struktura projektu je zobrazena níže:

![Průzkumník řešení zobrazení souborů a adresářů WebApp1](mvc/_static/project-structure-controller-view.png)

Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:

```html
<h1>Hello world!</h1>
```

Spusťte aplikaci.

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview).

Následující funkce vyžadují migraci z ukázkového projektu ASP.NET MVC do projektu ASP.NET Core:

* obsah na straně klienta (šablony stylů CSS, písma a skripty)

* kontrolery

* zobrazení

* modely

* sdružování

* filtry

* Přihlaste se nebo se přihlaste Identity (to se provádí v dalším kurzu).

## <a name="controllers-and-views"></a>Řadiče a zobrazení

* Zkopírujte každou z metod z ASP.NET MVC `HomeController` do nové `HomeController` . V ASP.NET MVC je jako návratový typ metody typu "předdefinovaná šablona" [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` . `ActionResult`implementuje `IActionResult` , takže není nutné měnit návratový typ metod akce.

* Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.

## <a name="test-each-method"></a>Testování jednotlivých metod

Soubor rozložení a styly nebyly dosud migrovány, takže vykreslená zobrazení obsahují pouze obsah v zobrazení souborů. Soubory rozložení vygenerované odkazy pro `About` zobrazení a `Contact` nebudou zatím k dispozici.

Vyvolání vygenerovaných zobrazení z prohlížeče na běžící aplikaci ASP.NET Core nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core. Příklad: `https://localhost:44375/home/about`.

![Stránka kontaktu](mvc/_static/contact-page.png)

Všimněte si nedostatku stylů a položek nabídky. Styl bude opraven v následující části.

## <a name="static-content"></a>Statický obsah

V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru. V ASP.NET Core je statický obsah hostovaný v adresáři *wwwroot* . Zkopírujte statický obsah z aplikace ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core. V tomto ukázkovém převodu:

* Zkopírujte soubor *favicon. ico* z projektu ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.

Projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do adresářů *obsahu* a *skriptů* . Šablona, která vygenerovala projekt ASP.NET MVC, odkazuje na Bootstrap v souboru rozložení (*views/Shared/_Layout. cshtml*). Soubory *bootstrap.js* a *bootstrap. CSS* mohou být zkopírovány z projektu ASP.NET MVC do adresáře *wwwroot* v novém projektu. Místo toho tento dokument přidá podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v následující části.

## <a name="migrate-the-layout-file"></a>Migrovat soubor rozložení

* Zkopírujte soubor *_ViewStart. cshtml* z adresáře *zobrazení* projektu ASP.NET MVC do adresáře *zobrazení* projektu ASP.NET Core. Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.

* Vytvořte *zobrazení/sdílený* adresář.

* *Volitelné:* Zkopírujte *_ViewImports. cshtml* z adresáře *zobrazení* projektu *FullAspNetCore* MVC do adresáře *zobrazení* projektu ASP.NET Core. Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* . Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro). V novém souboru rozložení se používají pomocníky značek. Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.

* Zkopírujte soubor *_Layout. cshtml* z *zobrazení/sdíleného* adresáře projektu ASP.NET MVC do *zobrazení/* sdíleného adresáře ASP.NET Core projektu.

Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):

* Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).

* Odebrat `@Scripts.Render("~/bundles/modernizr")` .

* Odkomentujte `@Html.Partial("_LoginPartial")` řádek kolem řádku (Obklopte řádek s `@*...*@` ). Další informace najdete v tématu [migrace ověřování a Identity ASP.NET Core](xref:migration/identity)

* Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).

* Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zobrazí web v prohlížeči. Měl by se teď správně načíst s očekávanými styly.

* *Volitelné:* Zkuste použít nový soubor rozložení. Zkopírujte soubor rozložení z projektu *FullAspNetCore* . Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Řešení chyb HTTP 500

Existuje mnoho problémů, které mohou způsobit chybové zprávy HTTP 500, které neobsahují žádné informace o zdroji problému. Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500. Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace. Podívejte se na příklad v následujícím kódu:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500. V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru. Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

Tento článek ukazuje, jak zahájit migraci projektu ASP.NET MVC na [ASP.NET Core mvc](xref:mvc/overview) 2,1. V tomto procesu se zvýrazní spousta věcí, které se změnily z ASP.NET MVC. Migrace z ASP.NET MVC je proces s více kroky. Tento článek popisuje:

* Počáteční nastavení
* Základní řadiče a zobrazení
* Statický obsah
* Závislosti na straně klienta.

Informace o migraci konfigurace a Identity kódu najdete v tématu [migrace konfigurace do ASP.NET Core](xref:migration/configuration) a [migrace ověřování a Identity ASP.NET Core](xref:migration/identity).

> [!NOTE]
> Čísla verzí v ukázkách nemusí být aktuální, aktualizujte projekty odpovídajícím způsobem.

## <a name="create-the-starter-aspnet-mvc-project"></a>Vytvoření projektu počáteční ASP.NET MVC

K předvedení upgradu začneme vytvořením aplikace ASP.NET MVC. Vytvořte ho s názvem *WebApp1* , aby obor názvů odpovídal ASP.NET Coremu projektu vytvořenému v dalším kroku.

![Dialogové okno Nový projekt sady Visual Studio](mvc/_static/new-project.png)

![Dialogové okno Nová webová aplikace: šablona projektu MVC vybraná v panelu šablony ASP.NET](mvc/_static/new-project-select-mvc-template.png)

*Volitelné:* Změňte název řešení z *WebApp1* na *Mvc5*. Visual Studio zobrazí nový název řešení (*Mvc5*), který usnadňuje podávání tohoto projektu z dalšího projektu.

## <a name="create-the-aspnet-core-project"></a>Vytvoření projektu ASP.NET Core

Vytvořte novou *prázdnou* ASP.NET Core webovou aplikaci se stejným názvem jako předchozí projekt (*WebApp1*), aby se obory názvů ve dvou projektech shodovaly. Stejný obor názvů usnadňuje kopírování kódu mezi dvěma projekty. Tento projekt vytvořte v jiném adresáři než předchozí projekt, abyste mohli použít stejný název.

![Dialogové okno Nový projekt](mvc/_static/new_core.png)

![Dialog nové webové aplikace v ASP.NET: prázdná šablona projektu vybraná na panelu šablon ASP.NET Core](mvc/_static/new-project-select-empty-aspnet5-template.png)

* *Volitelné:* Vytvořte novou aplikaci ASP.NET Core pomocí šablony projektu *webové aplikace* . Pojmenujte projekt *WebApp1*a vyberte možnost ověřování **jednotlivých uživatelských účtů**. Přejmenujte tuto aplikaci na *FullAspNetCore*. Při vytváření tohoto projektu se šetří čas v převodu. Konečný výsledek lze zobrazit v kódu generovaném šablonou, kód může být zkopírován do projektu pro převod nebo v porovnání s projektem generovaným šablonou.

## <a name="configure-the-site-to-use-mvc"></a>Konfigurace lokality pro použití MVC

* Při cílení na .NET Core se ve výchozím nastavení odkazuje na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) . Tento balíček obsahuje balíčky běžně používané aplikacemi MVC. Pokud cílíte .NET Framework, musí být odkazy na balíček uvedeny jednotlivě v souboru projektu.

`Microsoft.AspNetCore.Mvc`je ASP.NET Core architekturu MVC. `Microsoft.AspNetCore.StaticFiles`je obslužná rutina statického souboru. Aplikace ASP.NET Core výslovně přihlašovat pro middleware, například pro obsluhu statických souborů. Další informace najdete v tématu [statické soubory](xref:fundamentals/static-files).

* Otevřete soubor *Startup.cs* a změňte kód tak, aby odpovídal následujícímu:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>Metoda rozšíření přidá obslužnou rutinu statického souboru. `UseMvc`Metoda rozšíření přidává směrování. Další informace najdete v tématu [spuštění](xref:fundamentals/startup) a [Směrování](xref:fundamentals/routing)aplikace.

## <a name="add-a-controller-and-view"></a>Přidat kontroler a zobrazení

V této části se přidávají minimální kontroler a zobrazení, které slouží jako zástupné symboly pro kontroler MVC ASP.NET a zobrazení migrované v další části.

* Přidejte adresář *řadičů* .

* Přidejte do adresáře *Controllers* **třídu controller** s názvem *HomeController.cs* .

![Dialogové okno Přidat novou položku](mvc/_static/add_mvc_ctl.png)

* Přidejte adresář *zobrazení* .

* Přidání *zobrazení/domovského* adresáře

* Přidejte ** Razor zobrazení** s názvem *index. cshtml* do *zobrazení/domovského* adresáře.

![Dialogové okno Přidat novou položku](mvc/_static/view.png)

Struktura projektu je zobrazena níže:

![Průzkumník řešení zobrazení souborů a adresářů WebApp1](mvc/_static/project-structure-controller-view.png)

Obsah souboru *views/Home/index. cshtml* nahraďte následujícím kódem:

```html
<h1>Hello world!</h1>
```

Spusťte aplikaci.

![Webová aplikace otevřená v Microsoft Edge](mvc/_static/hello-world.png)

Další informace najdete v tématu [řadiče](xref:mvc/controllers/actions) a [zobrazení](xref:mvc/views/overview).

Následující funkce vyžadují migraci z ukázkového projektu ASP.NET MVC do projektu ASP.NET Core:

* obsah na straně klienta (šablony stylů CSS, písma a skripty)

* kontrolery

* zobrazení

* modely

* sdružování

* filtry

* Přihlaste se nebo se přihlaste Identity (to se provádí v dalším kurzu).

## <a name="controllers-and-views"></a>Řadiče a zobrazení

* Zkopírujte každou z metod z ASP.NET MVC `HomeController` do nové `HomeController` . V ASP.NET MVC je jako návratový typ metody typu "předdefinovaná šablona" [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ve ASP.NET Core MVC se místo toho vrací metody akcí `IActionResult` . `ActionResult`implementuje `IActionResult` , takže není nutné měnit návratový typ metod akce.

* Zkopírujte soubory *About. cshtml*, *Contact. cshtml*a *index. cshtml* Razor zobrazení z projektu ASP.NET MVC do projektu ASP.NET Core.

## <a name="test-each-method"></a>Testování jednotlivých metod

Soubor rozložení a styly nebyly dosud migrovány, takže vykreslená zobrazení obsahují pouze obsah v zobrazení souborů. Soubory rozložení vygenerované odkazy pro `About` zobrazení a `Contact` nebudou zatím k dispozici.

* Vyvolání vygenerovaných zobrazení z prohlížeče na běžící aplikaci ASP.NET Core nahraďte aktuální číslo portu číslem portu použitým v projektu ASP.NET Core. Příklad: `https://localhost:44375/home/about`.

![Stránka kontaktu](mvc/_static/contact-page.png)

Všimněte si nedostatku stylů a položek nabídky. Styl bude opraven v následující části.

## <a name="static-content"></a>Statický obsah

V ASP.NET MVC 5 a starších byl statický obsah hostovaný z kořenového adresáře webového projektu a byl vzájemně promíchán se soubory na straně serveru. V ASP.NET Core je statický obsah hostovaný v adresáři *wwwroot* . Zkopírujte statický obsah z aplikace ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core. V tomto ukázkovém převodu:

* Zkopírujte soubor *favicon. ico* z projektu ASP.NET MVC do adresáře *wwwroot* v projektu ASP.NET Core.

Projekt ASP.NET MVC používá pro svůj styl [bootstrap](https://getbootstrap.com/) a ukládá spouštěcí soubory do adresářů *obsahu* a *skriptů* . Šablona, která vygenerovala projekt ASP.NET MVC, odkazuje na Bootstrap v souboru rozložení (*views/Shared/_Layout. cshtml*). Soubory *bootstrap.js* a *bootstrap. CSS* mohou být zkopírovány z projektu ASP.NET MVC do adresáře *wwwroot* v novém projektu. Místo toho tento dokument přidá podporu pro Bootstrap (a další knihovny na straně klienta) pomocí sítě CDN v následující části.

## <a name="migrate-the-layout-file"></a>Migrovat soubor rozložení

* Zkopírujte soubor *_ViewStart. cshtml* z adresáře *zobrazení* projektu ASP.NET MVC do adresáře *zobrazení* projektu ASP.NET Core. Soubor *_ViewStart. cshtml* se v ASP.NET Core MVC nezměnil.

* Vytvořte *zobrazení/sdílený* adresář.

* *Volitelné:* Zkopírujte *_ViewImports. cshtml* z adresáře *zobrazení* projektu *FullAspNetCore* MVC do adresáře *zobrazení* projektu ASP.NET Core. Odeberte všechny deklarace oboru názvů v souboru *_ViewImports. cshtml* . Soubor *_ViewImports. cshtml* poskytuje obory názvů pro všechny soubory zobrazení a přináší [pomocníkům značek](xref:mvc/views/tag-helpers/intro). V novém souboru rozložení se používají pomocníky značek. Soubor *_ViewImports. cshtml* je pro ASP.NET Core nový.

* Zkopírujte soubor *_Layout. cshtml* z *zobrazení/sdíleného* adresáře projektu ASP.NET MVC do *zobrazení/* sdíleného adresáře ASP.NET Core projektu.

Otevřete soubor *_Layout. cshtml* a proveďte následující změny (dokončený kód je zobrazen níže):

* Nahraďte `@Styles.Render("~/Content/css")` `<link>` elementem pro načtení *bootstrap. CSS* (viz níže).

* Odebrat `@Scripts.Render("~/bundles/modernizr")` .

* Odkomentujte `@Html.Partial("_LoginPartial")` řádek kolem řádku (Obklopte řádek s `@*...*@` ). Další informace najdete v tématu [migrace ověřování a Identity ASP.NET Core](xref:migration/identity)

* Nahraďte `@Scripts.Render("~/bundles/jquery")` `<script>` elementem (viz níže).

* Nahraďte `@Scripts.Render("~/bundles/bootstrap")` `<script>` elementem (viz níže).

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

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

Zobrazí web v prohlížeči. Měl by se teď správně načíst s očekávanými styly.

* *Volitelné:* Zkuste použít nový soubor rozložení. Zkopírujte soubor rozložení z projektu *FullAspNetCore* . Nový soubor rozložení používá [pomocníky značek](xref:mvc/views/tag-helpers/intro) a má další vylepšení.

## <a name="configure-bundling-and-minification"></a>Konfigurace sdružování a minifikace

Informace o tom, jak nakonfigurovat sdružování a minifikace, najdete v článku [sdružování a minifikace](xref:client-side/bundling-and-minification).

## <a name="solve-http-500-errors"></a>Řešení chyb HTTP 500

Existuje mnoho problémů, které mohou způsobit chybové zprávy HTTP 500, které neobsahují žádné informace o zdroji problému. Například pokud soubor *views/_ViewImports. cshtml* obsahuje obor názvů, který v projektu neexistuje, je vygenerována chyba HTTP 500. Ve výchozím nastavení se v ASP.NET Core aplikace `UseDeveloperExceptionPage` rozšíření přidá do `IApplicationBuilder` a spustí se při *vývoji*konfigurace. Podívejte se na příklad v následujícím kódu:

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

ASP.NET Core převede neošetřené výjimky na chybové odpovědi HTTP 500. V těchto odpovědích obvykle nejsou podrobnosti o chybě zahrnuty, aby nedocházelo k odhalení potenciálně citlivých informací o serveru. Další informace najdete na [stránce s výjimkou vývojáře](xref:fundamentals/error-handling#developer-exception-page).

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
