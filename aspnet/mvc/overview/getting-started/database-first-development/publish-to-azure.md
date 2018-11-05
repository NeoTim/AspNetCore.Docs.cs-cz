---
uid: mvc/overview/getting-started/database-first-development/publish-to-azure
title: MVC Database First serveru publikovat do Azure | Dokumentace Microsoftu
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/22/2014
ms.assetid: 7131f1c1-cef3-4396-ab44-ed4519676546
msc.legacyurl: /mvc/overview/getting-started/database-first-development/publish-to-azure
msc.type: authoredcontent
ms.openlocfilehash: 1d2c26c211c5c8d97076327d01fe59d5ba4dc9ac
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021632"
---
<a name="publish-mvc-database-first-site-to-azure"></a>MVC Database First serveru publikovat do Azure
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.
> 
> Tato části této série se zaměřuje na publikování webové aplikace a databáze do Azure. Si můžete přečíst v tomto tématu se dozvíte o publikování webové aplikace a databáze, ale ve skutečnosti provádět kroky, které je třeba spustit na začátku tohoto kurzu. Zobrazit [Začínáme](setting-up-database.md).


## <a name="deploy-your-web-app-on-azure"></a>Nasazení webové aplikace v Azure

Účet Azure k dokončení tohoto kurzu potřebujete:

- Je možné [zdarma otevřít účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) – budete dostávat kredity můžete použít k vyzkoušení placených služeb Azure a dokonce i po jejich použití až můžete účet ponechat a dál používat bezplatné služby Azure.
- Je možné [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) – vaše předplatné MSDN vám dává kredity každý měsíc, můžete použít k placení za služby Azure.

Chcete-li publikovat webovou aplikaci, klikněte pravým tlačítkem na projekt a vyberte **publikovat**.

![publikování webu](publish-to-azure/_static/image1.png)

Vyberte možnost Microsoft Azure Websites.

![Vyberte Azure](publish-to-azure/_static/image2.png)

Pokud nejste přihlášení k Azure, zadejte svoje přihlašovací údaje účtu Azure. Vyberte nová vytvořit novou webovou aplikaci.

![Nový web](publish-to-azure/_static/image3.png)

Vytvořte jedinečný název pro vaši webovou aplikaci. Budete vědět, že název je jedinečný Pokud se zobrazí zelená značka zaškrtnutí vpravo od názvu pole. Vyberte oblast pro vaši webovou aplikaci. Vyberte **vytvořit nový server** pro databázi a zadejte uživatelské jméno a heslo pro tento nový server databáze. Až budete hotovi, klikněte na tlačítko **vytvořit**.

![Vytvoření webu](publish-to-azure/_static/image4.png)

Hodnoty připojení jsou nyní Hotovo. Můžete ponechat tyto hodnoty ponechá beze změn.

![připojení](publish-to-azure/_static/image5.png)

Klikněte na tlačítko **Další**.

Pro nastavení, Všimněte si, že dvě připojení k databázi zadávají - ApplicationDBContext a ContosoUniversityDataEntities. ApplicationDBContext je připojení k účtu tabulky uživatelů. Tyto hodnoty zobrazit pouze připojovací řetězce databáze. Neznamená to, že tyto databáze bude publikován při publikování webu. Po dokončení publikování webové aplikace, publikuje se váš projekt databáze.

![](publish-to-azure/_static/image6.png)

Tři tečky (...) vedle připojení k databázi zobrazuje podrobnosti o připojovacím řetězci. Klikněte na tlačítko se třemi tečkami vedle ContosoUniversityDataEntities.

![](publish-to-azure/_static/image7.png)

Poznamenejte si název databázového serveru a databáze. Název serveru je náhodně generované. Název databáze je jednoduchý název vašeho webu pomocí  **\_db** připojí. Oba názvy budete potřebovat později při publikování databáze.

Klikněte na tlačítko **OK** zavřete okno připojovací řetězec databáze.

V okně Publikovat Web, klikněte na tlačítko **Další** zobrazíte náhled.

![](publish-to-azure/_static/image8.png)

Klikněte na tlačítko Spustit Náhled zobrazíte seznam souborů k publikování. Protože při prvním publikování tohoto webu, v seznamu je každý odpovídající soubor v projektu.

Klikněte na tlačítko **publikovat**.

V podokně výstupu se zobrazí výsledek publikace.

![](publish-to-azure/_static/image9.png)

Po publikování web je immedialely spuštění ve webovém prohlížeči. Nasazení webu a můžete zaregistrovat nový uživatel k webu; ale tabulek v projektu ContosoUniversityData dosud nebyly publikovány. Pokud kliknete na seznamu students odkaz dojde k chybě.

## <a name="publish-database-to-sql-azure"></a>Publikování databáze SQL Azure

Před publikováním databáze, musí se ujistěte, že místního počítače může připojit k serveru databáze. Omezuje brány firewall pro databázový server, které počítače může připojit k databázi. Budete muset přidat IP adresu počítače do povolených IP adres pro bránu firewall.

Přihlaste se ke svému účtu Azure na webu Azure portal.

Vyberte vaši novou databázi a vyberte **spravovat**.

![Správa](publish-to-azure/_static/image10.png)

Musíte nakonfigurovat váš databázový server umožňuje připojení z vašeho počítače. Vyberete-li spravovat, zobrazí se výzva přidat aktuální IP adresu, protože k databázovému serveru povolené. Vyberte Ano.

![Přidat ip adresu](publish-to-azure/_static/image11.png)

Je pravděpodobné, že IP adresa, kterou jste přidali v předchozím kroku není jedinou IP adresu, kterou je potřeba nakonfigurovat pro připojení. Pokuste se přihlásit k databázi a zjistěte, jestli připojení máte správně nastavený. Zadejte uživatele a heslo, které jste vytvořili dříve.

![přihlášení](publish-to-azure/_static/image12.png)

Pokud se zobrazí chybová zpráva, budete muset přidat další IP adresu. Chyba na zprávu klikněte a zobrazit podrobnosti o chybě. V podrobnostech se zobrazí, které je potřeba přidat IP adresu. Poznamenejte si tuto IP adresu.

![není povoleno](publish-to-azure/_static/image13.png)

Zavřít toto okno přihlášení a vraťte se do portálu Azure portal.

Přejděte na řídicí panel pro vaši databázi. Klikněte na tlačítko **Spravovat povolené IP adresy**.

![Správa ip adres](publish-to-azure/_static/image14.png)

Teď musíte přidat IP adresu z chybové zprávy. Změňte oblasti povolené IP adresy, které zahrnují jeden z chybové zprávy, nebo přidejte tuto IP adresu jako samostatný záznam.

![Přidejte novou adresu](publish-to-azure/_static/image15.png)

Uložte změny do povolených IP adres.

Kliknutím na spravovat a zkuste se znovu přihlásit k databázi. Budete muset počkat několik minut, než je povolené IP adresy pro bránu firewall správně nakonfigurovaná. Když se můžete úspěšně přihlásit databáze, dokončení nastavení připojení k databázi.

Toto okno správy můžete nechat otevřené, protože zkontroluje výsledek nasazení databáze za chvíli.

Vraťte se do projektu databáze. Klikněte pravým tlačítkem na projekt a vyberte **publikovat**.

![publikování databáze](publish-to-azure/_static/image16.png)

V okně publikování databáze vyberte **upravit**.

![Upravit](publish-to-azure/_static/image17.png)

Zadejte název databázového serveru a ověřování pověření pro server. Po zadání přihlašovacích údajů, vyberte databázi, kterou jste vytvořili ze seznamu dostupných databází. Ve výchozím nastavení Visual Studio nastaví název pole databáze na název vašeho projektu, který nemusí být stejné jako databáze, kterou jste vytvořili.

![](publish-to-azure/_static/image18.png)

Klikněte na tlačítko OK.

Bude pravděpodobně chcete uložit tento profil, abyste mohli publikovat aktualizace budoucí bez nutnosti opětovného zadání všechny informace o připojení. Vyberte **vytvořit profil**.

![Uložit profil](publish-to-azure/_static/image19.png)

Vytvoří soubor v projektu s názvem **ContosoUniversityData.publish.xml**. Při příštím publikování databáze do Azure, jednoduše načtěte tento profil.

Nyní, klikněte na tlačítko **publikovat** k vytvoření databáze v Azure.

![Publikování](publish-to-azure/_static/image20.png)

Publikování výsledky se zobrazí po spuštění nějakou dobu.

![výsledky](publish-to-azure/_static/image21.png)

Teď můžete přejít zpět na portál pro správu pro vaši databázi. Aktualizovat návrhové zobrazení a Všimněte si, že byly nasazeny 3 tabulek předem vyplněný daty.

![nové tabulky](publish-to-azure/_static/image22.png)

Nyní jste připraveni k otestování webové aplikace, který je nasazený do Azure. Přejděte do webové aplikace v Azure (například http://contosositeexample.azurewebsites.net/). Klikněte na odkaz pro seznam studentů a měli byste vidět zobrazení indexu pro studenty.

![Zobrazení](publish-to-azure/_static/image23.png)

V některých případech nutné databáze a připojení nějakou dobu správně nakonfigurovat. Pokud obdržíte chybu, počkejte několik minut a pak to zkuste znovu.

## <a name="conclusion"></a>Závěr

Tato série poskytuje jednoduchý příklad toho, jak generovat kód z existující databáze, která umožňuje uživatelům upravovat, aktualizovat, vytvářet a odstraňovat data. Používá ASP.NET MVC 5, Entity Framework a ASP.NET generování uživatelského rozhraní pro vytvoření projektu.

Úvodní příklad vývoje Code First najdete v tématu [Začínáme s ASP.NET MVC 5](../introduction/getting-started.md).

Složitější příklad naleznete v tématu [vytváření datového modelu Entity Framework pro aplikaci ASP.NET MVC 4](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Všimněte si, že rozhraní API pro DbContext, který používáte pro práci s daty v databázi první je stejný jako rozhraní API můžete použít pro práci s daty v Code First. I v případě, že máte v úmyslu použít první databáze, můžete zjistíte, jak zpracovat složitější scénáře, jako je čtení a aktualizace souvisejících dat, zpracování konfliktů souběžnosti, a tak dále z Code First kurzu. Jediný rozdíl spočívá ve vytvoření databáze, třída kontextu a tříd entit.

> [!div class="step-by-step"]
> [Předchozí](enhancing-data-validation.md)
