---
uid: web-pages/overview/testing-and-debugging/introduction-to-debugging
title: Úvod do ladění ASP.NET – webové stránky servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Ladění je proces najít a opravit chyby ve znakových stránek. Tato kapitola ukazuje některé nástroje a techniky, které můžete použít k ladění a analyz...
ms.author: riande
ms.date: 02/20/2014
ms.assetid: 68de4326-7611-4b9b-b5f6-79b7adc3069f
msc.legacyurl: /web-pages/overview/testing-and-debugging/introduction-to-debugging
msc.type: authoredcontent
ms.openlocfilehash: e5302492f01cbd507e0b0fd995f21621bf6f04c8
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021310"
---
<a name="introduction-to-debugging-aspnet-web-pages-razor-sites"></a>Úvod do ladění ASP.NET – webové stránky servery (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje různé způsoby, jak ladit stránky na webu rozhraní ASP.NET Web Pages (Razor). Ladění je proces najít a opravit chyby ve znakových stránek.
>
> **Co se dozvíte:**
>
> - Jak zobrazit informace, které pomůžou analyzovat a ladění stránek.
> - Jak používat ladění nástroje v sadě Visual Studio.
>
>
> Toto jsou funkce technologie ASP.NET v následujícím článku:
>
> - `ServerInfo` Pomocné rutiny.
> - `ObjectInfo` pomocné rutiny.
>
>
> ## <a name="software-versions"></a>Verze softwaru
>
>
> - Webové stránky ASP.NET (Razor) 3
> - Visual Studio 2013
>
>
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2. Můžete použít nástroj WebMatrix 3 ale integrovaný ladicí program nepodporuje.


Důležitou součástí Poradce při potížích s chybami a problémy v kódu je se jim vyhnout na prvním místě. Můžete to udělat tak, že vložíte části kódu, které můžou způsobovat chyby do `try/catch` bloky. Další informace najdete v části o zpracování chyb v [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890).

`ServerInfo` Pomocné rutiny je diagnostický nástroj, který poskytuje přehled informací o prostředí webového serveru, který je hostitelem vaší stránky. Taky se naučíte HTTP žádost o informace, které se odešle, když prohlížeč požaduje na stránce. `ServerInfo` Aktuální identitu uživatele, typu prohlížeče, který vytvořil požadavek, zobrazí pomocná a tak dále. Tento druh informací vám pomohou vyřešit běžné problémy.

1. Vytvoření nové webové stránky s názvem *ServerInfo.cshtml*.
2. Na konci stránky těsně před uzavírací značku `</body>` značky, přidejte `@ServerInfo.GetHtml()`:

    [!code-cshtml[Main](introduction-to-debugging/samples/sample1.cshtml)]

    Můžete přidat `ServerInfo` kódu kamkoli do stránky. Ale přidání na konci se oddělovat jeho výstup z jiné stránky obsahu, který usnadňuje čtení.

    > [!NOTE]
    >
    > **Důležité** byste měli odebrat všechny diagnostický kód z vašich webových stránek před přesunutím webové stránky na provozním serveru. To platí pro `ServerInfo` pomocné rutiny, jakož i jiné diagnostické postupy v tomto článku, které se týkají přidání kódu do stránky. Nechcete návštěvnících webu zobrazíte informace o název serveru, uživatelská jména, cesty na serveru a podobné podrobnosti, protože tento typ informací může být užitečné lidí se zlými úmysly.
3. Uložit na stránku a spustíte ji v prohlížeči.

    ![Ladění 1](introduction-to-debugging/_static/image1.jpg)

    `ServerInfo` Zobrazí pomocná čtyři tabulky informace na stránce:

   - Konfigurace serveru. Tato část obsahuje informace o hostování webového serveru, včetně názvu počítače, na verzi ASP.NET, kterou používáte, název domény a čas serveru.
   - Proměnné serveru technologie ASP.NET. Tato část obsahuje podrobné informace o mnoho podrobností protokolu HTTP (volaná proměnné HTTP) a hodnoty, které jsou součástí každého požadavku webové stránky.
   - Informace o modulu HTTP Runtime. Tato část obsahuje podrobnosti o verzi rozhraní Microsoft .NET Framework, ve které běží webové stránky, cesta, údaje o mezipaměti a tak dále. (Protože jste se naučili v [Úvod do ASP.NET webové programování pomocí syntaxe Razor](https://go.microsoft.com/fwlink/?LinkId=202890), pomocí syntaxe jsou postavené na společnosti Microsoft ASP.NET web server technologii, která sama o sobě založená na rozsáhlé softwaru syntaxi Razor rozhraní ASP.NET Web Pages vývoj pro knihovnu s názvem rozhraní .NET Framework.)
   - Proměnné prostředí. Tato část obsahuje seznam všech proměnných místního prostředí a jejich hodnoty na webovém serveru.

     Úplný popis všechny žádost o informace o serveru a je nad rámec tohoto článku, ale vidíte, že `ServerInfo` pomocné rutiny vrací velké množství diagnostické informace. Další informace o hodnotách, které `ServerInfo` vrátí, naleznete v tématu [rozpoznán proměnné prostředí](https://technet.microsoft.com/library/dd560744(WS.10).aspx) na webu Microsoft TechNet a [proměnné serveru IIS](https://msdn.microsoft.com/library/ms524602(VS.90).aspx) na webu MSDN.

## <a name="embedding-output-expressions-to-display-page-values"></a>Vkládání výstup výrazy k zobrazení hodnot stránky

Dalším způsobem, jak vidět, co se děje ve vašem kódu, je vložit výrazy výstup na stránce. Jak už víte, můžete přímo tak, že přidáte něco jako výstup hodnotu proměnné `@myVariable` nebo `@(subTotal * 12)` na stránku. Pro ladění, můžete umístit tyto výrazy výstup strategická místa v kódu. To umožňuje zobrazit hodnotu klíče proměnné nebo výsledek výpočtů při spuštění vaší stránky. Po dokončení ladění, můžete odebrat výrazy nebo přidat komentář navýšení kapacity. Tento postup znázorňuje typický způsob, jak používat vložené výrazy pro ladění na stránce.

1. Vytvoří novou stránku služby WebMatrix, který je pojmenován *OutputExpression.cshtml*.
2. Nahraďte obsah následujícím kódem stránky:

    [!code-html[Main](introduction-to-debugging/samples/sample2.html)]

    V příkladu se používá `switch` příkaz a zkontrolujte hodnotu `weekday` proměnné a poté zobrazte je jiný výstupní zprávy podle toho, který den v týdnu. V tomto příkladu `if` zablokuje v rámci první blok kódu libovolně změny den v týdnu tak, že přidáte jeden den na hodnotu aktuální den v týdnu. Jedná se o chybu zavedeny jako ukázka.
3. Uložit na stránku a spustíte ji v prohlížeči.

    Na stránce zobrazí zprávu pro chybný den v týdnu. Ať den v týdnu, ve skutečnosti je, zobrazí se vám zpráva za jeden den později. I když v tomto případě vědět proč zprávy je vypnutý, (protože kód záměrně nastaví hodnota nesprávné dne), ve skutečnosti je často obtížné vědět, kde neuvádíme nesprávné v kódu. Chcete-li ladit, je potřeba zjistit, co se děje na hodnotu klíče objekty a proměnné, jako `weekday`.
4. Přidat výstupní výrazy vložením `@weekday` jak je znázorněno na dvou místech indikován komentáře v kódu. Tyto výrazy výstupu se zobrazí hodnoty proměnné v daném okamžiku v provádění kódu.

    [!code-csharp[Main](introduction-to-debugging/samples/sample3.cs?highlight=2-3,15-16)]
5. Uložte a spusťte na stránku v prohlížeči.

    Na stránce se zobrazí skutečné den v týdnu nejprve pak aktualizovaná den v týdnu, který výsledkem sečtení jeden den a výslednou zprávu z `switch` příkazu. Výstup z dvou proměnné výrazů (`@weekday`) nemá žádné mezery mezi dny, protože jste nepřidali žádné HTML `<p>` značky do výstupu; výrazy jsou jen pro testování.

    ![Ladění 2](introduction-to-debugging/_static/image2.jpg)

    Teď vidíte, kde je chyba. Při prvním zobrazení `weekday` proměnné v kódu, zobrazuje správný den. Při zobrazení ho ještě jednou po `if` blokovat v kódu, vypnutý den o jedna. Díky tomu víte, že má došlo mezi prvním a druhém vzhled proměnnou den v týdnu. Pokud toto Skutečná chyba, tento typ přístupu by vám zúžit umístění kód, který je příčinou problému.
6. Opravte kód na stránce odebráním dvě výstup výrazů, které jste přidali, a odebírání kód, který změní den v týdnu. Zbývající a kompletní blok kódu vypadá jako v následujícím příkladu:

    [!code-cshtml[Main](introduction-to-debugging/samples/sample4.cshtml)]
7. Spuštění stránky v prohlížeči. Tentokrát se zobrazí správný zpráva zobrazí skutečné dne v týdnu.

## <a name="using-the-objectinfo-helper-to-display-object-values"></a>Použití pomocné rutiny ObjectInfo k zobrazení hodnoty objektu

`ObjectInfo` Zobrazí pomocná typ a hodnotu každého objektu, který mu předáte. Slouží k zobrazení hodnoty proměnných a objektů v kódu (stejně jako výrazy výstup z předchozího příkladu). Navíc uvidíte datový typ informace o objektu.

1. Otevřete soubor s názvem *OutputExpression.cshtml* , který jste vytvořili dříve.
2. Následující blok kódu nahraďte celý kód na stránce:

    [!code-html[Main](introduction-to-debugging/samples/sample5.html)]
3. Uložte a spusťte na stránku v prohlížeči.

    ![Ladění 4](introduction-to-debugging/_static/image3.jpg)

    V tomto příkladu `ObjectInfo` pomocné rutiny zobrazí dvě položky:

   - Typ První proměnné, typ je `DayOfWeek`. Druhé proměnné, typ je `String`.
   - Hodnota V takovém případě vzhledem k tomu, že jste již zobrazit hodnotu proměnné pozdrav na stránce, hodnota se zobrazí znovu při předání proměnné k `ObjectInfo`.

     Pro složitější objekty `ObjectInfo` pomocné rutiny můžete zobrazit informace &#8212; v podstatě můžete zobrazit typy a hodnoty všech vlastností objektu.

## <a name="using-debugging-tools-in-visual-studio"></a>Pomocí nástroje pro ladění v sadě Visual Studio

Pro pokročilejší možnosti ladění, použijte [sady Visual Studio](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017). Pomocí sady Visual Studio můžete nastavit zarážku v kódu na řádku, který chcete zkontrolovat.

![nastavit zarážku](introduction-to-debugging/_static/image1.png)

Při testování webu prováděním kódu se zastaví na zarážce.

![dosažení zarážky](introduction-to-debugging/_static/image2.png)

Můžete zkontrolovat aktuální hodnoty proměnných a krokovat kódu řádek po řádku.

![Zobrazit hodnoty](introduction-to-debugging/_static/image3.png)

Informace o používání integrovaný ladicí program v sadě Visual Studio k ladění stránek ASP.NET Razor, naleznete v tématu [programování webových stránek ASP.NET (Razor) pomocí sady Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854).

## <a name="additional-resources"></a>Další prostředky

- [Programování webových stránek ASP.NET (Razor) pomocí sady Visual Studio](https://go.microsoft.com/fwlink/?LinkId=205854)
- [Proměnné serveru IIS](https://msdn.microsoft.com/library/ms524602(VS.90).aspx) (MSDN)
- [Proměnné prostředí rozpoznán](https://technet.microsoft.com/library/dd560744(WS.10).aspx) (TechNet)
