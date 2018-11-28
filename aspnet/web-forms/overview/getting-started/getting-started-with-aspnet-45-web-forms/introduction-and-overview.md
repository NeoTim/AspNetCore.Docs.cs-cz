---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2013 | Dokumentace Microsoftu
author: Erikre
description: Tato série podrobných kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET pomocí technologie ASP.NET 4.5 a službu Microsoft Visual Studio působivý...
ms.author: riande
ms.date: 09/08/2014
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: b9ed6ce4ac13f047f53c56e183433cbd038ea15c
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450681"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2013"></a>Začínáme s webovými formuláři ASP.NET 4.5 a sadou Visual Studio 2013
====================
podle [Erik Reitan](https://github.com/Erikre)

[Stáhněte si ukázkový projekt Wingtip Toys (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) nebo [stáhnout elektronickou knihu (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

Tato série podrobných kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET pomocí technologie ASP.NET 4.5 a službu Microsoft Visual Studio Express 2013 for Web. 

## <a name="introduction"></a>Úvod

Tato série kurzů vás provede kroky potřebné k vytvoření aplikace webových formulářů ASP.NET pomocí Visual Studio Express 2013 pro webové aplikace a technologii ASP.NET 4.5.

Aplikace vytvoříte jmenuje **Wingtip Toys**. Je zjednodušený příklad webového serveru front úložiště, který se prodává zboží online. V této sérii kurzů označuje nové funkce, které jsou k dispozici v technologii ASP.NET 4.5.

Komentáře jsou úvodní a uděláme veškeré úsilí, aby aktualizace na základě vašich návrhů v této sérii kurzů.

### <a name="download-completed-project"></a>Stažení se dokončilo projektu

Můžete stáhnout projektu C#, který obsahuje dokončení kurzu.

- [Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2013 – na adresář Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)

### <a name="review-the-content-by-taking-the-related-aspnet-web-forms-quiz"></a>Zkontrolujte obsah provedením související kvíz webových formulářů ASP.NET

Po dokončení tohoto kurzu, otestujte svoje znalosti a posílit klíčové koncepty provedením [ASP.NET Web Forms kvíz](http://quizapp.cloudapp.net/?quiz=ASP.NET&cdn_id=2014-01-17-001). Tohoto kvízu je navržená speciálně z obsahu obsažené v této sérii kurzů. Každý dotaz do ukončení kvízu obsahuje vysvětlení společně s odkazy na další pokyny.

- [Webové formuláře ASP.NET kvíz](http://quizapp.cloudapp.net/?quiz=ASP.NET)

### <a name="audience"></a>Cílová skupina

Jeho zamýšlenou cílovou skupinou v této sérii kurzů je zkušení vývojáři, kteří jsou novinky webových formulářů ASP.NET. Vývojářem dotčené v této řadě kurzů by měl mít následující schopnosti:

- Známý s objektem orientovaný programovací jazyk (OOP)
- Dobře známé koncepty vývoj pro Web (HTML, CSS a JavaScript)
- Dobře známé koncepty relační databáze
- Dobře známé koncepty n vrstvou architekturu

Pokud jste zajímat téma oblastech uvedených výše, vezměte v úvahu revize následujícím obsahem:

- [Začínáme s Visual C#](https://msdn.microsoft.com/library/a72418yk.aspx)
- [Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)
- [Relační databáze](http://en.wikipedia.org/wiki/Relational_database)
- [Vícevrstvé architektury](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a>Funkce aplikací

Webový formulář ASP.NET funkce uvedené v této sérii:

- Projekt webové aplikace (ne webový projekt)
- webové formuláře
- Stránky předlohy, konfigurace
- Bootstrap
- Entity Framework Code nejprve LocalDB
- Žádost o ověření
- Ovládací prvky dat silného typu vazby anotacemi dat modelu a hodnota se zprostředkovatelé
- Protokol SSL a protokolem OAuth
- ASP.NET Identity, konfigurace a autorizace
- Nerušivý ověření
- Směrování
- Zpracování chyb v ASP.NET

### <a name="application-scenarios-and-tasks"></a>Scénáře aplikací a úloh

Úlohy, které jsme vám ukázali v této sérii patří:

- Vytváření, revizí a spuštění nového projektu
- Vytvoření struktury databáze
- Inicializace a synchronizace replik indexů databáze
- Přizpůsobení uživatelského rozhraní pomocí styly, grafiku a na stránku předlohy
- Přidání stránky a navigace
- Zobrazení Podrobnosti o nabídce a data produktu.
- Vytvoření nákupního košíku
- Podpora přidání SSL a protokolem OAuth
- Přidání způsob platby
- Včetně role správce a uživatele k aplikaci
- Omezení přístupu ke konkrétní stránky a složka
- Po nahrání souboru do webové aplikace
- Implementace ověření vstupu
- Registrace trasy pro webovou aplikaci
- Implementace zpracování chyb a protokolování chyb

## <a name="overview"></a>Přehled

Pokud jste ještě na webové formuláře ASP.NET ale mít seznámení se s koncepty programování, mít správný kurz. Pokud jste již obeznámeni s webových formulářů ASP.NET, vám může přinést za nové funkce, které jsou k dispozici v technologii ASP.NET 4.5 v této sérii kurzů. Pokud nejste obeznámeni s programování konceptů a webových formulářů ASP.NET, najdete v dalších kurzů, které jsou k dispozici ve webových formulářích [Začínáme](../../../index.md) části na webu technologie ASP.NET.

Konkrétní **nejnovější** technologie ASP.NET 4.5 obsahuje zadaný v této webové formuláře sérii patří následující:

- Jednoduché uživatelské rozhraní pro vytváření projektů nabídku [podporu pro více platforem ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (webové formuláře, MVC a webového rozhraní API).
- [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), rozložení a motivy architektura, která poskytuje interaktivní možnosti návrhu a motivů.
- [ASP.NET Identity](../../../../identity/index.md), nový systém členství technologie ASP.NET, který funguje stejně ve všech platforem ASP.NET a funguje s webhosting softwaru než služby IIS.
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx), aktualizace k Entity Frameworku, který umožňuje načtení a manipulaci s daty jako silně typované objekty, přístup k datům asynchronně, zpracování přechodné chyby připojení a protokolování příkazů jazyka SQL.

Úplný seznam funkcí technologie ASP.NET 4.5, naleznete v tématu [ASP.NET and Web Tools pro Visual Studio 2013 – poznámky k](../../../../visual-studio/overview/2013/release-notes.md).

### <a name="the-wingtip-toys-sample-application"></a>Ukázkové aplikace Wingtip Toys

Následující snímek obrazovky poskytnout rychlý přehled aplikace webových formulářů ASP.NET, který vytvoříte v této sérii kurzů. Při spuštění aplikace z Visual Studio Express 2013 for Web, zobrazí se následující webové domovské stránky.

![Vzorkový – výchozí stránku](introduction-and-overview/_static/image1.png)

Můžete zaregistrovat jako nový uživatel nebo Přihlaste se jako stávajícího uživatele. Navigace je k dispozici v horní části pro každou kategorii produktu načtením dostupných produktů z databáze.

Výběrem odkazu produkty budou moci zobrazit seznam všech dostupných produktů.

![Vzorkový - produkty](introduction-and-overview/_static/image2.png)

Uvidíte také podrobnosti jednotlivých produktů výběrem některé z uvedených produktů.

![Vzorkový – podrobnosti o produktu](introduction-and-overview/_static/image3.png)

Jako uživatel můžete registrovat a přihlaste se pomocí funkce výchozí šablony webových formulářů. V tomto kurzu také vysvětluje, jak se přihlásit pomocí existujícího účtu Gmail. Kromě toho můžete se přihlásit jako správce přidávat a odebírat produkty z databáze.

![Přihlaste se na adresář Wingtip Toys-](introduction-and-overview/_static/image4.png)

Jakmile jste přihlášení jako uživatel, můžete přidat produkty do nákupního košíku a ověření přes PayPal. Všimněte si, že je funkční spolu s sandboxu pro vývojáře společnosti PayPal určený této ukázkové aplikaci. Žádný skutečný peněžní transakce nebude trvat místo.

![Vzorkový – nákupní košík](introduction-and-overview/_static/image5.png)

PayPal potvrdí účtu, pořadí a platební údaje.

![Vzorkový - PayPal](introduction-and-overview/_static/image6.png)

Po návratu z PayPal, můžete zkontrolovat a dokončete vaši objednávku.

![Vzorkový – pořadí revize](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte v počítači nainstalovaný následující software:

- [Microsoft Visual Studio 2013](https://www.microsoft.com/visualstudio/11/downloads#vs) nebo [Microsoft Visual Studio Express 2013 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web). Rozhraní .NET Framework se instaluje automaticky.

V této sérii kurzů používá Microsoft Visual Studio Express 2013 for Web. K dokončení této sérii kurzů můžete použít buď Microsoft Visual Studio Express 2013 for Web nebo Microsoft Visual Studio 2013.

> [!NOTE] 
> 
> Microsoft Visual Studio 2013 a Microsoft Visual Studio Express 2013 for Web bude často se označuje jako Visual Studio v celé této sérii kurzů.


Pokud už máte verzi sady Visual Studio nainstalovaný, proces instalace u stávající verzi nainstalovat Visual Studio 2013 nebo Microsoft Visual Studio Express 2013 for Web. Servery, které jste vytvořili v předchozích verzích se dají otevřít v sadě Visual Studio 2013 a pokračujte otevřením v předchozích verzích.

> [!NOTE] 
> 
> Tento názorný průvodce předpokládá, že jste vybrali *vývoj pro Web* kolekce nastavení při prvním spuštění sady Visual Studio. Další informace najdete v tématu [postupy: Výběr nastavení prostředí vývoje webu](https://msdn.microsoft.com/library/ff521558.aspx).


## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Po instalaci předpokladů, budete chtít začít vytvářet nový webový projekt, který se zobrazí v této sérii kurzů. Pokud byste chtěli **volitelně** spuštění ukázkové aplikace, která vytvoří v této sérii kurzů, můžete ho stáhnout z webu MSDN ukázky. Tento soubor ke stažení obsahuje následující prvky:

- Ukázková aplikace v *Northwind* složky.
- Prostředky použité k vytvoření ukázkové aplikace v *Northwind prostředky* složky *Northwind* složky.

#### <a name="download-the-file-from-msdn-samples-site"></a>Stáhněte soubor z webu MSDN ukázky:

[Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2013 – na adresář Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 

Stažení <em>ZIP</em> souboru. Zobrazíte dokončený projekt, který vytvoří v této sérii kurzů, vyhledejte a vyberte <em>jazyka C#</em>složky <em>ZIP</em> souboru. Uložit <em>jazyka C#</em> folderto složce můžete použít pro práci s projekty sady Visual Studio 2013. Ve výchozím nastavení je složky projektů Visual Studio 2013 následující:

<strong>C:\Users&#92;</strong><strong><em>&lt;uživatelské jméno&gt;</em></strong><strong>\Documents\Visual Studio 2013\Projects</strong>

Přejmenovat ***jazyka C#*** složku ***Northwind***.

> [!NOTE]
> Pokud už máte složku s názvem *Northwind* ve vaší složce projekty dočasně Přejmenujte tuto složku před přejmenováním *jazyka C#* složku *Northwind*.


Chcete-li spustit dokončený projekt, otevřete *Northwind* složky a dvojím kliknutím *WingtipToys.sln* souboru. Visual Studio 2013 se otevře projekt. Pak klikněte pravým tlačítkem *Default.aspx* souborů v okně Průzkumník řešení a v místní nabídce klikněte na tlačítko zobrazit v prohlížeči.

### <a name="tutorial-support-and-comments"></a>Kurz podpory a komentáře

Pomocí informací v části Q a A je součástí [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ukázka (C#) pro jakékoli dotazy nebo připomínky.

Komentáře v této sérii kurzů jsou úvodní a při aktualizaci v této sérii kurzů veškeré úsilí, bude proveden rozdělení účet opravy nebo návrhy na vylepšení, která jsou k dispozici v kurzu komentáře.

Pokud dojde k chybě při vývoji nebo na webu se nespustí správně, chybové zprávy mohou být složité možné příčiny problému nebo nemusí vysvětlují, jak ho opravit. Abychom vám pomohli s uvedeny některé obvyklé scénáře problém, můžete použít také [fóra ASP.NET](https://forums.asp.net/) nebo části Q a A je součástí [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) () C#) vzorku. Pokud se zobrazí chybová zpráva nebo něco nefunguje tak, jak projděte následující kurzy, nezapomeňte se podívat výše uvedené umístění.

> [!div class="step-by-step"]
> [Next](create-the-project.md)
