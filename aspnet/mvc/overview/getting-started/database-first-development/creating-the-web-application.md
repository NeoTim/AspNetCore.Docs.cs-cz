---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: 'Kurz: Vytvořte webovou aplikaci a datové modely pro EF Database First s ASP.NET MVC'
description: Tento článek se zaměřuje na vytvoření webové aplikace a generování datové modely založené na databázových tabulek.
author: Rick-Anderson
ms.author: riande
ms.date: 01/23/2019
ms.topic: tutorial
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: 095d355866c9ab8fba3759f3e05e2a521992f3d6
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889766"
---
# <a name="tutorial-create-the-the-web-application-and-data-models-for-ef-database-first-with-aspnet-mvc"></a>Kurz: Vytvořte webovou aplikaci a datové modely pro EF Database First s ASP.NET MVC

 Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.

Tento článek se zaměřuje na vytvoření webové aplikace a generování datové modely založené na databázových tabulek.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET
> * Generovat modelů

## <a name="prerequisites"></a>Požadavky

* [Začínáme s Entity Framework 6 Database First pomocí MVC 5](setting-up-database.md)

## <a name="create-an-aspnet-web-app"></a>Vytvoření webové aplikace ASP.NET

V nové řešení nebo do stejného řešení jako databázového projektu vytvořte nový projekt v sadě Visual Studio a vyberte **webová aplikace ASP.NET** šablony. Pojmenujte projekt **ContosoSite**.

![Vytvoření projektu](creating-the-web-application/_static/image1.png)

Klikněte na **OK**.

V okně Nový projekt ASP.NET, vyberte **MVC** šablony. Můžete vymazat **hostovat v cloudu** možnost prozatím, protože budete nasazovat aplikaci do cloudu později. Klikněte na tlačítko **OK** k vytvoření aplikace.

Projekt je vytvořen s výchozí soubory a složky.

V tomto kurzu budete používat Entity Framework 6. Verze rozhraní Entity Framework můžete zkontrolovat ve vašem projektu prostřednictvím okna spravovat balíčky NuGet. V případě potřeby aktualizujte na verzi rozhraní Entity Framework.

![Zobrazit verze](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a>Generovat modelů

Modely Entity Framework teď vytvoříte z databázových tabulek. Tyto modely jsou třídy, které budete používat pro práci s daty. Každý model zrcadlí tabulky v databázi a obsahuje vlastnosti, které odpovídají sloupcům v tabulce.

Klikněte pravým tlačítkem myši **modely** a pak zvolte položku **přidat** a **nová položka**.

V okně Přidat novou položku vyberte **Data** v levém podokně a **datový Model Entity ADO.NET** z možností v prostředním podokně. Pojmenujte nový soubor modelu **ContosoModel**.

Klikněte na **Přidat**.

V Průvodci modelem Entity Data vyberte **EF designeru z databáze**.

Klikněte na **Další**.

Pokud máte připojení k databázi definována v rámci vývojového prostředí, může se zobrazit jeden z těchto připojení předem vybraná. Chcete vytvořit nové připojení k databázi, kterou jste vytvořili v první části tohoto kurzu. Klikněte na tlačítko **nové připojení** tlačítko.

V okně Vlastnosti připojení zadat název místního serveru, kde byla vytvořena databáze (v tomto případě **(localdb) \Projects13**). Po zadání názvu serveru, vyberte z dostupných databází ContosoUniversityData.

![nastavit vlastnosti připojení](creating-the-web-application/_static/image8.png)

Klikněte na **OK**.

Vlastnosti správné připojení se teď zobrazují. Můžete použít výchozí název připojení v souboru Web.Config.

Klikněte na **Další**.

Vyberte nejnovější verzi rozhraní Entity Framework.

Klikněte na **Další**.

Vyberte **tabulky** ke generování modely pro všechny tři tabulky.

Klikněte na tlačítko **Dokončit**.

Pokud se zobrazí upozornění zabezpečení, vyberte **OK** pokračoval šablony.

Modely se generují z databázové tabulky a diagramu se zobrazí, který ukazuje vlastnosti a vztahy mezi tabulkami.

![Diagram modelu](creating-the-web-application/_static/image11.png)

Složku modely nyní obsahuje mnoho nových souborů související s modely, které byly generovány z databáze.

**ContosoModel.Context.cs** soubor obsahuje třídu, která je odvozena z **DbContext** třídy a poskytuje vlastnosti pro každou třídu modelu, odpovídající do databázové tabulky. **Course.cs**, **Enrollment.cs**, a **Student.cs** soubory obsahují, které představují tabulky databáze třídy modelu. Třídy kontextu a tříd modelu bude používat při práci s generování uživatelského rozhraní.

Než budete pokračovat s tímto kurzem, sestavte projekt. V další části bude generovat kód založený na datové modely, ale, že část nebude fungovat, pokud projekt nebyl sestaven.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření webové aplikace ASP.NET
> * Vygeneruje modely

Pokračujte k dalším článku se dozvíte, jak vytvořit vygenerování kódu založeném na datové modely.
> [!div class="nextstepaction"]
> [Generování zobrazení](generating-views.md)