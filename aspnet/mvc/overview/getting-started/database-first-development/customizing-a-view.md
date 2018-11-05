---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'EF Database First s ASP.NET MVC: přizpůsobení zobrazení | Dokumentace Microsoftu'
author: Rick-Anderson
description: Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. Tento kurz seri...
ms.author: riande
ms.date: 10/01/2014
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: f66e097d53514ab3842e04cd545ca626c652478a
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021207"
---
<a name="ef-database-first-with-aspnet-mvc-customizing-a-view"></a>EF Database First s ASP.NET MVC: přizpůsobení zobrazení
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.
> 
> Tato části této série se zaměřuje na změny automaticky generované zobrazení k vylepšení v prezentaci.


## <a name="add-enrolled-courses-to-student-details"></a>Přidat zaregistrovaná kurzy Podrobnosti studenta

Generovaný kód poskytuje dobrým výchozím bodem pro vaši aplikaci, ale neposkytuje nutně všechny funkce, které potřebujete ve vaší aplikaci. Můžete upravit kód pro konkrétní požadavkům vaší aplikace. V současné době aplikace zaregistrovaná kurzy pro vybrané student nezobrazuje. V této části přidáte zaregistrované kurzy pro každého studenta do **podrobnosti** zobrazení pro studenta.

Otevřít **Students/Details.cshtml**a pod poslední &lt;/dl&gt; kartu, ale před uzavírající &lt;/div&gt; značky, přidejte následující kód.

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

Tento kód vytvoří tabulku, která se zobrazí řádek pro každý záznam v tabulce registrace pro vybrané studentů. **Zobrazení** metoda vykreslí HTML pro objekt (modelItem), který představuje výraz. Použití zobrazení metody (místo v kódu jednoduše vložení hodnoty vlastnosti) do Ujistěte se, že hodnota formátována správně na základě jeho typu a šablonu pro daný typ. V tomto příkladu každý výraz vrátí jedinou vlastnost z aktuální záznam ve smyčce a hodnoty jsou primitivní typy, které jsou generovány jako text.

Přejděte do zobrazení pro studenty/Index znovu a vyberte **podrobnosti** pro jeden z studenty. Uvidíte, že registrovaná kurzy byla zahrnuta v zobrazení.

![studenta s registrací](customizing-a-view/_static/image1.png)

> [!div class="step-by-step"]
> [Předchozí](changing-the-database.md)
> [další](enhancing-data-validation.md)
