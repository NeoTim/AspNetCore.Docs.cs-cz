---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'Kurz: Přizpůsobení zobrazení pro EF Database First s ASP.NET MVC aplikace'
description: Tento kurz se zaměřuje na změny automaticky generované zobrazení k vylepšení v prezentaci.
author: Rick-Anderson
ms.author: riande
ms.date: 01/24/2019
ms.topic: tutorial
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: 89b8a0eb84b6e287c45bc141c68a2c76e63b0e41
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236494"
---
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a>Kurz: Přizpůsobení zobrazení pro EF Database First s ASP.NET MVC aplikace

Pomocí generování uživatelského rozhraní ASP.NET, MVC a Entity Framework, můžete vytvořit webovou aplikaci, která poskytuje rozhraní pro existující databázi. V této sérii kurzů se dozvíte, jak automaticky vygenerovat kód, který umožňuje uživatelům zobrazit, upravit, vytvořit a odstranit data, která se nachází v databázové tabulce. Generovaný kód odpovídá sloupců v tabulce databáze.

Tento kurz se zaměřuje na změny automaticky generované zobrazení k vylepšení v prezentaci.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat kurzů na stránku podrobností studenta
> * Ověřit, že se na kurzy přidán na stránku

## <a name="prerequisites"></a>Požadavky

* [Změna databáze](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a>Přidat podrobnosti o studentovi kurzy

Generovaný kód poskytuje dobrým výchozím bodem pro vaši aplikaci, ale neposkytuje nutně všechny funkce, které potřebujete ve vaší aplikaci. Můžete upravit kód pro konkrétní požadavkům vaší aplikace. V současné době aplikace zaregistrovaná kurzy pro vybrané student nezobrazuje. V této části přidáte zaregistrované kurzy pro každého studenta do **podrobnosti** zobrazení pro studenta.

Otevřít **zobrazení** > **studenty** > *Details.cshtml*. Pod poslední &lt;/dl&gt; značky, ale před uzavírající &lt;/div&gt; značky, přidejte následující kód.

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

Tento kód vytvoří tabulku, která se zobrazí řádek pro každý záznam v tabulce registrace pro vybrané studentů. **Zobrazení** metoda vykreslí HTML pro objekt (modelItem), který představuje výraz. Použití zobrazení metody (místo v kódu jednoduše vložení hodnoty vlastnosti) do Ujistěte se, že hodnota formátována správně na základě jeho typu a šablonu pro daný typ. V tomto příkladu každý výraz vrátí jedinou vlastnost z aktuální záznam ve smyčce a hodnoty jsou primitivní typy, které jsou generovány jako text.

## <a name="confirm-courses-are-added"></a>Potvrďte, že jsou přidány kurzy

Spuštění řešení. Klikněte na tlačítko **seznamu studentů** a vyberte **podrobnosti** pro jeden z studenty. Uvidíte, že registrovaná kurzy byla zahrnuta v zobrazení.

![studenta s registrací](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání kurzů na stránku podrobností studenta
> * Potvrdit, že jsou na stránku přidá kurzy

Přejděte k dalšímu kurzu, přečtěte si, jak přidat datových poznámek k určení požadavků na ověření a zobrazení formátování.
> [!div class="nextstepaction"]
> [Vylepšení ověřování dat](enhancing-data-validation.md)
