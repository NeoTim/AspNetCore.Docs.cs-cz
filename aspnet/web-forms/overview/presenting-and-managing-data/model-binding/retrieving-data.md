---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: Načtení a zobrazení dat ovládacím prvkem modelu vazby a webových formulářů | Dokumentace Microsoftu
author: Rick-Anderson
description: V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Data interakce díky vazby modelu další přímo-...
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: c53c27f4852eab9813bd917315111e7cd3b04953
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396282"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře
====================

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
>  Vzoru vazby modelu funguje s technologií přístupu všechny data. V tomto kurzu budete používat Entity Framework, ale můžete použít technologii přístup data, která je nejvíc známými. Z ovládacího prvku serveru vázané na data, jako je například ovládacího prvku GridView, ListView, DetailsView nebo FormView zadáte názvy metod používaných pro výběr, aktualizace, odstraňování a vytváření data. V tomto kurzu můžete zadat hodnotu pro metody SelectMethod. 
> 
> V rámci této metody pro načítání dat zadáte logiku. V dalším kurzu nastavíte hodnoty pro UpdateMethod a DeleteMethod InsertMethod.
>
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v C# nebo Visual Basic. Ke stažení kódu funguje pomocí sady Visual Studio 2012 a novější. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2017 šablonu uvedenou v tomto kurzu.
> 
> V tomto kurzu spustíte aplikaci v sadě Visual Studio. Můžete také nasadit aplikaci do poskytovatele hostitelských služeb a zpřístupnit přes internet. Společnost Microsoft nabízí bezplatné webových hostitelských služeb pro až 10 webových serverů ve  
>  [Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604). Informace o tom, jak nasadit webový projekt sady Visual Studio do Azure App Service Web Apps, najdete v článku [nasazení webu ASP.NET pomocí sady Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) řady. Tento kurz také ukazuje způsob použití migrace Entity Framework Code First pro nasazení databáze SQL serveru do služby Azure SQL Database.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> - Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017
>   
> V tomto kurzu se také pracuje s Visual Studio 2012 a Visual Studio 2013, ale existují určité rozdíly v šabloně uživatelského rozhraní a projektu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

* Vytváření datových objektů, které odpovídají vysoké školy se zúčastnili studenti zapsáni na kurzy
* Vytváření databázových tabulek z objektů
* Naplnění databáze testovací data
* Zobrazení dat v webového formuláře

## <a name="create-the-project"></a>Vytvoření projektu

1. Vytvořte v sadě Visual Studio 2017 **webová aplikace ASP.NET (.NET Framework)** projekt s názvem **ContosoUniversityModelBinding**.

   ![Vytvoření projektu](retrieving-data/_static/image19.png)

2. Vyberte **OK**. Zobrazí se dialogové okno Vybrat šablonu.

   ![Vyberte webových formulářů](retrieving-data/_static/image3.png)

3. Vyberte **webových formulářů** šablony. 

4. V případě potřeby změňte ověření **jednotlivé uživatelské účty**. 

5. Vyberte **OK** pro vytvoření projektu.

## <a name="modify-site-appearance"></a>Upravit vzhled webu

   Proveďte několik změn pro přizpůsobení vzhledu webu. 
   
   1. Otevřete soubor Site.Master.
   
   2. Změna názvu zobrazíte **Contoso University** a ne **My ASP.NET Application**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. Změní celý text záhlaví z **název_aplikace** k **Contoso University**.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. Změna navigačních odkazů záhlaví na lokalitu odpovídající značky. 
   
      Odebrat odkazy na **o** a **kontakt** a místo toho propojit **studenty** stránky, které vytvoříte.

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. Uložte Site.Master.

## <a name="add-a-web-form-to-display-student-data"></a>Přidejte webový formulář pro zobrazení údajů studentů

   1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt, vyberte **přidat** a potom **nová položka**. 
   
   2. V **přidat novou položku** dialogové okno, vyberte **webové formuláře se stránkou předlohy** šablony a pojmenujte ho **Students.aspx**.

      ![Vytvoření stránky](retrieving-data/_static/image5.png)

   3. Vyberte **Přidat**.
   
   4. Webový formulář stránky předlohy, vyberte **Site.Master**.
   
   5. Vyberte **OK**.
   

## <a name="add-the-data-model"></a>Přidání datového modelu

V **modely** složky, přidejte třídu pojmenovanou **UniversityModels.cs**.

   1. Klikněte pravým tlačítkem na **modely**vyberte **přidat**a potom **nová položka**. Zobrazí se dialogové okno **Přidat novou položku**.

   2. V levé navigační nabídce vyberte **kód**, pak **třídy**.

      ![Vytvoření tříd modelu](retrieving-data/_static/image20.png)

   3. Název třídy **UniversityModels.cs** a vyberte **přidat**.

      V tomto souboru, definujte `SchoolContext`, `Student`, `Enrollment`, a `Course` třídy následujícím způsobem:

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      `SchoolContext` Třída odvozena z `DbContext`, který spravuje připojení k databázi a změny v datech.

      V `Student` třídy, Všimněte si, že atributy použité `FirstName`, `LastName`, a `Year` vlastnosti. Tento kurz používá pro ověření dat těchto atributů. Pro zjednodušení kódu, jenom tyto vlastnosti jsou označeny ověření dat atributy. V projektu aplikace skutečný platit atributů ověření pro všechny vlastnosti nutnosti ověření.

   4. Uložte UniversityModels.cs.

## <a name="set-up-the-database-based-on-classes"></a>Nastavení databáze, které jsou založeny na třídách

Tento kurz používá [migrace Code First](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/) k vytváření objektů a databázových tabulek. Tyto tabulky ukládání informací o studenty a jejich přípravy.

   1. Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**.

   2. V **Konzola správce balíčků**, spusťte tento příkaz:  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      Pokud se příkaz úspěšně dokončí, zobrazí se zpráva s informacemi o tom, že migrace byla povolena.

      ![povolení migrace](retrieving-data/_static/image8.png)

      Všimněte si, že soubor s názvem *Configuration.cs* se vytvořil. `Configuration` Třída nemá `Seed` metodu, která můžete předvyplnit databázových tabulek s testovací data.

## <a name="pre-populate-the-database"></a>Předběžnému naplnění databáze

   1. Otevřete Configuration.cs.
   
   2. Přidejte následující kód, který `Seed` metody. Přidejte také `using` příkaz `ContosoUniversityModelBinding. Models` oboru názvů.

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. Uložte Configuration.cs.

   4. V konzole Správce balíčků, spusťte příkaz **přidat migrace počáteční**.

   5. Spusťte příkaz **aktualizace databáze**.

      Pokud se zobrazí výjimka při spuštění tohoto příkazu `StudentID` a `CourseID` hodnoty mohou být odlišné od `Seed` hodnoty metody. Otevřete tyto databázové tabulky a vyhledejte existující hodnoty pro `StudentID` a `CourseID`. Přidejte tyto hodnoty do kódu pro synchronizace replik indexů `Enrollments` tabulky.

## <a name="add-a-gridview-control"></a>Přidání ovládacího prvku GridView.

Pomocí dat z databáze mají údaj vyplněný teď jste připravení tato data načíst a zobrazit je. 

1. Otevřete Students.aspx.

2. Vyhledejte `MainContent` zástupný symbol. V rámci tohoto zástupného symbolu, přidejte **GridView** ovládací prvek, který obsahuje tento kód.

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   Co je potřeba mějte na paměti:
   * Všimněte si, že tato hodnota nastavena pro `SelectMethod` vlastnost v prvku GridView. Tato hodnota určuje metodu použitou k načtení dat prvku GridView, kterou vytvoříte v dalším kroku. 
   
   * `ItemType` Je nastavena na `Student` třídy vytvořili dříve. Toto nastavení umožňuje odkazovat na vlastnosti třídy v kódu. Například `Student` třída má kolekci s názvem `Enrollments`. Můžete použít `Item.Enrollments` načíst tuto kolekci a pak použít [syntaxi LINQ](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) načíst každý student je zaregistrovaná kredity součet.
   
3. Save Students.aspx.

## <a name="add-code-to-retrieve-data"></a>Přidejte kód pro načtení dat

   V souboru modelu code-behind Students.aspx přidejte zadaný pro metodu `SelectMethod` hodnotu. 
   
   1. Open Students.aspx.cs.
   
   2. Přidat `using` příkazů `ContosoUniversityModelBinding. Models` a `System.Data.Entity` obory názvů.

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. Přidejte metodu, která jste zadali pro `SelectMethod`:

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      `Include` Klauzule zvyšuje výkon dotazů, ale není povinné. Bez `Include` klauzule, data jsou načítány s použitím [ *opožděné načtení*](https://en.wikipedia.org/wiki/Lazy_loading), což zahrnuje odesílání samostatný dotaz do databáze pokaždé, když týkající se data načítají. S `Include` klauzule, data jsou načítány s použitím *předběžné načítání*, což znamená, že jeden databázový dotaz načte všechny související data. Pokud související data se nepoužívá, předběžné načítání je méně efektivní, protože je načtena další data. Ale v takovém případě předběžné načítání umožňuje nejlepší výkon, protože se pro každý záznam zobrazí související data.

      Další informace o aspektech týkajících se výkonu při načítání související data, najdete v článku **opožděné Eager a explicitní načítání souvisejících dat** tématu [čtení souvisejících dat s Entity Framework v ASP.NET Aplikace MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) článku.

      Ve výchozím nastavení data seřazené podle hodnoty vlastnosti označeným jako klíč. Můžete přidat `OrderBy` klauzule můžete zadat hodnotu různé řazení. V tomto příkladu výchozí `StudentID` vlastnost se používá pro řazení. V [řazení, stránkování a filtrování dat](sorting-paging-and-filtering-data.md) článku, uživatel je povolená a vyberte sloupec pro řazení.
 
   4. Save Students.aspx.cs.

## <a name="run-your-application"></a>Spuštění aplikace 

Spuštění webové aplikace (**F5**) a přejděte **studenty** stránku, která se zobrazí následující:

   ![zobrazení dat](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>Automatické generování metody vazby modelu

Při práci prostřednictvím v této sérii kurzů, můžete jednoduše zkopírovat kód z kurzu do projektu. Jednou z nevýhod tohoto přístupu je, že nemusí se dozvěděli o funkci poskytovaný sadou Visual Studio k automatickému generování kódu pro metody vazby modelu. Při práci na vašich vlastních projektů, automatické generování kódu můžete vám ušetří čas a nápovědu získáte představu o tom, jak implementovat operace. Tato část popisuje funkci generování automatických kódu. Tato část je pouze informativní a neobsahuje žádný kód, který budete muset implementovat ve vašem projektu. 

Při nastavování hodnoty pro `SelectMethod`, `UpdateMethod`, `InsertMethod`, nebo `DeleteMethod` vlastností v kódu značek, můžete vybrat **vytvořit novou metodu** možnost.

![Vytvořit metodu](retrieving-data/_static/image18.png)

Visual Studio nejen vytvoří metodu v kódu s správný podpis, ale také vygeneruje implementační kód k provedení této operace. Pokud nejprve nastavíte `ItemType` vlastnost před použitím automatické generování kódu funkce, které zadejte generovaný kód používá pro operace. Například při nastavení `UpdateMethod` se automaticky vygeneruje vlastnost, následující kód:

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

Tento kód znovu, nemusí být přidány do projektu. V dalším kurzu budete implementovat metody pro aktualizaci, odstranění a přidání nových dat.

## <a name="summary"></a>Souhrn

V tomto kurzu jste vytvořili tříd datových modelů a generují databázi z těchto tříd. Naplní tabulek databáze se testovací data. Vazby modelu se používá k načtení dat z databáze a pak se zobrazují data v GridView.

V dalším [kurzu](updating-deleting-and-creating-data.md) v této sérii budou moci aktualizace, odstraňování a vytváření data.

> [!div class="step-by-step"]
> [Next](updating-deleting-and-creating-data.md)
