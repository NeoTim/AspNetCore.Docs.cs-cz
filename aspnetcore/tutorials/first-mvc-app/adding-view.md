---
title: Přidání zobrazení do ASP.NET Core aplikace MVC
author: rick-anderson
description: Přidání zobrazení do jednoduché ASP.NET Core aplikace MVC
ms.author: riande
ms.date: 8/04/2019
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5510fb6844452571ca764e21640f0bd16444c782
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660207"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Přidání zobrazení do ASP.NET Core aplikace MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

V této části upravíte třídu `HelloWorldController` tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor. Šablony zobrazení založené na Razor mají příponu *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době metoda `Index` vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. Ve třídě `HelloWorldController` nahraďte metodu `Index` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá metodu <xref:Microsoft.AspNetCore.Mvc.Controller.View*> kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (známé také jako *metody akcí*), jako je například metoda `Index` výše, obecně vracejí <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu, jako je `string`.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat **zobrazení Razor**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte zobrazení `Index` pro `HelloWorldController`.

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **ASP .NET Core** .
  * V prostředním podokně vyberte **stránku zobrazení MVC** .
  * Do pole **název** zadejte *index* .
  * Vyberte **Nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení */souboru Hello/index. cshtml* Razor si nahraďte následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` v `HelloWorldController` neudělala mnoho; spustil příkaz `return View();`, který určuje, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda (`Index`), takže v */views/HelloWorld/index.cshtml* se používá. Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **soukromí** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci metody `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

Nahraďte obsah souborů *views/Shared/_Layout. cshtml* následujícím kódem. Změny jsou zvýrazněny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

Předchozí kód provedl následující změny:

* 3 výskyty `MvcMovie` k `Movie App`.
* Prvek ukotvení `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

V předchozím kódu byl vynechán [pomocný atribut `asp-area=""` kotvicí značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) a hodnota atributu, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

**Poznámka**: kontroler `Movies` nebyl implementován. V tomto okamžiku není odkaz `Movie App` funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* . Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení nebo ji můžete nastavit na hodnotu `null`, takže nebude použit žádný soubor rozložení.

Změňte název a `<h2>` element zobrazení */HelloWorld/index. cshtml* zobrazit soubor:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu změnu zobrazení.

`ViewData["Title"] = "Movie List";` v kódu výše nastaví vlastnost `Title` slovníku `ViewData` na seznam filmů. Vlastnost `Title` se používá v prvku `<title>` HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld`. Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří pomocí `ViewData["Title"]` nastavíme v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Obsah v šabloně zobrazení *index. cshtml* je sloučen s šablonou zobrazení View */shared/_Layout. cshtml* . Do prohlížeče se pošle jedna odpověď HTML. Šablony rozložení usnadňují provádění změn, které se vztahují na všechny stránky v aplikaci. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné době metoda `Welcome` ve třídě `HelloWorldController` přebírá `name` a parametr `ID` a následně vypisuje hodnoty přímo do prohlížeče. Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že kontroler umístí dynamická data (parametry), která šablona zobrazení potřebuje ve slovníku `ViewData`, ke kterému bude mít přístup šablona zobrazení.

V *HelloWorldController.cs*změňte metodu `Welcome` tak, aby se do `ViewData` slovníku přidala hodnota `Message` a `NumTimes`. `ViewData` slovníku je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí `NumTimes`Hello. Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do slovníku `ViewData` a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše byl `ViewData` slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňován nad přístupem ke slovníku `ViewData`. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)
> [Další](adding-model.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V této části upravíte třídu `HelloWorldController` tak, aby používala soubory zobrazení [Razor](xref:mvc/views/razor) k čistě zapouzdření procesu generování odpovědí HTML na klienta.

Vytvoříte soubor šablony zobrazení pomocí Razor. Šablony zobrazení založené na Razor mají příponu *. cshtml* . Poskytují elegantní způsob, jak vytvořit výstup HTML pomocí C#.

V současné době metoda `Index` vrací řetězec se zprávou, která je pevně zakódována ve třídě Controller. Ve třídě `HelloWorldController` nahraďte metodu `Index` následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Předchozí kód volá metodu <xref:Microsoft.AspNetCore.Mvc.Controller.View*> kontroleru. Pomocí šablony zobrazení vygeneruje odpověď HTML. Metody kontroleru (známé také jako *metody akcí*), jako je například metoda `Index` výše, obecně vracejí <xref:Microsoft.AspNetCore.Mvc.IActionResult> (nebo třídu odvozenou z <xref:Microsoft.AspNetCore.Mvc.ActionResult>), nikoli typu, jako je `string`.

## <a name="add-a-view"></a>Přidání zobrazení

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na složku *views/HelloWorld* a **přidejte > novou položku**.

* V dialogovém okně **Přidat novou položku – MvcMovie**

  * Do vyhledávacího pole v pravém horním rohu zadejte *zobrazení.*

  * Vybrat **zobrazení Razor**

  * Ponechejte hodnotu pole **název** , *index. cshtml*.

  * Vyberte **Přidat**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidejte zobrazení `Index` pro `HelloWorldController`.

* Přidejte novou složku s názvem *views/HelloWorld*.
* Přidejte nový soubor do složky *views/HelloWorld* name *index. cshtml*.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku *zobrazení* a pak **přidejte > novou složku** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na složku *views/HelloWorld* a pak **přidejte > nový soubor**.
* V dialogovém okně **nový soubor** :

  * V levém podokně vyberte **Web** .
  * V prostředním podokně vyberte **prázdný soubor HTML** .
  * Do pole **název** zadejte *index. cshtml* .
  * Vyberte **Nový**.

![Dialogové okno Přidat novou položku](adding-view/_static/add_view_mac.png)

---

Obsah zobrazení */souboru Hello/index. cshtml* Razor si nahraďte následujícím způsobem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:{PORT}/HelloWorld`. Metoda `Index` v `HelloWorldController` neudělala mnoho; spustil příkaz `return View();`, který určuje, že metoda by měla použít soubor šablony zobrazení k vykreslení odpovědi do prohlížeče. Vzhledem k tomu, že není zadaný název souboru šablony zobrazení, MVC použije výchozí soubor zobrazení. Výchozí soubor zobrazení má stejný název jako metoda (`Index`), takže v */views/HelloWorld/index.cshtml* se používá. Následující obrázek ukazuje řetězec "Hello z naší šablony zobrazení". pevně zakódovaný v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a stránky rozložení

Vyberte odkazy nabídky (**MvcMovie**, **Home**a **Privacy**). Na každé stránce se zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementováno v souboru *views/Shared/_Layout. cshtml* . Otevřete soubor *views/Shared/_Layout. cshtml* .

Šablony [rozložení](xref:mvc/views/layout) umožňují určit rozložení kontejneru HTML webu na jednom místě a pak ho použít na více stránek na webu. Najděte `@RenderBody()` řádek. `RenderBody` je zástupný symbol, ve kterém se zobrazí všechny stránky specifické pro zobrazení, *zabalené* na stránce rozložení. Pokud například vyberete odkaz na **soukromí** , zobrazení **/Domů/soukromí. cshtml** se vykreslí v rámci metody `RenderBody`.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>Změna názvu, zápatí a odkazu v nabídce v souboru rozložení

* V části název a zápatí změňte `MvcMovie` na `Movie App`.
* Změňte prvek kotvy `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` na `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Následující kód ukazuje zvýrazněné změny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

V předchozím označení byl [pomocný atribut `asp-area` ukotvení značek](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) vynechán, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Poznámka**: kontroler `Movies` nebyl implementován. V tomto okamžiku není odkaz `Movie App` funkční.

Uložte změny a vyberte odkaz na **ochranu osobních údajů** . Všimněte si, jak se v nadpisu na kartě prohlížeč zobrazuje **Zásada ochrany osobních údajů – aplikace pro video** namísto **zásad ochrany osobních údajů – film MVC**:

![Karta soukromí](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte odkaz **Domů** a Všimněte si, že text nadpisu a kotvy také zobrazuje **filmovou aplikaci**. Provedli jsme změnu jednou v šabloně rozložení a všechny stránky v lokalitě odrážely nový text odkazu a nový nadpis.

Projděte si soubor *views/_ViewStart. cshtml* :

```cshtml
@{
    Layout = "_Layout";
}
```

Soubor *views/_ViewStart. cshtml* přináší do každého zobrazení soubor views */shared/_Layout. cshtml* . Vlastnost `Layout` lze použít k nastavení jiného zobrazení rozložení nebo ji můžete nastavit na hodnotu `null`, takže nebude použit žádný soubor rozložení.

Změňte název a `<h2>` element zobrazení */HelloWorld/index. cshtml* zobrazit soubor:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste viděli, který bit kódu změnu zobrazení.

`ViewData["Title"] = "Movie List";` v kódu výše nastaví vlastnost `Title` slovníku `ViewData` na seznam filmů. Vlastnost `Title` se používá v prvku `<title>` HTML na stránce rozložení:

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

Uložte změnu a přejděte na `https://localhost:{PORT}/HelloWorld`. Všimněte si, že se změnil název prohlížeče, primární nadpis a sekundární záhlaví. (Pokud nevidíte změny v prohlížeči, můžete zobrazit obsah uložený v mezipaměti. Stisknutím kombinace kláves CTRL + F5 v prohlížeči vynutíte načtení odpovědi ze serveru.) Název prohlížeče se vytvoří pomocí `ViewData["Title"]` nastavíme v šabloně zobrazení *index. cshtml* a další "-filmové aplikace" přidané v souboru rozložení.

Všimněte si také, jak byl obsah v šabloně zobrazení *index. cshtml* sloučen s šablonou zobrazení *zobrazení/shared/_Layout. cshtml* a že byla do prohlížeče odeslána jedna odpověď HTML. Šablony rozložení umožňují snadno provádět změny, které se vztahují na všechny stránky aplikace. Další informace najdete v tématu [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu filmů](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Náš malý bit "data" (v tomto případě "Hello z naší šablony zobrazení" zpráva) je pevně zakódována, i když. Aplikace MVC má "V" (zobrazení) a Vy máte "C" (Controller), ale zatím ne "M" (model).

## <a name="passing-data-from-the-controller-to-the-view"></a>Předávání dat z kontroleru do zobrazení

Akce kontroleru se vyvolají v reakci na příchozí požadavek adresy URL. Třída kontroleru je místo, kde je kód vytvořen, který zpracovává příchozí požadavky prohlížeče. Kontroler načítá data ze zdroje dat a rozhoduje o tom, jaký typ reakce se má zpět do prohlížeče poslat zpátky. Šablony zobrazení lze použít z kontroleru k vygenerování a formátování odpovědi HTML do prohlížeče.

Řadiče jsou zodpovědné za poskytování dat vyžadovaných pro šablonu zobrazení, aby vygenerovala odpověď. Osvědčený postup: šablony **zobrazení by neměly** provádět obchodní logiku ani pracovat s databází přímo. Místo toho by šablona zobrazení měla fungovat jenom s daty, která mu poskytl kontroler. Udržování tohoto "oddělení obav" pomáhá udržet kód čistě, testovatelné a udržovatelný.

V současné době metoda `Welcome` ve třídě `HelloWorldController` přebírá `name` a parametr `ID` a následně vypisuje hodnoty přímo do prohlížeče. Místo toho, aby kontroler tuto odpověď vygeneroval jako řetězec, změňte řadič tak, aby místo něj používal šablonu zobrazení. Šablona zobrazení vygeneruje dynamickou odpověď, což znamená, že pro vygenerování odpovědi musí být z kontroleru předány vhodné bity dat. Provedete to tak, že kontroler umístí dynamická data (parametry), která šablona zobrazení potřebuje ve slovníku `ViewData`, ke kterému bude mít přístup šablona zobrazení.

V *HelloWorldController.cs*změňte metodu `Welcome` tak, aby se do `ViewData` slovníku přidala hodnota `Message` a `NumTimes`. `ViewData` slovníku je dynamický objekt, což znamená, že lze použít libovolný typ; objekt `ViewData` nemá žádné definované vlastnosti, dokud do něj nevložíte nějaký text. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapuje pojmenované parametry (`name` a `numTimes`) z řetězce dotazu v adresním řádku na parametry v metodě. Úplný soubor *HelloWorldController.cs* vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

Objekt `ViewData` Dictionary obsahuje data, která budou předána zobrazení.

Vytvořte šablonu zobrazení Vítejte s názvem *views/HelloWorld/Welcome. cshtml*.

Vytvoříte smyčku v šabloně zobrazení *Welcome. cshtml* , která zobrazí `NumTimes`Hello. Nahraďte obsah *zobrazení/HelloWorld/Welcome. cshtml* následujícím způsobem:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

Data jsou přijímána z adresy URL a předána do kontroleru pomocí [pořadače modelu MVC](xref:mvc/models/model-binding) . Kontroler zabalí data do slovníku `ViewData` a předá tento objekt zobrazení. Zobrazení pak data vykreslí jako HTML do prohlížeče.

![Zobrazení osobních údajů zobrazuje úvodní popisek a frázi Hello Rick zobrazené čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V ukázce výše byl `ViewData` slovník použit k předání dat z kontroleru do zobrazení. Později v tomto kurzu se používá model zobrazení k předávání dat z kontroleru do zobrazení. Přístup k modelu zobrazení pro předávání dat je obecně mnohem upřednostňován nad přístupem ke slovníku `ViewData`. Další informace najdete v tématu [kdy použít ViewBag, ViewData nebo TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) .

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)
> [Další](adding-model.md)

::: moniker-end
