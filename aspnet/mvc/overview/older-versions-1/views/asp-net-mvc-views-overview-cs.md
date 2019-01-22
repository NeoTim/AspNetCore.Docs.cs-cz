---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
title: ASP.NET MVC – zobrazení přehled (C#) | Dokumentace Microsoftu
author: StephenWalther
description: Co se zobrazení ASP.NET MVC a jak se liší od stránku HTML? V tomto kurzu Stephen Walther vás seznámí s zobrazení a předvádí, jak můžete t...
ms.author: riande
ms.date: 02/16/2008
ms.assetid: 152ab1e5-aec2-4ea7-b8cc-27a24dd9acb8
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
msc.type: authoredcontent
ms.openlocfilehash: a8e64a99549584f150d64d909ac97210257b1147
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444126"
---
<a name="aspnet-mvc-views-overview-c"></a>ASP.NET MVC – přehled zobrazení (C#)
====================
podle [Stephen Walther](https://github.com/StephenWalther)

> Co se zobrazení ASP.NET MVC a jak se liší od stránku HTML? V tomto kurzu Stephen Walther vás seznámí s zobrazení a ukazuje, jak můžete využít výhod zobrazení dat a pomocných rutin HTML v zobrazení.


Účelem tohoto kurzu je poskytne stručný úvod do ASP.NET MVC zobrazení, zobrazení dat a pomocných rutin HTML. Na konci tohoto kurzu budete vědět, jak vytvářet nová zobrazení, předat data z kontroleru zobrazení a použití pomocných rutin HTML ke generování obsahu v zobrazení.

## <a name="understanding-views"></a>Principy zobrazení

Pro ASP nebo ASP.NET ASP.NET MVC nezahrnuje nic, který přímo odpovídá na stránku. V aplikaci MVC rozhraní ASP.NET není stránku na disku, který odpovídá cestě v adrese URL, kterou zadáte do adresního řádku prohlížeče. Nejbližší věc do stránky v aplikaci ASP.NET MVC se volá *zobrazení*.

V aplikaci ASP.NET MVC se mapují příchozí požadavky prohlížeče na akce kontroleru. Zobrazení může vrátit akce kontroleru. Akce kontroleru může ale provést i jiný druh akce, jako je probíhá přesměrování na jiný akce kontroleru.

Výpis 1 obsahuje řadič jednoduché s názvem HomeController. HomeController zpřístupňuje s názvem Index() a Details() dvě akce kontroleru.

**Výpis 1 - HomeController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample1.cs)]

První akci Index() akci, můžete vyvolat zadáním následující adresy URL do adresního řádku prohlížeče:

/ Home/Index

Druhou akci Details() akci, můžete vyvolat tak, že do prohlížeče zadáte tuto adresu:

/ Home/podrobností

Akce Index() vrátí zobrazení. Většinu akcí, které vytvoříte vrátí zobrazení. Akce však může vrátit jiné druhy výsledky akce. Například akce Details() vrátí RedirectToActionResult, který přesměruje na akce Index() příchozího požadavku.

Akce Index() obsahuje následující jediný řádek kódu:

View();

Tento řádek kódu vrátí zobrazení, které musí být v následujícím umístění na vašem webovém serveru:

\Views\Home\Index.aspx

Cesta k zobrazení je odvozen z názvu kontroleru a názvu akce kontroleru.

Pokud dáváte přednost, může být explicitní o zobrazení. Následující řádek kódu vrátí zobrazení s názvem Fred:

Zobrazení (Fred);

Pokud je spuštěn tento řádek kódu, zobrazení je vrácen z následující cestu:

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> Pokud budete chtít vytvořit testy jednotek pro aplikace ASP.NET MVC je vhodné k explicitnímu názvy zobrazení. Tímto způsobem můžete vytvořit testování částí k ověření, že očekávané zobrazení byl vrácen akce kontroleru.


## <a name="adding-content-to-a-view"></a>Přidávání obsahu do zobrazení

Zobrazení je standard (dokumentu HTML, který může obsahovat skriptů X). Přidat dynamický obsah k zobrazení pomocí skriptů.

Například zobrazení v informacích 2 ukazuje aktuální datum a čas.

**Listing 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample2.aspx)]

Všimněte si, že tělo stránky HTML v zobrazení 2 obsahuje následující skript:

&lt;% Response.Write(DateTime.Now); %&gt;

Použít skript oddělovače &lt;% a %&gt; označit začátek a konec skriptu. Tento skript je napsána v jazyce C#. Zobrazí aktuální datum a čas voláním metody Response.Write() metody k vykreslení obsahu v prohlížeči. Skript oddělovače &lt;% a %&gt; lze použít k provedení jednoho nebo více příkazů.

Vzhledem k tomu, že volání metody Response.Write() tak často, Microsoft vám poskytne zástupce pro volání metody Response.Write() metody. Zobrazení v informacích 3 používá oddělovače &lt;% = a %&gt; jako zástupce pro volání metody Response.Write().

**Listing 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample3.aspx)]

Libovolný jazyk .NET můžete použít ke generování dynamického obsahu v zobrazení. Za normálních okolností budete používat buď Visual Basic .NET nebo C# zapsat kontrolerů a zobrazení.

## <a name="using-html-helpers-to-generate-view-content"></a>Použití pomocných rutin HTML k vygenerování zobrazení obsahu

Aby bylo snazší k přidání obsahu do zobrazení, můžete využít výhod nástroje s názvem *pomocné rutiny HTML*. Pomocné rutiny HTML, je obvykle metodu, která z nich generuje řetězec. Pomocné rutiny HTML můžete použít ke generování standardní elementy jazyka HTML, jako jsou textová pole, odkazy, rozevírací seznamy a pole se seznamem.

Například zobrazení výpisu 4 využívá registrů tři pomocných rutin HTML – Pomocníci BeginForm() TextBox() a Password() – ke generování přihlášení formulář (viz obrázek 1).

**Listing 4 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample4.aspx)]


[![Dialogové okno Nový projekt](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)

**Obrázek 01**: Standardní přihlašovací formulář ([kliknutím ji zobrazíte obrázek v plné velikosti](asp-net-mvc-views-overview-cs/_static/image2.png))


Všechny metody pomocných rutin HTML se nazývají na vlastnosti Html zobrazení. Například voláním metody Html.TextBox() vykreslení textové pole.

Všimněte si, že používáte skript oddělovače &lt;% = a %&gt; při volání metody Html.TextBox() i Html.Password() pomocné rutiny. Tyto pomocné rutiny jednoduše vrátit řetězec. Je třeba volat metody Response.Write() k vykreslení řetězec, který se v prohlížeči.

Použití metody pomocné rutiny HTML není povinné. Jejich usnadnit vám život díky snížení objemu HTML a skript, který budete muset napsat. Zobrazení výpisu 5 vykreslí přesně stejný formulář jako zobrazení výpisu 4 bez použití pomocných rutin HTML.

**Listing 5 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample5.aspx)]

Máte také možnost vytvoření vlastních pomocných rutin HTML. Můžete například vytvořit GridView() Pomocná metoda, která automaticky zobrazí sadu záznamů databáze v tabulku HTML. V tomto tématu budeme věnovat v tomto kurzu **pomocných rutin HTML vytvořením Custom**.

## <a name="using-view-data-to-pass-data-to-a-view"></a>Pomocí zobrazení dat k předávání dat k zobrazení

Zobrazení dat použijete k předání dat z kontroleru zobrazení. Představte si zobrazit data, jako je balíček, který můžete odeslat prostřednictvím e-mailu. Všechna data z kontroleru předána do zobrazení musí být odeslána pomocí tohoto balíčku. Například kontroler v informacích 6 přidá zprávu zobrazíte data.

**Výpis 6 - ProductController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample6.cs)]

Kontroler ViewData vlastnost představuje kolekci párů názvu a hodnoty. Výpis 6 metodu Index() přidá položku do kolekce dat zobrazení s názvem zprávy s hodnotou Hello World!. Při zobrazení je vrácený metodou Index(), data zobrazení je automaticky předána do zobrazení.

Zobrazení výpisu 7 načte zprávy z dat zobrazení a vykreslí zprávy do prohlížeče.

**Listing 7 -- \Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample7.aspx)]

Všimněte si, že zobrazení využívá metodu pomocné rutiny HTML Html.Encode() při vykreslování zprávy. Pomocné rutiny HTML Html.Encode() kóduje zvláštní znaky, jako &lt; a &gt; do znaků, které jsou bezpečné pro zobrazení na webové stránce. Pokaždé, když se vám zobrazit obsah, který uživatel odešle na web, by měl kódování obsahu, aby se zabránilo útoků založených na injektáži JavaScriptu.

(Protože jsme vytvořili zprávu že si v ProductController nepotřebujeme ve skutečnosti ke kódování zprávy. Nicméně je dobré se vždy volat metodu Html.Encode() při zobrazení obsahu načítá ze zobrazení dat v rámci zobrazení.)

Ve výpisu 7 jsme využil předání jednoduché řetězcovou zprávu z řadiče zobrazení dat zobrazení. Zobrazení dat můžete použít také k předání dalších typů dat, jako jsou kolekce záznamů databáze z kontroleru zobrazení. Například pokud chcete zobrazit obsah databázové tabulky produktů v zobrazení, pak by úspěšně prošel zpracováním kolekce databáze záznamů v zobrazení data.

Máte také možnost předat data silného typu zobrazení z kontroleru zobrazení. V tomto tématu budeme věnovat v tomto kurzu **Principy silného typu zobrazení dat a zobrazení**.

## <a name="summary"></a>Souhrn

Tento kurz poskytuje stručný úvod do ASP.NET MVC zobrazení, zobrazení dat a pomocných rutin HTML. V prvním oddílu jste zjistili, jak přidat nová zobrazení do projektu. Jste zjistili, že je nutné přidat zobrazení do správné složky pro volání z určitý kontroler. Dále jsme probírali tématu pomocných rutin HTML. Jste se naučili, jak pomocných rutin HTML umožňují snadno vygenerovat standardní obsah ve formátu HTML. Nakonec jste zjistili, jak využít výhod zobrazení dat k předání dat z kontroleru zobrazení.

> [!div class="step-by-step"]
> [Next](creating-custom-html-helpers-cs.md)
