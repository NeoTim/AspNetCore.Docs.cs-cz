---
uid: web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
title: Přidání vrstvy obchodní logiky do projektu, který používá vazbu modelu a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 7ef664b3-1cc8-4cbf-bb18-9f0f3a3ada2b
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/adding-business-logic-layer
msc.type: authoredcontent
ms.openlocfilehash: 4ba1830ce51f257e18880f752b249dbeb77f504e
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021648"
---
<a name="adding-business-logic-layer-to-a-project-that-uses-model-binding-and-web-forms"></a>Přidání vrstvy obchodní logiky do projektu, který používá vazbu modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Tento kurz ukazuje, jak pomocí vazby modelu vrstvy obchodní logiky. Nastavíte člen OnCallingDataMethods k určení, že objekt než aktuální stránku slouží k volání metody data.
> 
> V tomto kurzu vychází z vytvořeného v projektu [starší](retrieving-data.md) části této série.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.


## <a name="what-youll-build"></a>Co budete vytvářet

Vazby modelu umožňuje ukládat kód interakce data v souboru kódu pro webovou stránku nebo ve třídě samostatné obchodní logiku. Z předchozích kurzů ukázaly, jak používat soubory kódu na pozadí pro kód interakce data. Tento postup funguje u malých sítích, ale to může vést k kód opakování a větší potíže při udržování velké lokality. To může být velmi obtížné programově testovat kód, který se nachází v soubory kódu na pozadí, protože neexistuje žádný abstraktní vrstvu.

A centralizovat data interakce kód, můžete vytvořit vrstvy obchodní logiky, která obsahuje veškerou logiku pro interakci s daty. Poté je zapotřebí zavolat vrstvy obchodní logiky z vašich webových stránek. Tento kurz ukazuje, jak přesunout veškerý kód, který jste napsali v předchozích kurzech do vrstvy obchodní logiky a pak použít tento kód ze stránek.

V tomto kurzu budete:

1. Přesunout kód z použití modelu code-behind soubory do vrstvy obchodní logiky
2. Změňte své ovládací prvky data vázaná k volání metody v vrstvy obchodní logiky

## <a name="create-business-logic-layer"></a>Vytvoření vrstvy obchodní logiky

Teď vytvoříte třídu, která je volána z webových stránek. Metody v této třídě vypadat podobně jako metody, které jste použili v předchozích kurzech a atributy zprostředkovatele hodnoty.

Nejprve přidejte novou složku s názvem **BLL**.

![Přidat složku](adding-business-logic-layer/_static/image1.png)

Ve složce BLL vytvořte novou třídu s názvem **SchoolBL.cs**. Bude obsahovat všechny operace dat, které původně nacházejí v soubory kódu na pozadí. Metody jsou téměř stejné jako metody v souboru kódu na pozadí, ale bude obsahovat některé změny.

Je nejdůležitější změny si uvědomit, že jsou již spuštěny kód z v rámci instance **stránky** třídy. Obsahuje třídy stránky **TryUpdateModel** metoda a **ModelState** vlastnost. Pokud tento kód je přesunuta do vrstvy obchodní logiky, už žádné instance třídy stránky pro volání těchto členů. Chcete-li získat chcete tento problém vyřešit, je nutné přidat **ModelMethodContext** parametr na jakoukoli metodu, která přistupuje k TryUpdateModel nebo ModelState. Tento parametr ModelMethodContext použijte volání TryUpdateModel nebo načíst ModelState. Chcete-li změnit cokoli, co na webové stránce, aby se zohlednily tento nový parametr nepotřebujete.

Nahraďte kód v SchoolBL.cs následujícím kódem.

[!code-csharp[Main](adding-business-logic-layer/samples/sample1.cs)]

## <a name="revise-existing-pages-to-retrieve-data-from-business-logic-layer"></a>Upravit existující stránky k načtení dat z vrstvy obchodní logiky

Nakonec převede na stránkách Students.aspx AddStudent.aspx a Courses.aspx pomocí dotazů v souboru kódu na pozadí pomocí vrstvy obchodní logiky.

Soubory kódu na pozadí pro studenty, AddStudent a kurzy odstranit nebo okomentovat následující metody dotazu:

- studentsGrid\_GetData
- studentsGrid\_UpdateItem
- studentsGrid\_DeleteItem
- addStudentForm\_metody InsertItem
- coursesGrid\_GetData

Teď by měl mít žádný kód v souboru kódu na pozadí, který se týká operace s daty.

**OnCallingDataMethods** obslužná rutina události vám umožní zadat objekt, který použijete pro metody data. V Students.aspx přidejte hodnotu pro tuto obslužnou rutinu události a změnit názvy metod data na názvy metod ve třídě obchodní logiku.

[!code-aspx[Main](adding-business-logic-layer/samples/sample2.aspx?highlight=3-4,8)]

V souboru kódu na pozadí pro Students.aspx Definujte obslužnou rutinu události pro událost CallingDataMethods. V této obslužné rutiny události zadejte třídu obchodní logiku pro operace s daty.

[!code-csharp[Main](adding-business-logic-layer/samples/sample3.cs)]

V AddStudent.aspx proveďte podobné změny.

[!code-aspx[Main](adding-business-logic-layer/samples/sample4.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample5.cs)]

V Courses.aspx proveďte podobné změny.

[!code-aspx[Main](adding-business-logic-layer/samples/sample6.aspx?highlight=3-4)]

[!code-csharp[Main](adding-business-logic-layer/samples/sample7.cs)]

Spusťte aplikaci a Všimněte si, že všechny stránky fungovat jako předtím měli. Logiku ověřování také funguje správně.

## <a name="conclusion"></a>Závěr

V tomto kurzu se znovu strukturované aplikace pomocí vrstvy přístupu k datům a vrstvu obchodní logiky. Zadali jste, že ovládací prvky dat pomocí objektu, který není na aktuální stránce pro operace s daty.

> [!div class="step-by-step"]
> [Předchozí](using-query-string-values-to-retrieve-data.md)
