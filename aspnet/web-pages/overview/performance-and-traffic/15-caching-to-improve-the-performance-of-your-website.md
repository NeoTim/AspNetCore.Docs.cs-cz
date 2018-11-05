---
uid: web-pages/overview/performance-and-traffic/15-caching-to-improve-the-performance-of-your-website
title: Ukládání dat do webového rozhraní ASP.NET do mezipaměti stránek webu (Razor) pro zajištění lepšího výkonu | Dokumentace Microsoftu
author: Rick-Anderson
description: Můžete urychlit svůj web tak, že ho store – to znamená, mezipaměti – výsledků data, která by obvykle trvat docela dlouho načíst nebo zpracovávat...
ms.author: riande
ms.date: 02/14/2014
ms.assetid: 961e525b-7700-469e-8a68-d7010b6fb68c
msc.legacyurl: /web-pages/overview/performance-and-traffic/15-caching-to-improve-the-performance-of-your-website
msc.type: authoredcontent
ms.openlocfilehash: ede341e02869a9c81cbe2fa7ef97345dc87519a1
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020362"
---
<a name="caching-data-in-an-aspnet-web-pages-razor-site-for-better-performance"></a>Ukládání do mezipaměti dat na webu rozhraní ASP.NET Web Pages (Razor) pro zajištění lepšího výkonu
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak pomocí Pomocníka pro informace o mezipaměti pro dosažení vyššího výkonu na webových stránkách ASP.NET (Razor) webu. Můžete urychlit svůj web tak, že ho uložit &#8212; tedy mezipaměti &#8212; výsledků data, která obvykle by trvat docela dlouho načíst nebo zpracovávat a nemění často.
> 
> **Co se dozvíte:** 
> 
> - Jak používat ukládání do mezipaměti ke zlepšení rychlosti odezvy vašeho webu.
> 
> Toto jsou funkce technologie ASP.NET v následujícím článku:
> 
> - `WebCache` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


Pokaždé, když uživatel požádá o stránku z lokality, webový server má provést úkony za účelem splnění žádosti. Některé stránky na serveru může být potřeba provádět úlohy, které dlouho (relativně), třeba načítání dat z databáze. I v případě, že tyto úlohy nevyřídí dlouho v absolutních číslech, pokud váš web narazí hodně provoz, můžete ušetřit na celou řadu jednotlivých požadavků, které způsobují webový server k provedení úlohy složitá nebo pomalé až spoustu práce. Takže v konečném důsledku to může ovlivnit výkon lokality.

Jedním způsobem, jak zvýšit výkon vašeho webu za okolností tímto způsobem je ukládání dat do mezipaměti. Pokud váš web získá opakované požadavky stejné informace a informace není potřeba změnit pro každou osobu a není čas citlivé, ne znovu načíst nebo přepočítání, můžete načíst data jednou a potom uložení výsledků. Při příštím žádost pochází k tomu informace, stačí ho obdržíte z mezipaměti.

Obecně platí mezipaměti informace, které se nemění příliš často. Když vkládáte informace v mezipaměti, je uložen v paměti na webovém serveru. Můžete určit, jak dlouho je do mezipaměti, ze sekund na dny. Po vypršení doby ukládání do mezipaměti tyto informace se automaticky odebere z mezipaměti.

> [!NOTE]
> Položky v mezipaměti může být odstraněna z důvodu jiné než jejich platnost. Například webový server může být dočasně nedostatek paměti a je jedním ze způsobů jeho paměti mohl uvolnit paměť vyvoláním položky z mezipaměti. Jak uvidíte, i když jste dali informace do mezipaměti, máte ověřte, že je stále existuje, když je potřebujete.


Představte si, že váš web má stránka zobrazující aktuální teplotu a předpověď počasí. Chcete-li získat tento typ informací, může odeslat požadavek na externí služby. Protože tyto informace nedojde ke změně většinu (v rámci dvouhodinovými časovými období, třeba) a od externích volání vyžaduje čas a šířku pásma, je vhodným kandidátem pro ukládání do mezipaměti.

## <a name="adding-caching-to-a-page"></a>Přidání ukládání do mezipaměti na stránku

Technologie ASP.NET obsahuje `WebCache` pomocné rutiny, které umožňuje snadno přidat ukládání do mezipaměti na váš web a přidání dat do mezipaměti. V tomto postupu vytvoříte stránky, která ukládá do mezipaměti na aktuální čas. Reálný příklad, nejedná se vzhledem k tomu, že aktuální čas je něco, která se často mění a, která kromě není komplexní k výpočtu. Je ale dobrým způsobem ke znázornění, ukládání do mezipaměti v akci.

1. Přidejte novou stránku s názvem *WebCache.cshtml* na web.
2. Přidejte následující kód a kód na stránku:

    [!code-cshtml[Main](15-caching-to-improve-the-performance-of-your-website/samples/sample1.cshtml)]

    Když jste data do mezipaměti, vložíte ho do mezipaměti pomocí názvu to je jedinečný v rámci webu. V takovém případě budete používat položku mezipaměti s názvem `CachedTime`. Toto je `cacheItemKey` je znázorněno v příkladu kódu.

    Kód nejprve čte `CachedTime` položky mezipaměti. Pokud vrátí hodnotu (tj. Pokud není položka mezipaměti null), kód jenom nastaví hodnotu proměnné čas na data v mezipaměti.

    Nicméně pokud položka mezipaměti neexistuje (to znamená, je null), kód nastaví hodnotu času, přidá ho do mezipaměti a nastaví vypršení na jednu minutu. Po jedné minutě položka mezipaměti je ignorována. (Výchozí hodnota vypršení platnosti položky v mezipaměti je 20 minut). Příkaz `WebCache.Set(cacheItemKey, time, 1, false)` ukazuje, jak přidat aktuální čas do mezipaměti a nastavit jeho dobu platnosti na 1 minutu. Nastavení *slidingExpiration* parametr `false` znamená, že čas vypršení platnosti nedojde k jeho prodloužení pokaždé, když je požadováno. Její platnost vyprší přesně 1 minutu po původně přidal do mezipaměti. Pokud nastavíte tuto hodnotu na `true` 1 minuta čas vypršení platnosti se vynuluje pokaždé, když je požadována hodnota z mezipaměti.

    Tento kód ukazuje vzor, který vždy používejte, když jste data do mezipaměti. Než se ponoříte něco z mezipaměti, vždy nejprve zkontrolujte, zda `WebCache.Get` Metoda vrátila hodnotu null. Mějte na paměti, že položka mezipaměti vypršela nebo mohlo dojít k odebrání z nějakého důvodu, takže jakékoli daná položka se nikdy zaručeně v mezipaměti.
3. Spustit *WebCache.cshtml* v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Při prvním požadavku na stránku, časové údaje v mezipaměti nejsou, a kód musí přidat časovou hodnotu do mezipaměti.

    ![mezipaměť-1](15-caching-to-improve-the-performance-of-your-website/_static/image1.jpg)
4. Aktualizovat *WebCache.cshtml* v prohlížeči. Tentokrát je časové údaje v mezipaměti. Všimněte si, že nedošlo ke změně doby od posledního zobrazení stránky.

    ![mezipaměť-2](15-caching-to-improve-the-performance-of-your-website/_static/image2.jpg)
5. Počkejte minutu vyprázdnit mezipaměť a pak aktualizujte stránku. Na stránce znovu označuje, že časové údaje nebyl nalezen v mezipaměti a čas aktualizace se přidá do mezipaměti.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


- [Zobrazení dat v grafu](https://go.microsoft.com/fwlink/?LinkId=202895)
- [Reference k rozhraní API WebCache](https://msdn.microsoft.com/library/system.web.helpers.webcache(v=vs.99).aspx) (MSDN)
