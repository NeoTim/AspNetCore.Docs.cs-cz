---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'EF Database First s ASP.NET MVC: rozšířené ověřování dat | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 12/29/2014
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: df2cd99619f097c9f392e8fe7352c1ce3a69c8df
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021661"
---
<a name="ef-database-first-with-aspnet-mvc-enhancing-data-validation"></a>EF Database First s ASP.NET MVC: rozšířené ověřování dat
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.
> 
> Tato části této série se zaměřuje na přidání anotací dat do datového modelu k určení požadavků na ověření a zobrazení formátování. Byl vylepšen závislosti na zpětnou vazbu od uživatelů v části komentáře.


## <a name="add-data-annotations"></a>Přidání anotací dat

Jak už jste viděli v dřívějším tématu, některá pravidla ověřování dat se automaticky použijí se vstupem uživatele. Například můžete pouze zadat číslo pro vlastnost na podnikové úrovni. Chcete-li určit další pravidla pro ověření dat, můžete přidat anotacemi dat do vaší třídy modelu. Tyto poznámky platí v celé vaší webové aplikace pro zadanou vlastnost. Můžete také použít atributy formátování, které se mění, jak se zobrazují vlastnosti; například změna hodnoty použité pro textové popisky.

V tomto kurzu přidáte datových poznámek k omezení délky zadané vlastnosti FirstName, LastName a MiddleName hodnoty. V databázi tyto hodnoty jsou omezené na 50 znaků. Nicméně ve webové aplikaci tento limit počtu znaků není aktuálně používá. Pokud uživatel zadá více než 50 znaků. pro jednu z těchto hodnot, na stránce havaruje při pokusu o uložení hodnoty do databáze. Bude také omezit na podnikové úrovni pro hodnoty v rozmezí 0 až 4.

Otevřít **Student.cs** soubor **modely** složky. Přidejte následující zvýrazněný kód do třídy.

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

V Enrollment.cs přidejte následující zvýrazněný kód.

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

Sestavte řešení.

Přejděte na stránku pro úpravy nebo vytváření student. Pokud se pokusíte k zadání více než 50 znaků, zobrazí se chybová zpráva.

![zobrazit chybová zpráva](enhancing-data-validation/_static/image1.png)

Přejděte na stránku pro úpravy registrace a pokus o poskytují známku vyjádřenou nad 4.

![Chyba rozsahu na podnikové úrovni](enhancing-data-validation/_static/image2.png)

Úplný seznam datových poznámek ověření můžete provést u třídy a vlastnosti, naleznete v tématu [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).

## <a name="add-metadata-classes"></a>Přidání třídy metadat

Přidávání atributů ověření přímo do třídy modelu funguje, když nepočítáte databáze, kterou chcete změnit; Nicméně pokud změny databáze a potřebujete se znovu vygenerovat třídu modelu, budou ztraceny všechny atributy, které jste použili pro třídu modelu. Tento přístup může být velmi neefektivní a náchylné ke ztrátě důležitých ověřovacích pravidel.

K tomuto problému vyhnout, můžete přidat třídu metadat, která obsahuje atributy. Když přiřadíte třídy modelu pro třídu metadat, jsou tyto atributy použité pro model. V takovém případě může znovu vygenerovat třídu modelu bez ztráty všechny atributy, které se použily třídu metadat.

V **modely** složky, přidejte třídu pojmenovanou **Metadata.cs**.

![Přidat třídu metadat](enhancing-data-validation/_static/image3.png)

Nahraďte kód v Metadata.cs následujícím kódem.

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

Tyto třídy metadata obsahují všechny atributy ověřování, které jste použili předtím na třídy modelu. **Zobrazení** atribut se používá ke změně hodnoty používané pro textové popisky.

Nyní je třeba přidružit tříd modelu třídy metadat.

V **modely** složky, přidejte třídu pojmenovanou **PartialClasses.cs**.

Obsah souboru nahraďte následujícím kódem.

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

Všimněte si, že každá třída je označena jako `partial` třídy a každý odpovídá názvu a oboru názvů jako třída, která není automaticky vygenerován. Použitím atributu metadat na částečné třídy zajistíte, že atributy ověření dat se použije pro automaticky generované třídy. Tyto atributy nesmí být ztraceny po budete znovu generovat třídy modelu, protože metadat atributu se použije v částečné třídy, které se znovu vygeneroval.

Chcete-li znovu vygenerovat automaticky vygenerované třídy, otevřete soubor ContosoModel.edmx. Ještě jednou klikněte pravým tlačítkem na návrhové ploše a vyberte **aktualizace modelů z databáze**. I když nedošlo ke změně databáze, tento proces se znova vygeneruje třídy. V **aktualizovat** kartu, vyberte možnost **tabulky** a **Dokončit**.

![Aktualizovat tabulky](enhancing-data-validation/_static/image4.png)

Uložte soubor ContosoModel.edmx, aby se změny projevily.

Otevřete soubor Student.cs nebo Enrollment.cs a Všimněte si, že atributy ověření dat, které jste použili dříve už nejsou v souboru. Ale spusťte aplikaci a Všimněte si, že ověřovací pravidla, se uplatní při zadávání dat.

> [!div class="step-by-step"]
> [Předchozí](customizing-a-view.md)
> [další](publish-to-azure.md)
