---
title: Přidání zobrazení do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidání zobrazení do jednoduché ASP.NET Core aplikace MVC
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 1c29b59f9306774316ff37eeb57cc441fe5c7370
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820079"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Přidání zobrazení do ASP.NET Core aplikace MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

V této části upravíte `HelloWorldController` třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor. Šablony zobrazení založené na Razor mají příponu *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. `Index` Ve třídě nahraďte `Index`metodunásledujícímkódem: `HelloWorldController`

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (označované také *jako metody akcí*), jako je `Index` například metoda výše, obecně vracejí <xref:Microsoft.AspNetCore.Mvc.IActionResult> výjimku (nebo třídu odvozenou <xref:Microsoft.AspNetCore.Mvc.ActionResult>od), nikoli typ, `string`jako je například.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat **zobrazení Razor**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Index` Přidejte zobrazení`HelloWorldController`pro.

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V **nový soubor** dialogové okno:

  * V levém podokně vyberte **Web** .
  * V prostředním podokně vyberte **prázdný soubor HTML** .
  * Do pole **název** zadejte *index. cshtml* .
  * Vyberte **Nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení */souboru Hello/index. cshtml* Razor si nahraďte následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda v nepříliš velkém rozsahu spustila příkaz `return View();`, který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. `HelloWorldController` `Index` Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda (`Index`), takže v */views/HelloWorld/index.cshtml* se používá. Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. `@RenderBody()` Najděte řádek. `RenderBody`je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se `RenderBody` vykreslí v rámci metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

Obsah souboru *Views\Shared\_layout. cshtml* nahraďte následujícím kódem. Změny jsou zvýrazněny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Předchozí kód provedl následující změny:

* 3 výskyty `MvcMovie` do `Movie App`.
* Prvek `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` ukotvení k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

V předchozím kódu `asp-area=""` byl vynechán [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a hodnota atributu, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

**Poznámka:** `Movies` Kontroler nebyl implementován. V tomto okamžiku `Movie App` není odkaz funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```HTML
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */Shared/_Layout. cshtml* . Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo `null` ji lze nastavit tak, aby se nepoužil žádný soubor rozložení. `Layout`

Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.

`ViewData["Title"] = "Movie List";`ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů". Vlastnost se používá `<title>` v prvku HTML na stránce rozložení: `Title`

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld`. Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se `ViewData["Title"]` sadou, kterou jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Obsah v šabloně zobrazení *index. cshtml* se sloučí s šablonou zobrazení views */Shared/_Layout. cshtml* . Do prohlížeče se pošle jedna odpověď HTML. Šablony rozložení usnadňují provádění změn, které se vztahují na všechny stránky v aplikaci. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčeným postupem: Šablony zobrazení by neměly provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné `name` době `Welcome` metoda ve `ID` třídě přebírá a parametr a následně výstup hodnot přímo do prohlížeče. `HelloWorldController` Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, `ViewData` ve slovníku, ke kterému může přistupovat šablona zobrazení.

V *HelloWorldController.cs*změňte `Welcome` `NumTimes` `ViewData` metodu tak, aby se do slovníku přidala hodnota a.`Message` Slovník je dynamický objekt, což znamená, že `ViewData` lze použít jakýkoliv typ; objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. `ViewData` [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes`. Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad přístupem `ViewData` ke slovníku. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)Další
> [](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V této části upravíte `HelloWorldController` třídu tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor. Šablony zobrazení založené na Razor mají příponu *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. `Index` Ve třídě nahraďte `Index`metodunásledujícímkódem: `HelloWorldController`

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (označované také *jako metody akcí*), jako je `Index` například metoda výše, obecně vracejí <xref:Microsoft.AspNetCore.Mvc.IActionResult> výjimku (nebo třídu odvozenou <xref:Microsoft.AspNetCore.Mvc.ActionResult>od), nikoli typ, `string`jako je například.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat **zobrazení Razor**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Index` Přidejte zobrazení`HelloWorldController`pro.

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V **nový soubor** dialogové okno:

  * V levém podokně vyberte **Web** .
  * V prostředním podokně vyberte **prázdný soubor HTML** .
  * Do pole **název** zadejte *index. cshtml* .
  * Vyberte **Nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení */souboru Hello/index. cshtml* Razor si nahraďte následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda v nepříliš velkém rozsahu spustila příkaz `return View();`, který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. `HelloWorldController` `Index` Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda (`Index`), takže v */views/HelloWorld/index.cshtml* se používá. Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. `@RenderBody()` Najděte řádek. `RenderBody`je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se `RenderBody` vykreslí v rámci metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

* V části nadpis a zápatí změňte `MvcMovie` na. `Movie App`
* Změňte element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` kotvy na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Následující kód ukazuje zvýrazněné změny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

V předchozím kódu byl `asp-area` [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Poznámka:** `Movies` Kontroler nebyl implementován. V tomto okamžiku `Movie App` není odkaz funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```HTML
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */Shared/_Layout. cshtml* . Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo `null` ji lze nastavit tak, aby se nepoužil žádný soubor rozložení. `Layout`

Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.

`ViewData["Title"] = "Movie List";`ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů". Vlastnost se používá `<title>` v prvku HTML na stránce rozložení: `Title`

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld`. Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se `ViewData["Title"]` sadou, kterou jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Všimněte si také, jak byl obsah v šabloně zobrazení *index. cshtml* sloučen se šablonou zobrazení *zobrazení/Shared/_Layout. cshtml* a že do prohlížeče byla odeslána jedna odpověď HTML. Šablony rozložení umožňují snadno provádět změny, které se vztahují na všechny stránky aplikace. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčeným postupem: Šablony zobrazení by neměly provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné `name` době `Welcome` metoda ve `ID` třídě přebírá a parametr a následně výstup hodnot přímo do prohlížeče. `HelloWorldController` Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, `ViewData` ve slovníku, ke kterému může přistupovat šablona zobrazení.

V *HelloWorldController.cs*změňte `Welcome` `NumTimes` `ViewData` metodu tak, aby se do slovníku přidala hodnota a.`Message` Slovník je dynamický objekt, což znamená, že `ViewData` lze použít jakýkoliv typ; objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. `ViewData` [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes`. Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad přístupem `ViewData` ke slovníku. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)Další
> [](adding-model.md)

::: moniker-end
