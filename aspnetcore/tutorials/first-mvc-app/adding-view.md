---
title: Přidání zobrazení do aplikace core MVC ASP.NET
author: rick-anderson
description: Přidání zobrazení do jednoduché aplikace ASP.NET Core MVC
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660207"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Přidání zobrazení do aplikace core MVC ASP.NET

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

V této části `HelloWorldController` upravíte třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistému zapouzdření procesu generování odpovědí HTML klientovi.

Soubor šablony zobrazení vytvoříte pomocí razor. Šablony zobrazení založené na holicích strojky mají příponu *.cshtml.* Poskytují elegantní způsob, jak vytvořit výstup HTML s C#.

V současné `Index` době metoda vrátí řetězec se zprávou, která je pevně zakódována ve třídě kontroleru. Ve `HelloWorldController` třídě nahraďte metodu `Index` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu řadiče. Používá šablonu zobrazení ke generování odpovědi HTML. Metody kontroleru (označované také jako *metody akce*), například výše `Index` uvedená metoda, obecně vrátí (nebo <xref:Microsoft.AspNetCore.Mvc.IActionResult> třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu. `string`

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním horním poli zadejte *zobrazení*

  * Vybrat **zobrazení břitva**

  * Zachovat hodnotu pole **Název,** *Index.cshtml*.

  * Vyberte **Přidat**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte `Index` zobrazení `HelloWorldController`pro .

* Přidejte novou složku s názvem *Zobrazení/HelloWorld*.
* Přidejte nový soubor do názvu složky *Views/HelloWorld* *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > nový soubor**.
* V dialogovém okně **Nový soubor:**

  * V levém podokně vyberte **jádro technologie ASP .NET** Core.
  * V prostředním podokně vyberte **Zobrazit stránku MVC.**
  * Do pole **Název** zadejte *Rejstřík.*
  * Vyberte **nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Nahraďte obsah souboru *zobrazení Zobrazení/HelloWorld/Index.cshtml* holicí strojek následujícím:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` v `HelloWorldController` neudělal moc; spustil příkaz `return View();`, který specifikoval, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi na prohlížeč. Vzhledem k tomu, že nebyl zadán název souboru šablony zobrazení, mvc výchozí použití výchozího souboru zobrazení. Výchozí soubor zobrazení má stejný název`Index`jako metoda ( ), takže v */Views/HelloWorld/Index.cshtml* se používá. Na obrázku níže je zobrazen řetězec "Dobrý den, z našeho zobrazit šablony!" pevně zakódované v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změna zobrazení a stránek rozložení

Vyberte odkazy na nabídky (**MvcMovie**, **Home**a **Privacy**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Zobrazení/Sdílené/_Layout.cshtml.* Otevřete soubor *Zobrazení/Sdílené/_Layout.cshtml.*

[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu. Najděte `@RenderBody()` linku. `RenderBody`je zástupný symbol, ve kterém se zobrazují všechny stránky specifické pro zobrazení, které vytvoříte, *zabalené* na stránce rozložení. Pokud například vyberete odkaz **Ochrana osobních údajů,** zobrazení **Zobrazení/Domů/Privacy.cshtml** se vykreslí uvnitř `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna odkazu na název, zápatí a nabídky v souboru rozložení

Nahraďte obsah souboru *Views/Shared/_Layout.cshtml* následujícími značkami. Změny jsou zvýrazněny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Předchozí značky provedly následující změny:

* 3 výskyty `MvcMovie` `Movie App`až .
* Kotevní prvek `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

V předchozích značkách `asp-area=""` byl vynechán atribut a hodnota atributu [Anchor Tag Helper,](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

**Poznámka:** `Movies` Řadič nebyl implementován. V tomto okamžiku `Movie App` odkaz není funkční.

Uložte změny a vyberte odkaz **Soukromí.** Všimněte si, jak název na kartě prohlížeče zobrazuje **Zásady ochrany osobních údajů - Aplikace Film** místo **Zásady ochrany osobních údajů - Mvc Movie**:

![Karta Ochrana osobních údajů](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a všimněte si, že název a kotva text také zobrazit **Film App**. Byli jsme schopni provést změnu jednou v šabloně rozložení a všechny stránky na webu odrážejí nový text odkazu a nový název.

Prohlédněte si soubor *Views/_ViewStart.cshtml:*

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *Views/_ViewStart.cshtml* přináší do každého zobrazení soubor *Zobrazení/Shared/_Layout.cshtml.* Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení `null` nebo k jeho nastavení tak, aby nebyl použit žádný soubor rozložení.

Změňte název `<h2>` a prvek souboru *zobrazení/HelloWorld/Index.cshtml:*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` prvek se mírně liší, takže můžete vidět, který bit kódu změní zobrazení.

`ViewData["Title"] = "Movie List";`ve výše uvedeném `Title` kódu `ViewData` nastaví vlastnost slovníku na "Seznam filmů". Vlastnost `Title` se používá `<title>` v elementu HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu `https://localhost:{PORT}/HelloWorld`a přejděte na . Všimněte si, že název prohlížeče, primární nadpis a sekundární nadpisy se změnily. (Pokud změny v prohlížeči nevidíte, je možné, že si prohlížíte obsah uložený v mezipaměti. Stisknutím kláves Ctrl+F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče je `ViewData["Title"]` vytvořen s jsme nastavit v šabloně zobrazení *Index.cshtml* a další "- Movie App" přidán do souboru rozložení.

Obsah v šabloně zobrazení *Index.cshtml* je sloučen se šablonou *zobrazení Zobrazení/Sdílené/_Layout.cshtml.* Do prohlížeče je odeslána jedna odpověď HTML. Šablony rozložení usnadňují provádění změn, které se použijí na všech stránkách aplikace. Další informace naleznete v [tématu Layout](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý kousek "data" (v tomto případě "Dobrý den, z našeho zobrazení šablony!" zpráva) je však pevně zakódována. Aplikace MVC má "V" (zobrazení) a máte "C" (řadič), ale zatím žádné "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z řadiče do zobrazení

Akce kontroleru jsou vyvolány v reakci na příchozí požadavek url. Třída kontroleru je místo, kde je napsán kód, který zpracovává příchozí požadavky prohlížeče. Správce načte data ze zdroje dat a rozhodne, jaký typ odpovědi má být odeslán zpět do prohlížeče. Zobrazit šablony lze použít z řadiče pro generování a formátování odpovědi HTML do prohlížeče.

Správci jsou zodpovědní za poskytnutí údajů požadovaných k vytvoření šablony zobrazení k vykreslení odpovědi. Osvědčený postup: Zobrazit šablony by **neměly** provádět obchodní logiku nebo pracovat přímo s databází. Šablona zobrazení by měla fungovat pouze s daty, která jí poskytuje kontrolor. Udržování tohoto "oddělení obavy" pomáhá udržovat kód čistý, testovatelný a udržovatelný.

V současné `Welcome` době metoda `HelloWorldController` ve `name` třídě `ID` trvá a a parametr a pak výstupy hodnoty přímo do prohlížeče. Spíše než mít řadič vykreslit tuto odpověď jako řetězec, změňte řadič použít šablonu zobrazení místo. Šablona zobrazení generuje dynamickou odpověď, což znamená, že příslušné bity dat musí být předány z řadiče do zobrazení, aby bylo možné generovat odpověď. To tím, že řadič umístit dynamická data (parametry), `ViewData` které šablona zobrazení potřebuje ve slovníku, který šablona zobrazení pak přístup.

V *HelloWorldController.cs*změňte `Welcome` metodu `Message` `NumTimes` pro přidání `ViewData` a hodnoty do slovníku. Slovník `ViewData` je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj něco nevložíte. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry ve vaší metodě. Kompletní *soubor HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` slovníku obsahuje data, která budou předána do zobrazení.

Vytvořte úvodní šablonu zobrazení s názvem *Zobrazení/HelloWorld/Welcome.cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome.cshtml,* která zobrazuje "Hello" `NumTimes`. Nahraďte obsah *zobrazení/HelloWorld/Welcome.cshtml* následujícím:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou převzata z adresy URL a předána kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Řadič zabalí data `ViewData` do slovníku a předá tento objekt do zobrazení. Zobrazení pak vykreslí data jako HTML do prohlížeče.

![Zobrazení ochrany osobních údajů zobrazující uvítací štítek a frázi Hello Rick zobrazenou čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Ve výše uvedeném `ViewData` příkladu byl slovník použit k předání dat z řadiče do zobrazení. Později v kurzu se model zobrazení používá k předání dat z řadiče do zobrazení. Přístup modelu zobrazení k předávání dat je `ViewData` obecně velmi upřednostňován před slovníkovým přístupem. Další informace naleznete v tématu [When to use ViewBag, ViewData nebo TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)

V dalším kurzu je vytvořena databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)
> [další](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V této části `HelloWorldController` upravíte třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistému zapouzdření procesu generování odpovědí HTML klientovi.

Soubor šablony zobrazení vytvoříte pomocí razor. Šablony zobrazení založené na holicích strojky mají příponu *.cshtml.* Poskytují elegantní způsob, jak vytvořit výstup HTML s C#.

V současné `Index` době metoda vrátí řetězec se zprávou, která je pevně zakódována ve třídě kontroleru. Ve `HelloWorldController` třídě nahraďte metodu `Index` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu řadiče. Používá šablonu zobrazení ke generování odpovědi HTML. Metody kontroleru (označované také jako *metody akce*), například výše `Index` uvedená metoda, obecně vrátí (nebo <xref:Microsoft.AspNetCore.Mvc.IActionResult> třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu. `string`

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním horním poli zadejte *zobrazení*

  * Vybrat **zobrazení břitva**

  * Zachovat hodnotu pole **Název,** *Index.cshtml*.

  * Vyberte **Přidat**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte `Index` zobrazení `HelloWorldController`pro .

* Přidejte novou složku s názvem *Zobrazení/HelloWorld*.
* Přidejte nový soubor do názvu složky *Views/HelloWorld* *Index.cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na složku *Zobrazení* a potom **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem myši na složku *Zobrazení/HelloWorld* a potom **na přidat > nový soubor**.
* V dialogovém okně **Nový soubor:**

  * V levém podokně vyberte **Web.**
  * V prostředním podokně vyberte **Vyprázdnit soubor HTML.**
  * Do pole Název zadejte *Soubor Index.cshtml.* **Name**
  * Vyberte **nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Nahraďte obsah souboru *zobrazení Zobrazení/HelloWorld/Index.cshtml* holicí strojek následujícím:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` v `HelloWorldController` neudělal moc; spustil příkaz `return View();`, který specifikoval, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi na prohlížeč. Vzhledem k tomu, že nebyl zadán název souboru šablony zobrazení, mvc výchozí použití výchozího souboru zobrazení. Výchozí soubor zobrazení má stejný název`Index`jako metoda ( ), takže v */Views/HelloWorld/Index.cshtml* se používá. Na obrázku níže je zobrazen řetězec "Dobrý den, z našeho zobrazit šablony!" pevně zakódované v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změna zobrazení a stránek rozložení

Vyberte odkazy na nabídky (**MvcMovie**, **Home**a **Privacy**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *Zobrazení/Sdílené/_Layout.cshtml.* Otevřete soubor *Zobrazení/Sdílené/_Layout.cshtml.*

[Šablony rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejnerů HTML webu na jednom místě a pak ho použít na více stránkách webu. Najděte `@RenderBody()` linku. `RenderBody`je zástupný symbol, ve kterém se zobrazují všechny stránky specifické pro zobrazení, které vytvoříte, *zabalené* na stránce rozložení. Pokud například vyberete odkaz **Ochrana osobních údajů,** zobrazení **Zobrazení/Domů/Privacy.cshtml** se vykreslí uvnitř `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna odkazu na název, zápatí a nabídky v souboru rozložení

* V elementech title a `MvcMovie` footer změňte na `Movie App`.
* Změňte prvek `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`ukotvení na .

Následující značky zobrazují zvýrazněné změny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

V předchozích značkách `asp-area` byl atribut [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Poznámka:** `Movies` Řadič nebyl implementován. V tomto okamžiku `Movie App` odkaz není funkční.

Uložte změny a vyberte odkaz **Soukromí.** Všimněte si, jak název na kartě prohlížeče zobrazuje **Zásady ochrany osobních údajů - Aplikace Film** místo **Zásady ochrany osobních údajů - Mvc Movie**:

![Karta Ochrana osobních údajů](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a všimněte si, že název a kotva text také zobrazit **Film App**. Byli jsme schopni provést změnu jednou v šabloně rozložení a všechny stránky na webu odrážejí nový text odkazu a nový název.

Prohlédněte si soubor *Views/_ViewStart.cshtml:*

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *Views/_ViewStart.cshtml* přináší do každého zobrazení soubor *Zobrazení/Shared/_Layout.cshtml.* Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení `null` nebo k jeho nastavení tak, aby nebyl použit žádný soubor rozložení.

Změňte název `<h2>` a prvek souboru *zobrazení/HelloWorld/Index.cshtml:*

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` prvek se mírně liší, takže můžete vidět, který bit kódu změní zobrazení.

`ViewData["Title"] = "Movie List";`ve výše uvedeném `Title` kódu `ViewData` nastaví vlastnost slovníku na "Seznam filmů". Vlastnost `Title` se používá `<title>` v elementu HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu `https://localhost:{PORT}/HelloWorld`a přejděte na . Všimněte si, že název prohlížeče, primární nadpis a sekundární nadpisy se změnily. (Pokud změny v prohlížeči nevidíte, je možné, že si prohlížíte obsah uložený v mezipaměti. Stisknutím kláves Ctrl+F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče je `ViewData["Title"]` vytvořen s jsme nastavit v šabloně zobrazení *Index.cshtml* a další "- Movie App" přidán do souboru rozložení.

Všimněte si také, jak byl obsah v šabloně zobrazení *Index.cshtml* sloučen se šablonou *zobrazení Zobrazení/Sdílené/_Layout.cshtml* a do prohlížeče byla odeslána jedna odpověď HTML. Šablony rozložení usnadňují provádění změn, které se použijí na všech stránkách aplikace. Další informace naleznete v [tématu Rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý kousek "data" (v tomto případě "Dobrý den, z našeho zobrazení šablony!" zpráva) je však pevně zakódována. Aplikace MVC má "V" (zobrazení) a máte "C" (řadič), ale zatím žádné "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z řadiče do zobrazení

Akce kontroleru jsou vyvolány v reakci na příchozí požadavek url. Třída kontroleru je místo, kde je napsán kód, který zpracovává příchozí požadavky prohlížeče. Správce načte data ze zdroje dat a rozhodne, jaký typ odpovědi má být odeslán zpět do prohlížeče. Zobrazit šablony lze použít z řadiče pro generování a formátování odpovědi HTML do prohlížeče.

Správci jsou zodpovědní za poskytnutí údajů požadovaných k vytvoření šablony zobrazení k vykreslení odpovědi. Osvědčený postup: Zobrazit šablony by **neměly** provádět obchodní logiku nebo pracovat přímo s databází. Šablona zobrazení by měla fungovat pouze s daty, která jí poskytuje kontrolor. Udržování tohoto "oddělení obavy" pomáhá udržovat kód čistý, testovatelný a udržovatelný.

V současné `Welcome` době metoda `HelloWorldController` ve `name` třídě `ID` trvá a a parametr a pak výstupy hodnoty přímo do prohlížeče. Spíše než mít řadič vykreslit tuto odpověď jako řetězec, změňte řadič použít šablonu zobrazení místo. Šablona zobrazení generuje dynamickou odpověď, což znamená, že příslušné bity dat musí být předány z řadiče do zobrazení, aby bylo možné generovat odpověď. To tím, že řadič umístit dynamická data (parametry), `ViewData` které šablona zobrazení potřebuje ve slovníku, který šablona zobrazení pak přístup.

V *HelloWorldController.cs*změňte `Welcome` metodu `Message` `NumTimes` pro přidání `ViewData` a hodnoty do slovníku. Slovník `ViewData` je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj něco nevložíte. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry ve vaší metodě. Kompletní *soubor HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` slovníku obsahuje data, která budou předána do zobrazení.

Vytvořte úvodní šablonu zobrazení s názvem *Zobrazení/HelloWorld/Welcome.cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome.cshtml,* která zobrazuje "Hello" `NumTimes`. Nahraďte obsah *zobrazení/HelloWorld/Welcome.cshtml* následujícím:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou převzata z adresy URL a předána kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Řadič zabalí data `ViewData` do slovníku a předá tento objekt do zobrazení. Zobrazení pak vykreslí data jako HTML do prohlížeče.

![Zobrazení ochrany osobních údajů zobrazující uvítací štítek a frázi Hello Rick zobrazenou čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

Ve výše uvedeném `ViewData` příkladu byl slovník použit k předání dat z řadiče do zobrazení. Později v kurzu se model zobrazení používá k předání dat z řadiče do zobrazení. Přístup modelu zobrazení k předávání dat je `ViewData` obecně velmi upřednostňován před slovníkovým přístupem. Další informace naleznete v tématu [When to use ViewBag, ViewData nebo TempData.](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)

V dalším kurzu je vytvořena databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)
> [další](adding-model.md)

::: moniker-end
