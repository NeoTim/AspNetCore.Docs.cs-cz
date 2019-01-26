---
uid: mvc/overview/getting-started/database-first-development/setting-up-database
title: 'Kurz: Začínáme s EF Database First pomocí MVC 5'
description: Tento článek popisuje, jak začít s existující databáze a rychle vytvořit webovou aplikaci, která umožňuje uživatelům interakci s daty.
author: Rick-Anderson
ms.author: riande
ms.date: 01/23/2019
ms.topic: tutorial
ms.assetid: 095abad4-3bfe-4f06-b092-ae6a735b7e49
msc.legacyurl: /mvc/overview/getting-started/database-first-development/setting-up-database
msc.type: authoredcontent
ms.openlocfilehash: 8b094b7c334eaad510c46b55a99ec727b9c381c2
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889922"
---
# <a name="tutorial-get-started-with-ef-database-first-using-mvc-5"></a>Kurz: Začínáme s EF Database First pomocí MVC 5

Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze. V poslední části série nasadíte lokalitu a databázi do Azure.

Tento článek popisuje, jak začít s existující databáze a rychle vytvořit webovou aplikaci, která umožňuje uživatelům interakci s daty. Používá Entity Framework 6 a MVC 5 k vytvoření webové aplikace. Funkce technologie ASP.NET generování uživatelského rozhraní umožňuje automaticky vygenerovat kód pro zobrazení, aktualizaci, vytvoření a odstranění dat. Pomocí nástroje pro publikování v sadě Visual Studio, můžete snadno nasadit lokalitu a databázi do Azure.

Tato části této série se zaměřuje na vytvoření databáze a vyplnění daty.

Tato série byl zapsán s příspěvky z Petr Dykstra a Rick Anderson. Byl vylepšen závislosti na zpětnou vazbu od uživatelů v části komentáře.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení databáze

## <a name="prerequisites"></a>Požadavky

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

## <a name="introduction"></a>Úvod

Tento článek se zabývá situace, kdy databázi máte a chcete pro generování kódu pro webovou aplikaci na základě polí dané databáze. Tento přístup se nazývá Database First vývoje. Pokud již nemáte existující databázi, můžete použít přístupu říká vývoje Code First, který zahrnuje definování datových tříd a generování databáze z vlastnosti třídy.

## <a name="set-up-the-database"></a>Nastavení databáze

Aby napodobovaly prostředí, které máte existující databázi, nejprve vytvořit databázi s předem vyplněný daty a potom vytvořit webové aplikace, která se připojuje k databázi.

V tomto kurzu byla vyvinutá pomocí LocalDB. Můžete použít existující databázový server namísto LocalDB, ale v závislosti na vaší verzi sady Visual Studio a typ databáze, všechny datové nástroje v sadě Visual Studio nemusí být podporován. Pokud nástroje nejsou k dispozici pro vaši databázi, budete muset provést některé kroky specifické pro databázi v sadě management suite pro vaši databázi.

Pokud máte potíže s databází nástroje ve vaší verzi sady Visual Studio, ujistěte se, že máte nainstalovanou nejnovější verzi nástroje databáze. Informace o aktualizaci nebo instalaci databáze nástroje najdete v tématu [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/hh297027).

Spusťte sadu Visual Studio a vytvořte **databázový projekt SQL Server**. Pojmenujte projekt **ContosoUniversityData**.

![Vytvoření projektu databáze](setting-up-database/_static/image1.png)

Nyní máte projekt prázdnou databázi. Tato databáze do Azure nasadí později v tomto kurzu, budete muset nastavit Azure SQL Database jako cílovou platformu pro projekt. Nastavení cílové platformě není nasazena ve skutečnosti databázi. pouze znamená, že databázový projekt bude ověřte, zda je návrh databáze kompatibilní s cílovou platformu. Chcete-li nastavení cílové platformy, otevřete **vlastnosti** pro projekt a vyberte **Microsoft Azure SQL Database** pro cílovou platformu.

Můžete vytvořit tabulky pro tento kurz potřeba přidáním skripty SQL, které určují tabulky. Klikněte pravým tlačítkem na projekt a přidat novou položku. Vyberte **tabulek a zobrazení** > **tabulky** a pojmenujte ho *Student*.

V tabulce souboru nahraďte následující kód k vytvoření tabulky pomocí příkazu T-SQL.

[!code-sql[Main](setting-up-database/samples/sample1.sql)]

Všimněte si, že v okně návrhu se automaticky synchronizuje s kódem. Můžete pracovat s kódem nebo Návrhář.

![Zobrazit kód a návrh](setting-up-database/_static/image5.png)

Přidejte další tabulku. Tentokrát pojmenujte ji kurzu a pomocí následujícího příkazu T-SQL.

[!code-sql[Main](setting-up-database/samples/sample2.sql)]

A ještě jednou vytvořte tabulku s názvem registrace opakujte.

[!code-sql[Main](setting-up-database/samples/sample3.sql)]

Můžete naplnit databázi daty pomocí skriptu, který se spouští po nasazení databáze. Skript po nasazení přidáte do projektu. Klikněte pravým tlačítkem na projekt a přidat novou položku. Vyberte **uživatelské skripty** > **skript po nasazení**. Můžete použít výchozí název.

Přidejte následující kód T-SQL do skriptu po nasazení. Tento skript jednoduše přidá data do databáze, pokud není nalezen žádný odpovídající záznam. Nelze přepsat nebo odstranit všechna data, která jste zadali do databáze.

[!code-sql[Main](setting-up-database/samples/sample4.sql)]

Je důležité si uvědomit, že je skript po nasazení spustit pokaždé, když se nasadit projekt databáze. Proto budete muset pečlivě zvažte své požadavky na při zápisu tohoto skriptu. V některých případech můžete chtít začít ze známé sady dat pokaždé, když je projekt nasazen. V ostatních případech nemusíte chtít upravit existující data žádným způsobem. Na základě vašich požadavků, můžete rozhodnout, zda je nutné skript po nasazení nebo co je potřeba zahrnout do skriptu. Další informace o naplnění databáze pomocí skriptu po nasazení najdete v tématu [včetně dat v databázový projekt SQL Server](https://blogs.msdn.com/b/ssdt/archive/2012/02/02/including-data-in-an-sql-server-database-project.aspx).

Teď máte 4 soubory skriptu SQL, ale žádné skutečné tabulky. Jste připraveni nasadit projekt databáze na instanci localdb. V sadě Visual Studio klikněte na tlačítko Start (nebo F5) k vytvoření a nasazení projektu databáze. Zkontrolujte, **výstup** kartu k ověření, že sestavení a nasazení bylo úspěšné.

Chcete-li zobrazit, že se vytvořil novou databázi, otevřete **Průzkumník objektů systému SQL Server** a vyhledejte název projektu na serveru správná místní databáze (v tomto případě **(localdb) \ProjectsV13**).

Zobrazit tabulky jsou naplněný daty, klikněte pravým tlačítkem na tabulku a vyberte **Data zobrazení**.

![Zobrazit data tabulky](setting-up-database/_static/image9.png)

Zobrazí se upravovat zobrazení dat tabulky. Pokud vyberete třeba **tabulky** > **dbo.course** > **Data zobrazení**, viz tabulku s tři sloupce (**kurzu**, **Title**, a **kredity**) a čtyři řádky.

## <a name="additional-resources"></a>Další zdroje

Úvodní příklad vývoje Code First najdete v tématu [Začínáme s ASP.NET MVC 5](../introduction/getting-started.md). Složitější příklad naleznete v tématu [vytváření datového modelu Entity Framework pro aplikaci ASP.NET MVC 4](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).

Informace o výběru přístupem Entity Framework najdete v tématu [vývojové přístupy s Entity Framework](https://msdn.microsoft.com/library/ms178359.aspx#dbfmfcf).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení databáze

Přejděte k dalším článku se dozvíte, jak vytvořit webové aplikace a datových modelů.
> [!div class="nextstepaction"]
> [Vytvoření webové aplikace a datových modelů](creating-the-web-application.md)