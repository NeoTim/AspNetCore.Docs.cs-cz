---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'EF Database First s ASP.NET MVC: generování zobrazení | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/29/2014
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: 7d925573dd4cdf5c1a36e51f312e18093bd35043
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021085"
---
<a name="ef-database-first-with-aspnet-mvc-generating-views"></a>EF Database First s ASP.NET MVC: generování zobrazení
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.
> 
> Tato části této série se soustředí na používání ASP.NET generování uživatelského rozhraní pro generování kontrolerů a zobrazení.


## <a name="add-scaffold"></a>Přidat vygenerované uživatelské rozhraní

Jste připraveni vygenerovat kód, který bude poskytovat operace standardních datových tříd modelu. Přidat kód tak, že přidáte položku vygenerované uživatelské rozhraní. Existuje mnoho možností pro typ generování uživatelského rozhraní, které můžete přidat; v tomto kurzu se zahrne vygenerované uživatelské rozhraní kontroler a zobrazení, které odpovídají vzorům studenty a registrace, který jste vytvořili v předchozí části.

Můžete zachovat konzistenci ve vašem projektu, přidáte nový kontroler ke stávající **řadiče** složky. Klikněte pravým tlačítkem myši **řadiče** a pak zvolte položku **přidat** – **novou vygenerovanou položku**.

![Přidat vygenerované uživatelské rozhraní](generating-views/_static/image1.png)

Vyberte **kontroler MVC 5 se zobrazeními, používá nástroj Entity Framework** možnost. Tato možnost bude generovat kontroler a zobrazení pro aktualizaci, odstranění, vytváření a zobrazování dat v modelu.

![Přidat kontroler mvc](generating-views/_static/image2.png)

Vyberte **Student** pro třídu modelu, vyberte **ContosoUniversityEntities** pro třídy kontextu. Zachovat název kontroleru jako **StudentsController**,

![Zadejte kontroler](generating-views/_static/image3.png)

Klikněte na tlačítko **přidat**.

Pokud se zobrazí chyba, pravděpodobně jste nesestavili projektu v předchozí části. Pokud ano, zkuste sestavit projekt a pak znovu přidání vygenerované položky.

Po dokončení procesu generování kódu se zobrazí nový kontroler a zobrazení ve vašem projektu.

![Vyberte zobrazení](generating-views/_static/image4.png)

Znovu proveďte stejné kroky, ale přidat vygenerované uživatelské rozhraní pro třídu pro zápis. Po dokončení byste měli mít **EnrollmentsController.cs** soubor a složku ve složce **zobrazení** s názvem **registrace** s Create, odstranění, podrobností, úpravy a indexu zobrazení.

![Vyberte zobrazení](generating-views/_static/image5.png)

## <a name="add-links-to-new-views"></a>Přidat odkazy pro nové zobrazení

Zjednodušit přechod na nové zobrazení, můžete přidat několik hypertextové odkazy k zobrazení indexu pro studenty a registrací. Otevřete soubor v **Views/Home/Index.cshtml**, což je domovská stránka pro váš web. Přidejte následující kód níže jumbotron.

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

První parametr metody ActionLink je text, který se zobrazí v odkazu. Druhý parametr je akce a třetí parametr je název kontroleru. Například na první odkaz odkazuje na akci indexu v StudentsController. Skutečné hypertextový odkaz je vytvořený z těchto hodnot. Na první odkaz, takže v konečném důsledku přejdou uživatelé k **Index.cshtml** soubor **zobrazení/studenty** složky.

## <a name="display-student-views"></a>Zobrazení studenta

Ověříte, že kód přidaný do projektu správně zobrazí seznam studenty a umožňuje uživatelům upravovat, vytvořit nebo odstranit záznamech studentů v databázi.

Klikněte pravým tlačítkem myši **Views/Home/Index.cshtml** a vyberte možnost **zobrazit v prohlížeči**. Na této stránce klikněte na odkaz pro seznam studentů.

![](generating-views/_static/image6.png)

Na této stránce Všimněte si, že seznam studentů a odkazy, chcete-li změnit tato data.

![seznam studentů](generating-views/_static/image7.png)

Klikněte na tlačítko **vytvořit nový** propojit a zadání několika hodnot pro nové studenty.

![Vytvoření nového objektu student](generating-views/_static/image8.png)

Klikněte na tlačítko **vytvořit**a Všimněte si, že přidání nového objektu student do seznamu.

![seznam s nového objektu student](generating-views/_static/image9.png)

Vyberte **upravit** propojit a změnit některé z hodnot pro student.

![Upravit studenta](generating-views/_static/image10.png)

Klikněte na tlačítko **Uložit**a Všimněte si, že se změnil záznam studentů.

Nakonec vyberte **odstranit** propojit a potvrďte, že chcete odstranit záznam kliknutím **odstranit** tlačítko.

![Odstranit studenta](generating-views/_static/image11.png)

Bez psaní kódu, přidané zobrazení, které provádějí běžné operace s daty v tabulce studentů.

Mohli jste si všimnout, že je textový popisek pro pole založeného na vlastnosti databáze (například **LastName**) což není nutně co byste chtěli zobrazit na webové stránce. Například můžete chtít raději popisek bude **příjmení**. Později v tomto kurzu se opravit tento problém se zobrazením.

## <a name="display-enrollment-views"></a>Zobrazení registrace

Vaše databáze obsahuje vztah jeden mnoho mezi tabulkami, studenty a registrace a vztah jeden mnoho mezi tabulkami kurzu a registrace. Zobrazení pro registraci správně zpracovat tyto vztahy. Přejděte na domovskou stránku pro web a vyberte **seznamu registrací** odkaz a pak **vytvořit nový** odkaz. Zobrazení zobrazí formulář pro vytvoření nového záznamu registrace. Zejména Všimněte si, že formulář obsahuje dva rozevírací seznamy, které se vyplní s hodnotami ze souvisejících tabulek.

![Vytvoření registrace](generating-views/_static/image12.png)

Kromě toho ověření zadané hodnoty je automaticky použity na základě datového typu pole. Na podnikové úrovni vyžaduje číslo, takže se zobrazí chybová zpráva, pokud se pokusíte zadejte na nekompatibilní hodnotu.

![Ověřovací zpráva](generating-views/_static/image13.png)

Ověření, že automaticky generované zobrazení povolení uživatelé pracovat s daty v databázi. V dalším kurzu této série se aktualizovat databázi a provádět odpovídající změny ve webové aplikaci.

> [!div class="step-by-step"]
> [Předchozí](creating-the-web-application.md)
> [další](changing-the-database.md)
