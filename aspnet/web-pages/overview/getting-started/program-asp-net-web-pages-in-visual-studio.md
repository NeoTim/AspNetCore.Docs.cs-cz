---
uid: aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
title: Programování webových stránek technologie ASP.NET (Razor) pomocí sady Visual Studio | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento dodatek vysvětluje, jak můžete použít Visual Studio 2010 a program Visual Web Developer 2010 Express do programu rozhraní ASP.NET Web Pages se syntaxí Razor.
ms.author: riande
ms.date: 02/13/2014
ms.assetid: 0acfec5a-48f2-4766-a801-a0f426966f0a
msc.legacyurl: /web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio
msc.type: authoredcontent
ms.openlocfilehash: 5b8df17ec1021d133579e23cb4f5b0d0f67d4c7c
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020452"
---
<a name="programming-aspnet-web-pages-razor-using-visual-studio"></a>Programování webových stránek ASP.NET (Razor) pomocí sady Visual Studio
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak můžete pomocí sady Visual Studio nebo Visual Web Developer Express do programu websites webových stránek ASP.NET (Razor).
>
> Co se dozvíte
>
> - Co je potřeba nainstalovat (Pokud se něco) pro práci s webovými stránkami ASP.NET ve vaší verzi sady Visual Studio.
> - Jak přidat podporu pro webové stránky ASP.NET pro aplikaci Visual Web Developer 2010 Express.
> - Jak používat funkce v sadě Visual Studio pro práci s ASP.NET Razor pages, včetně technologie IntelliSense a ladicí program.
>
>
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
>
>
> - Webové stránky ASP.NET (Razor) 3
> - Visual Studio 2013
> - Služba WebMatrix 3
>
>
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2, Visual Studio 2012, Visual Studio 2010 a službě WebMatrix 2.


Můžete naprogramovat ASP.NET Web pages se syntaxí Razor pomocí služby WebMatrix nebo mnoha jiných editorů kódu. Můžete také použít Microsoft Visual Studio, což je plně vybavené integrované vývojové prostředí (IDE), která poskytuje výkonnou sadu nástrojů pro vytváření mnoho typů aplikací (ne jenom weby). Chcete-li pracovat se stránkami ASP.NET Razor, můžete použít [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017).

Jsou dvě užitečné funkce, které poskytuje Visual Studio pro programování s webovými stránkami ASP.NET Razor:

- *Technologie IntelliSense*. Funkce technologie IntelliSense, integrované do sady Visual Studio je komplexnější než IntelliSense ve Webmatrixu.
- *Ladicí program*. Ladicí program umožňuje řešit kódu zastavte program je spuštěn, zkoumání proměnné a krokování kódu řádek po řádku.

## <a name="using-visual-studio-with-different-versions-of-aspnet-web-pages"></a>Pomocí sady Visual Studio s různými verzemi nástroje ASP.NET Web Pages

Pro vývoj webových aplikací ASP.NET v sadě Visual Studio 2017, nainstalujte **vývoj pro ASP.NET a web** pracovního vytížení.

Visual Studio 2012 a Visual Studio 2013 zahrnují podporu pro rozhraní ASP.NET Web Pages. (Balíčky, které jsou nutné pro podporu rozhraní ASP.NET Web Pages nainstalují při instalaci sady Visual Studio.)

Visual Studio 2010 nezahrnuje podporu ve výchozím nastavení pro rozhraní ASP.NET Web Pages. Rozhraní ASP.NET Web Pages pomocí sady Visual Studio 2010, musíte nainstalovat balíček ASP.NET MVC. ASP.NET Web Pages 2 získáte instalace technologie ASP.NET MVC 4.

Následující tabulka shrnuje podporu pro rozhraní ASP.NET Web Pages v různých verzích systému Visual Studio.

|  | Visual Studio 2010 | Visual Studio 2012 | Visual Studio 2013 |
| --- | --- | --- | --- |
| **Rozhraní ASP.NET Web Pages 2** | Instalace technologie ASP.NET MVC 4 | (Zahrnuto) | (Zahrnuto) |
| **ASP.NET – webové stránky 3** |  | Aktualizace technologie ASP.NET webové stránky 3 přes NuGet | (Zahrnuto) |

Pokud chcete pracovat se službou Visual Studio 2010, přečtěte si téma [instalace podpory pro rozhraní ASP.NET Web Pages v sadě Visual Studio 2010](#vs2010support).

## <a name="launching-visual-studio-from-webmatrix"></a>Spuštění sady Visual Studio ze služby WebMatrix

Pokud jste vytvořili projekt v nástroji WebMatrix a chcete ho přepněte do aplikace Visual Studio, služba WebMatrix poskytuje možnost jednoduše otevřete projekt v sadě Visual Studio. Musíte mít nainstalovanou sadu Visual Studio v počítači pro toto tlačítko Povolit. Následující obrázek ukazuje tlačítko ve Webmatrixu.

![Spusťte sadu Visual Studio](program-asp-net-web-pages-in-visual-studio/_static/image1.png)

Když kliknete na tlačítko, projekt otevřen v sadě Visual Studio. Můžete přepínat vpřed a zpět mezi službu WebMatrix a sady Visual Studio bez problémů. Pokud se všechny soubory byly změněny v jiném prostředí a být potřeba znovu načíst k získání nejnovějších změn, budete upozorněni.

## <a name="creating-aspnet-razor-site-in-visual-studio"></a>Vytváří se syntaxe Razor rozhraní ASP.NET Web v sadě Visual Studio

Chcete-li v sadě Visual Studio můžete vytvořit web ASP.NET Razor:

1. Otevřít Visual Studio.
2. V **souboru** nabídky, klikněte na tlačítko **nový web**.

    ![Vytvořit nový web](program-asp-net-web-pages-in-visual-studio/_static/image2.png)
3. V **nový web** dialogového okna, vyberte jazyk, který chcete použít (Visual C# nebo Visual Basic).
4. Vyberte **webové stránky ASP.NET (Razor)** šablony.

    ![webu Razor](program-asp-net-web-pages-in-visual-studio/_static/image3.png)
5. Klikněte na tlačítko **OK**.

Nový projekt existuje a naplní se některé výchozí webové stránky, abychom vám mohli začít.

### <a name="using-intellisense"></a>Používání atributu IntelliSense

Teď, když jste vytvořili webu, najdete v článku Jak funguje technologie IntelliSense v sadě Visual Studio.

1. Na webu jste právě vytvořili, otevřete *stránku Default.cshtml* stránky.
2. Po `<h3>` značky na stránce zadejte `@ServerInfo.` (včetně tečky). Všimněte si, jak technologie IntelliSense zobrazuje dostupné metody pro `ServerInfo` pomocné rutiny v rozevíracím seznamu.

    ![technologie IntelliSense](program-asp-net-web-pages-in-visual-studio/_static/image4.png)
3. Vyberte `GetHtml` metody ze seznamu a potom stiskněte klávesu Enter. Technologie IntelliSense automaticky vyplní metodu. (Jak s jakoukoli metodou v jazyce C#, je nutné přidat `()` znaky za metodu.) Dokončený kód `GetHtml` metoda vypadá jako v následujícím příkladu:

    [!code-cshtml[Main](program-asp-net-web-pages-in-visual-studio/samples/sample1.cshtml)]
4. Stiskněte kombinaci kláves Ctrl + F5 ke spuštění stránky. Je to, co bude stránka vypadat, když se zobrazí v prohlížeči:

    ![výchozí stránku v prohlížeči](program-asp-net-web-pages-in-visual-studio/_static/image5.png)
5. Zavřete prohlížeč.

### <a name="using-the-debugger"></a>Pomocí ladicího programu

1. V horní části *stránku Default.cshtml* stránku po řádek, který začíná `Page.Title`, přidejte následující řádek kódu:

    [!code-csharp[Main](program-asp-net-web-pages-in-visual-studio/samples/sample2.cs)]
2. Šedé okraj editoru nalevo od kódu, klikněte vedle tohoto nového řádku, chcete-li přidat *zarážku*. Zarážka je, že se ladicí program zastaví, abyste si mohli zobrazit, co se děje v daném okamžiku spuštění programu značku.

    ![nastavit zarážku](program-asp-net-web-pages-in-visual-studio/_static/image6.png)
3. Odeberte volání `ServerInfo.GetHtml` metoda a přidejte volání `@myTime` proměnné na příslušné místo. Toto volání zobrazí aktuální hodnotu čas, který je vrácen nový řádek kódu.
4. Stiskněte klávesu F5 ke spuštění stránky v ladicím programu. Na stránce se zastaví na zarážce, kterou jste nastavili. Následující obrázek ukazuje, co bude stránka vypadat jako v editoru se zarážkou (žlutě).

    ![ladění zarážku](program-asp-net-web-pages-in-visual-studio/_static/image7.png)
5. Na panelu nástrojů ladění, klikněte na tlačítko **Krokovat s vnořením** tlačítko (nebo stisknutím klávesy F11) spusťte další řádek kódu. Pokaždé, když kliknete na toto tlačítko přechodu spuštění na další řádek kódu.

    ![Krokovat s vnořením tlačítko](program-asp-net-web-pages-in-visual-studio/_static/image8.png)
6. Zkoumat hodnoty `myTime` proměnné tak, že ukazatel myši nad ním nebo zkontrolováním hodnoty zobrazené v **lokální** a **zásobník volání** systému windows. Visual Studio umožňuje zobrazit hodnotu proměnné.

    ![hodnoty času zobrazit](program-asp-net-web-pages-in-visual-studio/_static/image9.png)
7. Až skončíte zkoumání proměnné a krokování kódem, stiskněte klávesu F5, aby kontinuálně běžely na stránku bez zastavení na každém řádku. Jakmile budete hotovi, krokování kódu, prohlížeč zobrazí na stránce.

Další informace o ladicím programu a o tom, jak ladit kód v sadě Visual Studio najdete v tématu [návod: ladění webové stránky v aplikaci Visual Web Developer](https://msdn.microsoft.com/library/z9e7w6cs.aspx).

## <a name="using-razor-in-aspnet-mvc-projects-with-visual-studio"></a>V projektech ASP.NET MVC pomocí sady Visual Studio pomocí syntaxe Razor

Syntaxe Razor se také často používá v projektech ASP.NET MVC. MVC je výkonný, na základě vzorů způsob, jak vytvářet dynamické weby. Pokud webové stránky ASP.NET Web přestane být obtížné zachovat, můžete chtít zvažte jeho převod na aplikace ASP.NET MVC. Příklad vytvoření aplikace MVC, naleznete v tématu [Začínáme s ASP.NET MVC 5](../../../mvc/overview/getting-started/introduction/getting-started.md).

<a id="vs2010support"></a>
## <a name="installing-support-for-aspnet-web-pages-in-visual-studio-2010"></a>Instalace podpory pro webové stránky ASP.NET v sadě Visual Studio 2010

V této části najdete postup instalace nástroje webových stránek ASP.NET a Visual Web Developer Express 2010 pro sadu Visual Studio.

1. Pokud ještě nemáte instalačního programu webové platformy, můžete ji stáhněte z následující adresy URL:

    [https://www.microsoft.com/web/downloads/platform.aspx](https://www.microsoft.com/web/downloads/platform.aspx)
2. Spuštění instalačního programu webové platformy.
3. Klikněte na tlačítko **produkty** kartu.

    ![Karta produktů instalace webové platformy](program-asp-net-web-pages-in-visual-studio/_static/image10.png)
4. Vyhledejte **ASP.NET MVC 4** (pro ASP.NET Web Pages 2) a potom klikněte na tlačítko **přidat**. Tyto produkty zahrnují Visual Studio tools pro vytváření webů ASP.NET Razor.

    ![Možnosti instalace instalace webové platformy](program-asp-net-web-pages-in-visual-studio/_static/image11.png)
5. Klikněte na tlačítko **nainstalovat** k dokončení instalace.
