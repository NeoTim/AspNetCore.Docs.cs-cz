---
title: Přidání zobrazení do aplikace ASP.NET Core MVC
author: rick-anderson
description: Přidání zobrazení pro jednoduchou aplikaci ASP.NET Core MVC
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 5267e5a49bb6ecdd4cef671989f111eae7a64ec4
ms.sourcegitcommit: 4e87712029de2aceb1cf2c52e9e3dda8195a5b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53381813"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>Přidání zobrazení do aplikace ASP.NET Core MVC

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části můžete změnit `HelloWorldController` třídu použít [Razor](xref:mvc/views/razor) zobrazení soubory k čistě zapouzdření proces generování odpovědi HTML do klienta.

Vytvoříte soubor šablony zobrazení pomocí syntaxe Razor. Šablony Razor na základě zobrazení mají *.cshtml* příponu souboru. Poskytují elegantní způsob, jak vytvořit výstup ve formátu HTML s C#.

Aktuálně `Index` metoda vrátí řetězec a zobrazí se zpráva, která je pevně zakódovaný ve třídě controller. V `HelloWorldController` třídy, nahraďte `Index` metodu s následujícím kódem:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

Vrátí předchozí kód `View` objektu. Zobrazit šablonu používá ke generování odpověď ve formátu HTML v prohlížeči. Metody kontroleru (označované také jako metody akce), jako `Index` metody popsané výše, obvykle vracet [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) (nebo třída odvozená z `ActionResult`), není typu jako řetězce.

## <a name="add-a-view"></a>Přidání zobrazení

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klikněte pravým tlačítkem na *zobrazení* složky a potom **Přidat > Nová složka** a pojmenujte složku *HelloWorld*.

* Klikněte pravým tlačítkem na *zobrazení/HelloWorld* složky a potom **Přidat > Nová položka**.

* V **přidat novou položku - MvcMovie** dialogového okna

  * Do vyhledávacího pole v pravém horním rohu, zadejte *zobrazení*

  * Vyberte **zobrazení Razor**

  * Zachovat **název** pole hodnotu *Index.cshtml*.

  * Vyberte **přidat**

![Přidat novou položku – dialogové okno](adding-view/_static/add_view.png)

<!-- Code -------------------------->
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Přidat `Index` zobrazit `HelloWorldController`.

* Přidat novou složku s názvem *zobrazení/HelloWorld*.
* Přidat nový soubor, který *zobrazení/HelloWorld* název složky *Index.cshtml*.

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na *zobrazení* složky a potom **Přidat > Nová složka** a pojmenujte složku *HelloWorld*.
* Klikněte pravým tlačítkem na *zobrazení/HelloWorld* složky a potom **Přidat > Nový soubor**.
* V **nový soubor** dialogové okno:

  * Vyberte **webové** v levém podokně.
  * Vyberte **prázdný HTML soubor** v prostředním podokně.
  * Typ *Index.cshtml* v **název** pole.
  * Vyberte **nové**.

![Přidat novou položku – dialogové okno](adding-view/_static/add_view.png)

---  
<!-- End of VS tabs -->

Nahraďte obsah *Views/HelloWorld/Index.cshtml* Razor zobrazení souboru následujícím kódem:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

Přejděte na adresu `https://localhost:xxxx/HelloWorld`. `Index` Metoda ve `HelloWorldController` nedělalo většinu; spustili příkaz `return View();`, která zadané, že metoda by měla používat soubor šablony zobrazení k vykreslení odezva do prohlížeče. Protože jste nezadali explicitní název souboru šablony zobrazení, MVC na výchozí pomocí *Index.cshtml* zobrazení souboru v */zobrazení/HelloWorld* složky. Následující obrázek ukazuje řetězec "Hello z našich zobrazit šablonu!" pevně zakódované v zobrazení.

![Okno prohlížeče](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>Změnit zobrazení a rozložení stránky

Vyberte nabídky odkazy (**MvcMovie**, **Domů**, a **ochrany osobních údajů**). Každá stránka zobrazuje stejné rozložení nabídky. Rozložení nabídky je implementována v *Views/Shared/_Layout.cshtml* souboru. Otevřít *Views/Shared/_Layout.cshtml* souboru.

[Rozložení](xref:mvc/views/layout) šablony umožňují zadat kontejner rozložení HTML vašeho webu na jednom místě a použijte ji na několika stránkách ve vaší lokalitě. Najít `@RenderBody()` řádku. `RenderBody` je zástupný symbol, kde všechny v zobrazení konkrétní stránky, můžete vytvořit, zobrazit *zabalené* stránce rozložení. Například, pokud jste vybrali **ochrany osobních údajů** odkaz, **Views/Home/Privacy.cshtml** je zobrazení vykresleno uvnitř `RenderBody` metody.

## <a name="change-the-title-and-menu-link-in-the-layout-file"></a>Změnit název a nabídky odkaz v souboru rozložení

* Do prvku název změnit `MvcMovie` k `Movie App`.
* Změnit anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` k `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.

Následující kód ukazuje zvýrazněné změny:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24)]

V předchozím kódu `asp-area` [atribut ukotvení pomocné rutiny značky](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) byla vynechána, protože tato aplikace nepoužívá [oblasti](xref:mvc/controllers/areas).

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**Poznámka:**: `Movies` Kontroler není implementovaná. V tomto okamžiku `Movie App` odkaz není funkční.

Uložte změny a vyberte **ochrany osobních údajů** odkaz. Všimněte si, jak se zobrazuje jako nadpis informace o kartě prohlížeče **ochrany osobních údajů – filmová aplikace** místo **ochrany osobních údajů – Mvc Movie**:

![Ochrana osobních údajů karty](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

Vyberte **Domů** propojit a Všimněte si také zobrazit text nadpisu a ukotvení **filmová aplikace**. Jsme byli schopni jednou provést změnu v šabloně rozložení, a mít všechny stránky na webu nový text odkazu a nový název.

Zkontrolujte *Views/_ViewStart.cshtml* souboru:

```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* souboru přináší *Views/Shared/_Layout.cshtml* soubor pro každé zobrazení. `Layout` Vlastnost slouží k nastavení zobrazení jiné rozložení, nebo ji nastavte na `null` , použije se žádný soubor rozložení.

Změna názvu a `<h2>` elementu *Views/HelloWorld/Index.cshtml* zobrazení souboru:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

Název a `<h2>` element se mírně liší, abyste si mohli zobrazit, které verze kódu změní zobrazení.

`ViewData["Title"] = "Movie List";` v kódu nad sad `Title` vlastnost `ViewData` slovník, který "Seznam video". `Title` Vlastnost se používá v `<title>` prvek HTML na stránce rozložení:

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

Uložte změny a přejděte do `https://localhost:xxxx/HelloWorld`. Všimněte si, že došlo ke změně názvu prohlížeče, záhlaví primární a sekundární záhlaví. (Pokud se nezobrazí změny v prohlížeči, pravděpodobně jste zobrazili obsah uložený v mezipaměti. Stisknutím kláves Ctrl + F5 v prohlížeči k vynucení odpověď ze serveru, který se má načíst.) Název prohlížeče se vytvoří s `ViewData["Title"]` jsme si nastavili *Index.cshtml* zobrazit šablony a další "-video aplikace" přidá soubor rozložení.

Všimněte si také způsob, jak v obsahu *Index.cshtml* zobrazit šablonu byl sloučen s *Views/Shared/_Layout.cshtml* zobrazit šablonu a odpověď o jedné HTML byl odeslán do prohlížeče. Rozložení šablony usnadňují skutečně provést změny, které platí pro všechny stránky v aplikaci. Další informace najdete tady [rozložení](xref:mvc/views/layout).

![Zobrazení seznamu Movie](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

Naše něco "data" (v tomto případě "Hello z našich zobrazit šablonu!" zpráva) je pevně zakódované, i když. Aplikace MVC má "V" (Zobrazit) a máte "C" (controller), ale žádné "M" (modelu) ještě.

## <a name="passing-data-from-the-controller-to-the-view"></a>Předání dat z Kontroleru zobrazení

Akce kontroleru jsou vyvolány v reakci na příchozí adrese URL žádosti. Třída kontroleru je kód níž je zapsána, který zpracovává příchozí požadavky prohlížeče. Kontroler načte data ze zdroje dat a rozhodne, jaký typ odpověď k odeslání zpět do prohlížeče. Zobrazit šablony lze použít z kontroleru pro generování a formátovat odpověď ve formátu HTML v prohlížeči.

Kontrolery odpovídají za poskytování dat vyžaduje, aby šablona zobrazení k vykreslení odpovědi. Osvědčený postup: Zobrazit šablony by měl **není** provádění obchodní logiky nebo pracovat přímo s databází. Zobrazit šablonu místo toho by měla fungovat jenom s data, která je poskytována kontroleru. Zachování tohoto "oddělené oblasti zájmu" udržet kód čistý, možností intenzivního testování a udržovatelný.

V současné době `Welcome` metodu `HelloWorldController` třídy přijímá `name` a `ID` parametr a potom výstupy hodnoty přímo do prohlížeče. Spíše než mít řadič vykreslení této odpovědi jako řetězec, změňte kontroler místo toho použít šablonu zobrazení. Šablona zobrazení generuje dynamické odpovědi, což znamená, že odpovídající částí dat musí být předán z kontroleru zobrazení k vygenerování odpovědi. To udělat tak, že kontroler umístit dynamických dat (parametry), která vyžaduje zobrazení šablony `ViewData` slovník, který se pak můžou zobrazit šablonu.

V *HelloWorldController.cs*, změnit `Welcome` metoda pro přidání `Message` a `NumTimes` hodnota, která se `ViewData` slovníku. `ViewData` Slovník je dynamický objekt, což znamená, že můžete použít libovolný typ; `ViewData` objekt nemá žádné definované vlastnosti, dokud je něco v ji vložíte. [Systém vazby modelu MVC](xref:mvc/models/model-binding) automaticky mapují pojmenované parametry (`name` a `numTimes`) z řetězce dotazu do adresního řádku parametrům ve své metodě. Kompletní *HelloWorldController.cs* souboru vypadá takto:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` Objekt dictionary, obsahuje data, která bude předána do zobrazení. 

Vytvoření šablony úvodní zobrazení s názvem *Views/HelloWorld/Welcome.cshtml*.

Vytvoříte smyčka v *Welcome.cshtml* zobrazit šablonu, která se zobrazí "Hello" `NumTimes`. Nahraďte obsah *Views/HelloWorld/Welcome.cshtml* následujícím kódem:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

Uložte změny a přejděte na následující adresu URL:

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

Data přijatá z adresy URL a předat pomocí řadiče [vazač modelu MVC](xref:mvc/models/model-binding) . Balíčky data do kontroleru `ViewData` slovníku a, které se objekt předá do zobrazení. Zobrazení pak vykreslí data ve formátu HTML v prohlížeči.

![Ochrana osobních údajů zobrazení úvodní popisek a frází Hello Rick zobrazí čtyřikrát](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

V příkladu výše `ViewData` slovník byla použita k předání dat z kontroleru zobrazení. Později v tomto kurzu se používá model zobrazení k předání dat z kontroleru zobrazení. Přístup modelu zobrazení k předávání dat je obvykle mnohem upřednostňované nad `ViewData` slovníku přístup. Zobrazit [použití položek ViewBag, ViewData a TempData ](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) Další informace.

V dalším kurzu se vytvoří databáze filmů.

> [!div class="step-by-step"]
> [Předchozí](adding-controller.md)
> [další](adding-model.md)
