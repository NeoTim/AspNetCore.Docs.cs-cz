---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Zobrazení datových položek a podrobností | Dokumentace Microsoftu
author: Erikre
description: V této sérii kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio Community 2017 pro Web
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207431"
---
<a name="display-data-items-and-details"></a>Zobrazení datových položek a podrobnosti
====================
podle [Erik Reitan](https://github.com/Erikre)

> V této sérii kurzů se naučíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio Community 2017 pro Web.

V tomto kurzu se dozvíte, jak k zobrazení datových položek a podrobnosti položky dat s webovými formuláři ASP.NET a Entity Framework Code First. V tomto kurzu navazuje na předchozí kurz o "Uživatelské rozhraní a navigace" jako část série kurzů Store Wingtip hračka. V kurzu dokončené produkty na *ProductsList.aspx* stránky a podrobnosti produktu na *ProductDetails.aspx* se zobrazí stránka.

## <a name="what-you-learn"></a>Co jste se naučili

- Přidejte ovládací prvek dat pro zobrazení databáze produktů.
- Připojení ovládacího prvku na vybraná data.
- Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu.
- Analyzovat hodnotu řetězce dotazu a použít ho k filtrování dat z načtené databáze.

Vlastnosti představené v tomto kurzu zahrnují vazby modelu a zprostředkovatele hodnot.

## <a name="add-a-data-control-to-display-products"></a>Přidejte ovládací prvek dat pro zobrazení produktů
 
Máte několik možností vytvoření vazby dat k ovládacímu prvku serveru. Nejběžnější patří:

 * Přidání ovládacího prvku zdroje dat
 * Ruční přidání kódu
 * Implementace vazby modelu

### <a name="use-a-data-source-control-to-bind-data"></a>Vytvoření vazby dat pomocí ovládacího prvku zdroje dat

Přidání ovládacího prvku zdroje dat odkazuje ovládací prvek zdroje dat na ovládací prvek, který zobrazuje data. S tímto přístupem můžete deklarativně, místo prostřednictvím kódu programu, připojení ke zdrojům dat serverové ovládací prvky.

### <a name="code-by-hand-to-bind-data"></a>Kód ručně pro vytvoření vazby dat

Kódování ručně zahrnuje:

1. Čtení hodnoty
2. Kontrola, zda je null
3. Převod na příslušný typ.
4. Kontrola úspěchu převodu
5. Provádění dotazu se převedená hodnota 

S tímto přístupem budete mít plnou kontrolu nad logiky přístupu k datům.

### <a name="use-model-binding-to-bind-data"></a>Svázat data pomocí vazby modelu

Vazby modelu svážete výsledky s mnohem menším množstvím kódu a poskytuje možnosti opakovaně používat funkce v rámci aplikace. To zjednodušuje práci s logiky zaměřený na kód – přístup k datům při stálém poskytování bohatých a datové vazby framework.

## <a name="display-products"></a>Zobrazit produkty

V tomto kurzu použijete k vytvoření vazby dat vazby modelu. Chcete-li nakonfigurovat ovládací prvek data pomocí vazby modelu vyberte data, nastavte ovládacího prvku `SelectMethod` vlastnost na metodu v kódu stránky. Ovládací prvek dat volá metodu v příslušnou dobu v životním cyklu stránky a automaticky sváže s vrácenými daty. Není nutné explicitně volat `DataBind` metody.

Práce pomocí následujících kroků, upravíte *ProductList.aspx* značek pro zobrazení produktů.

1. V **Průzkumníka řešení**, otevřete *ProductList.aspx*.

2. Nahraďte stávající kód následujícím kódem: 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

Předchozí kód používá **ListView** ovládací prvek s názvem `productList` pro zobrazení produktů.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Styly a šablony, můžete definovat jak **ListView** ovládací prvek zobrazí data. Je vhodné pro data v libovolné opakující se struktura. I když to **ListView** příklad jednoduše zobrazí dat z databáze, můžete navíc bez kódu, umožňují uživatelům pro úpravy, vložení a odstranění dat a řadit a stránkovat data.

Při nastavení `ItemType` vlastnost **ListView** řídit vazbový výraz `Item` je k dispozici a ovládací prvek stane silného typu. Jak je uvedeno v předchozím kurzu, můžete vybrat Podrobnosti objektu položky s podporou technologie IntelliSense, jako je například určení `ProductName`:

![Zobrazení dat položek a podrobnosti o – technologie IntelliSense](display_data_items_and_details/_static/image1.png)

S vazbou modelu, zadáváte `SelectMethod` hodnotu (`GetProducts`). Toto je metoda, přidejte do kódu vzadu pro zobrazení produktů v dalším kroku.

### <a name="add-code-to-display-products"></a>Přidejte kód pro zobrazení produktů

V tomto kroku přidáte kód k naplnění **ListView** ovládacího prvku pomocí dat z databáze produktů. Kód podporuje zobrazení všech produktů a jednotlivé kategorie produktů.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductList.aspx* a pak vyberte **zobrazit kód**.
2. Nahraďte existující kód ve třídě *ProductList.aspx.cs* soubor s tímto:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Tento kód ukazuje `GetProducts` metoda, která **ListView** ovládacího prvku `ItemType` odkazuje na vlastnost v *ProductList.aspx*. Omezit rozsah výsledků do kategorie konkrétní databáze, nastaví kód `categoryId` hodnotu z řetězce dotazu, který je předán *ProductList.aspx*. `QueryStringAttribute` Třídy v `System.Web.ModelBinding` obor názvů slouží k načtení proměnné řetězce dotazu `id`hodnotu. Toto dá pokyn modelu, v době běhu, kterým se sváže vazba hodnotu řetězce dotazu do `categoryId` parametru.

Když platnou kategorii (`categoryId`) je předán, výsledky jsou omezeny na databázi produkty dané kategorie. Například pokud *ProductsList.aspx* Toto je adresa URL stránky:

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

Na stránce se zobrazí pouze produkty, kde `categoryId` rovná `1`.

Pokud není předán žádný řetězec dotazu, zobrazí se všechny produkty.

Zdrojů hodnot pro tyto metody jsou volány *hodnota poskytovatelé* (například `QueryString`), a parametr atributy, které označují které zprostředkovatele hodnot pro použití se nazývají *hodnoty atributů poskytovatele* ( například `id`). Technologie ASP.NET obsahuje zprostředkovatele hodnot a atributů pro všechny typické webové formuláře aplikace uživatele vstupního zdroje. Patří mezi ně řetězec dotazu, soubory cookie, hodnot formuláře, ovládací prvky, zobrazit stav, stav relace a vlastnosti profilu. Můžete taky psát vlastní hodnotu poskytovatelů.

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci nyní chcete-li zobrazit všechny produkty nebo kategorie produktů.

1. V sadě Visual Studio, stiskněte klávesu **F5** ke spuštění aplikace.
 V prohlížeči se otevře a zobrazí *Default.aspx* stránky.

2. V nabídce kategorie produktu, vyberte **auta**.

   *ProductList.aspx* se zobrazí stránka zobrazující pouze produkty **auta** kategorie. Dále v tomto kurzu se zobrazí podrobnosti o produktu.

    ![Zobrazení dat položek a podrobnosti - automobilů](display_data_items_and_details/_static/image2.png)

3. Vyberte **produkty** z hlavní nabídky.
 *ProductList.aspx* stránka nyní zobrazuje všechny produkty. 

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image3.png)

4. Zavřete prohlížeč a vraťte se do sady Visual Studio.

### <a name="add-a-data-control-to-display-product-details"></a>Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu

Upravit *ProductDetails.aspx* značky, které jste přidali v předchozím kurzu zobrazíte informace o konkrétním produktu:

1. V **Průzkumníka řešení**, otevřete *ProductDetails.aspx*.

2. Nahraďte stávající kód tento kód:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

Tento kód používá **FormView** ovládací prvek pro zobrazení Podrobnosti o konkrétního produktu. Používá metody, stejně jako se používají k zobrazení dat v *ProductList.aspx*. **FormView** ovládacího prvku se používá k zobrazení jeden záznam v čase ze zdroje dat. Při použití **FormView** ovládacího prvku, je vytvořit šablony můžete zobrazit a upravit hodnoty vázané na data. Tyto šablony obsahují ovládací prvky, výrazy, vazby a formátování, které definují vzhled formuláře a funkce.

Předchozí kód s připojením k databázi se vyžaduje další kód.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductDetails.aspx* a pak vyberte **zobrazit kód**.  
   *ProductDetails.aspx.cs* soubor se zobrazí.

2. Nahraďte stávající kód s tímto:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Tento kód kontroluje "`productID`" hodnotu řetězce dotazu. Pokud platnou hodnotu nenajde, zobrazí se odpovídající produkt. Pokud řetězec dotazu nebyl nalezen nebo její hodnota není platná, zobrazí se žádné produkty.

### <a name="run-the-application"></a>Spuštění aplikace

Nyní můžete spustit aplikaci chcete zobrazit podrobnosti konkrétního produktu na základě ID produktu.

1. V sadě Visual Studio, stiskněte klávesu **F5** ke spuštění aplikace.  
 V prohlížeči se otevře na *Default.aspx*.

2. V nabídce Kategorie vyberte **lodě**.  
 *ProductList.aspx* zobrazí se stránka.

3. Vyberte **papír loď**.  
 *ProductDetails.aspx* zobrazí se stránka.   

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image4.png)

V dalším kurzu přidáte do aplikace Wingtip Toys nákupního košíku.

## <a name="additional-resources"></a>Další zdroje

[Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> [Předchozí](ui_and_navigation.md)
> [další](shopping-cart.md)
