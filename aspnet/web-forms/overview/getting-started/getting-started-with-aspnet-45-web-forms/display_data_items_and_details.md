---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Zobrazení datových položek a podrobností | Dokumentace Microsoftu
author: Erikre
description: V této sérii kurzů se seznámíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio 2017
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396218"
---
<a name="display-data-items-and-details"></a>Zobrazení datových položek a podrobnosti
====================
by [Erik Reitan](https://github.com/Erikre)

> V této sérii kurzů se naučíte se základy vytváření aplikace webových formulářů ASP.NET s ASP.NET 4.7 a Microsoft Visual Studio 2017.

V tomto kurzu se dozvíte, jak zobrazit datové položky a podrobnosti položky dat s webovými formuláři ASP.NET a Entity Framework Code First. V tomto kurzu navazuje na předchozí kurz o "Uživatelské rozhraní a navigace" jako část série kurzů Store Wingtip hračka. Po dokončení tohoto kurzu, zobrazí se vám produkty na *ProductsList.aspx* stránky a podrobnosti produktu na *ProductDetails.aspx* stránky.

## <a name="youll-learn-how-to"></a>Se dozvíte, jak:

- Přidejte ovládací prvek dat pro zobrazení produktů z databáze
- Připojení ovládacího prvku na vybraná data
- Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu z databáze
- Načíst hodnotu z řetězce dotazu a tuto hodnotu použít k omezení dat načtených z databáze

### <a name="features-introduced-in-this-tutorial"></a>Vlastnosti představené v tomto kurzu:

- Vazby modelu
- Zprostředkovatele hodnot

## <a name="add-a-data-control"></a>Přidejte ovládací prvek dat

Vazba dat k ovládacímu prvku serveru, můžete použít několik různých možností. Nejběžnější patří:

 * Přidání ovládacího prvku zdroje dat
 * Ruční přidání kódu
 * Pomocí vazby modelu

### <a name="use-a-data-source-control-to-bind-data"></a>Vytvoření vazby dat pomocí ovládacího prvku zdroje dat

Přidání ovládacího prvku zdroje dat umožňuje propojit ovládací prvek zdroje dat k ovládacímu prvku, který se zobrazí data. S tímto přístupem můžete deklarativně, místo prostřednictvím kódu programu, připojení ke zdrojům dat serverové ovládací prvky.

### <a name="code-by-hand-to-bind-data"></a>Kód ručně pro vytvoření vazby dat

Kódování ručně zahrnuje:

1. Čtení hodnoty
2. Kontrola, zda je null
3. Převod na příslušný typ.
4. Kontrola úspěchu převodu
5. Pomocí hodnoty v dotazu 

Tento přístup umožňuje mít plnou kontrolu nad logiky přístupu k datům.

### <a name="use-model-binding-to-bind-data"></a>Svázat data pomocí vazby modelu

Vazby modelu vám umožňuje vytvořit vazbu výsledky s mnohem menším množstvím kódu a poskytuje možnosti opakovaně používat funkce v rámci aplikace. To zjednodušuje práci s logiky zaměřený na kód – přístup k datům při stálém poskytování bohatých a datové vazby framework.

## <a name="display-products"></a>Zobrazit produkty

V tomto kurzu použijete k vytvoření vazby dat vazby modelu. Chcete-li nakonfigurovat ovládací prvek data pomocí vazby modelu vyberte data, nastavte ovládacího prvku `SelectMethod` vlastnost s názvem metody v kódu stránky. Ovládací prvek dat volá metodu v příslušnou dobu v životním cyklu stránky a automaticky sváže s vrácenými daty. Není nutné explicitně volat `DataBind` metody.

1. V **Průzkumníka řešení**, otevřete *ProductList.aspx*.
2. Nahraďte stávající kód tento kód:   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

Tento kód používá **ListView** ovládací prvek s názvem `productList` pro zobrazení produktů.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Styly a šablony, můžete definovat jak **ListView** ovládací prvek zobrazí data. Je vhodné pro data v libovolné opakující se struktura. I když to **ListView** příklad jednoduše zobrazí dat z databáze, můžete navíc bez kódu, umožňují uživatelům pro úpravy, vložení a odstranění dat a řadit a stránkovat data.

Nastavením `ItemType` vlastnost **ListView** řídit vazbový výraz `Item` je k dispozici a ovládací prvek stane silného typu. Jak je uvedeno v předchozím kurzu, můžete vybrat Podrobnosti objektu položky s podporou technologie IntelliSense, jako je například určení `ProductName`:

![Zobrazení dat položek a podrobnosti o – technologie IntelliSense](display_data_items_and_details/_static/image1.png)

Také používáte vazby modelu k určení `SelectMethod` hodnotu. Tato hodnota (`GetProducts`) odpovídá metodu přidejte do kódu vzadu pro zobrazení produktů v dalším kroku.

### <a name="add-code-to-display-products"></a>Přidejte kód pro zobrazení produktů

V tomto kroku přidáte kód k vyplnění **ListView** ovládacího prvku pomocí produktu data z databáze. Kód podporuje zobrazení všech produktů a jednotlivé kategorie produktů.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductList.aspx* a pak vyberte **zobrazit kód**.
2. Nahraďte existující kód ve třídě *ProductList.aspx.cs* soubor s tímto:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Tento kód ukazuje `GetProducts` metoda, která **ListView** ovládacího prvku `ItemType` odkazuje na vlastnost v *ProductList.aspx* stránky. Omezit rozsah výsledků do kategorie konkrétní databáze, nastaví kód `categoryId` hodnotu z hodnotu řetězce dotazu, který je předán *ProductList.aspx* stránce, pokud *ProductList.aspx* stránka je Přejde. `QueryStringAttribute` Třídy v `System.Web.ModelBinding` obor názvů slouží k načtení hodnoty proměnné řetězce dotazu `id`. Toto dá pokyn vazby modelu pro pokus o vytvoření vazby hodnotu z řetězce dotazu do `categoryId` parametr v době běhu.

Když platnou kategorii je předán jako řetězec dotazu na stránce výsledky dotazu jsou omezené na tyto produkty v databázi, které odpovídají `categoryId` hodnotu. Například pokud *ProductsList.aspx* Toto je adresa URL stránky:


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

Na stránce se zobrazí pouze produkty, kde `categoryId` rovná `1`.

Všechny produkty se zobrazí, pokud žádný řetězec dotazu je zahrnuta, když *ProductList.aspx* stránka se nazývá.

Zdroje hodnoty pro tyto metody jsou označovány jako *hodnota poskytovatelů* (například *QueryString*), a parametr atributy, které označují které zprostředkovatele hodnot pro použití se označují jako *hodnoty atributů poskytovatele* (například `id`). Technologie ASP.NET obsahuje zprostředkovatele hodnot a odpovídající atributy pro všemi typické zdroji uživatelský vstup v aplikaci webových formulářů, jako je například řetězec dotazu, soubory cookie, hodnot formuláře, ovládací prvky, zobrazit stav, stav relace a vlastnosti profilu. Můžete taky psát vlastní hodnotu poskytovatelů.

### <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci nyní chcete-li zobrazit všechny produkty nebo kategorie produktů.

1. Stisknutím klávesy **F5** během činnosti v sadě Visual Studio ke spuštění aplikace.  
   V prohlížeči se otevře a zobrazí *Default.aspx* stránky.

2. Vyberte **auta** z navigační nabídky kategorie produktu.  
   *ProductList.aspx* stránce zobrazí pouze **auta** kategorie produktů. Později v tomto kurzu zobrazíte podrobnosti o produktu.  

    ![Zobrazení dat položek a podrobnosti - automobilů](display_data_items_and_details/_static/image2.png)

3. Vyberte **produkty** z navigační nabídce v horní části.  
   Znovu *ProductList.aspx* se zobrazí stránka, ale tentokrát zobrazuje úplný seznam produktů.   

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image3.png)

4. Zavřete prohlížeč a vraťte se do sady Visual Studio.

### <a name="add-a-data-control-to-display-product-details"></a>Přidejte ovládací prvek dat zobrazíte podrobnosti o produktu

V dalším kroku upravíte značky *ProductDetails.aspx* stránky, které jste přidali v předchozím kurzu zobrazíte informace o konkrétních produktech.

1. V **Průzkumníka řešení**, otevřete *ProductDetails.aspx*.

2. Nahraďte stávající kód tento kód:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    Tento kód používá **FormView** ovládací prvek pro zobrazení Podrobnosti o konkrétního produktu. Tento kód použije metody jako metody slouží k zobrazení dat v *ProductList.aspx* stránky. **FormView** ovládacího prvku se používá k zobrazení jeden záznam v čase ze zdroje dat. Při použití **FormView** ovládacího prvku, je vytvořit šablony můžete zobrazit a upravit hodnoty vázané na data. Tyto šablony obsahují ovládací prvky, výrazy, vazby a formátování, které definují vzhled formuláře a funkce.

Předchozí kód s připojením k databázi se vyžaduje další kód.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na *ProductDetails.aspx* a potom klikněte na tlačítko **zobrazit kód**.  
   *ProductDetails.aspx.cs* soubor se zobrazí.

2. Nahraďte stávající kód s tímto kódem:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Tento kód kontroluje "`productID`" hodnotu řetězce dotazu. Pokud není nalezena hodnota platný řetězec dotazu, zobrazí se odpovídající produkt. Pokud řetězec dotazu nebyl nalezen nebo její hodnota není platná, zobrazí se žádné produkty.

### <a name="run-the-application"></a>Spuštění aplikace

Nyní můžete spustit aplikaci, abyste viděli zobrazí jednotlivé produkty podle ID produktu.

1. Stisknutím klávesy **F5** během činnosti v sadě Visual Studio ke spuštění aplikace.  
   V prohlížeči se otevře a zobrazí *Default.aspx* stránky.

2. Vyberte **lodě** navigační nabídce kategorie.  
   *ProductList.aspx* zobrazí se stránka.

3. Vyberte **papíru loď** ze seznamu produktů.
   *ProductDetails.aspx* zobrazí se stránka.

    ![Zobrazení dat položek a podrobnosti - produkty](display_data_items_and_details/_static/image4.png)
    
4. Zavřete prohlížeč.


## <a name="additional-resources"></a>Další zdroje

[Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali značek a kódu zobrazit produkty a podrobnosti o produktu. Jste se dozvěděli o ovládací prvky dat silného typu, vazby modelu a zprostředkovatele hodnot. V dalším kurzu přidáte do nákupního košíku ukázkové aplikace Wingtip Toys. 

> [!div class="step-by-step"]
> [Předchozí](ui_and_navigation.md)
> [další](shopping-cart.md)
