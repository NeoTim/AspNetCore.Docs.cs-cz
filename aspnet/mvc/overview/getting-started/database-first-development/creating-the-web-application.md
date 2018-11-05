---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: 'EF Database First s ASP.NET MVC: vytvoření webové aplikace a datových modelů | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: 6679b61326bd016481d96a4b5d58ec006f86b633
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020794"
---
<a name="ef-database-first-with-aspnet-mvc-creating-the-web-application-and-data-models"></a>EF Database First s ASP.NET MVC: vytvoření webové aplikace a datových modelů
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.
> 
> Tato části této série se zaměřuje na vytvoření webové aplikace a generování datové modely založené na databázových tabulek.


## <a name="create-a-new-aspnet-web-application"></a>Vytvořit novou webovou aplikaci ASP.NET

V nové řešení nebo do stejného řešení jako databázového projektu vytvořte nový projekt v sadě Visual Studio a vyberte **webová aplikace ASP.NET** šablony. Pojmenujte projekt **ContosoSite**.

![Vytvoření projektu](creating-the-web-application/_static/image1.png)

Klikněte na tlačítko **OK**.

V okně Nový projekt ASP.NET, vyberte **MVC** šablony. Můžete vymazat **hostovat v cloudu** možnost prozatím, protože budete nasazovat aplikaci do cloudu později. Klikněte na tlačítko **OK** k vytvoření aplikace.

![Vyberte šablonu mvc](creating-the-web-application/_static/image2.png)

Projekt je vytvořen s výchozí soubory a složky.

V tomto kurzu budete používat Entity Framework 6. Verze rozhraní Entity Framework můžete zkontrolovat ve vašem projektu prostřednictvím okna spravovat balíčky NuGet. V případě potřeby aktualizujte na verzi rozhraní Entity Framework.

![Zobrazit verze](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a>Generovat modelů

Modely Entity Framework teď vytvoříte z databázových tabulek. Tyto modely jsou třídy, které budete používat pro práci s daty. Každý model zrcadlí tabulky v databázi a obsahuje vlastnosti, které odpovídají sloupcům v tabulce.

Klikněte pravým tlačítkem myši **modely** a pak zvolte položku **přidat** a **nová položka**.

![Přidat novou položku](creating-the-web-application/_static/image4.png)

V okně Přidat novou položku vyberte **Data** v levém podokně a **datový Model Entity ADO.NET** z možností v prostředním podokně. Pojmenujte nový soubor modelu **ContosoModel**.

![Vytvoření modelu](creating-the-web-application/_static/image5.png)

Klikněte na tlačítko **přidat**.

V Průvodci modelem Entity Data vyberte **EF designeru z databáze**.

![Generovat z databáze](creating-the-web-application/_static/image6.png)

Klikněte na tlačítko **Další**.

Pokud máte připojení k databázi definována v rámci vývojového prostředí, může se zobrazit jeden z těchto připojení předem vybraná. Chcete vytvořit nové připojení k databázi, kterou jste vytvořili v první části tohoto kurzu. Klikněte na tlačítko **nové připojení** tlačítko.

![připojení k databázi](creating-the-web-application/_static/image7.png)

V okně Vlastnosti připojení zadat název místního serveru, kde byla vytvořena databáze (v tomto případě **(localdb) \ProjectsV12**). Po zadání názvu serveru, vyberte z dostupných databází ContosoUniversityData.

![nastavit vlastnosti připojení](creating-the-web-application/_static/image8.png)

Klikněte na tlačítko **OK**.

Vlastnosti správné připojení se teď zobrazují. Můžete použít výchozí název připojení v souboru Web.Config

![nastavení připojení](creating-the-web-application/_static/image9.png)

Klikněte na tlačítko **Další**.

Vyberte **tabulky** ke generování modely pro všechny tři tabulky.

![Vybrat tabulky](creating-the-web-application/_static/image10.png)

Klikněte na tlačítko **Dokončit**.

Pokud se zobrazí upozornění zabezpečení, vyberte **OK** pokračoval šablony.

Modely se generují z databázové tabulky a diagramu se zobrazí, který ukazuje vlastnosti a vztahy mezi tabulkami.

![Diagram modelu](creating-the-web-application/_static/image11.png)

Složku modely nyní obsahuje mnoho nových souborů související s modely, které byly generovány z databáze.

![Zobrazit nové soubory modelu](creating-the-web-application/_static/image12.png)

**ContosoModel.Context.cs** soubor obsahuje třídu, která je odvozena z **DbContext** třídy a poskytuje vlastnosti pro každou třídu modelu, odpovídající do databázové tabulky. **Course.cs**, **Enrollment.cs**, a **Student.cs** soubory obsahují, které představují tabulky databáze třídy modelu. Třídy kontextu a tříd modelu bude používat při práci s generování uživatelského rozhraní.

Než budete pokračovat s tímto kurzem, sestavte projekt. V další části bude generovat kód založený na datové modely, ale, že část nebude fungovat, pokud projekt nebyl sestaven.

> [!div class="step-by-step"]
> [Předchozí](setting-up-database.md)
> [další](generating-views.md)
