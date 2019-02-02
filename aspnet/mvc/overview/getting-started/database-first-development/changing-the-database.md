---
uid: mvc/overview/getting-started/database-first-development/changing-the-database
title: 'Kurz: Změnit databázi pro EF Database First s ASP.NET MVC aplikace'
description: Tento kurz se zaměřuje na provádět aktualizaci struktura databáze změny a šíření tuto změnu v rámci webové aplikace.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: cfd5c083-a319-482e-8f25-5b38caa93954
msc.legacyurl: /mvc/overview/getting-started/database-first-development/changing-the-database
msc.type: authoredcontent
ms.openlocfilehash: 52cad1120908cf0d4f85770f8e2690f9415c5f56
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667632"
---
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a>Kurz: Změnit databázi pro EF Database First s ASP.NET MVC aplikace

Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.

Tento kurz se zaměřuje na provádět aktualizaci struktura databáze změny a šíření tuto změnu v rámci webové aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat sloupec
> * Přidat vlastnost do zobrazení

## <a name="prerequisites"></a>Požadavky

* [Generování zobrazení](generating-views.md)

## <a name="add-a-column"></a>Přidat sloupec

Při aktualizaci struktury tabulky v databázi, je potřeba zajistit, že vaše změna rozšířena na datový model, zobrazení a kontroler.

Pro účely tohoto kurzu přidáte nový sloupec do tabulky Student k zaznamenání prostřední jméno studenta. Chcete-li přidat tento sloupec, otevřete projekt databáze a otevřete soubor Student.sql. Pomocí návrháře nebo kód T-SQL, přidejte sloupec s názvem **MiddleName** , který je NVARCHAR(50) a povoluje hodnoty NULL.

Nasaďte tuto změnu do místní databáze spuštěním projektu databáze (nebo F5). Nové pole se přidá do tabulky. Pokud v Průzkumníku objektů SQL serveru ji nevidíte, klikněte na tlačítko Aktualizovat v podokně.

![Zobrazit nový sloupec](changing-the-database/_static/image2.png)

Nový sloupec existuje v tabulce databáze, ale neexistuje momentálně ve třídě datového modelu. Je nutné aktualizovat modelu, který má obsahovat nový sloupec. V **modely** složku, otevřete **ContosoModel.edmx** soubor k zobrazení diagramu modelu. Všimněte si, že Student model neobsahuje vlastnost MiddleName. Klikněte pravým tlačítkem kamkoli na návrhové ploše a vyberte **aktualizace modelů z databáze**.

V Průvodci aktualizacemi, vyberte **aktualizovat** kartu a potom vyberte **tabulky** > **dbo** > **Student**. Klikněte na tlačítko **Dokončit**.

Po dokončení procesu aktualizace zahrnuje nový databázový diagram **MiddleName** vlastnost. Uložit **ContosoModel.edmx** souboru. Je nutné uložit tento soubor pro novou vlastnost mají být předány **Student.cs** třídy. Teď jste aktualizovali databáze a modelu.

Sestavte řešení.

## <a name="add-the-property-to-the-views"></a>Přidat vlastnost do zobrazení

Bohužel zobrazení stále neobsahují nové vlastnosti. Chcete-li aktualizovat zobrazení máte dvě možnosti – můžete znovu vygenerovat zobrazení tak, že znovu přidáte generování uživatelského rozhraní pro třídu Student nebo můžete ručně přidat nové vlastnosti pro stávající zobrazení. V tomto kurzu přidáte základní kostry aplikace znovu vzhledem k tomu, že všechny vlastní změny nebyla provedena pro automaticky generované zobrazení. Můžete zvážit, když změny provedené zobrazení a nechcete, aby tyto změny přijít o ručním přidání vlastnost.

Aby se zajistilo zobrazení jsou znovu vytvořena, odstraňte **studenty** ve složce **zobrazení**a odstranit **StudentsController**. Klikněte pravým tlačítkem na **řadiče** složky a přidat generování uživatelského rozhraní pro **Student** modelu. Znovu, název kontroleru **StudentsController**. Vyberte **Přidat**.

Znovu sestavte řešení. Zobrazení nyní obsahovat vlastnost MiddleName.

![Zobrazit křestní jméno](changing-the-database/_static/image5.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat sloupec
> * Přidat vlastnost do zobrazení

Přejděte k dalšímu kurzu, přečtěte si, jak přizpůsobit zobrazení k zobrazení podrobností o záznamu studentů.
> [!div class="nextstepaction"]
> [Přizpůsobení zobrazení](customizing-a-view.md)