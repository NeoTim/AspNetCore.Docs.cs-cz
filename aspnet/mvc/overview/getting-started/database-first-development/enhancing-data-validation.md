---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'Kurz: Vylepšení ověřování dat pro EF Database First s ASP.NET MVC aplikace'
description: Tento kurz se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 85299d70c6cba52c1d40a42edfd429c96318134a
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236481"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a>Kurz: Vylepšení ověřování dat pro EF Database First s ASP.NET MVC aplikace

Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.

Tento kurz se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování. Byl vylepšen závislosti na zpětnou vazbu od uživatelů v části komentáře.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání anotací dat
> * Přidání třídy metadat

## <a name="prerequisites"></a>Požadavky

* [Přizpůsobení zobrazení](customizing-a-view.md)

## <a name="add-data-annotations"></a>Přidání anotací dat

Jak už jste viděli v dřívějším tématu, některá pravidla ověřování dat se automaticky použijí se vstupem uživatele. Například můžete pouze zadat číslo pro vlastnost na podnikové úrovni. Chcete-li určit další pravidla pro ověření dat, můžete přidat anotacemi dat do vaší třídy modelu. Tyto poznámky platí v celé vaší webové aplikace pro zadanou vlastnost. Můžete také použít atributy formátování, které se mění, jak se zobrazují vlastnosti; například změna hodnoty použité pro textové popisky.

V tomto kurzu přidáte datových poznámek k omezení délky zadané vlastnosti FirstName, LastName a MiddleName hodnoty. V databázi tyto hodnoty jsou omezené na 50 znaků. Nicméně ve webové aplikaci tento limit počtu znaků není aktuálně používá. Pokud uživatel zadá více než 50 znaků. pro jednu z těchto hodnot, na stránce havaruje při pokusu o uložení hodnoty do databáze. Bude také omezit na podnikové úrovni pro hodnoty v rozmezí 0 až 4.

Vyberte **modely** > **ContosoModel.edmx** > **ContosoModel.tt** a otevřete *Student.cs* souboru. Přidejte následující zvýrazněný kód do třídy.

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

Otevřít *Enrollment.cs* a přidejte následující zvýrazněný kód.

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

Sestavte řešení.

Klikněte na tlačítko **seznamu studentů** a vyberte **upravit**. Pokud se pokusíte k zadání více než 50 znaků, zobrazí se chybová zpráva.

![zobrazit chybová zpráva](enhancing-data-validation/_static/image1.png)

Přejděte zpět na domovskou stránku. Klikněte na tlačítko **seznamu registrací** a vyberte **upravit**. Pokus o zajištění kvality nad 4. Zobrazí se tato chyba: *Pole na podnikové úrovni musí být mezi 0 a 4.*

## <a name="add-metadata-classes"></a>Přidání třídy metadat

Přidávání atributů ověření přímo do třídy modelu funguje, když nepočítáte databáze, kterou chcete změnit; Nicméně pokud změny databáze a potřebujete se znovu vygenerovat třídu modelu, budou ztraceny všechny atributy, které jste použili pro třídu modelu. Tento přístup může být velmi neefektivní a náchylné ke ztrátě důležitých ověřovacích pravidel.

K tomuto problému vyhnout, můžete přidat třídu metadat, která obsahuje atributy. Když přiřadíte třídy modelu pro třídu metadat, jsou tyto atributy použité pro model. V takovém případě může znovu vygenerovat třídu modelu bez ztráty všechny atributy, které se použily třídu metadat.

V **modely** složky, přidejte třídu pojmenovanou *Metadata.cs*.

Nahraďte kód v *Metadata.cs* následujícím kódem.

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

Tyto třídy metadata obsahují všechny atributy ověřování, které jste použili předtím na třídy modelu. **Zobrazení** atribut se používá ke změně hodnoty používané pro textové popisky.

Nyní je třeba přidružit tříd modelu třídy metadat.

V **modely** složky, přidejte třídu pojmenovanou *PartialClasses.cs*.

Obsah souboru nahraďte následujícím kódem.

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

Všimněte si, že každá třída je označena jako `partial` třídy a každý odpovídá názvu a oboru názvů jako třída, která není automaticky vygenerován. Použitím atributu metadat na částečné třídy zajistíte, že atributy ověření dat se použije pro automaticky generované třídy. Tyto atributy nesmí být ztraceny po budete znovu generovat třídy modelu, protože metadat atributu se použije v částečné třídy, které se znovu vygeneroval.

Chcete-li znovu vygenerovat automaticky vygenerované třídy, otevřete *ContosoModel.edmx* souboru. Ještě jednou klikněte pravým tlačítkem na návrhové ploše a vyberte **aktualizace modelů z databáze**. I když nedošlo ke změně databáze, tento proces se znova vygeneruje třídy. V **aktualizovat** kartu, vyberte možnost **tabulky** a **Dokončit**.

Uložit *ContosoModel.edmx* souboru, aby se změny projevily.

Otevřít *Student.cs* souboru nebo *Enrollment.cs* soubor a Všimněte si, že jste provedli dříve atributy ověření dat už nejsou v souboru. Ale spusťte aplikaci a Všimněte si, že ověřovací pravidla, se uplatní při zadávání dat.

## <a name="additional-resources"></a>Další zdroje

Úplný seznam datových poznámek ověření můžete provést u třídy a vlastnosti, naleznete v tématu [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání datových poznámek
> * Přidání metadat třídy

Přejděte k dalšímu kurzu, kde se naučíte, jak publikovat webovou aplikaci a databázi do Azure.
> [!div class="nextstepaction"]
> [Publikování do Azure](publish-to-azure.md)