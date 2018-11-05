---
uid: web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
title: Použití hodnot řetězce dotazu k filtrování dat pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: b90978bd-795d-4871-9ade-1671caff5730
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/using-query-string-values-to-retrieve-data
msc.type: authoredcontent
ms.openlocfilehash: 490279ec8457535031387e955e67550052764fff
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021115"
---
<a name="using-query-string-values-to-filter-data-with-model-binding-and-web-forms"></a>Použití hodnot řetězce dotazu pro filtrování dat pomocí vazby modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Tento kurz ukazuje, jak předat hodnotu v řetězci dotazu a tuto hodnotu použít k načtení dat pomocí vazby modelu.
> 
> V tomto kurzu vychází z vytvořeného v projektu [starší](retrieving-data.md) části této série.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

1. Přidejte novou stránku zobrazíte zaregistrovaná kurzy pro student
2. Načíst zaregistrovaná kurzů pro vybranou studenty na základě hodnoty v řetězci dotazu
3. Přidání hypertextového odkazu s hodnotou řetězce dotazu v zobrazení mřížky na novou stránku

Kroky v tomto kurzu jsou velmi podobné co jste se naučili v předchozím [kurzu](sorting-paging-and-filtering-data.md) k filtrování zobrazených studenty na základě výběru uživatelem v rozevíracím seznamu. V tomto kurzu jste použili **ovládací prvek** atributu v metodě select k určení, že hodnota parametru pochází z ovládacího prvku. V tomto kurzu budete používat **QueryString** atributu v metodě select k určení, že hodnota parametru pochází z řetězce dotazu.

## <a name="add-new-page-for-displaying-a-students-courses"></a>Přidejte novou stránku pro zobrazení student získal kurzy

Přidat nový webový formulář používající stránku předlohy Site.master a pojmenujte stránku **kurzy**.

V **Courses.aspx** přidejte zobrazení mřížky kurzy pro vybrané studentů.

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample1.aspx)]

## <a name="define-the-select-method"></a>Definování metody select

V **Courses.aspx.cs**, přidejte metody select s názvem zadaným v mřížkovém zobrazení **metoda SelectMethod** vlastnost. V této metodě budete definovat dotaz pro načtení student získal kurzy a určit, že parametr pochází z hodnotu řetězce dotazu se stejným názvem jako parametr.

Nejprve je nutné přidat následující **pomocí** příkazy.

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample2.cs)]

K Courses.aspx.cs, přidejte následující kód:

[!code-csharp[Main](using-query-string-values-to-retrieve-data/samples/sample3.cs)]

Atribut QueryString znamená, že hodnotu řetězce dotazu s názvem StudentID automaticky přiřazen k parametru v této metodě.

## <a name="add-hyperlink-with-query-string-value"></a>Přidání hypertextového odkazu s hodnotou řetězce dotazu

V zobrazení mřížky na Students.aspx přidáte pole hypertextového odkazu, který odkazuje na vaše nová stránka kurzů. Hypertextový odkaz bude obsahovat hodnotu řetězce dotazu s id studenta.

V Students.aspx přidejte následující pole do sloupce zobrazení mřížky přímo pod pole pro celkový počet kredity.

[!code-aspx[Main](using-query-string-values-to-retrieve-data/samples/sample4.aspx?highlight=7-8)]

Spusťte aplikaci a Všimněte si, že zobrazení mřížky teď obsahuje odkaz na kurzy.

![Přidání hypertextového odkazu](using-query-string-values-to-retrieve-data/_static/image1.png)

Když kliknete na některý z odkazů, zobrazí se vám tento studentů zaregistrovaná kurzy.

![Zobrazit kurzy](using-query-string-values-to-retrieve-data/_static/image2.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu jste přidali odkaz s hodnotou řetězce dotazu. Použít tuto hodnotu řetězce dotazu pro parametr metody select.

V dalším [kurzu](adding-business-logic-layer.md), kód se přesune z použití modelu code-behind soubory do vrstvy obchodní logiky a vrstva přístupu k datům.

> [!div class="step-by-step"]
> [Předchozí](integrating-jquery-ui.md)
> [další](adding-business-logic-layer.md)
