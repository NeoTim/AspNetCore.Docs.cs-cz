---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Začínáme s ASP.NET 4.7 webové formuláře a sady Visual Studio 2017 | Dokumentace Microsoftu
author: Erikre
description: Tato série podrobných kurzů se seznámíte se základy vytváření webových formulářů ASP.NET aplikace pomocí ASP.NET 4.7 a Microsoft Visual Studio
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341548"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a>Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2017
====================

[Stáhněte si ukázkový projekt Wingtip Toys (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) nebo [stáhnout elektronickou knihu (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

V této sérii kurzů se dozvíte, jak vytvářet aplikace webových formulářů ASP.NET s ASP.NET 4.5 a Microsoft Visual Studio 2017. 

## <a name="introduction"></a>Úvod

V této sérii kurzů vás provede procesem vytvoření aplikace webových formulářů ASP.NET pomocí sady Visual Studio 2017 a technologii ASP.NET 4.5. Vytvoříte aplikaci s názvem **Wingtip Toys** – zjednodušené webu prezentace prodej zboží online. Během řady jsou zvýrazněny nových funkcí technologie ASP.NET 4.5.

### <a name="target-audience"></a>Cílová skupina

Vývojáři nové webové formuláře ASP.NET jsou cílovou skupinou pro tuto řadu kurzů.

Měli byste určitá znalost v následujících oblastech:

- Objektově orientované programování (OOP) a jazyky
- Vývoj pro web (HTML, CSS a JavaScript)
- Relační databáze
- N-vrstvou architekturu

Pokud chcete zkontrolovat tyto oblasti, vezměte v úvahu zkoumání následujícím obsahem:

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
- Ovládací prvky dat silného typu
- Vazby modelu
- Datové poznámky
- Zprostředkovatele hodnot
- Protokol SSL a protokolem OAuth
- ASP.NET Identity, konfigurace a autorizace
- Nerušivý ověření
- Směrování
- Zpracování chyb v ASP.NET

### <a name="application-scenarios-and-tasks"></a>Scénáře aplikací a úloh

Série kurzů úlohy patří:

- Vytváření, revizí a spuštění nového projektu
- Vytvoření struktury databáze
- Inicializace a synchronizace replik indexů databáze
- Přizpůsobení uživatelského rozhraní se styly, grafikou a na stránku předlohy
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

V této sérii kurzů je určené pro někdo zkušenosti s koncepty programování, ale nové webových formulářů ASP.NET. Pokud jste již obeznámeni s webovými formuláři ASP.NET, tuto řadu stále můžete informace o nových funkcích technologie ASP.NET 4.5. Čtenáři neznáte programování konceptů a webových formulářů ASP.NET, naleznete v části Další kurzy webových formulářů, které jsou součástí [Začínáme](../../../index.md) části na webu technologie ASP.NET.

ASP.NET 4.5 uvedené v této sérii kurzů zahrnuje následující funkce:

- Jednoduché uživatelské rozhraní pro vytváření projektů, které nabízí [podporu pro mnoho platforem ASP.NET](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (webové formuláře, MVC a webového rozhraní API).
- [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), rozložení, motivy a responzivní design framework.
- [ASP.NET Identity](../../../../identity/index.md), nový systém členství technologie ASP.NET, který funguje stejně ve všech platforem ASP.NET a funguje s webhosting softwaru než služby IIS.
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx)

  Aktualizace Entity Framework umožňuje:
  - Načítání a manipulaci s daty jako silně typované objekty
  - Asynchronní přístup k datům
  - Zpracování přechodné chyby připojení
  - Příkazy SQL

Úplný seznam funkcí technologie ASP.NET 4.5 naleznete v tématu [ASP.NET and Web Tools pro Visual Studio 2013 – poznámky k](../../../../visual-studio/overview/2013/release-notes.md).

### <a name="the-wingtip-toys-sample-application"></a>Ukázkové aplikace Wingtip Toys

Na následujících snímcích obrazovky jsou z aplikace webových formulářů ASP.NET, který vytvoříte v této sérii kurzů. Při spuštění aplikace v sadě Visual Studio se zobrazí následující webové domovské stránky.

![Vzorkový – výchozí stránku](introduction-and-overview/_static/image1.png)

Můžete zaregistrovat jako nový uživatel nebo Přihlaste se jako stávajícího uživatele. Horní navigační obsahuje odkazy na kategorie produktů a jejich produktů z databáze.

Pokud vyberete **produkty**, se zobrazí všechny produkty k dispozici. 

![Vzorkový - produkty](introduction-and-overview/_static/image2.png)

Pokud vyberete určitý produkt, zobrazí se podrobnosti o produktu.


![Vzorkový – podrobnosti o produktu](introduction-and-overview/_static/image3.png)

Jako uživatel můžete registrovat a přihlaste se pomocí funkce výchozí šablony webových formulářů. V tomto kurzu vysvětluje, jak se přihlásit pomocí existujícího účtu Gmail. Kromě toho můžete přihlásit jako správce přidávat a odebírat produkty z databáze.

![Přihlaste se na adresář Wingtip Toys-](introduction-and-overview/_static/image4.png)

Jakmile jste přihlášení jako uživatel, můžete přidat produkty do nákupního košíku a ověření přes PayPal. Ukázková aplikace je navržen pro práci v sandboxu pro vývojáře společnosti PayPal. Žádná skutečná peněžní transakce probíhá.

![Vzorkový – nákupní košík](introduction-and-overview/_static/image5.png)

PayPal potvrdí účtu, pořadí a platební informace.

![Vzorkový - PayPal](introduction-and-overview/_static/image6.png)

Po návratu z PayPal, můžete zkontrolovat a dokončete vaši objednávku.

![Vzorkový – pořadí revize](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že je v počítači nainstalován následující software:

- [Microsoft Visual Studio 2017 nebo Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).

Rozhraní .NET Framework se instaluje automaticky.

V této sérii kurzů používá Microsoft Visual Studio Community 2017. Můžete použít buď nebo Microsoft Visual Studio 2017 k dokončení této sérii kurzů.

Mějte na paměti následující skutečnosti související sady Visual Studio:

* Microsoft Visual Studio 2017 a Microsoft Visual Studio Community 2017 jsou označovány jako *sady Visual Studio* v celé této sérii kurzů.

* Visual Studio 2017 se nainstaluje vedle všechny starší verze už nainstalovaná. Servery, které jsou vytvořeny v dřívějších verzích lze otevřít v sadě Visual Studio 2017 a pokračujte otevřením v předchozích verzích.

* Při prvním spuštění sady Visual Studio, se předpokládá, že jste vybrali *vývoj pro Web* nastavení. Další informace najdete v tématu [jak: Vyberte nastavení prostředí vývoje webu](https://msdn.microsoft.com/library/ff521558.aspx).

Po instalaci požadavků, jste připraveni začít vytvářet webový projekt v této sérii kurzů.

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

 Úplnou vzorovou applicatiion v si můžete stáhnout kdykoli z webu MSDN ukázky:

[Začínáme s webovými formuláři ASP.NET 4.5 a Visual Studio 2013 – na adresář Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) 

 Tento soubor má následující položky:

- Ukázková aplikace v *Northwind* složky.
- Prostředky použité k vytvoření ukázkové aplikace v *Northwind prostředky* složky *Northwind* složky.

Stažení *ZIP* souboru. Pokud chcete zobrazit dokončené projekt, který vytvoří v této sérii kurzů, vyhledejte a vyberte *C#* složky v souboru ZIP. Uložit C# složku ke složce můžete použít pro práci s projekty aplikace Visual Studio. Ve výchozím nastavení je složka projektů sady Visual Studio 2017:

<strong>C:\Users&#92;</strong><strong><em>&lt;uživatelské jméno&gt;</em></strong><strong>\source\repos</strong>

Přejmenovat ***jazyka C#*** složku ***Northwind***.

> [!NOTE]
> Pokud už máte složku s názvem *Northwind* ve vaší složce projekty dočasně Přejmenujte tuto složku před přejmenováním *jazyka C#* složku *Northwind*.

Chcete-li spustit dokončený projekt, otevřete *Northwind* složky a dvojím kliknutím *WingtipToys.sln* souboru. Visual Studio 2017 otevřete projekt. Pak klikněte pravým tlačítkem *Default.aspx* ve **Průzkumníka řešení** a vyberte **zobrazit v prohlížeči**.

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a>Kontrola obsahu kvízu webových formulářů ASP.NET

Po dokončení série kurzů, kvízu Otestujte svoje znalosti a posílit klíčových konceptů. Každý dotaz poskytuje vysvětlení a odkazy na další pokyny.

 * [Webové formuláře ASP.NET kvíz](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a>Kurz podpory a komentáře

Pro otázky a připomínky, použijte oddíl Q a A součástí [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) stránka s ukázkou.

Si mohou komentáře k této sérii kurzů. Když se aktualizuje v této sérii kurzů, každý úsilí vezměte v úvahu opravy nebo návrhy na vylepšení.

Pokud dojde k chybě, může být matoucí, odpovídající chybové zprávy s dobrým vysvětlení o tom, jak ho opravit. Potřebujete pomoc, můžete zkontrolovat [fóra ASP.NET](https://forums.asp.net/). Jiné vhodným zdrojem je v části Q a A [Začínáme se službou webových formulářů ASP.NET 4.5 a Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) stránka s ukázkou. 

> [!div class="step-by-step"]
> [Next](create-the-project.md)
