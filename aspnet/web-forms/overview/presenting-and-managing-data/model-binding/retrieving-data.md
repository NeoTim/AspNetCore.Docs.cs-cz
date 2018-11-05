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
ms.openlocfilehash: b05f3780d7c4e4734b35c0d9377a89d6f3edb0f8
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021336"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>Načtení a zobrazení dat ovládacím prvkem vazby modelu a webové formuláře
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V této sérii kurzů ukazuje základní aspekty v použití vazby modelu s projektem aplikace webových formulářů ASP.NET. Vazby modelu díky dat interakce více přímočaré než pracující s daty objektů zdroje (například ObjectDataSource nebo SqlDataSource). Tato série začíná úvodní materiály a přesune pokročilejších pojmech v budoucích kurzech.
> 
> Vzoru vazby modelu funguje s technologií přístupu všechny data. V tomto kurzu budete používat Entity Framework, ale můžete použít technologii přístup data, která je nejvíc známými. Z ovládacího prvku serveru vázané na data, jako je například ovládacího prvku GridView, ListView, DetailsView nebo FormView zadáte názvy metod používaných pro výběr, aktualizace, odstraňování a vytváření data. V tomto kurzu můžete zadat hodnotu pro metody SelectMethod.
> 
> V rámci této metody pro načítání dat zadáte logiku. V dalším kurzu nastavíte hodnoty pro UpdateMethod a DeleteMethod InsertMethod.
> 
> Je možné [Stáhnout](https://go.microsoft.com/fwlink/?LinkId=286116) dokončený projekt v jazyce C# nebo VB. Ke stažení kódu funguje pomocí sady Visual Studio 2012 nebo Visual Studio 2013. Používá šablonu Visual Studio 2012, která se trochu liší od sady Visual Studio 2013 šablonu uvedenou v tomto kurzu.
> 
> V tomto kurzu spustíte aplikaci v sadě Visual Studio. Můžete také zpřístupnit aplikace přes Internet nasazením k poskytovateli hostingu. Společnost Microsoft nabízí bezplatné webových hostitelských služeb pro až 10 webových serverů ve  
>  [Bezplatný zkušební účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604). Informace o tom, jak nasadit webový projekt sady Visual Studio do Azure App Service Web Apps, najdete v článku [nasazení webu ASP.NET pomocí sady Visual Studio](../../deployment/visual-studio-web-deployment/introduction.md) řady. Tento kurz také ukazuje způsob použití migrace Entity Framework Code First pro nasazení databáze SQL serveru do služby Azure SQL Database.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Microsoft Visual Studio 2013 nebo Microsoft Visual Studio Express 2013 for Web
>   
> 
> V tomto kurzu funguje taky s Visual Studio 2012, ale bude existovat několik rozdílů v šabloně uživatelského rozhraní a projektu.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu budete:

1. Vytváření datových objektů, které odpovídají vysoké školy se zúčastnili studenti zapsáni na kurzy
2. Vytváření databázových tabulek z objektů
3. Naplnění databáze testovací data
4. Zobrazení dat v webového formuláře

## <a name="set-up-project"></a>Nastavení projektu

V sadě Visual Studio 2013, vytvořte nový **webová aplikace ASP.NET** volá **ContosoUniversityModelBinding**.

![Vytvoření projektu](retrieving-data/_static/image2.png)

Výběr šablony webových formulářů a nechte výchozí možnosti. Klikněte na tlačítko OK pro nastavení projektu.

![Vyberte webových formulářů](retrieving-data/_static/image3.png)

Nejprve vytvoříte několik malých změn, aby přizpůsobit vzhled webu. Otevřít **Site.Master** soubor a změňte nadpis na patří společnosti Contoso University místo Moje aplikace technologie ASP.NET.

[!code-aspx[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

Změňte text záhlaví z **název_aplikace** k **Contoso University**.

[!code-aspx[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

Také v Site.Master, změňte navigační odkazy, které se zobrazí v záhlaví tak, aby odrážely stránek, které jsou relevantní pro tuto lokalitu. Nebudete potřebovat buď **o** stránky nebo **kontakt** stránky je možné tyto odkazy odebrat. Místo toho budete potřebovat odkaz na stránku s názvem **studenty**. Tato stránka ještě nebyl vytvořen.

[!code-aspx[Main](retrieving-data/samples/sample3.aspx)]

Uložte a zavřete Site.Master.

Teď vytvoříte webový formulář pro zobrazení údajů studentů. Klikněte pravým tlačítkem na váš projekt a **přidat** **nová položka**. Vyberte **webové formuláře se stránkou předlohy** šablony a pojmenujte ho **Students.aspx**.

![Vytvoření stránky](retrieving-data/_static/image5.png)

Vyberte **Site.Master** jako hlavní stránka nového webového formuláře.

## <a name="create-the-data-models-and-database"></a>Vytvoření datové modely a databáze

Migrace Code First použije k vytvoření objektů a odpovídající databázových tabulek. Tyto tabulky uloží informace o studenty a jejich přípravy.

Ve složce modely, přidejte novou třídu s názvem **UniversityModels.cs**.

![Vytvoření tříd modelu](retrieving-data/_static/image7.png)

V tomto souboru Definujte třídy SchoolContext, studenty, registrace a kurzu následujícím způsobem:

[!code-csharp[Main](retrieving-data/samples/sample4.cs)]

SchoolContext třída je odvozena od položky DbContext, který spravuje připojení k databázi a změny v datech.

Ve třídě studentů, Všimněte si, že atributy, které se použily **FirstName**, **LastName**, a **rok** vlastnosti. Tyto atributy se použije pro ověřování dat v tomto kurzu. Pro zjednodušení kódu pro tento ukázkový projekt, jenom tyto vlastnosti byly označeny ověření dat atributy. V projektu aplikace skutečný platit pro všechny vlastnosti, které je třeba ověřenými daty, například vlastnosti v třídách registrace a kurzu atributů ověření.

Uložte UniversityModels.cs.

K nastavení databáze založené na těchto tříd budou používat nástroje pro migrace Code First.

V **Konzola správce balíčků**, spusťte příkaz:  
`enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

Pokud se příkaz úspěšně dokončí. zobrazí se zpráva s informacemi o tom, že povolíte migrace

![povolení migrace](retrieving-data/_static/image8.png)

Všimněte si, že se nový soubor s názvem **Configuration.cs** se vytvořil. V sadě Visual Studio je tento soubor otevřít automaticky po jeho vytvoření. Obsahuje třídu konfigurace **počáteční hodnoty** metodu, která umožňuje k předběžnému naplnění tabulky databáze s testovací data.

Přidejte následující kód k metodě počáteční hodnoty. Bude potřeba přidat **pomocí** příkaz **ContosoUniversityModelBinding.Models** obor názvů.

[!code-csharp[Main](retrieving-data/samples/sample5.cs)]

Uložte Configuration.cs.

V konzole Správce balíčků, spusťte příkaz `add-migration initial`.

Potom spusťte příkaz `update-database`.

Pokud se zobrazí výjimka při spuštění tohoto příkazu, je možné, že mají různé hodnoty pro StudentID a CourseID z hodnot v metodě počáteční hodnoty. Otevření těchto tabulek v databázi a najděte existující hodnoty pro StudentID a CourseID. Přidejte tyto hodnoty v kódu pro synchronizace replik indexů tabulce registrace.

Soubor databáze byl přidán, ale aktuálně je skryté v projektu. Klikněte na tlačítko **zobrazit všechny soubory** k zobrazení souboru.

![Zobrazit všechny soubory](retrieving-data/_static/image10.png)

Všimněte si, že soubor .mdf se teď zobrazí v aplikaci\_složku Data.

![soubor databáze](retrieving-data/_static/image12.png)

Poklikejte na soubor MDF a otevřete Průzkumníka serveru. Tabulky nyní existují a jsou naplněný daty.

![tabulky databáze](retrieving-data/_static/image14.png)

## <a name="display-data-from-students-and-related-tables"></a>Zobrazení dat z studenty a související tabulky

S daty v databázi nyní jste připraveni k načtení těchto dat a zobrazení na webové stránce. Budete používat **GridView** ovládací prvek pro zobrazení dat ve sloupci a řádky.

Otevřete Students.aspx a vyhledejte **MainContent** zástupný symbol. V rámci tohoto zástupného symbolu, přidejte **GridView** ovládací prvek, který obsahuje následující kód.

[!code-aspx[Main](retrieving-data/samples/sample6.aspx)]

Existuje několik důležitých konceptů v tomto kódu značek můžete setkat. Napřed si všimněte, že je hodnota pro **metoda SelectMethod** vlastnost v prvku GridView. Tato hodnota určuje název metody, která se používá k načítání dat pro prvku GridView. Tato metoda vytvoří v dalším kroku. Za druhé, Všimněte si, že **ItemType** je nastavena na třídu Student, který jste vytvořili dříve. Nastavením této hodnoty mohou odkazovat na vlastnosti této třídy v kódu značek. Například třída Student obsahuje kolekci s názvem registrace. Můžete použít **Item.Enrollments** načíst tuto kolekci a pak použijte syntaxi LINQ k načtení souhrnu zaregistrovaná kredity pro každého studenta.

V souboru kódu na pozadí, je třeba přidat metodu, která je určená pro **metoda SelectMethod** hodnotu. Otevřít **Students.aspx.cs**a přidejte **pomocí** příkazů **ContosoUniversityModelBinding.Models** a **System.Data.Entity**obory názvů.

[!code-csharp[Main](retrieving-data/samples/sample7.cs)]

Potom přidejte následující metodu. Všimněte si, že název této metody odpovídá názvu, kterou jste zadali pro metody SelectMethod.

[!code-csharp[Main](retrieving-data/samples/sample8.cs)]

**Zahrnout** klauzule zlepšuje výkon tohoto dotazu, ale není nezbytné pro dotaz můžete pracovat. Bez klauzule zahrnout by se data obnovit pomocí opožděné načtení, což zahrnuje odesílání samostatný dotaz do databáze pokaždé, když týkající se, že se data načítají. Poskytnutím klauzule zahrnutí jsou data načtena pomocí předběžné načítání, což znamená, že všechna související data je získat pomocí jediného dotazu databáze. V případech, kde je většina souvisejících dat nesmí být použit, nemůžou dočkat, až načítání může být méně efektivní, protože je načíst další data. Nicméně v tomto případě předběžné načítání poskytuje nejlepší výkon vzhledem k tomu, že se pro každý záznam zobrazí související data.

Další informace o posouzení výkonu při načítání související data, najdete v článku v části s názvem **opožděné Eager a explicitní načítání souvisejících dat** v [čtení souvisejících dat s Entity Framework v ASP Aplikace .NET MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) tématu.

Ve výchozím nastavení data seřazené podle hodnoty vlastnosti označeným jako klíč. Můžete přidat klauzuli OrderBy zadat jinou hodnotu pro řazení. V tomto příkladu se používá výchozí vlastnost StudentID pro řazení. V [řazení, stránkování a filtrování dat](sorting-paging-and-filtering-data.md) tématu, vám umožní uživateli vybrat sloupec pro řazení.

Spuštění webové aplikace a přejděte na stránku pro studenty. Na stránce studenty zobrazí následující informace studentů.

![zobrazení dat](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>Automatické generování metody vazby modelu

Při práci prostřednictvím v této sérii kurzů, můžete jednoduše zkopírovat kód z kurzu do projektu. Jednou z nevýhod tohoto přístupu je, že nemusí se dozvěděli o funkci poskytovaný sadou Visual Studio k automatickému generování kódu pro metody vazby modelu. Při práci na vašich vlastních projektů, automatické generování kódu můžete vám ušetří čas a nápovědu získáte představu o tom, jak implementovat operace. Tato část popisuje funkci generování automatických kódu. Tato část je pouze informativní a neobsahuje žádný kód, který budete muset implementovat ve vašem projektu.

Při nastavování hodnoty pro **metoda SelectMethod**, **UpdateMethod**, **InsertMethod**, nebo **DeleteMethod** vlastností v kódu značek můžete vybrat **vytvořit novou metodu** možnost.

![Vytvořit novou metodu](retrieving-data/_static/image18.png)

Visual Studio nejen vytvoří metodu v kódu s správný podpis, ale také vygeneruje implementační kód, abychom vám s prováděním operace. Pokud nejprve nastavíte **ItemType** vlastnost před použitím funkci generování automatických kód vygenerovaný kód bude používat tento typ pro operace. Například při nastavení vlastnosti UpdateMethod, následující kód je automaticky generován:

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

Výše uvedený kód znovu, nemusí být přidány do projektu. V dalším kurzu budete implementovat metod pro aktualizaci, odstranění a přidání nová data.

## <a name="conclusion"></a>Závěr

V tomto kurzu jste vytvořili tříd datových modelů a generují databázi z těchto tříd. Naplní tabulek databáze se testovací data. Vazby modelu se používá k načtení dat z databáze a pak se zobrazují data v GridView.

V dalším [kurzu](updating-deleting-and-creating-data.md) v této sérii, vám umožní aktualizace, odstraňování a vytváření data.

> [!div class="step-by-step"]
> [Next](updating-deleting-and-creating-data.md)
