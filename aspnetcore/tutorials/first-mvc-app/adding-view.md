---
title: Část 3 – přidání zobrazení do ASP.NET Core aplikace MVC
author: rick-anderson
description: Část 3 kurzu řady ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 970cc1fe6e0a0e0204ddafec09c15db6fed9aa66
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629753"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a>Část 3 – přidání zobrazení do ASP.NET Core aplikace MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

V této části upravíte `HelloWorldController` třídu pro použití [Razor](xref:mvc/views/razor) zobrazení souborů k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor . Razoršablony zobrazení založené na bázi mají příponu souboru *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době `Index` metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. Ve `HelloWorldController` třídě nahraďte `Index` metodu následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (označované také jako *metody akcí*), jako je například `Index` metoda výše, obecně vracejí výjimku <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou od <xref:Microsoft.AspNetCore.Mvc.ActionResult> ), nikoli typ, jako `string` je například.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat ** Razor zobrazení**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte `Index` zobrazení pro `HelloWorldController` .

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **ASP .NET Core** .
  * V prostředním podokně vyberte **stránku zobrazení MVC** .
  * Do pole **název** zadejte *index* .
  * Vyberte **Nové**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení souboru views/ *HelloWorld/index. cshtml* nahraďte Razor následujícím způsobem:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. `Index`Metoda v `HelloWorldController` nepříliš velkém rozsahu spustila příkaz `return View();` , který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda ( `Index` ), takže se použije šablona zobrazení v */views/HelloWorld/index.cshtml* . Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

Nahraďte obsah souborů *views/Shared/_Layout. cshtml* následujícím kódem. Změny jsou zvýrazněny:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Předchozí kód provedl následující změny:

* 3 výskyty `MvcMovie` do `Movie App` .
* Prvek ukotvení `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .

V předchozím kódu `asp-area=""` byl vynechán [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a hodnota atributu, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

**Poznámka**: `Movies` kontroler nebyl implementován. V tomto okamžiku není `Movie App` odkaz funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* . `Layout`Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo ji lze nastavit tak, aby se `null` nepoužil žádný soubor rozložení.

Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.

`ViewData["Title"] = "Movie List";` ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů". `Title`Vlastnost se používá v `<title>` prvku HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld` . Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se sadou, kterou `ViewData["Title"]` jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Obsah v šabloně zobrazení *index. cshtml* je sloučen s šablonou zobrazení View */shared/_Layout. cshtml* . Do prohlížeče se pošle jedna odpověď HTML. Šablony rozložení usnadňují provádění změn, které se vztahují na všechny stránky v aplikaci. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné době `Welcome` Metoda ve `HelloWorldController` třídě přebírá `name` a `ID` parametr a následně výstup hodnot přímo do prohlížeče. Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, ve `ViewData` slovníku, ke kterému může přistupovat šablona zobrazení.

V *HelloWorldController.cs*změňte `Welcome` metodu tak, aby se do `Message` slovníku přidala `NumTimes` hodnota a `ViewData` . `ViewData`Slovník je dynamický objekt, což znamená, že lze použít jakýkoliv typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry ( `name` a `numTimes` ) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData`Objekt Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes` . Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad `ViewData` přístupem ke slovníku. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md) 
>  [Další](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V této části upravíte `HelloWorldController` třídu pro použití [Razor](xref:mvc/views/razor) zobrazení souborů k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor . Razoršablony zobrazení založené na bázi mají příponu souboru *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době `Index` metoda vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. Ve `HelloWorldController` třídě nahraďte `Index` metodu následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá <xref:Microsoft.AspNetCore.Mvc.Controller.View*> metodu kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (označované také jako *metody akcí*), jako je například `Index` metoda výše, obecně vracejí výjimku <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou od <xref:Microsoft.AspNetCore.Mvc.ActionResult> ), nikoli typ, jako `string` je například.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat ** Razor zobrazení**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte `Index` zobrazení pro `HelloWorldController` .

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Web** .
  * V prostředním podokně vyberte **prázdný soubor HTML** .
  * Do pole **název** zadejte *index. cshtml* .
  * Vyberte **Nové**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení souboru views/ *HelloWorld/index. cshtml* nahraďte Razor následujícím způsobem:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. `Index`Metoda v `HelloWorldController` nepříliš velkém rozsahu spustila příkaz `return View();` , který určuje, že metoda má použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda ( `Index` ), takže v */views/HelloWorld/index.cshtml* se používá. Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **ochranu osobních údajů** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci `RenderBody` metody.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

* V části nadpis a zápatí změňte `MvcMovie` na `Movie App` .
* Změňte element kotvy `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` .

Následující kód ukazuje zvýrazněné změny:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

V předchozím kódu `asp-area` byl [atribut pomocník značek ukotvení](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Poznámka**: `Movies` kontroler nebyl implementován. V tomto okamžiku není `Movie App` odkaz funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* . `Layout`Vlastnost může být použita k nastavení jiného zobrazení rozložení nebo ji lze nastavit tak, aby se `null` nepoužil žádný soubor rozložení.

Změňte název a `<h2>` element zobrazení */Hello/index. cshtml* zobrazení souboru:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu se zobrazí.

`ViewData["Title"] = "Movie List";` ve výše uvedeném kódu nastaví `Title` vlastnost `ViewData` slovníku na "seznam filmů". `Title`Vlastnost se používá v `<title>` prvku HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld` . Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří se sadou, kterou `ViewData["Title"]` jsme nastavili v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Všimněte si také, jak byl obsah v šabloně zobrazení *index. cshtml* sloučen s šablonou zobrazení *zobrazení/shared/_Layout. cshtml* a že byla do prohlížeče odeslána jedna odpověď HTML. Šablony rozložení umožňují snadno provádět změny, které se vztahují na všechny stránky aplikace. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné době `Welcome` Metoda ve `HelloWorldController` třídě přebírá `name` a `ID` parametr a následně výstup hodnot přímo do prohlížeče. Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že řadič umístí dynamická data (parametry), která šablona zobrazení potřebuje, ve `ViewData` slovníku, ke kterému může přistupovat šablona zobrazení.

V *HelloWorldController.cs*změňte `Welcome` metodu tak, aby se do `Message` slovníku přidala `NumTimes` hodnota a `ViewData` . `ViewData`Slovník je dynamický objekt, což znamená, že lze použít jakýkoliv typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry ( `name` a `numTimes` ) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData`Objekt Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí text Hello `NumTimes` . Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do `ViewData` slovníku a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše `ViewData` byl slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňovaný nad `ViewData` přístupem ke slovníku. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md) 
>  [Další](adding-model.md)

::: moniker-end
