---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'Kurz: Generovat zobrazení pro EF Database First s ASP.NET MVC aplikace'
description: Tento článek se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.
author: Rick-Anderson
ms.author: riande
ms.date: 01/23/2019
ms.topic: tutorial
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: e1f6646cdf10d293268b92f44b018709e70c0f86
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889779"
---
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a>Kurz: Generovat zobrazení pro EF Database First s ASP.NET MVC aplikace

Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.

Tento článek se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat vygenerované uživatelské rozhraní
> * Přidat odkazy pro nové zobrazení
> * Zobrazení studenta
> * Zobrazení registrace

## <a name="prerequisite"></a>Předpoklad

* [Vytvoření webové aplikace a datových modelů](creating-the-web-application.md)

## <a name="add-scaffold"></a>Přidat vygenerované uživatelské rozhraní

Jste připraveni vygenerovat kód, který bude poskytovat operace standardních datových tříd modelu. Přidat kód tak, že přidáte položku vygenerované uživatelské rozhraní. Existuje mnoho možností pro typ generování uživatelského rozhraní, které můžete přidat; v tomto kurzu se zahrne vygenerované uživatelské rozhraní kontroler a zobrazení, které odpovídají vzorům studenty a registrace, který jste vytvořili v předchozí části.

Můžete zachovat konzistenci ve vašem projektu, přidáte nový kontroler ke stávající **řadiče** složky. Klikněte pravým tlačítkem myši **řadiče** a pak zvolte položku **přidat** > **novou vygenerovanou položku**.

Vyberte **kontroler MVC 5 se zobrazeními, používá nástroj Entity Framework** možnost. Tato možnost bude generovat kontroler a zobrazení pro aktualizaci, odstranění, vytváření a zobrazování dat v modelu.

![Přidat kontroler mvc](generating-views/_static/image2.png)

Vyberte **Student (ContosoSite.Models)** pro třídu modelu a vyberte **ContosoUniversityDataEntities (ContosoSite.Models)** pro třídy kontextu. Zachovat název kontroleru jako **StudentsController**.

Klikněte na **Přidat**.

Pokud se zobrazí chyba, pravděpodobně jste nesestavili projektu v předchozí části. Pokud ano, zkuste sestavit projekt a pak znovu přidání vygenerované položky.

Po dokončení procesu generování kódu se zobrazí nový kontroler a zobrazení ve vašem projektu **řadiče** a **zobrazení** > **studenty** složek .


Znovu proveďte stejné kroky, ale přidat vygenerované uživatelské rozhraní pro **registrace** třídy. Až budete hotovi, budete mít **EnrollmentsController.cs** soubor a složku ve složce **zobrazení** s názvem **registrace** Create, odstranění, podrobností, úpravy a indexu zobrazení.

## <a name="add-links-to-new-views"></a>Přidat odkazy pro nové zobrazení

Zjednodušit přechod na nové zobrazení, můžete přidat několik hypertextové odkazy k zobrazení indexu pro studenty a registrací. Otevřete soubor v **zobrazení** > **domácí** > *Index.cshtml*, což je domovská stránka pro váš web. Přidejte následující kód níže jumbotron.

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

První parametr metody ActionLink je text, který se zobrazí v odkazu. Druhý parametr je akce a třetí parametr je název kontroleru. Například na první odkaz odkazuje na akci indexu v StudentsController. Skutečné hypertextový odkaz je vytvořený z těchto hodnot. Na první odkaz, takže v konečném důsledku přejdou uživatelé k **Index.cshtml** soubor **zobrazení/studenty** složky.

## <a name="display-student-views"></a>Zobrazení studenta

Ověříte, že kód přidaný do projektu správně zobrazí seznam studenty a umožňuje uživatelům upravovat, vytvořit nebo odstranit záznamech studentů v databázi.

Klikněte pravým tlačítkem myši **zobrazení** > **Domů** > *Index.cshtml* a vyberte možnost **zobrazit v prohlížeči**. Na domovské stránce aplikace vyberte **seznamu studentů**.

![](generating-views/_static/image6.png)

Na **Index** stránky si všimněte seznamu studentů a odkazy, chcete-li změnit tato data. Vyberte **vytvořit nový** propojit a zadání několika hodnot pro nové studenty. Klikněte na tlačítko **vytvořit**a Všimněte si, že přidání nového objektu student do seznamu.

Zpět na **Index** stránky, vyberte **upravit** propojit a změnit některé z hodnot pro student. Klikněte na tlačítko **Uložit**a Všimněte si, že se změnil záznam studentů.

Nakonec vyberte **odstranit** propojit a potvrďte, že chcete odstranit záznam kliknutím **odstranit** tlačítko.

Bez psaní kódu, přidané zobrazení, které provádějí běžné operace s daty v tabulce studentů.

Mohli jste si všimnout, že je textový popisek pro pole založeného na vlastnosti databáze (například **LastName**) což není nutně co byste chtěli zobrazit na webové stránce. Například můžete chtít raději popisek bude **příjmení**. Později v tomto kurzu se opravit tento problém se zobrazením.

## <a name="display-enrollment-views"></a>Zobrazení registrace

Vaše databáze obsahuje vztah jeden mnoho mezi tabulkami, studenty a registrace a vztah jeden mnoho mezi tabulkami kurzu a registrace. Zobrazení pro registraci správně zpracovat tyto vztahy. Přejděte na domovskou stránku pro web a vyberte **seznamu registrací** odkaz a pak **vytvořit nový** odkaz.

Zobrazení zobrazí formulář pro vytvoření nového záznamu registrace. Zejména, Všimněte si, že formulář obsahuje **CourseID** rozevíracího seznamu a **StudentID** rozevíracího seznamu. Obě jsou naplněnou hodnotami ze souvisejících tabulek.

Kromě toho ověření zadané hodnoty je automaticky použity na základě datového typu pole. **Na podnikové úrovni** vyžaduje číslo, takže pokud se pokusíte zadejte na nekompatibilní hodnotu, zobrazí se chybová zpráva: *Pole na podnikové úrovni, musí být číslo.*

Ověření, že automaticky generované zobrazení povolení uživatelé pracovat s daty v databázi. V dalším kurzu této série se aktualizovat databázi a provádět odpovídající změny ve webové aplikaci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání vygenerované uživatelské rozhraní
> * Přidání odkazů na nové zobrazení
> * Zobrazení zobrazené studenta
> * Zobrazení zobrazené registrace

Přejděte k dalším článku se naučíte, jak změnit databázi.
> [!div class="nextstepaction"]
> [Změna databáze](changing-the-database.md)