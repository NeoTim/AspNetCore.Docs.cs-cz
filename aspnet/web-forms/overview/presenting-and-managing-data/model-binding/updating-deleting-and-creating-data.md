---
uid: web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
title: Aktualizace, odstraňování a vytváření data pomocí vazby modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 602baa94-5a4f-46eb-a717-7a9e539c1db4
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
msc.type: authoredcontent
ms.openlocfilehash: 127543b0696b01f136b340d07f6f806b6a6fb1eb
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021570"
---
<a name="updating-deleting-and-creating-data-with-model-binding-and-web-forms"></a>Aktualizace, odstraňování a vytváření data pomocí vazby modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Tento kurz ukazuje, jak vytvářet, aktualizovat a odstranit data pomocí vazby modelu. Bude nastavte následující vlastnosti:
> 
> - Metoda DeleteMethod
> - Metoda InsertMethod
> - Metoda UpdateMethod
> 
> Tyto vlastnosti zobrazí název metody, která zpracovává odpovídající operace. V rámci této metody poskytují logiku pro interakci s daty.
> 
> V tomto kurzu vychází z projektu vytvořeného v prvním [část](retrieving-data.md) řady.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

1. Přidání šablon dynamických dat
2. Povolit aktualizaci a odstraňování dat prostřednictvím metody vazby modelu
3. Použít pravidla validace dat – povolit při vytvoření nového záznamu v databázi

## <a name="add-dynamic-data-templates"></a>Přidání šablon dynamických dat

Pokud chcete poskytovat nejlepší uživatelské prostředí a minimalizovat opakování kódu, budete používat dynamická data šablony. Vám umožní snadnou integraci dynamických dat předem připravené šablony do existující lokalitu pomocí instalace balíčku NuGet.

Z **spravovat balíčky NuGet**, nainstalujte **DynamicDataTemplatesCS**.

![dynamické datové šablony](updating-deleting-and-creating-data/_static/image1.png)

Všimněte si, že váš projekt nyní obsahuje složku s názvem **DynamicData**. V této složce najdete šablony, které budou automaticky použita pro dynamické ovládací prvky ve webových formulářů.

![Složka dynamických dat](updating-deleting-and-creating-data/_static/image2.png)

## <a name="enable-updating-and-deleting"></a>Povolit aktualizace a odstranění

Povolení uživatelům aktualizovat a odstraňovat záznamy v databázi je velmi podobný procesu pro načítání dat. V **UpdateMethod** a **DeleteMethod** vlastnosti, zadejte názvy metod, které provádějí tyto operace. Pomocí ovládacího prvku GridView můžete také zadat automatické generování upravit a odstranit tlačítka. Následující zvýrazněný kód ukazuje budou přidány do kódu ovládacího prvku GridView.

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample1.aspx?highlight=4-5)]

V souboru kódu na pozadí přidat sadu pomocí příkazu pro **System.Data.Entity.Infrastructure**.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample2.cs)]

Potom přidejte následující aktualizace a odstranění metody.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample3.cs)]

**TryUpdateModel** metoda platí odpovídající hodnoty vázané na data z webového formuláře pro datovou položku. Položku dat načte na základě hodnoty parametru id.

## <a name="enforce-validation-requirements"></a>Vynutit požadavky na ověření

Ověření atributy, které jste použili k vlastnostem FirstName, LastName a rok ve třídě Student automaticky vynucuje při aktualizaci data. Ovládací prvky DynamicField přidat klientských a serverových validátory na základě atributů ověření. Vlastnosti FirstName a LastName jsou povinné. Jméno nemůže být delší než 20 znaků a příjmení nemůže být delší než 40 znaků. Rok musí být platná hodnota pro výčet AcademicYear.

Pokud uživatel je v rozporu mezi požadavky na ověřování, aktualizace nebude pokračovat. Pokud chcete zobrazit chybová zpráva, přidejte ovládací prvek souhrnu ověření nad prvku GridView. Chcete-li zobrazit chyby ověření z vazby modelu, nastavte **ShowModelStateErrors** vlastnost nastavena na hodnotu **true**. 

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample4.aspx)]

Spusťte webovou aplikaci a aktualizovat a odstraní všechny záznamy.

![aktualizace dat](updating-deleting-and-creating-data/_static/image3.png)

Při v režimu úprav hodnotu pro vlastnost rok automaticky takto rozevírací seznam, Všimněte si, že. Vlastnost Year je hodnota výčtu a šablona dynamických dat pro hodnotu výčtu určí rozevírací seznam pro úpravy. Tuto šablonu lze najít otevřením **výčet\_Edit.ascx** soubor **DynamicData**/**FieldTemplates** složky.

Pokud zadáte platné hodnoty, aktualizace se úspěšně dokončí. Pokud porušují jeden z těchto požadavků ověřování, aktualizace nebude pokračovat a je nad mřížkou zobrazí chybová zpráva.

![Chybová zpráva](updating-deleting-and-creating-data/_static/image4.png)

## <a name="add-new-records"></a>Přidat nové záznamy

Ovládací prvek GridView nezahrnuje **InsertMethod** vlastnost a nelze jej proto použít pro přidání nového záznamu s vazbou modelu. Můžete najít vlastnost InsertMethod **FormView**, **DetailsView**, nebo **ListView** ovládacích prvků. V tomto kurzu použijete ovládacího prvku FormView přidáte nový záznam.

Nejprve přidejte odkaz na novou stránku, kterou vytvoříte pro přidání nového záznamu. Nad ValidationSummary přidejte:

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample5.aspx)]

Nový odkaz se zobrazí v horní části stránky obsahu pro studenty stránky.

![Nový odkaz](updating-deleting-and-creating-data/_static/image5.png)

Pak přidejte nový webový formulář používající stránku předlohy a pojmenujte ho **AddStudent**. Vyberte Site.Master jako stránky předlohy.

Vykreslí se pole pro přidání nového studenta pomocí **DynamicEntity** ovládacího prvku. Ovládací prvek DynamicEntity vykreslí této upravitelné vlastnosti třídy zadaná ve vlastnosti ItemType. Vlastnost StudentID byl označen atributem **[ScaffoldColumn(false)]** atribut, takže není vykresleno. V zástupném symbolu MainContent AddStudent stránky přidejte následující kód.

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample6.aspx)]

V souboru kódu na pozadí (AddStudent.aspx.cs) přidejte **pomocí** příkaz **ContosoUniversityModelBinding.Models** oboru názvů.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample7.cs)]

Potom přidejte následující metody k určení jak vložit nový záznam a obslužnou rutinu události pro tlačítko Storno.

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample8.cs)]

Uložte všechny změny.

Spusťte webovou aplikaci a vytvoření nového objektu student.

![Přidání nového studenta](updating-deleting-and-creating-data/_static/image6.png)

Klikněte na tlačítko **vložit** a Všimněte si, že se vytvořila nová studentů.

![zobrazení nového objektu student](updating-deleting-and-creating-data/_static/image7.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu jste povolili aktualizaci, odstranění a vytvoření data. Můžete zajistit, že ověřovací pravidla se použijí při interakci s daty.

V dalším [kurzu](sorting-paging-and-filtering-data.md) v této sérii, vám umožní řazení, stránkování a filtrování dat.

> [!div class="step-by-step"]
> [Předchozí](retrieving-data.md)
> [další](sorting-paging-and-filtering-data.md)
