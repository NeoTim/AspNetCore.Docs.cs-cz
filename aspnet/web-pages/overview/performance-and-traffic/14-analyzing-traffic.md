---
uid: web-pages/overview/performance-and-traffic/14-analyzing-traffic
title: Sledování návštěvníka informace (Analytics) pro webové stránky ASP.NET (Razor) lokality | Dokumentace Microsoftu
author: Rick-Anderson
description: Poté, co jste získali váš web bude, můžete analyzovat provoz vašeho webu.
ms.author: riande
ms.date: 02/17/2014
ms.assetid: 360bc6e1-84c5-4b8e-a84c-ea48ab807aa4
msc.legacyurl: /web-pages/overview/performance-and-traffic/14-analyzing-traffic
msc.type: authoredcontent
ms.openlocfilehash: 57e6a0d4681f147faa5e9ca3b6ed0ef287d6a381
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020881"
---
<a name="tracking-visitor-information-analytics-for-an-aspnet-web-pages-razor-site"></a>Sledování návštěvníka informace (Analytics) pro ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje způsob použití pomocné rutiny pro přidání analýzy webů na stránky na webu rozhraní ASP.NET Web Pages (Razor).
> 
> Co se dozvíte:
> 
> - Jak odeslat informace o provozu webu poskytovatele analýz.
> 
> Toto jsou ASP.NET programování funkcí představených v následujícím článku:
> 
> - `Analytics` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Knihovnu ASP.NET Web Helpers (balíček NuGet)


Analýza je obecný termín pro technologie, která měří provoz na vašem webu to vám umožní pochopit, jak ostatní používají Web. Mnoho služeb analytics jsou k dispozici, včetně služby od Google, Yahoo, StatCounter a dalších.

Analýzy způsob, jak funguje je, že zaregistrujete účet s tímto poskytovatelem analytics, ve kterém zaregistrujete lokalitu, které chcete sledovat. Zprostředkovatel odešle fragment kódu jazyka JavaScript, který obsahuje ID nebo kód pro váš účet sledování. Přidejte javascriptový fragment kódu na webové stránky na webu, který chcete sledovat. (Obvykle přidáte fragment analytics zápatí ani rozložení stránky nebo jiné kódování HTML, který se zobrazí na každé stránce ve vaší lokalitě.) Pokud uživatelé požadují stránku, která obsahuje jeden z těchto fragmenty kódu jazyka JavaScript, fragment kódu odešle do poskytovatele analýz, který zaznamenává detailní informace o stránce informace o aktuální stránku.

Pokud chcete podívejte se na vaše Statistika webu přihlásíte webu poskytovatele analýz. Pak můžete zobrazit nejrůznější sestavy o vašem webu, jako jsou:

- Počet zobrazení stránek pro jednotlivé stránky. Znamená to (přibližně) kolik návštěvníků webu a které stránky na webu jsou ta úplně nejoblíbenější.
- Jak dlouho strávit lidí na konkrétní stránky. To se dozvíte, například, jestli je domovská stránka udržovat zájmu uživatelů.
- Jaké weby uživatelé byli před navštívili vašeho webu. To vám pomůže pochopit, jestli je provoz přicházející z odkazy z prohledávání a tak dále.
- Když uživatelé, kteří navštíví váš web a jak dlouho zůstávají.
- Které země jsou návštěvnících z.
- Jaké prohlížeče a operační systémy návštěvnících používají.

    ![Ch14traffic-1](14-analyzing-traffic/_static/image1.jpg)

## <a name="using-a-helper-to-add-analytics-to-a-page"></a>Použití pomocné rutiny na Analytics přidat na stránku

Rozhraní ASP.NET Web Pages zahrnuje několik pomocných rutin analytics (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, a `Analytics.GetStatCounterHtml`), které usnadňují správu fragmenty kódu jazyka JavaScript používá pro účely analýzy. Místo tím, jak a kde do kódu jazyka JavaScript, vše, co musíte udělat, je přidání pomocné rutiny na stránku. Jediná informace, které je potřeba zadat je název účtu, ID nebo sledování kódu. (Pro StatCounter, budete také muset poskytnout pár dalších hodnot.)

V tomto postupu vytvoříte stránku rozložení, který používá `GetGoogleHtml` pomocné rutiny. Pokud již máte účet s jednou z jiné poskytovatele analytických služeb, můžete místo toho použijte tento účet a provádět lehké úpravy podle potřeby.

> [!NOTE]
> Když vytvoříte účet analytics, je zaregistrovat adresu URL webu, který chcete sledovat. Pokud testujete všechno, co v místním počítači, můžete nebude sledovat skutečný provoz (pouze provoz je můžete), tak nebude možné zaznamenat a zobrazit statistiku serveru. Ale tento postup ukazuje, jak přidat analytics pomocné rutiny na stránku. Při publikování webu živého webu bude odesílat informace do vašeho zprostředkovatele datové analýzy.


1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste to ještě nepřidali.
2. Vytvoření účtu pomocí Google Analytics a poznamenejte název účtu.
3. Vytvoření rozložení stránky s názvem *Analytics.cshtml* a přidejte následující kód:

    [!code-cshtml[Main](14-analyzing-traffic/samples/sample1.cshtml)]

    > [!NOTE]
    > Je nutné umístit volání `Analytics` pomocné rutiny v těle webové stránky (před `</body>` značky). V opačném případě nebude prohlížeče spusťte skript.

    Pokud používáte jiný analytics poskytovatele, použijte jednu z následujících pomocné rutiny:

    - (Yahoo) `@Analytics.GetYahooHtml("myaccount")`
    - (StatCounter) `@Analytics.GetStatCounterHtml("project", "security")`
4. Nahraďte `myaccount` s názvem účtu, ID nebo sledování kód, který jste vytvořili v kroku 1.
5. Spuštění stránky v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)
6. V prohlížeči zobrazte zdroj stránky. Budete moct zobrazit vykreslený analýzy kódu:

    [!code-html[Main](14-analyzing-traffic/samples/sample2.html)]
7. Přihlášení na web Google Analytics a prozkoumejte statistiky pro váš web. Pokud spouštíte na živý web na stránku, uvidíte položku, která protokoluje najdete na stránce.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky

- [Web Google Analytics](https://www.google.com/analytics/)
- [Yahoo! Web Analytics lokality](http://help.yahoo.com/l/us/yahoo/ywa/)
- [StatCounter lokality](http://statcounter.com/)
