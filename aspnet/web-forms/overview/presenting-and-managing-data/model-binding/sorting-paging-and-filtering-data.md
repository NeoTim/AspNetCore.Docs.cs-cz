---
uid: web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
title: Řazení, stránkování a filtrování dat pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 266e7866-e327-4687-b29d-627a0925e87d
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/sorting-paging-and-filtering-data
msc.type: authoredcontent
ms.openlocfilehash: 624f98cea6030e0b7b022f86c4c1aa37f1db9726
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020947"
---
<a name="sorting-paging-and-filtering-data-with-model-binding-and-web-forms"></a>Řazení, stránkování a filtrování dat pomocí vazby modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Tento kurz ukazuje, jak přidat řazení, stránkování a filtrování dat pomocí vazby modelu.
> 
> V tomto kurzu vychází z projektu vytvořeného v prvním [část](retrieving-data.md) řady.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

1. Povolit řazení a stránkování dat
2. Povolit filtrování dat na základě výběru uživatelem.

## <a name="add-sorting"></a>Přidání řazení

Je velmi snadno povolit řazení v prvku GridView. V souboru Student.aspx, stačí nastavit **AllowSorting** k **true** v prvku GridView. Není nutné nastavovat **SortExpression** hodnotu pro každý sloupec jako hodnota se automaticky použije. GridView upraví dotaz pro přidání řazení dat podle vybrané hodnoty. Zvýrazněný kód uvedený níže ukazuje přidání, že budete muset udělat umožňující řazení.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample1.aspx?highlight=5)]

Spuštění webové aplikace a testování řazení záznamech studentů podle hodnot v různých sloupcích.

![studenti řazení](sorting-paging-and-filtering-data/_static/image2.png)

## <a name="add-paging"></a>Přidání stránkování

Povolení stránkování je také velmi snadné. V prvku GridView, nastavte **vlastnost AllowPaging** vlastnost **true** a nastavit **PageSize** na počet záznamů, které chcete zobrazit na jednotlivých stránkách. V tomto kurzu můžete ho nastavit na 4.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample2.aspx?highlight=5)]

Spusťte webovou aplikaci a Všimněte si, že teď záznamy jsou rozdělit přes více stránek s více než 4 záznamy zobrazené na jednu stránku.

![Přidání stránkování](sorting-paging-and-filtering-data/_static/image4.png)

Odložený dotaz zvyšuje efektivitu aplikace. Místo načtení celé datové sady, upraví prvku GridView. dotaz pro načtení záznamů pouze pro aktuální stránku.

## <a name="filter-records-by-user-selection"></a>Záznamy filtrovaly podle výběru uživatele

Vazby modelu přidá několik atributů, které vám umožní určit, jak nastavit hodnotu pro parametr metody vazby modelu. Tyto atributy jsou v **System.Web.ModelBinding** oboru názvů. Mezi ně patří:

- Ovládací prvek
- Soubor cookie
- Formulář
- Profil
- Řetězec dotazu
- Parametr RouteData
- Relace
- UserProfile
- Stav zobrazení

V tomto kurzu použijete hodnotu ovládacího prvku k filtrování záznamů, které se zobrazují v prvku GridView. Přidáte **ovládací prvek** atributu na metodu dotazu, který jste vytvořili dříve. V [později](using-query-string-values-to-retrieve-data.md) kurz, se použijí **QueryString** atribut pro parametr určující, zda je hodnota parametru pochází od hodnotu řetězce dotazu.

Nejprve přidejte výše ValidationSummary, rozevírací seznam pro filtrování, které studenty jsou uvedeny.

[!code-aspx[Main](sorting-paging-and-filtering-data/samples/sample3.aspx?highlight=3-11)]

V souboru kódu na pozadí upravte metodu vyberte získat hodnotu z ovládacího prvku a nastavte název parametru název ovládacího prvku, který obsahuje hodnotu.

Je nutné přidat **pomocí** příkaz pro **System.Web.ModelBinding** obor názvů, chcete-li vyřešit atribut ovládacího prvku.

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample4.cs)]

Následující kód ukazuje metody select znovu pracoval pro filtrování vrácená data na základě hodnoty z rozevíracího seznamu. Přidání atributu ovládacího prvku před parametr určuje, že hodnota pro tento parametr pochází z ovládacího prvku se stejným názvem.

[!code-csharp[Main](sorting-paging-and-filtering-data/samples/sample5.cs)]

Spusťte webovou aplikaci a vyberte jiné hodnoty z rozevíracího seznamu pro filtrování seznamu studentů.

![Filtr pro studenty](sorting-paging-and-filtering-data/_static/image6.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu jste povolili, řazení a stránkování dat. Také jste povolili, filtrování dat podle hodnoty ovládacího prvku.

V dalším [kurzu](integrating-jquery-ui.md) uživatelské rozhraní bude vylepšit integrací uživatelské rozhraní JQuery widgetu do šablony dynamická data.

> [!div class="step-by-step"]
> [Předchozí](updating-deleting-and-creating-data.md)
> [další](integrating-jquery-ui.md)
