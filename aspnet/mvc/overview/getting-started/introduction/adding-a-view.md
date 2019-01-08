---
title: Přidání zobrazení do aplikace MVC
author: Rick-Anderson
description: Přidání zobrazení do aplikace MVC
ms.author: riande
ms.date: 09/1721/2017
uid: mvc/overview/getting-started/introduction/adding-a-view
ms.openlocfilehash: 47447c82506cc0eb4dafabe272b3204f76a2edd7
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098659"
---
<a name="adding-a-view"></a>Přidání zobrazení
====================
Podle [Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

V této části se chystáte změnit `HelloWorldController` třídu použít zobrazení soubory šablon do čistě zapouzdření proces generování odpovědi HTML do klienta. 

Vytvoříte soubor šablony zobrazení pomocí [zobrazovací modul Razor](../../../../web-pages/overview/getting-started/introducing-razor-syntax-c.md). Šablony Razor na základě zobrazení mají *.cshtml* příponu souboru a poskytují elegantní způsob, jak vytvořit HTML výstup pomocí jazyka C#. Razor minimalizuje počet znaků a vyžaduje při psaní zobrazit šablonu stisknutí kláves a umožňuje rychlé, dynamika kódování pracovního postupu.

Aktuálně `Index` metoda vrátí řetězec a zobrazí se zpráva, která je pevně zakódovaný ve třídě controller. Změnit `Index` metodu chce volat kontrolery [zobrazení](/dotnet/api/microsoft.aspnetcore.mvc.controller.view#Microsoft_AspNetCore_Mvc_Controller_View) způsob, jak je znázorněno v následujícím kódu:

[!code-csharp[Main](adding-a-view/samples/sample1.cs?highlight=1,3)]

`Index` Výše uvedené metody používá šablonu zobrazení k vygenerování odpověď ve formátu HTML v prohlížeči. Metody kontroleru (označované také jako [metody akce](http://rachelappel.com/asp.net-mvc-actionresults-explained)), například `Index` metody popsané výše, obvykle vracet [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx) (nebo třída odvozená z [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult.aspx)), není primitivní typy, jako je řetězec.

Klikněte pravým tlačítkem myši *Views\HelloWorld* složky a klikněte na tlačítko **přidat**, pak klikněte na tlačítko **stránka zobrazení MVC 5 s rozložením (Razor)**.
  
![](adding-a-view/_static/image1.png)   
  
V **zadat název pro položku** dialogového okna zadejte *Index*a potom klikněte na tlačítko **OK**.  
  
![](adding-a-view/_static/image2.png)  
  
V **vybrat stránku rozložení** dialogového okna, přijměte výchozí nastavení  **\_Layout.cshtml** a klikněte na tlačítko **OK**.  
  
![](adding-a-view/_static/image3.png)  
  
V dialogovém okně výše uvedené *Views\Shared* v levém podokně je vybrána složka. Pokud jste měli soubor vlastní rozložení v jiné složce, můžete třeba vybrat. Později v tomto kurzu budeme mluvit o soubor rozložení

*MvcMovie\Views\HelloWorld\Index.cshtml* se vytvoří soubor.

![](adding-a-view/_static/image4.png)

Přidejte následující zvýrazněný kód.

[!code-cshtml[Main](adding-a-view/samples/sample2.cshtml?highlight=4-11)]

Klikněte pravým tlačítkem myši *Index.cshtml* a vyberte možnost **zobrazit v prohlížeči**.

![PI](adding-a-view/_static/image5.png)

Klikněte pravým tlačítkem myši klikněte *Index.cshtml* a vyberte možnost **zobrazení v nástroje Page Inspector.** Najdete v článku [nástroj Page Inspector kurzu](../../views/using-page-inspector-in-aspnet-mvc.md) Další informace.

Alternativně spusťte aplikaci a přejděte `HelloWorld` kontroler (`http://localhost:xxxx/HelloWorld`). `Index` Metoda ve vašem řadiči nedělalo množství práce, jednoduše spustili příkaz `return View()`, která zadané, že metoda by měla používat soubor šablony zobrazení k vykreslení odezva do prohlížeče. Protože jste nezadali explicitní název souboru šablony zobrazení pro použití, ASP.NET MVC na výchozí použití *Index.cshtml* zobrazení souboru v *\Views\HelloWorld* složky. Následující obrázek ukazuje řetězec &quot;Hello z našich zobrazit šablonu!&quot; pevně zakódované v zobrazení.

![](adding-a-view/_static/image6.png)

Vypadá hodně Dobrá. Všimněte si však, že v prohlížeči na záhlaví okna zobrazuje &quot;Index - Moje aplikace technologie ASP.NET "a velké objemy odkaz nahoře na stránce říká"Název aplikace." V závislosti na tom, jak malý provedete okno prohlížeče, možná budete muset kliknout na tři pruhy v pravém horním rohu zobrazíte na **Domů**, **o**, **kontakt**, **Zaregistrovat** a **přihlášení** odkazy.

## <a name="changing-views-and-layout-pages"></a>Změna zobrazení a rozložení stránky

Nejprve, kterou chcete změnit &quot;název_aplikace&quot; odkazu v horní části stránky. Tento text je společné pro všechny stránky. Ve skutečnosti implementaci na jenom jednom místě v projektu, i když se zobrazí na každé stránce v aplikaci. Přejděte */zobrazení/Shared* složky v **Průzkumníku řešení** a otevřete  *\_Layout.cshtml* souboru. Tento soubor se nazývá *stránku rozložení* a je ve sdílené složce, použít všechny ostatní stránky.

![_LayoutCshtml](adding-a-view/_static/image7.png)

Šablony rozložení umožňují zadat kontejner rozložení HTML vašeho webu na jednom místě a použijte ji na několika stránkách ve vaší lokalitě. Najít `@RenderBody()` řádku. `RenderBody` je zástupný symbol, kde všechny v zobrazení konkrétní stránky, můžete vytvořit, zobrazit &quot;zabalené&quot; stránce rozložení. Například, pokud jste vybrali **o** odkaz, *Views\Home\About.cshtml* je zobrazení vykresleno uvnitř `RenderBody` metody.

Změňte obsah prvku název. Změnit [ActionLink](https://msdn.microsoft.com/library/dd504972(v=vs.108).aspx) v šabloně rozložení z &quot;název_aplikace&quot; k &quot;MVC Movie&quot; a kontroler, z `Home` k `Movies`. Úplné rozložení souboru je uveden níže:

[!code-cshtml[Main](adding-a-view/samples/sample3.cshtml?highlight=6,20)]

Spusťte aplikaci a Všimněte si, že nyní říká &quot;MVC Movie &quot;. Klikněte na tlačítko **o** odkaz kde najdete v článku ukazuje, jak tuto stránku &quot;MVC Movie&quot;také. Jsme byli schopni jednou provést změnu v šabloně rozložení a mít všechny stránky na webu odrážely nový název.

![](adding-a-view/_static/image8.png)

Při prvním vytvoření *Views\HelloWorld\Index.cshtml* souboru, obsahuje následující kód:

[!code-cshtml[Main](adding-a-view/samples/sample4.cshtml)]

Výše uvedený kód Razor je explicitně nastavení stránky rozložení. Zkontrolujte *zobrazení\\soubor _ViewStart.cshtml* souboru, obsahuje přesně stejný kód Razor. *[Zobrazení\\soubor _ViewStart.cshtml](https://weblogs.asp.net/scottgu/archive/2010/10/22/asp-net-mvc-3-layouts.aspx)* soubor definuje společné rozložení, které budou používat všechna zobrazení, proto může přidávat komentáře out nebo odeberte tento kód z *Views\HelloWorld\ Index.cshtml* souboru.

[!code-cshtml[Main](adding-a-view/samples/sample5.cshtml?highlight=1-3)]

Můžete použít `Layout` vlastnost pro nastavení zobrazení jiné rozložení, nebo ji nastavte na `null` , použije se žádný soubor rozložení.

Teď Změníme název zobrazení indexu.

Open *MvcMovie\Views\HelloWorld\Index.cshtml*. Existují dvě místa, kde provést změnu: první, text, který se zobrazí v záhlaví prohlížeče a v hlavičce sekundární ( `<h2>` element). Budete je provedete trochu jinak, uvidíte, jaké verze kódu se změní kterou část aplikace.

[!code-cshtml[Main](adding-a-view/samples/sample6.cshtml?highlight=2,5)]

K označení názvu HTML zobrazení kódu nad sady `Title` vlastnost `ViewBag` objektu (což je v *Index.cshtml* zobrazit šablonu). Všimněte si, že šablona rozložení ( *Views\Shared\\_Layout.cshtml* ) používá tuto hodnotu v `<title>` element jako součást `<head>` část HTML, které jsme dřív ke změně.

[!code-cshtml[Main](adding-a-view/samples/sample7.cshtml?highlight=6)]

Použití této funkce `ViewBag` přístup, můžete snadno předat další parametry mezi zobrazit šablonu a soubor rozložení.

Spusťte aplikaci. Všimněte si, že došlo ke změně názvu prohlížeče, záhlaví primární a sekundární záhlaví. (Pokud se nezobrazí změny v prohlížeči, pravděpodobně jste zobrazili obsah uložený v mezipaměti. Stisknutím kláves Ctrl + F5 v prohlížeči k vynucení odpověď ze serveru, který se má načíst.) Název prohlížeče se vytvoří s `ViewBag.Title` jsme si nastavili *Index.cshtml* zobrazit šablony a další &quot;-filmová aplikace&quot; přidá soubor rozložení.

Všimněte si také způsob, jak v obsahu *Index.cshtml* zobrazit šablonu byl sloučen s  *\_Layout.cshtml* zobrazit šablonu a odpověď o jedné HTML byl odeslán do prohlížeče. Rozložení šablony usnadňují skutečně provést změny, které platí pro všechny stránky v aplikaci.

![](adding-a-view/_static/image9.png)

Naše nepatrné &quot;data&quot; (v tomto případě &quot;Hello z našich zobrazit šablonu!&quot; zprávu) je pevně zakódované, i když. Má aplikace MVC &quot;V&quot; (zobrazení) a máte &quot;C&quot; (controller), ale žádné &quot;M&quot; (modelu) ještě. Po chvíli projdeme postup vytvoření databáze a načíst datový model z něj.

## <a name="passing-data-from-the-controller-to-the-view"></a>Předání dat z Kontroleru zobrazení

Předtím, než jsme přejděte k databázi a mluvit o modely, ale nejprve se seznámíme předávání informací z kontroleru zobrazení. Kontroler třídy jsou vyvolány v reakci na příchozí adrese URL žádosti. Třída kontroleru je místo, kde píšete kód, který zpracovává příchozí prohlížeč požádá, načte data z databáze a nakonec rozhodne, jaký typ odpověď k odeslání zpět do prohlížeče. Zobrazit šablony pak lze z kontroleru pro generování a formátovat odpověď ve formátu HTML v prohlížeči.

Kontrolery odpovídají za poskytování libovolné dat nebo objekty jsou nutné v pořadí pro šablonu zobrazení k vykreslení odezva do prohlížeče. Osvědčený postup: **Zobrazit šablonu by nikdy provádění obchodní logiky nebo pracovat přímo s databází**. Zobrazit šablonu místo toho by měla fungovat jenom s data, která je poskytována kontroleru. Zachování to &quot;oddělení oblastí zájmu&quot; pomáhá udržovat váš kód čistý, možností intenzivního testování a jednodušší údržbu.

V současné době `Welcome` metodu akce v `HelloWorldController` třídy přijímá `name` a `numTimes` parametr a potom výstupy hodnoty přímo do prohlížeče. Spíše než mít řadič vykreslení této odpovědi jako řetězec, Změníme kontroler místo toho použít šablonu zobrazení. Zobrazit šablonu vygeneruje dynamické odpovědi, což znamená, že je potřeba předat odpovídající částí dat z kontroleru zobrazení k vygenerování odpovědi. Můžete to provést tak, že kontroler umístit dynamických dat (parametry), která vyžaduje zobrazení šablony `ViewBag` objekt, který se pak můžou zobrazit šablonu.

Vraťte se na *HelloWorldController.cs* soubor a změňte `Welcome` metoda pro přidání `Message` a `NumTimes` hodnota, která se `ViewBag` objektu. `ViewBag` je to dynamický objekt, což znamená, že můžete vložit cokoliv, co chcete `ViewBag` objekt nemá žádné definované vlastnosti, dokud je něco v ji vložíte. [Systém vazby modelu ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx) automaticky mapují pojmenované parametry (`name` a `numTimes`) z řetězce dotazu do adresního řádku parametrům ve své metodě. Kompletní *HelloWorldController.cs* souboru vypadá takto:

[!code-csharp[Main](adding-a-view/samples/sample8.cs)]

Nyní `ViewBag` objekt obsahuje data, která bude předána do zobrazení automaticky. Dále je třeba úvodní zobrazit šablonu! V **sestavení** nabídce vyberte možnost **sestavit řešení** (nebo Ctrl + Shift + B) k Ujistěte se, že je projekt kompilován. Klikněte pravým tlačítkem myši *Views\HelloWorld* složky a klikněte na tlačítko **přidat**, pak klikněte na tlačítko **stránka zobrazení MVC 5 s rozložením (Razor)**.
  
![](adding-a-view/_static/image10.png)   
  
V **zadat název pro položku** dialogového okna zadejte *úvodní*a potom klikněte na tlačítko **OK**.   
  
V **vybrat stránku rozložení** dialogového okna, přijměte výchozí nastavení  **\_Layout.cshtml** a klikněte na tlačítko **OK**.  
  
![](adding-a-view/_static/image11.png)   

*MvcMovie\Views\HelloWorld\Welcome.cshtml* se vytvoří soubor.

Nahraďte kód v *Welcome.cshtml* souboru. Vytvoříte smyčku, která uvádí, že &quot;Hello&quot; tolikrát, kolikrát uživatel uvádí, že by měl. Kompletní *Welcome.cshtml* souboru je uveden níže.

[!code-cshtml[Main](adding-a-view/samples/sample9.cshtml)]

Spusťte aplikaci a přejděte na následující adresu URL:

`http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4`

Data jsou na základě adresy URL a předat pomocí řadiče [modelu pořadače](http://odetocode.com/Blogs/scott/archive/2009/04/27/6-tips-for-asp-net-mvc-model-binding.aspx). Balíčky data do kontroleru `ViewBag` objektu a, které se objekt předá do zobrazení. Zobrazení pak zobrazí data jako kód HTML pro uživatele.

![](adding-a-view/_static/image12.png)

V příkladu výše jsme použili `ViewBag` objekt k předávání dat z kontroleru zobrazení. Později v tomto kurzu budeme používat model zobrazení k předání dat z kontroleru zobrazení. Přístup modelu zobrazení k předávání dat je mnohem obecně upřednostňované nad přístup kontejner objektů a dat zobrazení. Naleznete v příspěvku blogu [V silně typované zobrazení dynamické](https://blogs.msdn.com/b/rickandy/archive/2011/01/28/dynamic-v-strongly-typed-views.aspx) Další informace. 

Kontejneru, který byl typ z &quot;M&quot; pro model, ale není typ databáze. Pojďme se na to co jsme se naučili a vytvořit databázi videa.

> [!div class="step-by-step"]
> [Předchozí](adding-a-controller.md)
> [další](adding-a-model.md)
