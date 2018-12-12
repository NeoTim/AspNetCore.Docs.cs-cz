---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Upgradování projektů SignalR 1.x na verzi 2 | Dokumentace Microsoftu
author: pfletcher
description: Toto téma popisuje, jak upgradovat existující projekt SignalR 1.x na SignalR 2.x a jak řešit problémy, které mohou nastat během procesu upgradu...
ms.author: riande
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: 23ea23585b15395cf86bdad13885af32d1b64e79
ms.sourcegitcommit: 74e3be25ea37b5fc8b4b433b0b872547b4b99186
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53286841"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a>Upgradování projektů SignalR 1.x na verzi 2
====================
podle [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> Toto téma popisuje, jak upgradovat existující projekt SignalR 1.x na SignalR 2.x a jak řešit problémy, které mohou nastat během procesu upgradu.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - Funkce SignalR verze 1 a 2
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a>V tomto kurzu pomocí sady Visual Studio 2012
>
>
> Pokud chcete použít Visual Studio 2012 s tímto kurzem, postupujte takto:
>
> - Aktualizace vašeho [Správce balíčků](http://docs.nuget.org/docs/start-here/installing-nuget) na nejnovější verzi.
> - Nainstalujte [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).
> - Instalace webové platformy, vyhledejte a nainstalujte **technologie ASP.NET a webové nástroje 2013.1 pro Visual Studio 2012**. Tím se nainstaluje šablony sady Visual Studio pro funkci SignalR třídy jako **centra**.
> - Některé šablony (jako například **třídy pro spuštění OWIN**) nebudou k dispozici; pro ty, použijte místo toho soubor třídy.
>
>
> ## <a name="questions-and-comments"></a>Otázky a komentáře
>
> Napište prosím zpětnou vazbu o tom, jak vám líbilo v tomto kurzu a co můžeme zlepšit v komentářích v dolní části stránky. Pokud máte nějaké otázky, které přímo nesouvisejí, najdete v tomto kurzu, můžete je publikovat [fórum ASP.NET SignalR](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) nebo [StackOverflow.com](http://stackoverflow.com/).


Funkce SignalR 2 nabízí konzistentními možnostmi vývoje napříč platformami server pomocí [OWIN](http://owin.org). Tento článek popisuje několik kroků, které jsou potřeba k aktualizaci aplikace SignalR 1.x na verzi 2.

Zatímco je podpořit inovace aplikací SignalR 2, SignalR 1.x, i nadále podporovat.

Tento kurz popisuje, jak upgradovat hostované webové aplikace knihovnou SignalR 2. V místním prostředí aplikace (těch, které jsou hostiteli serveru do konzolové aplikace, služby Windows nebo jiné procesy) jsou nyní podporovány v rámci SignalR 2. Informace o tom, jak hned začít vytvářet aplikace v místním prostředí s funkcí SignalR 2 najdete v tématu [kurzu: SignalR v místním](../deployment/tutorial-signalr-self-host.md).

## <a name="contents"></a>Obsah

Následující části popisují úlohy, které jsou spojené s upgradování projektů SignalR a řešení potíží, které mohou nastat.

- [Příklad: Upgrade kurzu Začínáme se SignalR 2](#example)
- [Řešení potíží s chybami během upgradu](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a>Příklad: Upgrade Začínáme kurz aplikace knihovnou SignalR 2

V této části budete aktualizovat aplikace vytvořená v [SignalR 1.x verzi kurzu Začínáme](../older-versions/index.md) použití funkcí SignalR 2.

1. Po dokončení kurzu Začínáme, klikněte pravým tlačítkem na projekt a vyberte **vlastnosti**. Ověřte, že **Cílová architektura** je nastavena na **rozhraní .NET Framework 4.5.**
2. Otevřete konzolu Správce balíčků. Odebrat SignalR 1.x z projektu pomocí následujícího příkazu:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. Instalace funkcí SignalR 2 pomocí následujícího příkazu:

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. Na stránce HTML aktualizujte odkaz na skript pro funkci SignalR tak, aby odpovídala verzi skriptů nyní zahrnutý v projektu.

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. Ve třídě globální aplikace odeberte volání MapHubs.

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. Klikněte pravým tlačítkem na řešení a vyberte **přidat**, **novou položku...** . V dialogovém okně vyberte **třídy pro spuštění Owin**. Pojmenujte novou třídu **Startup.cs**.

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. Nahraďte obsah souboru Startup.cs následujícím kódem:

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    Atribut sestavení přidá třídu do vaší Owin při spuštění procesu, který provede `Configuration` metoda při spuštění Owin. To zase vyžaduje `MapSignalR` metodu, která vytvoří trasy pro všechny rozbočovače SignalR v aplikaci.
8. Spuštění projektu a zkopírujte adresu URL na hlavní stránku do jiného prohlížeče nebo podokně prohlížeče jako předtím. Každá stránka vyzve k zadání uživatelského jména a zprávy odeslané z každé stránky by se zobrazovat v oběma podokny prohlížeče.

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a>Řešení potíží s chybami během upgradu

Tato část popisuje problémy, které mohou nastat během upgradu. Úplnější seznam chyb a problémů, kterým může dojít u aplikace SignalR naleznete v tématu [řešení potíží s knihovnou SignalR](../testing-and-debugging/troubleshooting.md).

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a>"Volání je nejednoznačné mezi následující metody nebo vlastnosti.

Tato chyba nastane, pokud odkaz na `Microsoft.AspNet.SignalR.Owin` se neodebere. Tento balíček je zastaralý; odkaz musí být odstraněny a verzi 1.x balíčku hostitel ve vlastním procesu musí být odinstalován.

### <a name="hub-methods-fail-silently"></a>Bez upozornění nezdaří metod rozbočovače na

Ověřte, zda jsou odkazy na skript v klientovi, a který `OwinStartup` atribut pro třídu pro spuštění má správné třídy a názvy sestavení pro projekt. Také zkuste otevřít adresu rozbočovače (/ signalr/hubs) v prohlížeči. Další informace o co je špatně nabízejí případnou chybu, která se zobrazí.
