---
uid: web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
title: Vytvoření a použití pomocné rutiny v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje, jak vytvořit pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). Pomocné rutiny je opětovně použitelnou komponentu, která obsahuje kód a značky výkonu...
ms.author: riande
ms.date: 02/17/2014
ms.assetid: 46bff772-01e0-40f0-9ae6-9e18c5442ee6
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/creating-and-using-a-helper-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 3b54ba8a35a354eb0562a47866cd8b5dcb66bc86
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021505"
---
<a name="creating-and-using-a-helper-in-an-aspnet-web-pages-razor-site"></a>Vytvoření a použití pomocné rutiny na webu technologie ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak vytvořit pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). A *pomocné rutiny* je opětovně použitelnou komponentu, která obsahuje kód a značky k provedení úkolu, který může být zdlouhavý nebo složitý.
> 
> **Co se dozvíte:** 
> 
> - Jak vytvořit a používat jednoduché pomocné rutiny.
> 
> Toto jsou funkce technologie ASP.NET v následujícím článku:
> 
> - `@helper` Syntaxe.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="overview-of-helpers"></a>Přehled pomocné rutiny

Pokud potřebujete stejné úlohy provádět na různých stránkách ve vaší lokalitě, můžete použít pomocné rutiny. Rozhraní ASP.NET Web Pages obsahuje několik pomocných rutin a existuje mnoho dalších, které si můžete stáhnout a nainstalovat. (Seznam integrovaných pomocných rutin v ASP.NET Web Pages je uveden v [Stručná referenční příručka rozhraní API technologie ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202907).) Pokud žádná z existující pomocné rutiny nevyhovuje vašim potřebám, můžete vytvořit vlastní pomocné rutiny.

Pomocné rutiny vám umožní používat běžné blok kódu na několika stránkách. Předpokládejme, že na stránce často chcete vytvořit položku Poznámka, která je nastavena kromě Normální odstavce. Třeba poznámky se vytvoří jako `<div>` element, který byl navržen jako pole s ohraničením. Místo na stránku přidat tento stejný kód, pokaždé, když chcete zobrazit poznámky, můžete zabalit kód jako pomůcka pro. Pak můžete vložit poznámky s jedním řádkem kódu kdekoli ho potřebujete.

Použití pomocné rutiny tímto způsobem díky kód v jednotlivých stránek, jednodušší a snadněji čitelné. Je také usnadňuje udržovat lokalitu, protože pokud potřebujete změnit vzhled poznámky, můžete změnit kód v jednom místě.

## <a name="creating-a-helper"></a>Vytváření pomocné rutiny

Tento postup ukazuje, jak vytvořit pomocné rutiny, která vytvoří Všimněte si, jak bylo právě popsáno. Toto je jednoduchý příklad, ale vlastní pomocné rutiny může obsahovat libovolný značek a kódu ASP.NET, které potřebujete.

1. V kořenové složce webové stránky, vytvořte složku s názvem *aplikace\_kód*. Jde o název vyhrazené složky v technologii ASP.NET, kde můžete vložit kód pro komponenty, jako jsou pomocné rutiny.
2. V *aplikace\_kód* vytvořte novou složku *.cshtml* soubor a pojmenujte ho *MyHelpers.cshtml*.
3. Nahraďte existující obsah následujícím kódem:

    [!code-cshtml[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample1.cshtml)]

    Tento kód použije `@helper` syntaxi pro deklaraci nového Pomocníka s názvem `MakeNote`. Tento konkrétní pomocné rutiny umožňuje předat parametr s názvem `content` , který může obsahovat kombinaci textu a kódu. Pomocné rutiny vloží do těla Poznámka: pomocí řetězce `@content` proměnné.

    Všimněte si, že je soubor s názvem *MyHelpers.cshtml*, ale má název pomocné rutiny `MakeNote`. Více vlastních pomocných rutin můžete umístit do jednoho souboru.
4. Soubor uložte a zavřete.

## <a name="using-the-helper-in-a-page"></a>Na stránce využitím pomocné rutiny

1. V kořenové složce vytvořte nový prázdný soubor s názvem *TestHelper.cshtml*.
2. Přidejte následující kód do souboru:

    [!code-html[Main](creating-and-using-a-helper-in-an-aspnet-web-pages-site/samples/sample2.html)]

    Chcete-li volat pomocné rutiny, které jste vytvořili, použijte `@` za nímž následuje název souboru, kde pomocné rutiny je, tečku a potom název pomocné rutiny. (Pokud jste měli více složek *aplikace\_kód* složky, můžete použít syntaxi `@FolderName.FileName.HelperName` volat váš pomocný objekt v rámci žádné vnořené složky). Text, který přidáte do uvozovek v závorkách je text, který pomocné rutiny se zobrazí jako součást Poznámka na webové stránce.
3. Uložit na stránku a spustíte ji v prohlížeči. Pomocná rutina generuje Poznámka položku přímo ve kterém jste volali pomocné rutiny: mezi dva odstavce.

    ![Snímek obrazovky zobrazující stránku v prohlížeči a způsob, jakým pomocné rutiny vygeneruje kód, který vloží polem a zadaný text.](creating-and-using-a-helper-in-an-aspnet-web-pages-site/_static/image1.jpg)

## <a name="additional-resources"></a>Další prostředky


[Vodorovné menu jako pomocné rutiny Razor](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2341). Tento blog položky ze Mike Pope ukazuje postup vytvoření vodorovné nabídce jako pomůcka pro použití značek, šablon stylů CSS a kódu.

[Využití HTML5 v rozhraní ASP.NET Web Pages pomocné rutiny pro službu WebMatrix a ASP.NET MVC3](http://geekswithblogs.net/wildturtle/archive/2010/11/08/html5-in-asp.net-web-pages-helpers-for-webmatrix-and_aspnet_mvc3.aspx). Tento blog položky ze Sam Abraham ukazuje pomocné rutiny, která vykresluje HTML5 `Canvas` elementu.

[Rozdíl mezi @Helpers a @Functions v nástroji WebMatrix](http://www.mikesdotnetting.com/Article/173/The-Difference-Between-@Helpers-and-@Functions-In-WebMatrix). Popisuje tuto položku blogu podle Mike Brind `@helper` syntaxi a `@function` syntaxi a kdy se má použít.
