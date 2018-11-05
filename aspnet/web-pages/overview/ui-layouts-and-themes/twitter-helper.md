---
uid: web-pages/overview/ui-layouts-and-themes/twitter-helper
title: Rozhraní ASP.NET Web Pages pomocná rutina twitteru | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto tématu a aplikace ukazují, jak přidat do projektu služby WebMatrix 3 Pomocník Twitter. Obsahuje kód Pomocník Twitter a ukazuje způsob volání pomocné rutiny...
ms.author: riande
ms.date: 02/07/2014
ms.assetid: c1a1244e-b9c8-42e6-a00b-8456a4ec027c
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/twitter-helper
msc.type: authoredcontent
ms.openlocfilehash: 89c8c520cd32ca2ee24e6cd90e11f7bdf39c7a80
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020570"
---
<a name="twitter-helper-with-aspnet-web-pages"></a>Pomocná rutina Twitteru na webových stránkách ASP.NET
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto tématu a aplikace ukazují, jak přidat do projektu služby WebMatrix 3 Pomocník Twitter. Obsahuje kód Pomocník Twitter a ukazuje, jak volat metodu helper.
> 
> Tento kód pro soubor Twitter.cshtml vyvinula společnost **Tian Pan** společnosti Microsoft.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="introduction"></a>Úvod

Toto téma ukazuje, jak přidat Pomocník Twitter pro vaši aplikaci a pomocí syntaxe Razor pro volání metody helper. Pomocník Twitter umožňuje snadno začlenit tlačítka Twitteru a pomůcky ve vaší aplikaci. Chcete-li pomocí widgetu Twitter, jako je například timeline uživatele nebo výsledky hledání pro hashtagu, musíte nejdřív vytvořit [widgetů na Twitteru](https://twitter.com/settings/widgets). Po vytvoření vaší widgetů, zobrazí se id widgetu. Toto id widgetu předat jako parametr při volání pomocné metody, které ukazují widgetu.

Toto téma bylo napsáno pro verzi 1.1 rozhraní Twitter API. Přímo do projektu přidáte kód Pomocník Twitter, můžete aktualizovat kód pomocného objektu, pokud se změní rozhraní Twitter API.

Informace o instalaci služby WebMatrix najdete v tématu [Úvod do ASP.NET Web Pages 2 – Začínáme](../getting-started/introducing-aspnet-web-pages-2/getting-started.md).

## <a name="add-twitter-helper-to-your-project"></a>Pomocník Twitter přidat do projektu

Chcete-li přidat Pomocník Twitter, nejprve přidejte složku s názvem **aplikace\_kód** do projektu. Vytvořte soubor s názvem **Twitter.cshtml**.

![Složku App_Code](twitter-helper/_static/image1.png)

Nahraďte kód v Twitter.cshtml následujícím kódem.

[!code-cshtml[Main](twitter-helper/samples/sample1.cshtml)]

## <a name="call-twitter-methods-from-your-web-pages"></a>Volání metody Twitteru z webových stránek

Následující příklad ukazuje způsob použití metody Pomocník Twitter ze stránky ve vašem projektu. Ve vašem projektu můžete k nahrazení hodnoty parametrů s hodnotami, které jsou relevantní pro vaše potřeby. ID zadané widgetu můžete prozkoumat, jak fungují metody, ale budete chtít vytvořit vlastní pomůcky pro váš projekt.

Ne všechny parametry uvedené níže jsou požadovány. Volitelné parametry umožňují přizpůsobit, jak se zobrazí tlačítko nebo widgetu. Například použijte tlačítko pouze vyžaduje uživatelské jméno dodržovat, ale tento příklad ukazuje, jak zahrnují počet sledujících a jak určit velikost tlačítka a jazyk.

[!code-html[Main](twitter-helper/samples/sample2.html)]

## <a name="see-the-results"></a>Zobrazit výsledky

Ve výše uvedeném kódu vytvoří následující tlačítka a pomůcky. Tato tlačítka a pomůcky jsou plně funkční, není snímky obrazovky. Je zobrazeno tlačítko postupujte ve španělštině, protože parametr jazyka byla nastavena na hodnotu **es**.

### <a name="follow-button"></a>Použijte tlačítko

[Postupujte podle @aspnet)](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`

### <a name="tweet-button"></a>Tlačítko tweetu

[Tweet](https://twitter.com/share)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + '://platform.twitter.com/widgets.js'; fjs.parentNode.insertBefore(js, fjs); } }(document, 'script', 'twitter-wjs');</script>`

### <a name="user-timeline-profile"></a>Časová osa uživatele (profil)

[Tweety podle @aspnet](https://twitter.com/aspnet)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`

### <a name="favorites"></a>Oblíbené položky

[Oblíbené Tweety podle @Microsoft](https://twitter.com/Microsoft/favorites)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`

### <a name="list"></a>Seznam

[Tweetuje z @Microsoft/MS \_příjemce\_pruhy](https://twitter.com/microsoft/ms-consumer-brands/)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`

### <a name="search"></a>Hledat

[Tweetuje o &quot;#asp.net&quot;](https://twitter.com/search?q=%23asp.net)`<script>!function (d, s, id) { var js, fjs = d.getElementsByTagName(s)[0], p = /^http:/.test(d.location) ? 'http' : 'https'; if (!d.getElementById(id)) { js = d.createElement(s); js.id = id; js.src = p + "://platform.twitter.com/widgets.js"; fjs.parentNode.insertBefore(js, fjs); } }(document, "script", "twitter-wjs");</script>`
