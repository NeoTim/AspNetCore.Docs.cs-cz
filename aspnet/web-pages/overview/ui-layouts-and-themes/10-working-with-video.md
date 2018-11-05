---
uid: web-pages/overview/ui-layouts-and-themes/10-working-with-video
title: Zobrazení videa v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola popisuje zobrazení videa ASP.NET Web Pages se stránkou pro syntaxi Razor.
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 332fb3da-e2a5-460d-bb90-dd911e1e2c95
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/10-working-with-video
msc.type: authoredcontent
ms.openlocfilehash: 8f4b7186ae5c7b7b384ebcb23f7c9ad65caeb0bd
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021037"
---
<a name="displaying-video-in-an-aspnet-web-pages-razor-site"></a>Zobrazení videa na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak použít přehrávač videa (média) na webu rozhraní ASP.NET Web Pages (Razor) umožňuje uživatelům zobrazit videa, které jsou uloženy na serveru. ASP.NET Web Pages se syntaxí Razor umožňuje přehrát Flash (*SWF*), Media Player (*WMV*) a Silverlight (*.xap*) videa.
> 
> Co se dozvíte:
> 
> - Jak zvolit přehrávač videa.
> - Jak přidat video do webové stránky.
> - Jak nastavit atributy přehrávač videa.
> 
> Jedná se o syntaxi ASP.NET Razor stránky funkcí představených v následujícím článku:
> 
> - `Video` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 2
>   
> 
> V tomto kurzu funguje taky pomocí služby WebMatrix 3.


## <a name="introduction"></a>Úvod

Můžete chtít zobrazit videa ve vaší lokalitě. Jedním ze způsobů, který je propojení na lokalitu, která už má video, jako je YouTube. Pokud chcete vložit video z těchto webů přímo do vašich vlastních stránek, můžete obvykle se zobrazí kód HTML z lokality a potom ho zkopírujete do stránky. Například následující příklad ukazuje postup vložení YouTube videa:

[!code-html[Main](10-working-with-video/samples/sample1.html?highlight=10-14)]

Pokud chcete přehrát video, které je na svůj vlastní web (ne na veřejný web sdílení videí), nelze propojit přímo pomocí vloženého kódu tímto způsobem. Však můžete přehrát videa z webu pomocí `Video` pomocné rutiny, která vykresluje media player přímo na stránce.

<a id="Choosing_a_Video_Player"></a>
## <a name="choosing-a-video-player"></a>Výběr přehrávače videa

Existuje mnoho formátů videosouborů a každý formát obvykle vyžaduje jiný přehrávač a způsobu, jak nakonfigurovat přehrávač. Na stránkách ASP.NET Razor můžete přehrát video webovou stránku pomocí `Video` pomocné rutiny. `Video` Pomocné rutiny zjednodušuje proces vkládání videí na webové stránce, protože automaticky generuje `object` a `embed` elementy HTML, které se běžně používají ke přidat video do stránky.

`Video` Pomocné rutiny podporuje následující přehrávače médií:

- Adobe Flash
- Windows MediaPlayer
- Microsoft Silverlight

### <a name="the-flash-player"></a>Přehrávače pro Flash

`Flash` Aktér `Video` pomocné rutiny umožňují přehrávat animace Flash videa (*SWF* soubory) na webové stránce. Přinejmenším budete muset zadat cestu k souboru videa. Pokud chcete zadat pouze cestu, hráč používá výchozí hodnoty, které jsou nastavené v aktuální verzi pracovat bez velkých příprav. Výchozí nastavení jsou:

- Video se zobrazí pomocí jeho výchozí šířky a výšky a bez barvu pozadí.
- Video se přehraje automaticky při načtení stránky.
- Video smyčky nepřetržitě, dokud explicitně je zastavená.
- Video se škálovat zobrazíte všechna videa, místo oříznutí videa podle určité velikosti.
- Video se přehraje v okně.

### <a name="the-mediaplayer-player"></a>MediaPlayer Player

`MediaPlayer` Aktér `Video` pomocné rutiny umožňuje přehrát Windows Media Video (*WMV* soubory), Windows Media audio (*.wma* soubory) a MP3 (*.mp3* soubory) na webové stránce. Musí obsahovat cestu k souboru médií k přehrání; všechny ostatní parametry jsou volitelné. Pokud chcete zadat pouze cestu, přehrávač používá výchozí nastavení nastavte pomocí aktuální verze MediaPlayer, jako je třeba:

- Video se zobrazí pomocí jeho výchozí šířka a výška.
- Video se přehraje automaticky při načtení stránky.
- Video se přehraje jednou (ho nebude opakovat).
- Přehrávač zobrazuje úplnou sadu ovládacích prvků v uživatelském rozhraní.
- Video se přehraje v okně.

### <a name="the-silverlight-player"></a>Přehrávač Silverlight

`Silverlight` Aktér `Video` pomocné rutiny umožňuje přehrát Windows Media Video (*WMV* soubory), Windows Media zvuku (*.wma* soubory) a MP3 (*.mp3* soubory). Je nutné nastavit parametr cesty tak, aby odkazoval na balíček aplikace založené na technologii Silverlight (*.xap* souboru). Je třeba také nastavit parametry šířku a výšku. Všechny ostatní parametry jsou volitelné. Při použití technologie Silverlight přehrávače videa, pokud nastavíte jenom požadované parametry, zobrazí Silverlight přehrávač videa bez barvu pozadí.

> [!NOTE]
> V případě, že ještě neznáte Silverlight: *.xap* soubor je komprimovaný soubor, který obsahuje rozložení podle pokynů v *.xaml* souboru spravovaného kódu v sestavení a volitelné materiály. Můžete vytvořit *.xap* souboru v sadě Visual Studio jako projekt aplikace Silverlight.


`Silverlight` Přehrávač videa používá jak nastavení, které zadáte pro přehrávač a nastavení, které jsou součástí *.xap* souboru.

> [!TIP] 
> 
> <a id="SB_MimeTypes"></a>
> ### <a name="mime-types"></a>Typy MIME
> 
> Když prohlížeč stáhne soubor, prohlížeče, zajišťuje, typ souboru odpovídá typ MIME zadaný pro dokument, který se vykresluje. Typ MIME je typ nebo médium typ obsahu souboru. `Video` Pomocné rutiny používá následující typy MIME:
> 
> - `application/x-shockwave-flash`
> - `application/x-mplayer2`
> - `application/x-silverlight-2`


<a id="Playing_Flash"></a>
## <a name="playing-flash-swf-videos"></a>Přehrávání videa pro Flash (SWF)

Tento postup ukazuje, jak přehrávat animace Flash video s názvem *sample.swf*. V postupu se předpokládá, že máte složku s názvem *média* na váš web a že *SWF* soubor je v této složce.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste to ještě nepřidali.
2. Na webu, přidejte na stránku a pojmenujte ho *FlashVideo.cshtml*.
3. Přidejte následující kód na stránku: 

    [!code-cshtml[Main](10-working-with-video/samples/sample2.cshtml)]
4. Spuštění stránky v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.) Na stránce se zobrazí a video se přehraje automaticky. 

    ![[image]](10-working-with-video/_static/image1.jpg "ch08_video-1.jpg")

Můžete nastavit `quality` parametr pro Flash video a `low`, `autolow`, `autohigh`, `medium`, `high`, a `best`:

[!code-cshtml[Main](10-working-with-video/samples/sample3.cshtml)]

Můžete změnit na Flash video přehrát na konkrétní velikosti pomocí `scale` parametr, který můžete nastavit takto:

- `showall`. Díky tomu celé video viditelné při zachování původního poměru stran obrázku. Však může skončit s ohraničení na každé straně.
- `noorder`. To při zachování poměru stran původní škáluje videa, ale může být oříznuté.
- `exactfit`. Díky tomu celé video viditelné bez zachování původní poměr stran, ale může dojít k narušení.

Pokud nezadáte `scale` parametr celé video se nebude zobrazovat a původní poměr stran obrázku se nebude udržovat bez jakékoli oříznutí. Následující příklad ukazuje způsob použití `scale` parametr:

[!code-cshtml[Main](10-working-with-video/samples/sample4.cshtml)]

Flash player podporuje video režim nastavení s názvem `windowMode`. Tento parametr můžete nastavit `window`, `opaque`, a `transparent`. Ve výchozím nastavení `windowMode` je nastavena na `window`, která zobrazí video v samostatném okně na webové stránce. `opaque` Nastavení skryje všechno, co se za video na webové stránce. `transparent` Umožní její nastavení zajistit na pozadí webovou stránku zobrazit prostřednictvím videa, za předpokladu, že libovolné části videa je transparentní.

<a id="Playing_MediaPlayer"></a>
## <a name="playing-mediaplayer-wmv-videos"></a>Přehrávání MediaPlayer (*WMV*) videa

Následující postup ukazuje, jak k přehrání videa Media okno s názvem *sample.wmv* , který je *média* složky.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
2. Vytvoří novou stránku s názvem *MediaPlayerVideo.cshtml*.
3. Přidejte následující kód na stránku: 

    [!code-cshtml[Main](10-working-with-video/samples/sample5.cshtml)]
4. Spuštění stránky v prohlížeči. Video načte a přehraje automaticky. 

    ![[image]](10-working-with-video/_static/image2.jpg "ch08_video-2.jpg")

Můžete nastavit `playCount` na celé číslo, která určuje, kolikrát Pokud chcete přehrát video automaticky:

[!code-cshtml[Main](10-working-with-video/samples/sample6.cshtml)]

`uiMode` Parametr umožňuje určit, jaké ovládací prvky zobrazí v uživatelském rozhraní. Můžete nastavit `uiMode` k `invisible`, `none`, `mini`, nebo `full`. Pokud nezadáte `uiMode` parametr, bude se na video zobrazí se stavové okno hledání panelu, ovládací prvek tlačítka a ovládání hlasitosti kromě grafického okna. Tyto ovládací prvky se zobrazí také v případě použití přehrávače pro přehrání zvukového souboru. Tady je příklad, jak používat `uiMode` parametr:

[!code-cshtml[Main](10-working-with-video/samples/sample7.cshtml)]

Ve výchozím nastavení je zvuk na při přehrávání videa. Nastavením může ztlumit zvuk `mute` parametr na hodnotu true:

[!code-cshtml[Main](10-working-with-video/samples/sample8.cshtml)]

Hlasitost videa MediaPlayer můžete řídit tak, že nastavíte `volume` parametr na hodnotu mezi 0 a 100. Výchozí hodnota je 50. Tady je příklad:

[!code-cshtml[Main](10-working-with-video/samples/sample9.cshtml)]

<a id="Playing_Silverlight"></a>
## <a name="playing-silverlight-videos"></a>Přehrávání videa technologie Silverlight

Tento postup ukazuje, jak k přehrání videa, které jsou obsaženy v technologii Silverlight *.xap* stránka, která je ve složce s názvem *média*.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
2. Vytvoří novou stránku s názvem *SilverlightVideo.cshtml*.
3. Přidejte následující kód na stránku: 

    [!code-cshtml[Main](10-working-with-video/samples/sample10.cshtml)]
4. Spuštění stránky v prohlížeči. 

    ![[image]](10-working-with-video/_static/image3.jpg "ch08_video-3.jpg")

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Přehled technologie Silverlight](https://msdn.microsoft.com/library/bb404700(VS.95).aspx)

[Atributy značky Flash OBJEKT a vložení](http://kb2.adobe.com/cps/127/tn_12701.html)

[Windows Media Player 11 SDK PARAM značky](https://msdn.microsoft.com/library/aa392321(VS.85).aspx)
